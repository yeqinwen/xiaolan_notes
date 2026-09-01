
# 1 polyscope常用可视化

## 1.1 可视化mesh

```python
import igl
import polyscope as ps
import numpy as np

v,f = igl.read_triangle_mesh("assets/bunny.obj")

ps.init()
ps.register_surface_mesh("mesh", v,f,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 这两行有用
ps.set_shadow_blur_iters(3)  # 模糊的程度，这个数值可以设置很大
ps.set_shadow_darkness(0.5)  # 这个数值也可以设置很大
ps.set_up_dir("y_up")  # 这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉
ps.set_front_dir('z_front')  # 这两行有用
ps.set_SSAA_factor(4)
ps.set_open_imgui_window_for_user_callback(False)  # 用于将原始imgui的界面关掉
ps.show()

```

![](assets/Pasted%20image%2020260901222600.png)



## 1.2 可视化点云

```python
import polyscope as ps
import numpy as np

points = np.loadtxt("assets/bunny.txt")

ps.init()
ps.register_point_cloud("points", points,
                        color=np.array([0, 91, 255]) / 255,
                        radius=0.003)

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 这两行有用
ps.set_shadow_blur_iters(3)  # 模糊的程度，这个数值可以设置很大
ps.set_shadow_darkness(0.5)  # 这个数值也可以设置很大
ps.set_up_dir("y_up")  # 这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉
ps.set_front_dir('z_front')  # 这两行有用
ps.set_SSAA_factor(4)
ps.set_open_imgui_window_for_user_callback(False)  # 用于将原始imgui的界面关掉
ps.show()
```


![](assets/Pasted%20image%2020260901222739.png)


## 1.3 可视化线框


```python
import igl
import polyscope as ps
import numpy as np

v,f = igl.read_triangle_mesh("assets/bunny.obj")

e = igl.edges(f)

# 获得节点 和 连接关系
nodes = v[e].reshape(-1, 3)
edges = np.arange(nodes.shape[0]).reshape(-1, 2)

ps.init()
ps.register_surface_mesh("mesh", v,f,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.0003,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.register_curve_network("wireframe", nodes, edges,
                          color=[1, 1, 1],
                          radius=0.001
                          )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 这两行有用
ps.set_shadow_blur_iters(3)  # 模糊的程度，这个数值可以设置很大
ps.set_shadow_darkness(0.5)  # 这个数值也可以设置很大
ps.set_up_dir("y_up")  # 这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉
ps.set_front_dir('z_front')  # 这两行有用
ps.set_SSAA_factor(4)
ps.set_open_imgui_window_for_user_callback(False)  # 用于将原始imgui的界面关掉
ps.show()

```

| ![](assets/Pasted%20image%2020260901222854.png) | ![](assets/Pasted%20image%2020260901222926.png) |
| :---------------------------------------------: | :---------------------------------------------: |
|                 mesh+wireframe                  |                    wireframe                    |





