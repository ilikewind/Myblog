---
layout: post
title: "巧用torch.backends.cudnn.benchmark减少训练时间"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags:
    - pytorch
---



大家在训练深度学习模型的时候，经常会使用 GPU 来加速网络的训练。但是说起 torch.backends.cudnn.benchmark 这个 GPU 相关的 flag，可能有人会感到比较陌生。在一般场景下，只要简单地在 PyTorch 程序开头将其值设置为 True，就可以大大提升卷积神经网络的运行速度。既然如此神奇，为什么 PyTorch 不将其默认设置为 True？它的适用场景是什么？为什么使用它可以提升效率？答案就在本文之中. <!--break-->

## 浓缩版

设置 torch.backends.cudnn.benchmark=True 将会让程序在开始时花费一点额外时间，为整个网络的每个卷积层搜索最适合它的卷积实现算法，进而实现网络的加速。适用场景是网络结构固定（不是动态变化的），网络的输入形状（包括 batch size，图片大小，输入的通道）是不变的，其实也就是一般情况下都比较适用。反之，如果卷积层的设置一直变化，将会导致程序不停地做优化，反而会耗费更多的时间。

行了，对于只想大致了解的读者，可以就此打住了，本文主要说的就是这么一件事，感谢阅读。但对于想刨根问底的读者来说，咱们继续往下看！本文篇幅较长，目录如下：



- 背景知识
- torch.backends.cudnn.benchmark！
- 等等，这行代码要加在哪里？
- PyTorch 中对应的源代码
- 实验结果
- 总结
- 附录：测试代码



## 背景知识

在说 torch.backends.cudnn.benchmark 之前，我们首先简单介绍一下 cuDNN。cuDNN 是英伟达专门为深度神经网络所开发出来的 GPU 加速库，针对卷积、池化等等常见操作做了非常多的底层优化，比一般的 GPU 程序要快很多。大多数主流深度学习框架都支持 cuDNN，PyTorch 自然也不例外。在使用 GPU 的时候，PyTorch 会默认使用 cuDNN 加速。但是，在使用 cuDNN 的时候，torch.backends.cudnn.benchmark 模式是为 False。所以就意味着，我们的程序可能还可以继续提速！



卷积层是卷积神经网络中的最重要的部分，也往往是运算量最大的部分。如果我们可以在底层代码中提升卷积运算的效率的话，就可以在不改变给定的神经网络结构的情况下，大大提升其训练和预测的速度。



对于卷积这个操作来说，其实现方式是多种多样的。最简单的实现方式就是使用多层循环嵌套，对于每张输入图像，对于每个要输出的通道，对于每个输入的通道，选取一个区域，同指定卷积核进行卷积操作，然后逐行滑动，直到整张图像都处理完毕，这个方法一般被称为 direct 法，这个方法虽然简单，但是看到这么多循环，我们就知道效率在一般情况下不会很高了。除此之外，实现卷积层的算法还有基于 GEMM (General Matrix Multiply) 的，基于 FFT 的，基于 Winograd 算法的等等，而且每个算法还有自己的一些变体。在一个开源的 C++ 库 triNNity 中，就实现了接近 80 种的卷积前向传播算法！



每种卷积算法，都有其特有的一些优势，比如有的算法在卷积核大的情况下，速度很快；比如有的算法在某些情况下内存使用比较小。给定一个卷积神经网络（比如 ResNet-101），给定输入图片的尺寸，给定硬件平台，实现这个网络最简单的方法就是对所有卷积层都采用相同的卷积算法（比如 direct 算法），但是这样运行肯定不是最优的；比较好的方法是，我们可以预先进行一些简单的优化测试，在每一个卷积层中选择最适合（最快）它的卷积算法，决定好每层最快的算法之后，我们再运行整个网络，这样效率就会提升不少。



这里有一个问题，为什么我们可以提前选择每层的算法，即使每次我们送入网络训练的图片是不一样的？即每次网络的输入都是变化的，那么我怎么确保提前选出来的最优算法同样也适用于这个输入呢？原因就是，对于给定输入来说，其具体值的大小是不影响卷积的运行时间的，只有其尺寸才会影响。举例来说，我们只要固定输入大小都是 (8, 64, 224, 224)，即 batch_size 为 8，输入的通道为 64，宽和高为 224，那么卷积层的运行时间都是几乎不变的，无论其中每个像素具体的值是 0.1 还是 1000.0。



