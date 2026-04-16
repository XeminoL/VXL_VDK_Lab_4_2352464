# Full Exercise — Cooperative Task Scheduler with 5 LED Blink Tasks

## Overview
Implements a reusable cooperative scheduler (`scheduler.c/h`) that manages up to **10 concurrent tasks** via a single TIM2 interrupt. Demonstrates with 5 LEDs blinking at independent frequencies.

## Module Map
```
Lab_4.c (main)
├── TIM2 ISR ── SCH_Update()           [scheduler.c]
│
├── Setup ───── SCH_Init()              [scheduler.c]
│               SCH_Add_Task(Startup)   one-shot after 50 ticks
│
└── Main Loop ─ SCH_Dispatch_Tasks()    [scheduler.c]
                ├── Task_Startup → registers 5 periodic blink tasks
                ├── Task_Blink1 (period=50)
                ├── Task_Blink2 (period=100)
                ├── Task_Blink3 (period=150)
                ├── Task_Blink4 (period=200)
                └── Task_Blink5 (period=250)
```

## Hardware
| Component | GPIO |
|-----------|------|
| LED1–LED5 | PA0–PA4 |

## Files
| File | Description |
|------|-------------|
| `Lab_4.c` | Main program with task definitions |
| `Lab_4.hex` | Pre-compiled firmware |
| `Lab_4.pdsprj` | Proteus simulation |
| `Core/Src/scheduler.c` | Scheduler engine |
| `Core/Inc/scheduler.h` | Scheduler API & sTask struct |
