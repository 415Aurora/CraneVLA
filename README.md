# CraneVLA — Unity/PICO Teleoperation & Learning-Control Experiments

CraneVLA connects Unity crane simulation, PICO teleoperation, demonstration collection, LeRobot data preparation, RLinf/OpenPI policy adaptation and closed-loop evaluation. **`tower-crane-pico-unity` is the simulation and demonstration subsystem of CraneVLA, not a separate portfolio project.** 本仓库展示塔吊仿真项目的既有训练与诊断结果，仅包含文档、图表和对应标量数据，不包含完整源码、训练权重或原始示教数据。

## Project scope

The project uses physical-time validation, 10 Hz resampling, six-dimensional joint states and 16-step three-axis action windows. The artifacts below illustrate paired training and offline diagnosis. They do not establish physical-crane deployment, closed-loop improvement or generalization.

## System and project organization / 系统组成

**CraneVLA 是一个完整的塔吊仿真学习控制项目，`tower-crane-pico-unity` 是其核心子系统。** 项目链路为：Unity 场景与三轴执行 → PICO 遥操作及多视角示教 → 数据校验与 LeRobot 导出 → π0.5 微调适配 → 策略推理、Unity 闭环执行与失败诊断。

| Component / 组成 | Implemented work / 已有工作 |
|---|---|
| `tower-crane-pico-unity`: simulation and teleoperation | Unity Linux simulation, PICO client, camera views, three-axis commands, episode recording and control-safety handling / Unity 仿真、PICO 客户端、多视角画面、三轴指令、回合记录与控制安全处理 |
| Demonstration and data pipeline | Physical-time alignment, quality checks, 10 Hz Joint-v2 export, LeRobot video/state/action mapping / 物理时间对齐、质量检查、10 Hz 导出与视频、状态、动作映射 |
| RLinf/OpenPI adaptation | Tower-crane dataset/policy adapters, π0.5 SFT configurations, two-/three-view inputs and training records / 塔吊数据与策略适配、微调配置、双/三视角输入及训练记录 |
| Evaluation and diagnosis | Unity closed-loop task records, paired experiments, motion-history and auxiliary-supervision studies, occlusion diagnostics / 闭环任务记录、配对实验、运动历史与辅助监督研究、遮挡诊断 |

The names above identify components of the full local project; this public repository contains the curated documentation and result artifacts only. The local evaluation directory also named `tower-crane-pico-unity` belongs to the same project. It is not an additional independent project or a second public source repository.

上述名称表示完整本地项目的组成；本公开仓库仍为文档与结果展示仓库。另一个同名本地目录中的评测材料也归属 CraneVLA，不重复计算项目数量。

The implemented data contract stores the next absolute joint position and constructs each 16-step training chunk relative to the **same current position**. Six-dimensional state combines three joint positions and their velocities. Incomplete tail windows are excluded. Policy inputs use camera observations, joint state and task text; evaluation labels and goal ground truth must remain separate from policy inputs.

The task is simulated transport and target-region positioning of an already attached load. Recorded success criteria must be read with the corresponding evaluation protocol; they are not evidence of automatic hooking/unhooking, real-crane deployment or validated load-sway control. Training curves and development comparisons remain separate from final held-out generalization claims.

任务范围是已挂载货物的仿真搬运与目标区域定位。成功标签须结合对应评测协议解释，不扩展为自动挂钩/脱钩、实塔部署或已验证的防摆控制能力。已运行的训练与开发集诊断、尚待完成的正式泛化实验分别表述。

## Paired training

![Control versus pixel-supervision training curves](docs/media/pixel-training-comparison.png)

Historical training curves use 50-step moving means and logarithmic y axes. The auxiliary pixel-localization loss decreases; this does not by itself demonstrate improved closed-loop success. Total objectives differ between the arms, so their raw total-loss values should not be used as a model ranking.

历史配对训练：像素定位损失下降不等于闭环成功率提高；总损失包含项不同，不能据此给模型排名。

## Offline occlusion diagnosis

![Original image, goal occlusion and matched control occlusion](docs/media/occlusion-diagnostic.jpg)

The original cabin view is compared with a covered unloading point and a matched control region. These are intervention examples from transport and near-target phases, not demonstrations of autonomous task success.

原始图像、卸货点遮挡和匹配区域遮挡用于离线诊断；示例图不能单独证明因果结论或自主搬运成功。

[Figure context and source data / 图注与源数据](docs/visual-results.md) · [Asset provenance / 素材来源](docs/media/manifest.json)

## Contribution and attribution

I proposed the project concept and independently developed the custom software, algorithm adaptation and experiment workflows. A collaborator created the base Unity model from my concept. RLinf/OpenPI, Unity/PICO and other upstream tools retain their respective attribution.

本人提供项目思路并独立完成自有软件、算法适配及实验开发；基础Unity建模由协作者完成。上游模型、框架、工具和基础资产不计为个人原创。

## Reproducibility boundary

The included CSV files are archived training scalar records. The images are byte-for-byte copies of existing results, with SHA-256 recorded in the manifest. Source paths in the manifest refer to the preparation workspace, not downloadable files in this repository. Full simulation/training reproduction requires unpublished code, configurations, datasets and model artifacts. No new experimental run was performed for this showcase.