这样的话，因为我们固定了模型输入的尺寸大小，所以对每个卷积层来说，其接受的输入尺寸都是静态的，固定不变的，在提前做优化的时候我们只要使用随机初始化的相应尺寸的输入进行测试和选择就行了。



## torch.backends.cudnn.benchmark！


 说了这么多背景知识，但和 cudnn.benchmark 有何联系呢？实际上，设置这个 flag 为 True，我们就可以在 PyTorch 中对模型里的卷积层进行预先的优化，也就是在每一个卷积层中测试 cuDNN 提供的所有卷积实现算法，然后选择最快的那个。这样在模型启动的时候，只要额外多花一点点预处理时间，就可以较大幅度地减少训练时间。



这岂不是，用 cudnn.benchmark 一时爽，一直用一直爽吗？其实不然，在某些情况，使用它可能会大大增加运行时间！在背景知识里面我们已经提到过，但是在这里我们更加具体的定义一下，到底哪些因素会影响到卷积层的运行时间。



1. 首先，当然是卷积层本身的参数，常见的包括卷积核大小，stride，dilation，padding ，输出通道的个数等；
2. 其次，是输入的相关参数，包括输入的宽和高，输入通道的个数等；
3. 最后，还有一些其他的因素，比如硬件平台，输入输出精度、布局等等。



我们定义一个卷积场景的参数主要包括 (1) 和 (2)，因为在同一个程序中 (3) 往往都是相同的，我们暂且忽略不计。不同的卷积场景有不同的最优卷积算法，需要分别进行测试和选择。



据此我们可以看出来，首先如果我们的网络模型一直变的话，那肯定是不能设置 cudnn.benchmark=True 的。因为网络结构经常变，每次 PyTorch 都会自动来根据新的卷积场景做优化：这次花费了半天选出最合适的算法出来，结果下次你结构又变了，之前就白做优化了。不仅如此，还得要根据这个新的结构继续做选择最高效的算法组合，又花费不少的时间。这样反而会大大降低效率。



另外，我们输入的大小也不能变。对于一个卷积层，这次的输入形状比如是 (8, 3, 224, 224)，下次换成了 (8, 3, 112, 112)，那也不行。输入的情况变了，最优的算法不一定适用了（比如有的算法在大尺寸输入情况下速度快），PyTorch 还是会重新寻找最优算法的。注意，这里的 **batch size**，输入通道，图片大小都不能变。



不过一般的 CV 模型来说，网络的结构一般是不会动态变化的，其次，图像一般都 resize 到固定的尺寸，batch size 也是固定的。所以，在大部分情况下，我们都可以在程序中加上这行神奇的代码，来减少运行时间！



 ## 等等，这行代码要加在哪里？

之前在网上看到过一些博客提到使用 cudnn.benchmark=True ，但是没有明确说明这段代码要放到哪里。这里我怕有的读者和我当时一样，所以就加了这一小部分。其实一般加在开头就好，比如在设置使用 GPU 的同时，后边补一句:

```python
if args.use_gpu and torch.cuda.is_available():
    device = torch.device('cuda')
    torch.backends.cudnn.benchmark = True
else:
    device = torch.device('cpu')
```

当然某些情况下也可以在程序中多次改变 torch.backends.cudnn.benchmark 的值，玩点花样什么的。

## PyTorch 中对应的源代码

前边这些都是我在讲，那我们现在来看一下 PyTorch 中的源码，看看其原始的逻辑，代码来自 github.com/pytorch/pyto, 在 cuDNN 中选择卷积算法的核心也就是这部分，我简要地加了几句注释：

