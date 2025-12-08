---
layout: project
title: Torque Wrench Design
description: Class project CAD design
technologies: [MATLAB, ANSYS Mechanical]
image: /assets/images/TW.png
<p style="clear: both;"></p>
---
<p style="clear: both;"></p>

For my Mechanics of Materials class, I was tasked to design a torque wrench that met the following requirements:

1. Rated for 600 in-lbf, and must sustain a fully reversed torque of T = +/-600in-lbf for 10<sup>6</sup> cycles

2. Attain at least 1.0 mV/V output at the rated torque of 600 in-lbf

3. Safety factor of X<sub>o</sub> = 4 for yield failure

4. Safety factor of X<sub>k</sub> = 2 for crack growth from an assumed crack of depth 0.04" (1 mm)

5. Fatigue stress safety factor of X<sub>s</sub> = 1.5

6. Material must be a steel, aluminum, or titanium alloy


Derivation:

I decided to apply classical beam theory and modeled the system as a simple cantilever beam to simplify the calculations and model. I decided to work backwards from the specified safety factors to determine the dimensions and material choice.

![Determining handle dimensions]({{ "/assets/images/SafetyFactors.jpg" | relative_url }}){: .inline-image-l}

![Determining gauge output]({{ "/assets/images/GaugeOutput.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>


Since I had already derived the model's dimensions, I decided to select a material with a Young's Modulus that allowed for the strain gauge to have an acceptable sensitivity. Given that cost was not a concern, I decided to go for a Titanium alloy. Specifically, Ti-6Al-4V given its useful ductility and strength. It has a Young's Modulus of 16e6 psi, a tensile strength of 173e3 psi, fracture toughness of 78.4e3 psi in<sup>1/2</sup>, and a fatigue strength of 92.2e3 for 10<sup>6</sup> cycles.

Image(s) of CAD model:

![Torque Wrench Model]({{ "/assets/images/Full-Body CAD.jpg" | relative_url }}){: .inline-image-l}
![Height of Handle]({{ "/assets/images/Height.jpg" | relative_url }}){: .inline-image-l}
![Width of Handle]({{ "/assets/images/Width.jpg" | relative_url }}){: .inline-image-l}
![Length of Handle]({{ "/assets/images/Length.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

Below are screenshots of how the clamped driver head conditions and the load at the handle's end was applied:
![Clamped Boundary Conditions]({{ "/assets/images/ClampedBC.jpg" | relative_url }}){: .inline-image-l}
![Point Load]({{ "/assets/images/AppliedForce.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>


Normal strain contours (in the strain gauge direction):
![Normal Strain Contours]({{ "/assets/images/NormalStrainContours.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>


Contour plot of maximum principal stress from FEM:
![Max Principal Stresses]({{ "/assets/images/MaxPrincipalStresses.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

Maximum normal stress:
![Max Normal Stresses]({{ "/assets/images/NormalStressContours.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

Load Point Deflection:
![Load Point Deflection]({{ "/assets/images/LoadPointDeflection.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

Torque wrench sensitivity in mV/V (strain at strain gauge location):
![Torque Wrench Sensitvity]({{ "/assets/images/TorqueWrenchSensitivity.jpg" | relative_url }}){: .inline-image-l}
<p style="clear: both;"></p>

For this model, I have decided to use a metal foil strain gauge with a gauge factor (GF) of about 2, and with dimensions smaller than 0.3" in width. Additionally, given the predicted strain of 1.3e-3 for the rated load of 600lbf, I decided to look for a standardized gauge that met these requirements. Therefore, the strain gauge I chose was a SENS-BF350-3AA Precision Resistive Strain, with a monolithic size of 7.1mm x 4.5mm.


The maximum normal stress predicted by finite element analysis exceeds the hand-calculated value because classical beam theory does not account for the drive head geometry. The analytical model treats the torque wrench as a uniform cantilevered beam, so the calculated maximum stress corresponds to bending in the drive handle rather than the localized stress at the handle–drive interface.

When the FEM results are evaluated along the drive handle alone, the maximum normal stress is approximately 20.5 ksi, which closely matches the analytically predicted value of 21 ksi. The highest stresses occur at the clamped interface, where an abrupt geometric discontinuity and the combined transfer of bending and torsional loads produce localized stress concentrations not captured by beam theory.

The predicted displacements from FEM and hand calculations are nearly identical, indicating that the cantilever beam assumption accurately represents the global stiffness of the system. Strains extracted from the FEM at the strain-gauge locations yield a torque-wrench sensitivity exceeding the required 1.0 mV/V at 600 in-lbf, confirming that the design meets both structural and measurement performance requirements.

metal foil strain gauge with a gauge factor of 2 that is smaller than 0.3"

Supporting Code:


```matlab

    % REQUIREMENTS

    % non-ratcheing 3/8" drive instrumented torque wrench
% rated for 600 in-lbf

% maximize the voltage output, at least 1 mV/V at 600 in-lbf
% - higher output = more sensitivity and improved signal to noise ratio
% must have a fully reversed torque of +/-600 in-lbf for 10^6 cycles
% safety factor of Xo = 4 (yield/brittle fracture, you choose (material))
% safety factor of Xk = 2 for crack growth (assume crack depth 0.04 in)
% fatigue stress safety factor of Xs = 1.5
% material must be steel, aluminum, or titanium alloy

% ASSUMPTIONS
% can assume clamped boundary conditions in the upper 0.4" of the drive


%DESIGN GIVEN:

M = 600; % max torque (in-lbf)
L = 16; % length from drive to where load applied (inches)
h = 0.75; % width
b = 0.5; % thickness
c = 1.0; % distance from center of drive to center of strain gauge
E = 32.E6; % Young's Modulus (psi)
nu = 0.29; % Poisson's ratio
su = 370e3; % tensile strength use yield or ultimate depending on material (psi)
KIC = 15e3; % fracture toughness (psi in^1/2)
sfatigue = 115e3; % fatigue strength from Granta for 10^6 cycles
name = 'M42 Steel'; % material name

% TITANIUM
M = 600;
L = 16;
h = 0.75;
b = 0.3;
c = 1.0;
E = 16e6; % Young's Modulus (psi)
nu = 0.35; % Poisson's ratio
su = 173e3; % Tensile strength (psi)
KIC = 78.4e3; %fracture toughness (psi in^1/2)
sfatigue = 92.2e3; %fatigue strength from Granta for 10^6 cycles
name = 'Titanium Alloy'; %material name

% CALCS
P = M / L;
I = (b * (h^3)) / 12;
sigbendmax = (M*(h/2)) / I;


% OUTPUTS

%load point deflection (in)
deflection = (P * (L^3))/(3*E*I);
fprintf('\nMaximum deflection: %.3f in \n',deflection)

% max normal stress (kmax(si)
fprintf('Maxium normal stress: %d psi \n', sigbendmax)

%SAFETY FACTOR RESULTS

% safety factor for strength 
X0 = su/sigbendmax;
fprintf('Safety factor of stress: %.2f \n', X0)

% safety factor for crack growth
a = .04;
Ki = 1.12*sigbendmax*sqrt(pi*a);
Xk = KIC/Ki;
fprintf('Safety factor for crack growth: %.2f \n', Xk)

% safety factor for fatigue
Xs = sfatigue/sigbendmax;
fprintf('Safety factor for fatigue %.2f \n', Xs)

%STRAIN GAUGE RESULTS
% strain at gauge (microstrain)
e = sigbendmax/E;
fprintf('Strain at gauge: %e strain \n', e)

% output (mV/V at 600 in-lbf using half bridge)
K = 2; %strain gauge factor
Vout_Vin = (K/4)*2*e;
output = Vout_Vin*1000;
fprintf('Voltage output of strain gauge: %e mV/V \n', output)


fprintf('\nDesign Requirements & Safety Factors:\n')
fprintf('\n')
if output >= 1.0
    fprintf('Torque meets minimum requirements of 1.0 mV/V \n')
    else
    fprintf('Torque does not meet minimum requirements of 1.0 mV/V \n');
end

% Check if safety factors are acceptable
fprintf('\n')
if X0 >= 4
    fprintf('Safety factor for strength (yield or brittle) is acceptable: %.2f >= 4 \n', X0);
else
    fprintf('Safety factor for strength is not acceptable: %.2f < 4 \n', X0);
end

if Xk>=2 
    fprintf('Safety factor for crack growth is acceptable: %.2f >= 2\n', Xk)
else
    fprintf('Safety factor for crack growth is not acceptable: %.2f < 2 \n', X0);
end

if Xs >= 1.5
    fprintf('Safety factor for fatigue is acceptable: %.2f >= 1.5 \n', Xs);
else
    fprintf('Safety factor for fatigue is not acceptable: %.2f < 1.5 \n', Xs);
end

fprintf('\nMaterial Selection: %s \n', name)

fprintf('Load %.2f lbf\n', P)
```

