# 🔬 Microprocessors & Microcontrollers — Lab 4

> **STM32F103C6 — Cooperative Task Scheduler**

| | |
|---|---|
| **Student** | Dang Thai Khang — 2352464 |
| **Course** | C03010 — CC02 |
| **Institution** | Ho Chi Minh University of Technology (HCMUT) |
| **Lecturer** | Phan Van Sy |
| **Semester** | 2025 |

---

## 📌 Overview

This lab implements a **cooperative task scheduler** on the STM32F103C6, replacing ad-hoc timer flag polling with a structured scheduling framework. The scheduler manages multiple periodic tasks (LED blinking at different rates) running concurrently through a single timer interrupt.

### Tech Stack

- **MCU:** STM32F103C6 (ARM Cortex-M3)
- **IDE:** STM32CubeIDE (HAL Library)
- **Simulation:** Proteus 8
- **Language:** C
- **Key Concepts:** Cooperative scheduling, task management, function pointers

---

## 📂 Repository Structure

```
VXL_VDK_Lab_4_2352464/
└── FULL_EXERCISE/
    ├── Lab_4.c                # Main program (task definitions, scheduler setup)
    ├── Lab_4.hex              # Compiled firmware for Proteus
    ├── Lab_4.pdsprj           # Proteus schematic & simulation
    └── Core/
        ├── Inc/
        │   └── scheduler.h    # Scheduler API & task struct definition
        └── Src/
            └── scheduler.c    # Scheduler implementation
```

---

## 🧪 Scheduler Architecture

### Task Struct
```c
typedef struct {
    void (*pTask)(void);   // Function pointer to task
    uint32_t Delay;        // Initial delay before first run (ticks)
    uint32_t Period;       // Repeat interval (0 = one-shot)
    uint8_t  RunMe;        // Flag: incremented when task is ready
    uint32_t TaskID;       // Slot index in task array
} sTask;
```

### Scheduler API

| Function | Description |
|----------|-------------|
| `SCH_Init()` | Clear all task slots (max 10 tasks) |
| `SCH_Add_Task(func, delay, period)` | Register a task with initial delay and repeat period |
| `SCH_Update()` | Called in TIM2 ISR — decrements delays, sets RunMe flags |
| `SCH_Dispatch_Tasks()` | Called in main loop — executes ready tasks, removes one-shots |
| `SCH_Delete_Task(id)` | Remove a task by ID |

### Execution Flow
```
TIM2 ISR (every 10ms)
    └── SCH_Update()  →  decrement delays, flag ready tasks

Main Loop
    └── SCH_Dispatch_Tasks()  →  run flagged tasks, auto-delete one-shots
```

---

## 🧪 Task Configuration

The system uses a **2-phase startup**:

| Phase | Description |
|-------|-------------|
| **Phase 1** | `Task_Startup` (one-shot): turns on all 5 LEDs, then registers 5 blink tasks |
| **Phase 2** | 5 periodic blink tasks running concurrently at different rates |

### Blink Tasks

| Task | LED | Period | Blink Rate |
|------|-----|--------|------------|
| `Task_Blink1` | LED1 (PA0) | 50 ticks | 500ms (1 Hz) |
| `Task_Blink2` | LED2 (PA1) | 100 ticks | 1000ms (0.5 Hz) |
| `Task_Blink3` | LED3 (PA2) | 150 ticks | 1500ms (0.33 Hz) |
| `Task_Blink4` | LED4 (PA3) | 200 ticks | 2000ms (0.25 Hz) |
| `Task_Blink5` | LED5 (PA4) | 250 ticks | 2500ms (0.2 Hz) |

> Each tick = 10ms (TIM2 period). All tasks share the same timer interrupt.

---

## ⚙️ How to Run

### Proteus Simulation
1. Open `FULL_EXERCISE/Lab_4.pdsprj` in **Proteus 8+**
2. Load `Lab_4.hex` into the STM32 component
3. Click **Run** — observe 5 LEDs blinking at different frequencies

### Flash to Hardware
1. Create STM32F103C6 project in STM32CubeIDE
2. Copy `Lab_4.c` as `main.c` and `Core/` files into the project
3. Configure TIM2 with Prescaler=7999, Period=9 (10ms tick)
4. Build and flash via ST-Link

---

## 📝 Why Cooperative Scheduling?

| Approach | Pros | Cons |
|----------|------|------|
| Blocking `HAL_Delay()` | Simple | Can only do one thing at a time |
| Flag-based polling | Concurrent tasks | Messy with many timers/flags |
| **Cooperative Scheduler** | Clean API, scalable, easy to add/remove tasks | Tasks must not block |

---

## 📄 Report

The full lab report (PDF) with schematics, timing diagrams, and source code explanations is available in the course submission.

---

## 📜 License

This project is developed for educational purposes as part of the Microprocessors & Microcontrollers course at HCMUT.