```python
// 具体位置的网址：https://github.com/pytorch/pytorch/blob/b5fa9a340a0d174131ad0a452c395860d571b5b0/aten/src/ATen/native/cudnn/Conv.cpp#L701
template<typename perf_t>
void findAlgorithm(const ConvolutionArgs& args, bool benchmark, perf_t* algoPerf) {
  using search = algorithm_search<perf_t>;
  auto& cache = search::cache();

  // 如果缓存里面已经对该卷积场景优化的结果了，那么就直接返回，不找了
  if (cache.find(args.params, algoPerf)) {
    return;
  }

  // 如果在 PyTorch 程序中设置了 torch.backends.cudnn.deterministic=True,
  // 并且 cudnn.benchmark == False的话，那么就选那个默认的卷积算法，返回
  if (args.params.deterministic && !benchmark) {
    algoPerf->algo = search::DEFAULT_ALGO;
    if (args.params.dataType == CUDNN_DATA_HALF) {
      algoPerf->mathType = CUDNN_TENSOR_OP_MATH;
    } else {
      algoPerf->mathType = CUDNN_DEFAULT_MATH;
    }
    search::getWorkspaceSize(args, algoPerf->algo, &(algoPerf->memory));
    return;
  }

  // 再次检查一下缓存中有没有已经对该卷积场景做过选择，
  // recheck 的原因是可能其他线程可能在此期间优化过了
  if (benchmark) {
    if (cache.find(args.params, algoPerf)) {
      // re-check cache since another thread may have benchmarked the algorithm
      return;
    }
  }

  // 好，如果前边三关都过了的话，确实之前没有对该场景做出过优化，
  // 那就调用 search::findAlgorithm 来做 benchmarking。
  // 至于何为 search::findAlgorithm 函数，等等看下边。
  auto perfResults = search::findAlgorithm(args, benchmark);

  // 如果 findAlgorithm 程序运行成功了，并且程序不要求 determinnistic,
  // 使用 findAlgorithm 的结果
  // 否则的话，要求 deterministic，还是返回默认的卷积算法
  // for deterministic algo, look at all the perf results and return the best
  // deterministic algo
  if (perfResults.status == CUDNN_STATUS_SUCCESS &&
      !(args.params.deterministic && perfResults.determinism != CUDNN_DETERMINISTIC)) {

      // if benchmarking, map the original params with the found algo+math type for re-use
      if (benchmark) {
        // cache 只存需要 benchmark 的结果
        cache.insert(args.params, perfResults);

        // Free the cached blocks in our caching allocator. They are
        // needed here because the above benchmarking uses a huge amount of memory,
        // e.g. a few GBs.
        c10::cuda::CUDACachingAllocator::emptyCache();
      }

      *algoPerf = perfResults;
  } else {
      algoPerf->algo = search::DEFAULT_ALGO;
      if (args.params.dataType == CUDNN_DATA_HALF) {
        algoPerf->mathType = CUDNN_TENSOR_OP_MATH;
      } else {
        algoPerf->mathType = CUDNN_DEFAULT_MATH;
      }
      search::getWorkspaceSize(args, algoPerf->algo, &(algoPerf->memory));
  }
}


// 选择卷积 forward 算法的函数
// 具体位置的网址: https://github.com/pytorch/pytorch/blob/b5fa9a340a0d174131ad0a452c395860d571b5b0/aten/src/ATen/native/cudnn/Conv.cpp#L504
template<>
struct algorithm_search<cudnnConvolutionFwdAlgoPerf_t> {
  using perf_t = cudnnConvolutionFwdAlgoPerf_t;
  using algo_t = cudnnConvolutionFwdAlgo_t;

  // 默认算法来了！
  static constexpr auto DEFAULT_ALGO = CUDNN_CONVOLUTION_FWD_ALGO_IMPLICIT_PRECOMP_GEMM;
  static BenchmarkCache<perf_t>& cache() { return fwd_algos; }

  static perf_t findAlgorithm(const ConvolutionArgs& args, bool benchmark) {
    // CuDNN 实现的 forward 算法，任君选择：
    static const algo_t algos[] = {
         CUDNN_CONVOLUTION_FWD_ALGO_GEMM,
         CUDNN_CONVOLUTION_FWD_ALGO_FFT,
         CUDNN_CONVOLUTION_FWD_ALGO_FFT_TILING,
         CUDNN_CONVOLUTION_FWD_ALGO_IMPLICIT_GEMM,
         CUDNN_CONVOLUTION_FWD_ALGO_IMPLICIT_PRECOMP_GEMM,
         CUDNN_CONVOLUTION_FWD_ALGO_DIRECT,
         CUDNN_CONVOLUTION_FWD_ALGO_WINOGRAD,
         CUDNN_CONVOLUTION_FWD_ALGO_WINOGRAD_NONFUSED,
    };
    static constexpr int num_algos = CUDNN_CONVOLUTION_FWD_ALGO_COUNT;
    static_assert(sizeof(algos) / sizeof(algos[0]) == num_algos,
                  "Missing cuDNN convolution forward algorithms");
    int perf_count;
    std::unique_ptr<perf_t[]> perf_results(new perf_t[num_algos]);

    // 如果不进行 benchmark 的话，就是我们什么都不设置，PyTorch 默认情况下，
    // 会调用 cudnnGetConvolutionForwardAlgorithm_v7 ！
    if (!benchmark) {
      AT_CUDNN_CHECK(cudnnGetConvolutionForwardAlgorithm_v7(
          args.handle,
          args.idesc.desc(),
          args.wdesc.desc(),
          args.cdesc.desc(),
          args.odesc.desc(),
          num_algos,
          &perf_count,
          perf_results.get()));
    } else { // 如果使用 benchmark，会调用 cudnnFindConvolutionForwardAlgorithmEx ！
      size_t max_ws_size = getMaxWorkspaceSize(args, algos, num_algos);
      Workspace ws(max_ws_size);
      AT_CUDNN_CHECK(cudnnFindConvolutionForwardAlgorithmEx(
          args.handle,
          args.idesc.desc(), args.input.data_ptr(),
          args.wdesc.desc(), args.weight.data_ptr(),
          args.cdesc.desc(),
          args.odesc.desc(), args.output.data_ptr(),
          num_algos,
          &perf_count,
          perf_results.get(),
          ws.data,
          ws.size));
    }
    return getBestAlgorithm<perf_t>(perf_results.get(), args, perf_count);
  }
```

