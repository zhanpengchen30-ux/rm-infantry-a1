# RM Infantry A · 步兵机器人底层控制固件

> RoboMaster Infantry A 板 STM32F4 底层固件，基于 STM32Cube HAL + FreeRTOS 的多任务机器人控制框架。
> STM32F4 (Infantry A Board) robot firmware — STM32Cube HAL + FreeRTOS, multi-task control framework.

## 硬件平台

- 主控：RoboMaster Infantry A 板（STM32F4 系列）
- 实时系统：FreeRTOS（CMSIS-RTOS v2 接口）
- 外设：双路 CAN、USART（遥控器/调试）、DMA、GPIO、TIM
- 工具链：STM32CubeIDE / Keil MDK（含 `.ioc` CubeMX 工程）

## 功能模块

| 目录 | 内容 | 说明 |
| --- | --- | --- |
| `Core/Application` | `chassis_task` 底盘任务 · `gimbal_task` 云台任务 · `detect_task` 离线检测 · `board_com` 板间通信 | 机器人核心控制任务与板间数据交互 |
| `Core/BSP` | `bsp_can` · `bsp_usart` | CAN 滤波器配置、遥控器串口等底层驱动 |
| `Core/Algorithm` | `pid` · `ramp` | PID 控制器与斜坡/软启动函数 |
| `Core/Module` | `imu` · `motor` · `remote` | IMU 数据解析、电机封装、遥控器（DR16）解码 |
| `Core/Src` `Core/Inc` | HAL / FreeRTOS 生成代码 | CubeMX 自动生成，含时钟树与外设初始化 |

## 主要特性

- 底盘 + 云台双任务控制闭环，PID + 斜坡输出限幅
- 板间 CAN 通信协议（`board_com`）实现 A/C 板数据交换
- 遥控器（DR16）解码与离线检测（`detect_task`），提升系统鲁棒性
- 模块化分层：`Application`（任务） / `Module`（功能模块） / `BSP`（底层驱动） / `Algorithm`（控制算法）

## 目录结构

```
RM_Infantry_A_Board.ioc        # CubeMX 工程
Core/
├─ Application/                # 控制任务与板间通信
├─ BSP/                        # 底层外设驱动
├─ Algorithm/                  # PID / 斜坡
├─ Module/                     # IMU / 电机 / 遥控器
├─ Src/ Inc/                   # HAL + FreeRTOS 生成代码
Drivers/                       # STM32 HAL + CMSIS
Middlewares/Third_Party/       # FreeRTOS 源码
MDK-ARM/                       # Keil 工程
```

## 编译说明

1. 用 STM32CubeIDE 打开 `RM_Infantry_A_Board.ioc` 重新生成代码，或直接用 `MDK-ARM/` 下的 Keil 工程编译。
2. `Drivers/CMSIS` 中的预编译 DSP 库（`*.lib` / `*.a`）按 `.gitignore` 不纳入版本管理；编译前请确保本地 STM32Cube 固件包内含对应的 CMSIS-DSP，否则相关数学运算链接会失败。
3. FreeRTOS 任务在 `Core/Application` 中实现，由 `main.c` 中的调度器启动。

## 状态

功能任务模块（底盘 / 云台 / 通信 / 检测）已搭建并接入 FreeRTOS 调度，可作为步兵机器人底层控制的完整参考实现。

---

© 代码归作者所有。HAL / FreeRTOS 等第三方组件遵循其各自许可证（详见对应源码头文件）。
