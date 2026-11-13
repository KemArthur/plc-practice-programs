# plc-practice-programs

Practice PLC programs in ladder logic, built and tested in OpenPLC Editor. Each program has a brief, an I/O list, commented ladder, the logic written out as structured text, and a test record.

I'm an electrical engineering technician moving into control systems software. These programs are where I practise writing, documenting and testing PLC code the way it's done on a real project: a clear brief, a proper I/O list, a comment on every rung, and a test record that proves the program does what the brief says.

## Programs

| # | Program | What it shows | Tested |
|---|---|---|---|
| 01 | [Pump and fan interlock](01-pump-and-fan-interlock/) | Two seal-in circuits, one permissive (the fan needs the pump running) | Yes |
| 02 | [Duty/standby pump](02-duty-standby-pump/) | Automatic changeover to a standby pump on an overload trip | Yes |
| 03 | [Emergency stop with manual reset](03-emergency-stop-manual-reset/) | E-stop drops the run latch; release doesn't restart the plant | Yes |
| 04 | [Staggered pump start](04-staggered-pump-start/) | On-delay timer (TON) to stagger two motor starts | Yes |

More to follow: input debounce with a timer, run counting with a maintenance lockout, chained timers, and duty rotation.

## Wiring convention

These exercises treat stop buttons, E-stops and overloads as inputs that go **TRUE when pressed or tripped**, so the code uses **normally closed contacts** for them. On a real panel they're usually wired normally closed (fail-safe: a broken wire stops the plant), so the input is TRUE when healthy and the code would use normally open contacts instead. Start buttons are TRUE when pressed and use normally open contacts.

## Addressing

All the programs live in one OpenPLC project, which compiles to one PLC, so no two programs can share an address. Each program gets its own byte: program 01 uses `%IX0.x` and `%QX0.x`, program 02 uses `%IX1.x` and `%QX1.x`, and so on. Internal memory bits (run commands and latches) have no address.

## Opening the programs

Each folder has the program's `.ld` file. In OpenPLC Editor, add it to a project's `pous/programs` folder and it opens as a ladder program.

## Tools

- OpenPLC Editor (IEC 61131-3 ladder diagram)
- Tested in the OpenPLC simulator by forcing inputs and watching outputs and timer values
