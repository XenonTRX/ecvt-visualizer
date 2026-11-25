# e-CVT Visualizer

An interactive visualization of the **Toyota hybrid power-split transmission** (the "e-CVT"),
styled like Windows XP because it deserved to be.

**Live:** https://xenontrx.github.io/ecvt-visualizer/

A Toyota hybrid has no gears to shift and no clutches. One planetary gearset ties the engine,
the generator (MG1) and the output shaft together, and the ratio is set purely by choosing how
fast MG1 spins. This page lets you drag that around and watch every speed, torque and power
flow in the transmission respond.

## What it shows

| Panel | What's in it |
|---|---|
| **Lever diagram** | All three shaft speeds on one straight line, with the MG1 over-speed limit and a km/h scale |
| **Power split** | How engine power divides between the gear path and the electrical path, including the circulating-power region |
| **Engine map** | Torque vs. speed with constant-power hyperbolae and the efficient operating line |
| **Speed sweep** | Every shaft's rpm from standstill to top speed |
| **Relationships** | The equations behind all of it |
| **Operating points** | Worked numbers for eleven driving conditions |

## The maths

With a sun gear of `Zs` teeth and a ring gear of `Zr` teeth (30 and 78 on a Prius, so `k = 2.6`):

```
speeds    Zs·ω_MG1 + Zr·ω_out = (Zs + Zr)·ω_ICE
          ω_MG1 = 3.6·ω_ICE − 2.6·ω_out

torques   T_MG1  = Zs/(Zs+Zr) · T_ICE = 0.2778 · T_ICE
          T_ring = Zr/(Zs+Zr) · T_ICE = 0.7222 · T_ICE

power     P_mech = 0.7222 · T_ICE · ω_out
          P_elec = 0.2778 · T_ICE · ω_MG1
          f_elec = 1 − 0.7222 · ω_out / ω_ICE
```

Three consequences worth knowing:

- **The torque split never changes.** MG1 can only ever hold back 27.8 % of engine torque;
  everything else the wheels feel comes from MG2.
- **MG1's torque is the clutch.** Zero MG1 torque means the whole gearset carries nothing, which
  is how the engine disconnects in EV mode. There is no clutch pack anywhere in the box.
- **Above the mechanical point** (`ω_out/ω_ICE = 1.3846`) the electric fraction goes negative:
  MG1 motors, MG2 generates to feed it, and power circulates backwards through two conversions.
  That's the high-speed cruise penalty.

## Running it

It's a single static `index.html` with no build step and no dependencies.

```bash
python3 -m http.server 8787
```

## Caveats

Tooth counts 30/78 are the commonly cited Prius Gen-1/2/3 values, and they're editable in the UI
for any other input-split gearset. The engine torque curve and efficient operating line are
representative of a 1.5 L Atkinson unit, drawn for scale — not manufacturer data. All power
figures are lossless; real inverter and machine efficiency is roughly 90 % per conversion.
