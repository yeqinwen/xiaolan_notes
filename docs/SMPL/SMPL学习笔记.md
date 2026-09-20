


## 1 拟合扫描人体

### 1.1 设置姿态参数

```python
A_pose_np = np.zeros((1, 72))
A_pose_np[:, 3 * 1 + 2] = 0.1  # 右腿长开
A_pose_np[:, 3 * 2 + 2] = -0.1  # 左腿长开

A_pose_np[0, 3 * 1 + 1] = 0.3  # 右腿沿大腿轴线旋转 
A_pose_np[0, 3 * 2 + 1] = -0.3  # 左腿沿大腿轴线旋转 

A_pose_np[0, 3 * 13 + 2] = -0.2  # 左手
A_pose_np[0, 3 * 14 + 2] = 0.2  # 右手
A_pose_np[0, 3 * 16 + 2] = -0.8  # 左臂
A_pose_np[0, 3 * 17 + 2] = 0.8  # 右臂
```





### 1.2 保存拟合参数
```python
import json

with open("result_betas.json", "w") as fp:
    json.dump(
        {"betas": betas.detach().cpu().numpy().tolist()},
        fp,
        sort_keys=True,
        indent=4,
        ensure_ascii=False
    )

result_poses = poses.detach().cpu().numpy()
with open("result_poses.json", "w") as fp:
    json.dump(
        {"poses": poses.detach().cpu().numpy().tolist()},
        fp,
        sort_keys=True,
        indent=4,
        ensure_ascii=False
    )

with open("result_trans.json", "w") as fp:
    json.dump(
        {"trans": trans.detach().cpu().numpy().tolist()},
        fp,
        sort_keys=True,
        indent=4,
        ensure_ascii=False
    )
```

### 1.3 加载拟合参数

```python
import json

with open("result_poses.json", "r", encoding="utf-8") as fp:
    result_poses = json.load(fp)

with open("result_betas.json", "r", encoding="utf-8") as fp:
    result_betas = json.load(fp)

with open("result_trans.json", "r", encoding="utf-8") as fp:
    result_trans = json.load(fp)

result_poses = result_poses["poses"]
result_betas = result_betas["betas"]
result_trans = result_trans["trans"]
```