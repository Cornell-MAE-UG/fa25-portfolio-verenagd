---
layout: project
title: Wind Turbine Blade Desing
description: Class project CAD design
technologies: [MATLAB, ANSYS Mechanical]
image: /assets/images/BladeCAD.jpg
<p style="clear: both;"></p>
---
<p style="clear: both;"></p>

For my Fluids and Heat Transfer Laboratory class, we were divided into groups and tasked to design blades for a small-scale wind turbine and test them in an in-house wind tunnel. The requirements were the following:

**1. The blades must have a maximum length of 6 inches.** <br>
**2. The blades must be compatible with a standard hub piece 1" in radius**<br>
**3. The wind turbine must be designed to operate at a fixed angular velocity whose value is to be determined by your team, but should never exceed 2000 rpm.**<br>
**4. The wind turbine must be used in an environment where the wind follows a velocity distribution well described by a Weibull probability distribution with parameters k = 5 and c = 5:**<br>
**p(U) = (k/c)(U/c)<sup>k-1</sup>exp[-(U/c)<sup>k</sup>]**
    **where U is the incoming wind velocity in m/s.**

From the given requirements we decided on the parameters we can control and should take into account with our design:

**1. Angle of attack** <br>
**2. RPM**<br>
**3. Blade length (max 6")**<br>
**4. Twist**<br>
**5. Chord length**<br>
**6. Number of blades (restricted to 3 with the provded hub)**<br>
**7. Airfoil**<br>

My team decided to take the provided Weibull distribution function and graphed the distribution of the probability of oncoming velocity. The mean, median, and mode of the velocity is about 4.78 m/s. 

![Wind Power Curve]({{ "/assets/images/WindPowerCurve.jpg" | relative_url }}){: .inline-image-l style="max-width:500px;"} 
*Distribution of the probability of oncoming velocity*
<p style="clear: both;"></p>



Using this as our approximated incoming wind speed, we decided to calculate what the maximum amount of available power from the wind at that speed would be using the equation, **P<sub>wind</sub> = 0.5&rho;Av<sup>3</sup>.** With an approximated air density of 1.21 kg/m<sup>3</sup>, blade radius of about 5.9", and swept area of (A=&pi;*T<sup>2</sup>) 0.0706 m<sup>2</sup>, there is an **available 4.8W of power from the wind.**

We decided to work backwards from 2000 RPM requirement and develop an optimal tip-speed ratio for our system. Using the formula: **&lambda; = &omega;R / V** , where &omega; is the angular velocity of the blade (2000 RPM), R is the radius of the blade (5.9"), and V is the speed of the incoming wind (4.78 m/s), we get a resulting value of about 6.63. We decided on a final value of &lambda; = 6, given that we would not want to exceed the 2000 RPM value. To double check that we do not surpass 2000 RPM, we estimated what our angular velocity would be: &omega; = (&lambda;V)/R = 1808 RPM.

The optimal angle of attack, corresponding lift coefficient, and maximum lift-to-drag ratio were obtained from published low Reynolds number airfoil polar data for the airfoil SG66043. This airfoil was chosen since it is optimized for low Re, low velocity wind turbines. 

**Maximum lift to drag ratio:** L/D = 39.7 at &alpha; = 8.75<sup>&deg;</sup> <br>
**Optimal Lift coefficient:** C<sub>L</sub> = 1.4 <br>

**Local inflow angle along the blade:** <br>
&phi;(r) = arctan(2/3&lambda;(r)) <br>
where &lambda;(r) = &lambda;r/R

**Pitch (twist) distribution:**<br>
&theta;(r) = &phi;(r) - &alpha;<sub>opt</sub> <br>
This function creates a high pitch near the hub that decreases toward the tip and has an approximately linear twist profile over the span. The twist is important for the blade to be oriented at the optimal inflow angle, as the effective velocity vector changes along the length of the blade, given that each section of the blade has a different angular velocity.

![Pitch Angle Function]({{ "/assets/images/PitchAngleCurve.jpg" | relative_url }}){: .inline-image-l style="max-width:600px;"} 
*Pitch Angle as a function of blade length*
<p style="clear: both;"></p>

**Chord length distribution:**<br>
c(r) = (8&pi;Rsin&phi;(r))/(3BC<sub>L</sub>&lambda;(r))

Here, the input parameters are the number of blades (B), which we were told shall be 3, and the lift coefficient that we had determined to be 1.4. This function yields a large chord at the base of the hub followed by a tapered tip, which aids in reducing high-RPM efficiency, as well as mitigating structural failure.

![Chord Length Distribution]({{ "/assets/images/ChordLengthFunction.jpg" | relative_url }}){: .inline-image-l style="max-width:600px;"}
*Chord length as a function of blade length*
<p style="clear: both;"></p>

**Torque Estimation:** <br>
dQ = 4a'(1-a)&rho;U<sup>2</sup>&pi;r<sup>3</sup> <dr>

Assuming Betz-optimal induction factors: <br>
Axial: a = 0.333, Tangential: a' = 0.02

Worst case wind speed considered (wind tunnel maximum speed):
U = 8 m/s

Predicted maximum torque: Q = 3.4 Ncm
This lies within the 3.5 Ncm torque brake limit that was provided.

**Structural Analysis**
We decided to model the blade as a cantilevered beam where the greatest point of failure is at the hub. This possible point of failure is where the bending moment is greatest. &sigma;<sub>b</sub>= My/I, where M is the moment (in this case the downwards force from the blade's weight multiplied by the distance from the hub), y = the distance from the edge to the neutral axis, and I is the blade's moment of inertia. We decided to input our CAD model into ANSYS to gauge the maximum deflection given our parameters:
<p style="clear: both;"></p>

![Blade Structural Analysis]({{ "/assets/images/BladeANSYS.jpg" | relative_url }}){: .inline-image-l style="max-width:600px;"}
*ANSYS Rendering of Total Deformation of Blade*
<p style="clear: both;"></p>


**Experimental Procedure:**

Once we had 3D printed our blades, we evaluated their performance in an in-house wind tunnel. In order to characterize the behavior of our blade, we decided on generating power curves at selected wind speeds. We did so by mounting our blades onto the center hub and ramping up the wind speed until the blade started spinning. The applied load was then controlled by incrementally increasing the voltage of an attached torque brake, allowing the turbine to operate at steady rotational speeds across a range of RPMs. For each data point, we waited 30s to 1 min for the system to reach steady state, such that the torque we apply via the torque brake balances the aerodynamic torque produced by the blades. This torque, combined with the measured angular velocity, was used to compute the mechanical power output of the turbine, assuming P = &tau;&omega;. 

A LabView VI was set-up to collect data. The wind speed was measured via a Pitot-Static tube, the RPM...

![Power Curve 1]({{ "/assets/images/PowerCurve1.jpg" | relative_url }}){: .inline-image-l}
![Power Curve 2]({{ "/assets/images/PowerCurve2.jpg" | relative_url }}){: .inline-image-l}
![Power Curve 3]({{ "/assets/images/PowerCurve3.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

**Data Analysis**

To characterize blade performance, we evaluated the tip-speed ratio and power coefficient at the maximum power point for each run. Tests were conducted at wind speeds of 4.7 m/s, 5.6 m/s, and 6.8 m/s. 
Constant wind speed of 4.7 m/s: &lambda; = 3.599, Max C<sub>p</sub> = 0.029
Constant wind speed of 5.6 m/s: &lambda; = 4.27, Max C<sub>p</sub> = 0.032
Constant wind speed of 6.8 m/s: &lambda; = 4.24, Max C<sub>p</sub> = 0.034

As wind speed increased, power output also increased, consistent with the cubic dependence of available wind power on velocity (𝑃 ∝ 𝑉 <sup>3</sup>). For each wind speed, peak power occurred at lower RPMs, which aligns with the blade being designed to operate at an optimal tip-speed ratio. As wind speed increases, maintaining this ratio requires lower rotational speed. At higher RPMs, increased tangential blade velocity alters the relative wind direction, reducing the effective angle of attack once the blade operates beyond its optimal tip-speed ratio. This leads to reduced lift, increased drag torque, and decreased power output, trends that are reflected in the experimental results.


**Reflection**
The initial objectives for our wind turbine to successfully produce power, meet geometric requirements, and not structurally fail were all met. However, we were unable to keep the RPM of the blades under 2000. Below approximately 4.5 m/s, the aerodynamic torque was insufficient to overcome the Coulomb friction in the system, while wind speeds above roughly 5.6 m/s caused the turbine to overspeed. A second design iteration would therefore prioritize increasing low-speed torque while intentionally reducing high-RPM aerodynamic efficiency. This could be achieved by increasing chord length near the hub to enhance startup torque and modifying tip geometry to limit high-TSR performance, since the outer blade sections dominate power extraction at high rotational speeds.

CAD Model:<br>
![Blade CAD]({{ "/assets/images/BladeCAD.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>



