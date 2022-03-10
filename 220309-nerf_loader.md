##  NGP: NeRF Loader 目前可公开的情报点（基于220226版本的ngp）
*****
***- 文件运行逻辑***

`main.cu` -> `testbed.cu` -> `testbed_nerf.cu` -> `load_nerf(...)` -> `nerf_loader.cu`

***- Dataset的输入以及输出***

> 功能上，loader接受传入的jsons path，读取不同json中的数据，将图片数据（以half的精度）、经处理后的sharpness数据以及rays（若有）存入GPU中。其他meta_data存入NerfDataset的结构里。


***- 相对需要注意的点***

-  loader会打开传入文件路径下所有后缀为.json的文件，目的是enable不同相机内参的数据读入。在旧版ngp里，一个NeRFDataset只对应一组相机内参，在新版ngp下，超参以meta_data vector的形式存入，故而可以对应不同内参。但所有图片的resolution仍然必须一样。见：约2040行

- 在读入frames的时候，loader会对frames的路径做一个字符串sortion，因此在命名时需要`0`的对齐补位。比如:`image_0001.png`而不是`image_1.png`。不补位的后果是在render时training view的顺序是乱序。
见: `nerf_loader.cu`约201行


- 当json的 `n_frames`和 `frames`实际大小冲突时，loader会取两者中最小值剪裁`frames`。因此如果只想取前n个frame来训练，不需要删掉json文件的后边的frame，只需要改小n_frame参数即可。同时，debug 可能需要注意这点。见:`nerf_loader.cu`约205行

- 物体的aabb是一个正方体，因为代码写死把aabb unit进一个单位正方体里。见：约341行

- 除了物体的aabb外，loader还有相机的aabb，即:`cam_aabb` 用途尚未可知。见：约259行

- 未经魔改的ngp只接受以下类型的图片：8-bits如`j(e)pg/png/gif/bmp...`,或者half类型`.exr`。虽然有`stbi`支持load进float类型数据，但只支持float类型的`HDR`。见:stbi_load系列函数

- transform_matrix读入时会过一个nerf_matrix_to_ngp进行了一波换轴。并对相机的transform matrix的scale和translation。见：头文件 `nerf_load.h`约50行