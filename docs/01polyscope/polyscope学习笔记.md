
## 1 polyscope常用可视化

### 1.1 可视化mesh

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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

```

![](assets/Pasted%20image%2020260901222600.png)



### 1.2 可视化点云

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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()
```


![](assets/Pasted%20image%2020260901222739.png)


### 1.3 可视化线框


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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

```

| ![](assets/Pasted%20image%2020260901222854.png) | ![](assets/Pasted%20image%2020260901222926.png) |
| :---------------------------------------------: | :---------------------------------------------: |
|                 mesh+wireframe                  |                    wireframe                    |


## 2 读写mesh

### 2.1 python读写mesh

```python
import polyscope as ps
import numpy as np
import os

def read_obj(file_path):
    vertices = []
    faces = []

    with open(file_path, 'r') as f:
        for line in f:
            line = line.strip()
            if line.startswith('v '):  # 顶点
                parts = line.split()
                vertices.append([float(x) for x in parts[1:4]])
            elif line.startswith('f '):  # 面
                parts = line.split()
                face = []
                for item in parts[1:]:
                    # 取第一个数字（顶点索引），并转换为0-based索引
                    idx = int(item.split('/')[0])
                    # OBJ文件索引从1开始，转换为0-based
                    if idx > 0:
                        face.append(idx - 1)  # 正索引减1
                    else:
                        # 处理负数索引（相对于末尾）
                        face.append(len(vertices) + idx)
                faces.append(face)

    return np.array(vertices),np.array(faces)


def save_obj(filename, vertices, faces):
    os.makedirs(os.path.dirname(filename), exist_ok=True)

    with open(filename, 'w') as fp:
        for v in vertices:
            fp.write('v %f %f %f\n' % (v[0], v[1], v[2]))

        for f in (faces + 1):  # Faces are 1-based, not 0-based in obj files
            fp.write('f %d %d %d\n' % (f[0], f[1], f[2]))

    print("Saved:", filename)


# 读取mesh
v,f = read_obj("assets/bunny.obj")

# 可视化mesh
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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
save_obj("assets/new_bunny_python.obj",v,f)
```

### 2.2 libigl读写mesh

```python
import igl
import polyscope as ps
import numpy as np

# 读取mesh
v, f = igl.read_triangle_mesh("assets/bunny.obj")

