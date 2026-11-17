# 电控组第一次培训作业

STM32F103C8T6 最小系统板，Keil MDK 5.24 + STM32CubeMX（HAL 库），系统时钟 72MHz。

## 工程说明

- `homework1` —— 作业1：外接 LED 以 500ms 周期闪烁，用 `HAL_Delay` 延时
- `homework2` —— 作业2：同样效果，全程不用 `HAL_Delay`，改用 TIM2 定时器中断
- `homework3` —— 作业3：按键通过 EXTI 中断控制 LED 开关，带 20ms 软件消抖

作业4 就是提交这个仓库。作业5、6 还没做。

## 接线

```
LED   ：PB12 → 电阻 → LED → GND
按键  ：PA8  → 按键 → GND，引脚内部上拉，下降沿触发
```

## 怎么编译

每个目录下有 Keil 工程，打开 `MDK-ARM` 里的 `.uvprojx`，F7 编译，F8 下载。

要改引脚或时钟，用 CubeMX 打开同目录的 `.ioc`，改完重新 GENERATE CODE。自己写的代码都在
`Core/Src/main.c` 的 `USER CODE` 区域里，重新生成不会被覆盖。

## 遇到的问题

- 按键一开始配成了上升沿，按下没反应、松手才动，改成下降沿才对
- 定时器回调函数名打错一个字母（写成 `HAL_TIN_`），编译能过但灯完全不动，查了很久
- 几个工程各自带一套 HAL 库，提交前用 `.gitignore` 排掉了编译产物和用不到的 CMSIS 预编译库