行了，两个比较主要的源码看完了。这里可能大家有两个疑惑。第一个 torch.backends.cudnn.deterministic 又是啥？顾名思义，将这个 flag 置为 True 的话，每次返回的卷积算法将是确定的，即默认算法。如果配合上设置 Torch 的随机种子为固定值的话，应该可以保证每次运行网络的时候相同输入的输出是固定的。



第二个，

cudnnGetConvolutionForwardAlgorithm_v7 

vs

 cudnnFindConvolutionForwardAlgorithmEx



 (链接是到英伟达提供的官方 API)。我们已经知道 PyTorch 默认调用的是前者，设置 benchmark=True 会调用后者。根据官方讲解来说， Get 那个函数会使用一些人为设置的启发式的方法（heuristic）去选择程序所认为的最合适的算法。而 Find 那个函数是穷尽式的 (exhaustive search)，即会遍历所有可选的卷积进行比较。这么一说，其实 PyTorch 默认也是会对每层的卷积算法进行预先选择，速度比较快，但是选择出来的结果不是那么好，具体的选择机制并不是很清楚（没找到相关的资料）。

## 实验测试

看到这里有的同学可能还将信将疑。那我们既然讲了理论，看了源代码，还差一步，那就是跑出来结果对比一下嘛。别急，马上就来。



首先我们先看一下默认情况下的结果，输入大小 (32, 3, 224, 224)，具体的值是随机生成的不影响结果，使用的测试模型是 ResNet-101，GPU 是 GTX 1060，下边输出的时间代表一个 mini-batch 的正向和反向传播的时间：

```python
Model: ResNet-101
Device: cuda
Use CUDNN Benchmark: False
Number of runs: 100
Batch size: 32

1.2628223896026611
0.690316915512085
0.739039421081543
0.7383503913879395
0.7389490604400635
...
0.7488319873809814
0.7504653930664062
0.7499253749847412
```

再来看一下设置 `torch.backends.cudnn.benchmark=True` 之后相同条件下的用时：

```python
Model: ResNet-101
Device: cuda
Use CUDNN Benchmark: True
Number of runs: 100
Batch size: 32

3.634748935699463
0.5547430515289307
0.6138713359832764
0.6144607067108154
0.6148972511291504
...
0.6201446056365967
0.6197457313537598
0.6202619075775146
```