# 可视化mesh
ps.init()
ps.register_surface_mesh("mesh", v, f,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
igl.write_triangle_mesh("assets/new_bunny_igl.obj", v, f)

```

### 2.3 trimesh读写mesh

```python
import trimesh
import polyscope as ps
import numpy as np

# 读取mesh
mesh = trimesh.load("assets/bunny.obj")
v = mesh.vertices
f = mesh.faces

# 可视化mesh
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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
mesh.export("assets/new_bunny_trimesh.obj")

```

### 2.4 openmesh读写mesh

```python
import openmesh as om
import polyscope as ps
import numpy as np

# 读取mesh
mesh = om.read_trimesh('assets/bunny.obj')
v = mesh.points()
f = mesh.face_vertex_indices()

# 可视化mesh
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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
om.write_mesh("assets/new_bunny_openmesh.obj", mesh)
```

### 2.5 pymeshlab读写mesh

```python
import pymeshlab
import polyscope as ps
import numpy as np

# 读取mesh
ms = pymeshlab.MeshSet()
ms.load_new_mesh("assets/bunny.obj")

# 可视化方法1(pymeshlab已经集成了polyscope)
# ms.show_polyscope()


# 可视化方法2
mesh = ms.current_mesh()
v = mesh.vertex_matrix()
f = mesh.face_matrix()

ps.init()
ps.register_surface_mesh("mesh", v, f,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
ms.save_current_mesh("assets/new_bunny_pymeshlab.obj") # 不知道为什么会报错
```

### 2.6 gpytoolbox读写mesh

```python
import gpytoolbox as gpy
import polyscope as ps
import numpy as np

# 读取mesh
v,f = gpy.read_mesh("assets/bunny.obj")

# 可视化mesh
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
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

# 写入mesh
gpy.write_mesh("assets/new_bunny_gpytoolbox.obj",v,f)
```

## 3 视角设置

### 3.1 初始可视化

```python
import igl
import polyscope as ps
import numpy as np
import json

V, F = igl.read_triangle_mesh("assets/bunny.obj")
print(V.shape,F.shape)

ps.init()
ps.register_surface_mesh("mesh", V, F,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()
```

![](assets/Pasted%20image%2020260902215855.png)

### 3.2 获取视角参数

在可视化时，可以旋转mesh到合适的视角，然后将该视角参数保存为json文件，下次可视化时加载json参数设置视角即可

```python
import igl
import polyscope as ps
import numpy as np
import json

V, F = igl.read_triangle_mesh("assets/bunny.obj")
print(V.shape,F.shape)

ps.init()
ps.register_surface_mesh("mesh", V, F,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()


# 获得视图参数，并写入json
my_view = ps.get_view_as_json()
with open('my_view.json', "w") as fp:
    json.dump(my_view, fp, indent=4, ensure_ascii=False)

```

![](assets/Pasted%20image%2020260902220102.png)

### 3.3 加载保存的视角参数

```python
import igl
import polyscope as ps
import numpy as np
import json

V, F = igl.read_triangle_mesh("assets/bunny.obj")
print(V.shape,F.shape)

# 读取视图json文件
with open('my_view.json') as fin:
    my_view = json.load(fin)

ps.init()
ps.set_view_from_json(my_view)
ps.register_surface_mesh("mesh", V, F,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()

```

![](assets/Pasted%20image%2020260902220229.png)


### 3.4 直接设置视角参数

在代码内部直接设置视角参数，无需加载json文件

```python
import igl
import polyscope as ps
import numpy as np

V, F = igl.read_triangle_mesh("assets/bunny.obj")
print(V.shape, F.shape)

my_view = ("{\"farClipRatio\":20.0,\"fov\":45.0,\"nearClipRatio\":0.005,"
           "\"projectionMode\":\"Perspective\","
           "\"viewMat\":[0.628925979137421,-0.0,0.777465164661407,0.0164333805441856,"
           "0.477299362421036,0.78937029838562,-0.386108577251434,-0.0585889630019665,"
           "-0.613707959651947,0.613917350769043,0.496455520391464,-0.391957879066467,"
           "0.0,0.0,0.0,1.0],"
           "\"windowHeight\":720,\"windowWidth\":1280}")

ps.init()
ps.set_view_from_json(my_view)
ps.register_surface_mesh("mesh", V, F,
                         color=np.array([0, 91, 255]) / 255,
                         edge_width=0.3,
                         edge_color=[1, 1, 1],
                         smooth_shade=True,
                         # material="flat"
                         )

ps.set_view_projection_mode("perspective")  # orthographic 正交投影   perspective 透视投影
# ps.set_navigation_style("planar")  # ['turntable','free','planar','none','first_person']
ps.set_ground_plane_mode("shadow_only")  # ['none','tile','tile_reflection','shadow_only']
# ps.set_ground_plane_height(-0.001)  # 设置地平面高度
ps.set_shadow_blur_iters(3)  # 设置地平面阴影模糊程度
ps.set_shadow_darkness(0.5)  # 设置地平面阴影明暗程度
ps.set_up_dir("y_up")  # 设置y轴正方向向上（这个和设置视角会冲突，因此在添加视角参数时，这一行要注释掉）
ps.set_front_dir('z_front')  # 设置z轴正方向向前
ps.set_SSAA_factor(4) # 在截图时，设置为4时，截图会更清晰
ps.show()
```

![](assets/Pasted%20image%2020260902221432.png)


### 3.5 常用视角

常用的视角有：常用窗口、方形窗口、全屏窗口

**注意**：直接设置视角参数时，因为在 `my_view`视角参数里面已经设置了“透视投影”或“正交投影“，所以在代码里面就不要再设置了，`ps.set_view_projection_mode("perspective")`这一行要注释掉！

```python

# 常用窗口
my_view = ("{\"farClipRatio\":20.0,"
           "\"fov\":39.9119839509077,"
           "\"nearClipRatio\":0.005,"
           "\"projectionMode\":\"Orthographic\","
           "\"viewMat\":[0.905055642127991,-4.65661287307739e-10,0.425295770168304,0.0160223785787821,"
           "0.148309409618378,0.937226414680481,-0.315611660480499,-0.0960772186517715,"
           "-0.398598343133926,0.348720252513885,0.848241686820984,-0.358562052249908,"
           "0.0,0.0,0.0,1.0],"
           "\"windowHeight\":801,"
           "\"windowWidth\":1293}")

# 方形窗口
my_view = ("{\"farClipRatio\":20.0,"
           "\"fov\":35.0,"
           "\"nearClipRatio\":0.005,"
           "\"projectionMode\":\"Orthographic\","
           "\"viewMat\":[1.0,-0.0,0.0,0.0168603006750345,"
           "0.0,0.997785151004791,-0.066519021987915,-0.110035300254822,"
           "-0.0,0.066519021987915,0.997785151004791,-0.320515900850296,"
           "0.0,0.0,0.0,1.0],"
           "\"windowHeight\":600,"
           "\"windowWidth\":600}")
           
# 全屏窗口
my_view = ("{\"farClipRatio\":20.0,"
           "\"fov\":38.7801126847704,"
           "\"nearClipRatio\":0.005,"
           "\"projectionMode\":\"Orthographic\","
           "\"viewMat\":[0.992455244064331,2.91038304567337e-11,0.122607305645943,0.0192149728536606,"
           "0.0536937303841114,0.899007201194763,-0.434628546237946,-0.0625064224004745,"
           "-0.110224828124046,0.437932759523392,0.892224311828613,-0.363460153341293,"
           "0.0,0.0,0.0,1.0],"
           "\"windowHeight\":1055,"
           "\"windowWidth\":1920}")

```


| 常用窗口 |    ![](assets/Pasted%20image%2020260902220915.png)    |
| :--: | :---------------------------------------------------: |
| 方形窗口 |    ![](assets/Pasted%20image%2020260902220724.png)    |
| 全屏窗口 |    ![](assets/Pasted%20image%2020260902221033.png)    |
|      | # ps.set_view_projection_mode("perspective") 这一行要 注释掉 |

