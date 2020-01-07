---
layout: post
title: "Nifti, Cifti, Gifti文件简短的介绍"
subtitle: ""
catalog: true
author: "WangW"
head-style: text
tags:
    - Nifti
    - Cifti
    - Gifti
---

Update: 下载[Connectome Workbench](https://www.humanconnectome.org/software/get-connectome-workbench),使用 `wb_command -file-information <filepath>` 来看以下文件的简单信息.
或者直接使用`txt软件`打开文档

--- 

- [nifti官方介绍](https://www.nitrc.org/projects/nifti/), 在`document`可以找到该文件format文档. 
- [cifti官方介绍](https://www.nitrc.org/projects/cifti/), 
- [gifti官方介绍](https://www.nitrc.org/projects/gifti/), gifti是nifti的一部分.
- [HCP datasets wiki](https://wiki.humanconnectome.org/display/PublicData/HCP+Users+FAQ#HCPUsersFAQ-2.HowdoyougetCIFTIfilesintoMATLAB?)
<!--break-->

## Nifti 
- [Nifti-1文档](https://www.nitrc.org/docman/view.php/26/204/TheNIfTI1Format2004.pdf)
- [Nifti-2文档](https://www.nitrc.org/docman/view.php/26/1302/nifti2_doc.html)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20191230113249.png)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20191230113523.png)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20191230113531.png)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20191230113546.png)

一个实际图像表头如下：
```
<class 'nibabel.nifti1.Nifti1Header'> object, endian='<'
sizeof_hdr      : 348
data_type       : b''
db_name         : b''
extents         : 0
session_error   : 0
regular         : b'r'
dim_info        : 0
dim             : [  3 260 311 260   1   1   1   1]
intent_p1       : 0.0
intent_p2       : 0.0
intent_p3       : 0.0
intent_code     : none
datatype        : float32
bitpix          : 32
slice_start     : 0
pixdim          : [-1.   0.7  0.7  0.7  2.4  0.   0.   0. ]
vox_offset      : 0.0
scl_slope       : nan
scl_inter       : nan
slice_end       : 0
slice_code      : unknown
xyzt_units      : 10
cal_max         : 0.0
cal_min         : 0.0
slice_duration  : 0.0
toffset         : 0.0
glmax           : 0
glmin           : 0
descrip         : b'FSL5.0'
aux_file        : b''
qform_code      : mni
sform_code      : mni
quatern_b       : 0.0
quatern_c       : 1.0
quatern_d       : 0.0
qoffset_x       : 90.0
qoffset_y       : -126.0
qoffset_z       : -72.0
srow_x          : [-0.7  0.   0.  90. ]
srow_y          : [   0.     0.7    0.  -126. ]
srow_z          : [  0.    0.    0.7 -72. ]
intent_name     : b''
magic           : b'n+1'
```
没什么好说的，表头中`intent_code`,`intent_name`, `dim` 说明储存数据的意义；剩下的进一步说明数据详细信息，比如用`qform_code`说明通过q仿射矩阵能将实际坐标转到MNI空间去。

nifti-2有一些小变动，主要是表头类型，比如`short dim[8]`变成`int64_t dim[8]`等，具体细节不表。


## Cifti
- [Cifti-1文档](https://www.nitrc.org/plugins/mwiki/index.php/cifti:Cifti-1)
- [Cifti-2文档-main](https://www.nitrc.org/forum/attachment.php?attachid=341&group_id=454&forum_id=1955)
- [Cifti-2文档-appendix](https://www.nitrc.org/forum/attachment.php?attachid=342&group_id=454&forum_id=1955)


cifti-1:

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191230165527.png)


cifti-2:

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191230122628.png)


## Gifti
- [Gifti-1文档](https://www.nitrc.org/frs/download.php/2871/GIFTI_Surface_Format.pdf)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20191230122817.png)