从上边我们可以看出来，速度确实提升了，而且挺明显的，大概快了 15%（当然不同的网络，不同的硬件平台效果不一样）。以上是每个 mini-batch 的时间，可想而知每个 epoch 可以省的时间。而且除了开头第一次要额外花一点点时间之外，几乎没有副作用嘛。



现在我们再来测试一下，如果输入图片的形状是变化的，会怎么样。下边的例子随便生成了 5 组不同尺寸的输入，测试一下 cudnn.benchmark=True 的表现：



```python
Model: ResNet-101
Device: cuda
Use CUDNN Benchmark: True
Number of runs: 100
Batch size: 32
Number of scenes: 5

iteration 0 torch.Size([32, 3, 154, 154]) time: 3.30
iteration 0 torch.Size([32, 3, 80, 80])   time: 1.92
iteration 0 torch.Size([32, 3, 116, 116]) time: 2.12
iteration 0 torch.Size([32, 3, 118, 118]) time: 0.57
iteration 0 torch.Size([32, 3, 184, 184]) time: 2.67

iteration 1 torch.Size([32, 3, 154, 154]) time: 0.30
iteration 1 torch.Size([32, 3, 80, 80])   time: 0.16
iteration 1 torch.Size([32, 3, 116, 116]) time: 0.20
iteration 1 torch.Size([32, 3, 118, 118]) time: 0.21
iteration 1 torch.Size([32, 3, 184, 184]) time: 0.43

iteration 2 torch.Size([32, 3, 154, 154]) time: 0.35
iteration 2 torch.Size([32, 3, 80, 80])   time: 0.15
iteration 2 torch.Size([32, 3, 116, 116]) time: 0.20
iteration 2 torch.Size([32, 3, 118, 118]) time: 0.21
iteration 2 torch.Size([32, 3, 184, 184]) time: 0.43
...
```



而默认情况下的结果为：



```python
Model: ResNet-101
Device: cuda
Use CUDNN Benchmark: False
Number of runs: 100
Batch size: 32
Number of scenes: 5

iteration 0 torch.Size([32, 3, 191, 191]) time: 2.50
iteration 0 torch.Size([32, 3, 121, 121]) time: 0.39
iteration 0 torch.Size([32, 3, 208, 208]) time: 0.54
iteration 0 torch.Size([32, 3, 205, 205]) time: 0.57
iteration 0 torch.Size([32, 3, 185, 185]) time: 0.48

iteration 1 torch.Size([32, 3, 191, 191]) time: 0.47
iteration 1 torch.Size([32, 3, 121, 121]) time: 0.26
iteration 1 torch.Size([32, 3, 208, 208]) time: 0.54
iteration 1 torch.Size([32, 3, 205, 205]) time: 0.57
iteration 1 torch.Size([32, 3, 185, 185]) time: 0.48

iteration 2 torch.Size([32, 3, 191, 191]) time: 0.47
iteration 2 torch.Size([32, 3, 121, 121]) time: 0.26
iteration 2 torch.Size([32, 3, 208, 208]) time: 0.54
iteration 2 torch.Size([32, 3, 205, 205]) time: 0.57
iteration 2 torch.Size([32, 3, 185, 185]) time: 0.48
...
```



根据上边结果，我们可以知道，cudnn.benchmark=True 的时候，如果不停地改变输入形状，运行效率确实会很低，因为对于每个新遇到的卷积场景 PyTorch 都会去自动寻找最适合的卷积算法。但是，根据上边的源代码和结果我们也可以看出来，对于某个已经优化过的卷积场景，会保存到缓存 (cache) 中，下次再遇到的时候直接从缓存中读取结果就好了。相比之下，在 PyTorch 默认情况(即 cudnn.benchmark=False )，输入尺寸的变化并不影响效率。

有同学反应说使用附录中的代码测试之后，发现速度提升的效果不是很明显。原因可能是因为使用的 GPU 比较好，本身训练速度就很快，设置 cudnn.benchmark=True 之后可能会不太明显。而相比之下，因为我所使用的 GPU 比较一般，所以速度差距比较明显。对于 GPU 比较好的同学们来说，可以适当调高程序中的 batch size 再测试，或者干脆直接拿手头的检测或者分割的模型试一下效果嘛，一般情况下都会有速度上的提升。

