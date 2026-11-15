# 电控组第一阶段培训作业

STM32F103C8T6 最小系统板，Keil MDK-ARM + STM32CubeMX（HAL 库）。

## 开发环境

| 项 | 版本 |
|---|---|
| 芯片 | STM32F103C8T6（LQFP48，64KB Flash / 20KB RAM） |
| IDE | Keil MDK-ARM 5.24a，ARM Compiler 5.06 update 5 |
| 配置工具 | STM32CubeMX 6.18.1 |
| 固件包 | STM32Cube FW_F1 V1.8.7 |
| 下载器 | ST-Link V2（SWD） |
| 系统时钟 | 72MHz（HSE 8MHz 晶振 × 9，APB1 36MHz / APB2 72MHz） |

## 工程目录

| 目录 | 对应作业 | 实现内容 |
|---|---|---|
| `homework1` | 作业 1 | 外接 LED 以 500ms 周期闪烁，使用 `HAL_Delay` |
| `homework2` | 作业 2 | 同样效果，全程不使用 `HAL_Delay`，改用 `HAL_GetTick()` 时间差值 |
| `homework3` | 作业 3 | 按键经 EXTI 中断控制 LED 开关，含 20ms 软件消抖 |

作业 4（提交到 GitHub）即本仓库；作业 5、6 为选做，尚未完成。

## 硬件连接

| 功能 | 引脚 | 接法 |
|---|---|---|
| LED | PB12 | 推挽输出，外接 LED + 限流电阻 |
| 按键 | PA8 | 一端接 PA8，另一端接 GND；引脚内部上拉，下降沿触发 |

## 编译与下载

1. 打开对应目录下 `MDK-ARM\homeworkN.uvprojx`
2. `F7` 编译，`F8` 下载到芯片
3. 如需修改引脚或时钟，用 STM32CubeMX 打开同目录下的 `homeworkN.ioc`，改完 `GENERATE CODE`

> 自己写的代码都放在 `Core/Src/main.c` 的 `/* USER CODE BEGIN */ ... /* USER CODE END */` 之间，
> 重新生成代码时不会被覆盖。

## 代码要点

**作业 1**：`while(1)` 里翻转 + `HAL_Delay(500)`。主循环有 500ms 处于阻塞状态。

**作业 2**：记录上次翻转时刻，主循环每轮判断 `HAL_GetTick() - 上次 >= 500`，
满足才翻转并把上次时刻 `+= 500`。主循环全程不阻塞，可随时响应其他事件。

**作业 3**：EXTI 回调里只记录按下时刻并置标志；主循环消费标志，
用 `本次 - 上次接受的时刻 >= 20` 过滤机械抖动。中断里不做任何判断和延时。
