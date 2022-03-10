##  NGP: Train Nerf 目前可公开的情报点（基于220203版本的ngp）
*****

***- 别人or官方复现的ngp torch版本***
- [torch-ngp(cuda extension)](https://github.com/ashawkey/torch-ngp)
- [hashnerf(纯torch实现)](https://github.com/yashbhalgat/HashNeRF-pytorch)
- [官方torch extension](https://github.com/NVlabs/tiny-cuda-nn)

***- [代码流程图](https://drive.google.com/file/d/1p65_glQ6zkLa_k2sgPV5enRTxWmtrPUY/view?usp=sharing)***


***- 覆盖的函数***

`testbed_nerf.cu` 中 Testbed::train_nerf 与 Testbed::train_nerf_step

***- Testbed::train_nerf***

- `train_nerf`中会预先分配一些变量(e.g. `numsteps_counter`)的显存，然后执行train_nerf_step

***- Testbed::train_nerf_step***

-  `train_nerf_step`中同样一开始会分配一个scratch中的显存

- `generate_training_samples_nerf`中于nerf的采样策略不同，采样的是ray marching的采样方式。
    - ngp会额外维护一个mpi的density grid的数据结构帮助能够在物体采样
    - 采样中会进行2个pass。第一个pass只为算这根光线采了几个点(numsteps)和存储的起始位置(base)。
    - 每根光线的采样点个数是不一样的。
    - 第二个pass才会存每个采样点的信息(pos,dir,dt步进长度)，计算过程与第一个一样

- `compacted_numsteps` 与 `numsteps`的区别是compacted会根据计算的透明度提前终止，从而进一步加速。

- `rng.advance(ray_idx * N_MAX_RANDOM_SAMPLES_PER_RAY());`是为了伪随机数生成器每个线程生成随机数尽可能随机。

- 有`output_l2_reg` 与 `output_l1_reg_density`