## 总结

本文主要讲了：



- 一种可以在底层优化卷积层，进而在不改变所使用的卷积神经网络本身结构的情况下提高效率的方法；
- torch.backends.cudnn.benchmark 如何使用；
- 浏览了一遍 PyTorch 的 cuDNN 代码中有关卷积算法选择的部分；
- 测试了 torch.backends.cudnn.benchmark 的实际使用效果。



对于何时适合设置 torch.backends.cudnn.benchmark=True，一句话就是：如果卷积网络结构不是动态变化的，网络的输入 (batch size，图像的大小，输入的通道) 是固定的，那么就放心用吧！

## 附录

我所使用的部分测试代码如下：



```python
import time
import argparse
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision.models as models
import numpy as np

parser = argparse.ArgumentParser(description='Test for cudnn.benchmark')
parser.add_argument('--run_num', type=int, default=100, help='number of runs')
parser.add_argument('--batch_size', type=int, default=32, help='batch size')
parser.add_argument('--use_gpu', dest='use_gpu', action='store_true', default=False, help='use gpu')
parser.add_argument('--use_benchmark', dest='use_benchmark', action='store_true', default=False, help='use benchmark')
parser.add_argument('--exp_name', type=str, default='cudnn_test', help='output file name')
args = parser.parse_args()

if args.use_gpu and torch.cuda.is_available():
    device = torch.device('cuda')
    print('Using GPU: ', torch.cuda.get_device_name(0))
    if args.use_benchmark:
        torch.backends.cudnn.benchmark = True
        print('Using cudnn.benchmark.')
else:
    device = torch.device('cpu')
    print('Warning! Using CPU.')

images = torch.randn(args.batch_size, 3, 224, 224)
labels = torch.empty(args.batch_size, dtype=torch.long).random_(1000)

model = models.resnet101()
optimizer = torch.optim.SGD(model.parameters(), lr=0.001, momentum=0.9)
criterion = nn.CrossEntropyLoss()

model = model.to(device)
images = images.to(device)
labels = labels.to(device)

time_list = []

model.train()
for itr in range(args.run_num):
    start = time.time()
    outputs = model(images)

    loss = criterion(outputs, labels)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    end = time.time()
    print('iteration %d time: %.2f' % (itr, end-start))
    time_list.append(end-start)

with open(args.exp_name, 'w') as f:
    f.write('Device: ' + device.type + '\n')
    f.write('Use CUDNN Benchmark: ' + str(torch.backends.cudnn.benchmark) + '\n')
    f.write('Number of runs: ' + str(args.run_num) + '\n')
    f.write('Batch size: ' + str(args.batch_size) + '\n')
    f.write('Average time: %.2f s\n\n' % (np.mean(time_list)))

    for each in time_list:
        f.write(str(each))
        f.write('\n')
```

## 参考

**转载：[巧用torch.backends.cudnn.benchmark减少训练时间](https://mp.weixin.qq.com/s?__biz=MzI5MDUyMDIxNA==&mid=2247489758&idx=1&sn=b50ad62d1ddef18e40da3b047830fdbe&chksm=ec1ff527db687c31f5fc0b4a6cb5a618ce61fc6511e952df9033902cff4c357d1301ee3e08df&mpshare=1&scene=1&srcid=&sharer_sharetime=1564402073303&sharer_shareid=f17dbd4fecff14c70514c0b015b14536&key=c6f3456df8f4d3e32eb4476d98d381f45a5e785fc76837d47a4fbdaae0f3e338e148732783026fac05326be5460af55bf22f7b9202ccfc4f6b9ee75b6ae7e0b061c004314a081e2d5927b5ed539dac89&ascene=1&uin=MjM2ODY3MjU2Nw%3D%3D&devicetype=Windows+10&version=62060739&lang=zh_CN&pass_ticket=k7MwJnXgFG2EQwUHIEuNlo9OJOzfAB1R2vR1pq5P6ynp0zqhWT8T3FKAwKYh515N)**
