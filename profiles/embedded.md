# Profile: embedded
When: microcontroller firmware (STM32, ESP32, RTOS, C/C++ running on hardware). Use together with `code`.
<!-- agent-lite profile. Change only with the user's consent. -->

- No dynamic memory (`malloc`/`free`, `new`/`delete`, heap containers such as `std::vector`) by default. Never allocate or free in ISRs, control loops or other latency-critical paths.
- Deterministic execution, predictable latency, bounded computation and readable control flow come first.
- No register wrappers, indirection layers or callbacks unless they give a clear, concrete benefit.
- Build reports include memory use, e.g. `built ✓ (flash 41.2/64 KB, RAM 9.8/20 KB)`.
- Only hardware proves firmware: write `built ✓, needs HW test` and never assume it works on the board.
- Always ask first: flashing or erasing a board, option bytes, fuses, read-out protection, bootloader changes, and pin, clock or peripheral configuration (`.ioc`, clock tree, linker-script memory regions).
- Generated code (STM32CubeMX and similar): edit only inside the generator's user regions (e.g. `/* USER CODE BEGIN … */`) or the boundary recorded in the project's docs.
- Register bits, timings and electrical limits come from the datasheet or reference manual; cite the section or page.
