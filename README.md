# motor-pid-simulink
# 直流电机 PID 控制 Simulink 仿真

## 项目简介
本项目在 Simulink 中搭建了直流电机转速闭环控制系统，验证了 PID 参数对系统响应、稳态误差、超调及抗噪性能的影响，并分析了一阶低通滤波器（LPF）对反馈噪声的抑制效果与相位滞后代价。

## 电机参数
- 传递函数：`1/(60s+1)`（大惯性直流电机）
- 目标转速：1000
- 仿真时长：200s
- 控制器：连续 PID

## 实验结果

### 实验一：纯 P 控制（Kp 对比）
验证比例控制对响应速度和稳态误差的影响。

| Kp | 波形 | 稳态值 | 静差 | 结论 |
|:---|:---|:---|:---|:---|
| 5 | ![kp5](Results/exp1a_pure_p_kp5.png) | ~833 | 有 | 响应慢 |
| 10 | ![kp10](Results/exp1b_pure_p_kp10.png) | ~909 | 有 | 响应加快 |
| 20 | ![kp20](Results/exp1c_pure_p_kp20.png) | ~952 | 有 | 响应更快 |
| 50 | ![kp50](Results/exp1d_pure_p_kp50.png) | ~980 | 有 | 响应最快，仍有静差 |

**结论**：P 增大 → 响应变快、静差减小；但纯 P 永远无法消除静差。

### 实验二：纯 P + 噪声（D 对噪声敏感）
验证高频噪声对控制系统的污染及微分项对噪声的敏感度。

| 条件 | 波形 | 结论 |
|:---|:---|:---|
| 无噪声 | ![无噪声](Results/exp2a_pure_p_kp10_no_noise.png) | 平滑基准 |
| 有 200Hz 噪声 | ![有噪声](Results/exp2b_pure_p_kp10_with_noise.png) | 毛刺出现 |
| 有噪声 + Kd=1 | ![有噪声+D](Results/exp2c_pure_p_kp10_kd1_with_noise.png) | D 放大噪声 |

**结论**：微分项对高频噪声极其敏感，工程上需配合低通滤波使用。

### 实验三：LPF 滤波效果
验证一阶低通滤波器对反馈噪声的抑制效果。

| 条件 | 波形 | 结论 |
|:---|:---|:---|
| 无 LPF | ![无LPF](Results/exp3a_no_lpf_with_noise.png) | 毛刺明显 |
| 有 LPF（53Hz）| ![有LPF](Results/exp3b_with_lpf.png) | 平滑 |

**结论**：LPF 能有效衰减高频噪声，但会引入相位滞后，需权衡滤波强度与响应速度。

### 实验四：PI 控制（Ki 对比）
验证积分项对稳态误差的消除作用，以及积分过强的副作用。

| Ki | 波形 | 最终值 | 超调 | 结论 |
|:---|:---|:---|:---|:---|
| 0.5 | ![ki0.5](Results/exp4a_pi_kp10_ki0.5.png) | 1000 | 小 | **最优参数** |
| 1.0 | ![ki1.0](Results/exp4b_pi_kp10_ki1.0.png) | 1000 | 中 | 积分偏强 |
| 2.0 | ![ki2.0](Results/exp4c_pi_kp10_ki2.0.png) | 1000 | 大 | 积分过强 |

**结论**：I 消除静差；Ki 过大会导致严重超调甚至震荡。

### 实验五：PID 完整系统（Kd 对比）
验证微分项对超调的抑制作用，以及 D 过强时对噪声的放大。

| Kd | 波形 | 超调 | 噪声影响 | 结论 |
|:---|:---|:---|:---|:---|
| 0.1 | ![kd0.1](Results/exp5a_pid_kp10_ki0.5_kd0.1.png) | 小 | 无 | **最优综合参数** |
| 0.5 | ![kd0.5](Results/exp5b_pid_kp10_ki0.5_kd0.5.png) | 更小 | 轻微抖动 | D 开始放大噪声 |
| 1.0 | ![kd1.0](Results/exp5c_pid_kp10_ki0.5_kd1.0.png) | 更小 | 明显抖动 | D 过强 |

**结论**：D 抑制超调，但过大时会放大高频噪声，需与 LPF 配合使用。

## 总结
| 环节 | 作用 | 副作用 |
|:---|:---|:---|
| P | 加快响应 | 存在静差 |
| I | 消除静差 | 过强导致超调 |
| D | 抑制超调 | 放大高频噪声 |
| LPF | 滤除噪声 | 相位滞后 |

## 文件说明
- `model/motor_pid_simulink.slx`：Simulink 模型
- `Results/`：各组实验波形截图
- `README.md`：项目说明

## 使用方法
1. 用 MATLAB/Simulink 打开 `model/motor_pid_simulink.slx`
2. 修改 PID 参数或噪声幅值
3. 点击 Run 运行仿真
4. 双击 Scope 查看波形

## 环境要求
- MATLAB R2020a 或更高版本
- Simulink
- Control System Toolbox
