---
layout: post
title:  "How does TSDF works"
date:   2018-5-23 10:00:00 -0400
tag: cv
---

## How does TSDF works

### 介绍

- 深度相机获取的数据通常有噪声，而且一些场景（比如物体边缘）扫描出来的会有孔洞（没有深度的值的区域），三维模型重建需要消除噪声和孔洞的影响。
- 有一种方法是采用 TSDF 模型进行深度数据的融合，TSDF 模型将整个待重建的三维空间划分成网格：
![Alt text](/blog/assets/images/posts/voxel.png)
- 每个网格中存储了数值，示意图如下，网格模型中值的大小代表网格离重建好的表面的距离，如图表示的是重建的一个人的脸（**网格模型中值为 0 的部分，红线表示重建的表面，示意图给出的二维信息，实际是三维的**），重建好的表面到相机一侧都是正值，另一侧都是负值，网格点离重建好的表面距离越远绝对值越大，在网格模型中从正到负的穿越点表示重建好的场景的表面。
![Alt text](/blog/assets/images/posts/tsdf2.png)

### 算法流程
- 当相机测量到的深度值和网格到光心的距离近似相等时，更新网格加权融合时，加权的值小，也说明网格离待重建的场景表面近。
![Alt text](/blog/assets/images/posts/tsdf4.png)
- 求取sdf示意图: 
![Alt text](/blog/assets/images/posts/tsdf.png)
![Alt text](/blog/assets/images/posts/tsdf5.png)
![Alt text](/blog/assets/images/posts/tsdf6.png)

### 实现

```
// Initialize voxel grid
float * voxel_grid_TSDF = new float[voxel_grid_dim_x * voxel_grid_dim_y * voxel_grid_dim_z];
float * voxel_grid_weight = new float[voxel_grid_dim_x * voxel_grid_dim_y * voxel_grid_dim_z];
for (int i = 0; i < voxel_grid_dim_x * voxel_grid_dim_y * voxel_grid_dim_z; ++i) {
	voxel_grid_TSDF[i] = 1.0f;
}
memset(voxel_grid_weight, 0, sizeof(float) * voxel_grid_dim_x * voxel_grid_dim_y * voxel_grid_dim_z);
 
// Integrate and update tsdf
void Integrate(float * cam_K, float * cam2base, float * depth_im,
               int im_height, int im_width, int voxel_grid_dim_x, int voxel_grid_dim_y, int voxel_grid_dim_z,
               float voxel_grid_origin_x, float voxel_grid_origin_y, float voxel_grid_origin_z, float voxel_size, float trunc_margin,
               float * voxel_grid_TSDF, float * voxel_grid_weight) {

  int pt_grid_z = blockIdx.x;
  int pt_grid_y = threadIdx.x;

  for (int pt_grid_x = 0; pt_grid_x < voxel_grid_dim_x; ++pt_grid_x) {

    // Convert voxel center from grid coordinates to base frame camera coordinates
    float pt_base_x = voxel_grid_origin_x + pt_grid_x * voxel_size;
    float pt_base_y = voxel_grid_origin_y + pt_grid_y * voxel_size;
    float pt_base_z = voxel_grid_origin_z + pt_grid_z * voxel_size;

    // Convert from base frame camera coordinates to current frame camera coordinates
    float tmp_pt[3] = {0};
    tmp_pt[0] = pt_base_x - cam2base[0 * 4 + 3];
    tmp_pt[1] = pt_base_y - cam2base[1 * 4 + 3];
    tmp_pt[2] = pt_base_z - cam2base[2 * 4 + 3];
    float pt_cam_x = cam2base[0 * 4 + 0] * tmp_pt[0] + cam2base[1 * 4 + 0] * tmp_pt[1] + cam2base[2 * 4 + 0] * tmp_pt[2];
    float pt_cam_y = cam2base[0 * 4 + 1] * tmp_pt[0] + cam2base[1 * 4 + 1] * tmp_pt[1] + cam2base[2 * 4 + 1] * tmp_pt[2];
    float pt_cam_z = cam2base[0 * 4 + 2] * tmp_pt[0] + cam2base[1 * 4 + 2] * tmp_pt[1] + cam2base[2 * 4 + 2] * tmp_pt[2];

    if (pt_cam_z <= 0)
      continue;

    int pt_pix_x = roundf(cam_K[0 * 3 + 0] * (pt_cam_x / pt_cam_z) + cam_K[0 * 3 + 2]);
    int pt_pix_y = roundf(cam_K[1 * 3 + 1] * (pt_cam_y / pt_cam_z) + cam_K[1 * 3 + 2]);
    if (pt_pix_x < 0 || pt_pix_x >= im_width || pt_pix_y < 0 || pt_pix_y >= im_height)
      continue;

    float depth_val = depth_im[pt_pix_y * im_width + pt_pix_x];

    if (depth_val <= 0 || depth_val > 6)
      continue;

    float diff = depth_val - pt_cam_z;

    if (diff <= -trunc_margin)
      continue;

    // Integrate
    int volume_idx = pt_grid_z * voxel_grid_dim_y * voxel_grid_dim_x + pt_grid_y * voxel_grid_dim_x + pt_grid_x;
    float dist = fmin(1.0f, diff / trunc_margin);
    float weight_old = voxel_grid_weight[volume_idx];
    float weight_new = weight_old + 1.0f;
    voxel_grid_weight[volume_idx] = weight_new;
    voxel_grid_TSDF[volume_idx] = (voxel_grid_TSDF[volume_idx] * weight_old + dist) / weight_new;
  }
}

```

```
// Create point cloud content for ply file
for (int i = 0; i < voxel_grid_dim_x * voxel_grid_dim_y * voxel_grid_dim_z; i++) {

    // If TSDF value of voxel is less than some threshold, add voxel coordinates to point cloud
    if (std::abs(voxel_grid_TSDF[i]) < tsdf_thresh && voxel_grid_weight[i] > weight_thresh) {

      // Compute voxel indices in int for higher positive number range
      int z = floor(i / (voxel_grid_dim_x * voxel_grid_dim_y));
      int y = floor((i - (z * voxel_grid_dim_x * voxel_grid_dim_y)) / voxel_grid_dim_x);
      int x = i - (z * voxel_grid_dim_x * voxel_grid_dim_y) - (y * voxel_grid_dim_x);

      // Convert voxel indices to float, and save coordinates to ply file
      float pt_base_x = voxel_grid_origin_x + (float) x * voxel_size;
      float pt_base_y = voxel_grid_origin_y + (float) y * voxel_size;
      float pt_base_z = voxel_grid_origin_z + (float) z * voxel_size;
      fwrite(&pt_base_x, sizeof(float), 1, fp);
      fwrite(&pt_base_y, sizeof(float), 1, fp);
      fwrite(&pt_base_z, sizeof(float), 1, fp);
    }
  }
  fclose(fp);
}
```

#### Refernece: 
- https://github.com/andyzeng/tsdf-fusion
- https://github.com/personalrobotics/OpenChisel/blob/master/open_chisel/src/ProjectionIntegrator.cpp


















































































































5











