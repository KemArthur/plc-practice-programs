# 01: Pump and fan interlock

**Shows:** two seal-in (latching) circuits, and a permissive that makes one depend on the other.

## Brief

A chilled water pump and a cooling tower fan, each with its own Start and Stop button.

- The fan can't start unless the pump is running.
- If the pump stops for any reason, the fan stops at once.
- When the pump restarts, the fan stays off until its own Start is pressed.

## I/O list

| Tag | Type | Address | Description |
|---|---|---|---|
| Pump_Start_PB | Input | %IX0.0 | Pump start button, TRUE when pressed |
| Pump_Stop_PB | Input | %IX0.1 | Pump stop button, TRUE when pressed |
| Fan_Start_PB | Input | %IX0.2 | Fan start button, TRUE when pressed |
| Fan_Stop_PB | Input | %IX0.3 | Fan stop button, TRUE when pressed |
| Chilled_Water_Pump | Output | %QX0.0 | Chilled water pump contactor |
| Cooling_Tower_Fan | Output | %QX0.1 | Cooling tower fan contactor |
| Chilled_Water_Pump_Run | Memory | none | Pump run command. Latched by Pump_Start_PB, dropped by Pump_Stop_PB |
| Cooling_Tower_Fan_Run | Memory | none | Fan run command. Latched by Fan_Start_PB, needs the pump running, dropped by Fan_Stop_PB |

## Ladder (rung comments)

1. Start latches the pump command on. Stop drops it.
2. Drives the chilled water pump contactor from its latched command.
3. Fan can only start while the chilled water pump is running (interlock). Fan Stop, or the pump stopping, drops the latch. The fan does not restart by itself when the pump restarts.
4. Drives the cooling tower fan contactor from its latched command.

## Logic as structured text

```
Chilled_Water_Pump_Run := NOT Pump_Stop_PB AND (Chilled_Water_Pump_Run OR Pump_Start_PB);
Chilled_Water_Pump     := Chilled_Water_Pump_Run;
Cooling_Tower_Fan_Run  := NOT Fan_Stop_PB AND (Cooling_Tower_Fan_Run OR Fan_Start_PB) AND Chilled_Water_Pump;
Cooling_Tower_Fan      := Cooling_Tower_Fan_Run;
```

## Design notes

- Each run command is a memory bit, and a separate rung drives the output from it. That keeps the logic (when should it run?) apart from the output (switch the contactor), which makes adding interlocks or fault logic later easier.
- The pump sits in series inside the fan's seal-in, not just on the fan's output rung. So when the pump stops, the fan's latch drops too, and the fan can't come back on by itself when the pump restarts.

## Test record

Tested in the OpenPLC simulator.

| # | Action | Expected | Result |
|---|---|---|---|
| 1 | Press Fan_Start_PB with the pump off | Fan does not run | Pass |
| 2 | Press Pump_Start_PB | Pump runs and stays running after release | Pass |
| 3 | Press Fan_Start_PB | Fan runs and stays running after release | Pass |
| 4 | Press Fan_Stop_PB | Fan stops, pump keeps running | Pass |
| 5 | Start both, then press Pump_Stop_PB | Pump and fan both stop at once | Pass |
| 6 | Press Pump_Start_PB again (fan not started) | Pump runs, fan stays off until its own Start is pressed | Pass |
