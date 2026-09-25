PID-Irrigation-Simulation
PID-Controlled Irrigation Tank Simulation
What this is

A Python simulation of a PID (Proportional-Integral-Derivative) controller regulating the water level in a tank. This is a software rebuild of the same problem I worked on in my Automatic Control II course project, where I used a Programmable Logic Controller (PLC) to automate an irrigation system with basic ladder logic. This project revisits that same control problem — keeping a system at a target level despite disturbances — but implements the controller itself in code instead of PLC hardware.

The problem

Imagine a water tank that should stay at 50% full. Water drains out naturally over time, and a valve can add water back in. The question a PID controller answers is: how far should the valve open, at every moment, to get the tank back to 50% as quickly and smoothly as possible?

How a PID controller thinks

A PID controller looks at three things every time step:

P (Proportional) — how far off are we right now? If the tank is way below target, open the valve a lot. If it's close, barely adjust.
I (Integral) — have we been off-target for a while? This adds up past error over time. It catches the case where a small, persistent gap never gets corrected — like a valve that's almost enough but never quite closes it.
D (Derivative) — how fast is the error changing? This looks ahead and slows the response down if we're approaching the target quickly, to avoid overshooting past it.
The controller adds these three signals together to decide the valve output. Each one has a "gain" (kp, ki, kd) that controls how much weight it gets.

Code walkthrough

PIDController class — stores the target level (setpoint) and the three gains. compute() runs once per time step:

error = setpoint - measured_value — how far off we are
self.integral += error * dt — running total of past error
derivative = (error - self.prev_error) / dt — rate of change of the error
output = kp*error + ki*integral + kd*derivative — combine all three into one valve command
simulate() function — runs the tank for 100 time steps:

Each step, it asks the PID controller for an output
Clamps that output between 0–10 (the valve physically can't open more than that)
Updates the tank level: water added by the valve, minus a constant drain of 0.5 per step (simulating natural water loss)
Records the level at every step so it can be plotted
The plot shows the tank level climbing toward the 50% target line and settling — this is the same "close the loop until it stabilizes" behavior my PLC system did in hardware, just visualized here in software.

What I changed and learned

I tried different values for kp, ki, and kd to see their effect:

## Iteration Log

**v1 — initial values: kp=1.2, ki=0.3, kd=0.05**
Tank reached target smoothly but slowly.

**v2 — increased kp to 2.0**
Faster response, but noticeable overshoot past the 50% target before settling.

**v3 — increased kd to 0.3 (kp still 2.0)**
Overshoot from v2 was visibly reduced. The derivative term "braked" the
system as it approached the target, without losing the faster response
gained from raising kp. This is the final tuned version reflected in
the code above.

My background is in aerospace and control systems (pitch stability analysis, PLC-based automation), and I'm moving toward robotics and autonomous systems, where PID control is one of the most fundamental building blocks — used in everything from drone stabilization to robotic arm positioning. This project is a deliberate, hands-on bridge between the control theory I already know and the software skills the field requires.

Tech used

Python, Matplotlib (for plotting). Built and run on Replit.
