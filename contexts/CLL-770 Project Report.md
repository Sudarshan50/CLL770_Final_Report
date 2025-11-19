  
**![A close-up of a logoDescription automatically generated][image1]**

**CLL770: INTRODUCTION TO MICROFLUIDICS AND MICROFABRICATION**

PROJECT REPORT 

Modelling Sinusoidal Pressure–Driven Micro-Scale Flow in a Water-Based Star Microchannel

| Authors:  Dhruv Upadhyay(2022CH71484) Saket Kumar Singh(2022CH71478) Sudarshan Kumar Oraon(2022CH71511) Yugal Kishore Misra(2022CH71039)   | Acknowledgements: We express our sincere thanks to Professor Mohan Kumar Singh Verma, who through his valuable guidance, unending encouragement, and profound insights, contributed to making this research project a success. Professor Verma’s immense knowledge in the field of microfluidics and microfabrication has been the cornerstone of this study in terms of depth and direction. Without him, this would not have been all possible.  |
| :---- | :---- |

                                         **CLL770 Project – Peer Evaluation Form**  
 

Please evaluate your team members based on their contribution to the group.  
5 \= This team member made unique and irreplaceable contributions to the group.  
 4 \= This team member made important contributions to the group.  
3 \= This team member made a satisfactory contribution to the group.   
2 \= This team member made a sub-par contribution to the group.

1 \= This team member was frequently absent and contributed very little to the group. 

0 \= This team member was completely absent or disruptive to the group.

   
   
   
 

| SNo.  | Team Member Name  | Entry Number | Ranking  |
| :---: | ----- | :---- | :---- |
| 1  | Dhruv Upadhyay  | 2022CH71484  | 5  |
| 2  | Saket Kumar Singh  | 2022CH71478  | 5  |
| 3  | Sudarshan Kumar Oraon  | 2022CH71511  | 5  |
| 4  | Yugal Kishore Misra  | 2022CH71039  | 5  |

 

**Modelling Sinusoidal Pressure–Driven Micro-Scale Flow in a Water-Based Star Microchannel**

Dhruv Upadhyay(2022CH71484), Saket Kumar Singh(2022CH71478), Sudarshan Kumar Oraon(2022CH71511),  
Yugal Kishore Misra(2022CH71039)

**Abstract** :We present a detailed computational study of pulsatile microfluidic flow in a star-shaped 5-inlet converging channel, representing a synthetic venule (microvascular drainage network). The channel geometry is modeled in COMSOL Multiphysics as a three-dimensional laminar flow domain. Each of the five inlets is driven by a sinusoidal pressure boundary condition with equal amplitude but staggered phase shifts, while the outlet is held at reference pressure. Time-dependent incompressible Navier–Stokes equations (Laminar Flow interface) are solved for a water-like fluid. Results show that at any instant the inlet with the highest pressure (“active inlet”) dominates the flow, sending a high-velocity jet toward the common outlet and creating a rotating high-pressure zone around the star geometry over a cycle. Velocity and particle pathlines confirm that fluid from all inlets converges to the outlet, with the jet position sweeping cyclically as the driving phase shifts. Sensitivity analysis indicates that increasing the pressure amplitude scales up the flow magnitude, while increasing the pulsation frequency compresses the oscillation period but leaves the peak velocity unchanged. These findings suggest that multi-inlet pulsatile driving can produce nearly continuous net outflow while reconfiguring internal flow paths, a situation analogous to the phased flushing of capillaries into a venule.

**Introduction:-**

Microfluidic systems operate in the low-Reynolds-number (laminar) regime, where mixing and flow control must often be achieved by clever design of time-varying inputs or geometry. Biological microcirculation involves inherently pulsatile pressure waves (from the heartbeat) propagating through capillary networks. However, many lab-on-a-chip models use steady or simple time-invariant flows[\[1\]](https://pubmed.ncbi.nlm.nih.gov/39246195/#:~:text=drug%20discovery%2C%20neuroscience%2C%20and%20so,actuation%20based%20on%20electric%2C%20magnetic). Understanding pulsatile microscale flows is important for drug delivery, nutrient transport, and disease modeling at the capillary level. In the microvasculature, multiple capillaries converge into draining venules; yet organ-on-chip (OoC) studies often focus on the arterial/feeding side and overlook the pulsatile outflow dynamics on the drainage side[\[2\]](file://file_00000000b8f471fa8dff1f2cc1607cf0#:~:text=Gaps%20in%20existing%20work%3A%20Most,to%3A%20Drug%20delivery%20effectiveness%20Venular). We therefore construct a synthetic venule model: a star-shaped microchannel with five small inlet branches merging into one larger outlet. By applying phase-shifted sinusoidal pressure waves at the inlets, we emulate how asynchronous pulsations in a capillary network can interact downstream. ![][image2]

Numerical simulation (using COMSOL Multiphysics) allows detailed visualization of the unsteady flow fields, which would be difficult to measure experimentally at this scale. Time-dependent finite-element analysis solves the incompressible Navier–Stokes equations in 3D, generating velocity and pressure fields throughout the cycle. We examine how the instantaneous dominant inlet changes over time, how the flow jets reorient, and how the pressure distributes across the network. Sensitivity to driving amplitude and frequency is also assessed. Finally, we interpret these results in the context of microvascular drainage (e.g. nearly continuous venular flow despite upstream pulsatility) and microfluidic mixing (e.g. dynamic sweeping of interfaces, as in Glasgow & Aubry[\[3\]](https://pubs.rsc.org/en/content/articlelanding/2003/lc/b302569a#:~:text=unchanged%20but%20the%20instantaneous%20flow,as%20the%20outflow%20is%20constant)).

This report follows a standard structure. Surveys of relevant literature on pulsatile microflows, multi-inlet mixing, and Organ on a Chip vascular model. Explanation of the motivations for studying pulsatile drainage in synthetic venule geometry. Details of the COMSOL modeling methodology, including geometry setup, governing equations, and boundary conditions. Lastly, presents results (velocity/pressure snapshots, time traces, path lines) with figure captions and discusses the implications, model strengths, and simplifications (e.g. water vs blood, rigid walls) and proposes future extensions (non-Newtonian rheology, porous/elastic walls, coupled transport). Finally,  concludes with key findings and potential applications.

**Motivation**  
Our motivation is two-fold: (1) modeling microvascular drainage and (2) microfluidic mixing and OoC outflow behavior. (1) In vivo, capillary beds drain into venules; many small vessels funnel fluid into a single downstream channel. While the inflow (arterial) side often garnishes attention, the pulsatile dynamics on the venous side matter for overall circulation and tissue perfusion. By constructing a synthetic *venule* analog—a star-shaped microchannel where five small branches merge—we can study how pulsatile inputs interact and produce net outflow. Notably, if the inlets are phased, their oscillations can partially cancel, leading to a smoother net flow (a form of physiological “flow moderation”).  
(2) In microfluidic applications like mixing, reactors, or organ-on-chip platforms, understanding unsteady convergence flows is important. Laboratory studies often use steady flows to simplify, but real physiological flows are pulsatile. For example, a tissue-on-chip device might require simulating not only perfusion into the tissue but also drainage out. Multiple waste/effluent streams can converge, and time-varying actuation could improve mixing or transport of metabolites. Existing COMSOL examples demonstrate pulsatile flows in simple geometries, but seldom do they tackle a network with multiple converging pulsatile sources[\[5\]](file://file_00000000b8f471fa8dff1f2cc1607cf0#:~:text=pulsatile%20nature%20of%20real%20blood,to%3A%20Drug%20delivery%20effectiveness%20Venular)[\[6\]](https://www.comsol.com/model/download/938231/models.mfl.star_chip.pdf#:~:text=4%20%7C%20STAR,velocity%20is%20zero%20in%20the). 

**![There are three major types of blood vessels, namely the veins, arteries  and the capillaries.][image3]**

This gap is significant: asynchronous pulses from different inlets could lead to novel flow patterns (e.g. rotating jets) that neither steady nor single-inlet pulsatile models capture.  
Thus, by modeling a five-inlet pulsatile microchannel, we aim to show (a) how inlet dominance rotates over time, (b) how pressure gradients set up, (c) how the combined outflow behaves, and (d) how system response scales with pumping parameters. These insights can inform the design of microfluidic OOC drainage networks and microreactors. As a bonus, the model provides an easily replicable demonstration of pulsatile multi-inlet flows for educational purposes (e.g. illustrating phase-shift effects).

**Methodology**

Geometry and Mesh. The computational domain is a 3D star-shaped channel: five inlet branches symmetrically arranged around a central outlet. Each inlet branch is a straight rectangular cross-section channel (width and height on the order of tens to hundreds of micrometers, to mimic capillary scale). They converge into a common main channel leading to a single outlet. This geometry is similar to the COMSOL “Star-Shaped Microchannel” example[\[6\]](https://www.comsol.com/model/download/938231/models.mfl.star_chip.pdf#:~:text=4%20%7C%20STAR,velocity%20is%20zero%20in%20the)[\[7\]](https://www.comsol.com/model/download/938231/models.mfl.star_chip.pdf#:~:text=Figure%202%20shows%20the%20velocity,through%20the%20middle%20of%20the), though here dimensions were chosen to represent capillary-scale flow. Walls are rigid with no-slip boundary conditions. ![A drawing of a symbolAI-generated content may be incorrect.][image4]  
A finite-element mesh was generated using an extruded prism mesh (high aspect ratio elements along the channel length) to capture velocity gradients. Mesh refinement near the inlet junctions ensured accurate resolution of flow features. A time-dependent solver was used with sufficiently small-time steps (on the order of milliseconds) to resolve the sinusoidal flow cycle.  
Physics and Equations. We solved the incompressible Navier–Stokes equations (continuity and momentum conservation) for laminar flow. The working fluid is water at room temperature (density **ρ ≈ 1000 kg/m³**, dynamic viscosity **μ ≈ 1×10^–3** **Pa·s).** This choice simplifies the model (Newtonian, no particles or cells) while providing a baseline for hydrodynamic behavior. Blood’s non-Newtonian effects are not included but could be added in extensions. Gravity is neglected due to the small scale. The governing equations are:  
The governing equations, derived for single-phase, incompressible flow, are expressed in tensor notation are as follows: \-   
![A math equation with black text][image5]

**Boundary Conditions**

To accurately model pulsatile, micro-scale flow within the star-shaped microchannel, appropriate physical boundary conditions were applied in COMSOL Multiphysics:

**4.1 Wall Boundary Conditions**

All channel surfaces were assigned the **no-slip boundary condition**, ensuring that the fluid velocity at the walls is zero. This reflects realistic microfluidic and capillary flow, where viscous forces dominate due to low Reynolds numbers.

**4.2 Inlet Boundary Conditions**

Each of the five inlets was subjected to a **time-dependent sinusoidal pressure waveform** of the form:  
Pi(t)=P0+Asin⁡(ωt+i)

P0– baseline pressure  
**A** – pressure amplitude  
– angular frequency of pulsation  
i– phase shift associated with inlet *i*

The inlets were phase-shifted relative to one another, creating a rotating dominance pattern across inlet channels. This mimics the pulsatile nature of biological microvasculature, where pressure waves propagate with time lags due to the heart cycle.

**4.3 Outlet Boundary Conditions**

The single outlet was maintained at a **constant (reference) pressure**, enabling fluid to drain freely from the microchannel.  
A smooth pressure gradient from the inlets toward the outlet ensures physically realistic unidirectional flow.

**4.4 Initial Conditions**

The simulation was initialised using a **quiescent fluid** condition (zero initial velocity).  
This allows the sinusoidal pressure wave to fully drive the subsequent transient flow field.  
---

**5\. Results**  
The results highlight the dynamic and spatially varying flow behaviour arising from the sinusoidal pressure inputs.

**5.1 Time-Dependent Velocity Field**  
The velocity contours show that the inlet with the instantaneous highest pressure produces the strongest jet toward the outlet. ![A diagram of a graphAI-generated content may be incorrect.][image6]  
High-velocity zones (red) sweep across the microchannel depending on which inlet is dominant at a given time.  
Low-velocity regions (blue) shift, accordingly, demonstrating rotating jet patterns.  
This behaviour confirms the ability of phase-shifted pulsatile inlets to reorganize flow paths dynamically within microfluidic networks.

**5.2 Time-Dependent Pressure Distribution**  
The pressure field reveals a rotating high-pressure zone around the star geometry.  
At each time step, the inlet with the highest sinusoidal peak appears as a local red region.   
![A diagram of a graphAI-generated content may be incorrect.][image7]  
Pressure smoothly decays along the central outlet channel, validating expected flow direction.  
This demonstrates how sinusoidal, out-of-phase pressure waves propagate through micro-scale geometries.

**5.3 Pressure vs Time**  
The plot for all inlets shows correctly phase-shifted sinusoidal waveforms.  
Their staggered peaks confirm correct implementation of time-dependent boundary conditions and illustrate how inlet dominance shifts periodically. ![A graph of different colored linesAI-generated content may be incorrect.][image8]  
**5.4 Velocity vs Time for Pressure Amplitude Variation**  
Simulations with different amplitudes reveal:  
Higher amplitudes lead to proportionally higher peak velocities.  
The waveform shape (frequency) remains unaffected. ![A graph of a functionAI-generated content may be incorrect.][image9]  
Thus, **flow strength scales linearly with inlet pressure amplitude**.

**5.5 Velocity vs Time for Frequency Variation**  
Increasing the pulsation frequency results in:  
Compression of the velocity oscillation period.  
Peak velocity magnitudes remain unchanged.  
This indicates that **frequency controls oscillation timing, not intensity**, an important insight for modelling physiological flows.  
**![A graph of a functionAI-generated content may be incorrect.][image10]**  
**5.6 Particle Trajectories and Velocity Magnitude**  
Particle tracing illustrates that: ![A drawing of a structureAI-generated content may be incorrect.][image11]  
Particles from all inlets rapidly converge into a central high-velocity jet toward the outlet.  
Cross-sectional slices show smooth transition from several inlet streams to a unified outlet stream.  
This demonstrates the microchannel’s ability to mix and focus multiple inflows into a single coherent outflow.  
---

**6\. Conclusion**  
The COMSOL-based simulation successfully captures the essential physics of sinusoidal, pulsatile flow in a micro-scale star-shaped channel. Key conclusions include:

**Dynamic Flow Reorganization:**  
The rotating dominance of inlets due to phase-shifted sinusoidal pressures results in time-dependent redistribution of velocity profiles.

**Clear Physical Trends:**  
Pressure amplitude controls **flow magnitude**.  
Pulsation frequency controls **temporal behaviour** without affecting peak velocities.

**Microvascular Analogy:**  
The model effectively represents venule-like behaviour in biological systems, making it relevant for:

1. Organ-on-chip outflow modelling  
2. Microreactor mixing  
3. Drug-delivery and drainage studies at micro-scales

**Scope for Advancement:**  
Future improvements may include:

1. Wall compliance and endothelial layers  
2. Coupled drug transport and reaction kinetics  
3. Larger networks (1→N, N→N) for full microvascular mimicry  
4. Overall, the model serves as a robust foundation for understanding pulsatile microfluidic hydrodynamics.  
5. No-slip conditions are imposed on velocities on walls, besides, for the inlet, the system’s volume flow rate is given.

**References:**

1. COMSOL AB. *Star-Shaped Microchannel (Application ID 480\)*. Available at: [https://www.comsol.com/model/star-shaped-microchannel-480](https://www.comsol.com/model/star-shaped-microchannel-480?utm_source=chatgpt.com)

2. COMSOL AB. *Setting Up the Laminar Flow Interface – COMSOL Multiphysics®.* Available at: [https://www.comsol.com/support/learning-center/article/setting-up-the-laminar-flow-interface-102592/302](https://www.comsol.com/support/learning-center/article/setting-up-the-laminar-flow-interface-102592/302?utm_source=chatgpt.com) [COMSOL](https://www.comsol.com/support/learning-center/article/setting-up-the-laminar-flow-interface-102592/302?utm_source=chatgpt.com)

3. COMSOL AB. *Introduction to the Microfluidics Module.* (Version 5.4) PDF. Available at: [https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/IntroductionToMicrofluidicsModule.pdf](https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/IntroductionToMicrofluidicsModule.pdf?utm_source=chatgpt.com) [COMSOL Documentation](https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/IntroductionToMicrofluidicsModule.pdf?utm_source=chatgpt.com)

4. COMSOL AB. *The Microfluidics Module User’s Guide.* PDF. Available at: [https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/MicrofluidicsModuleUsersGuide.pdf](https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/MicrofluidicsModuleUsersGuide.pdf?utm_source=chatgpt.com) [COMSOL Documentation](https://doc.comsol.com/5.4/doc/com.comsol.help.mfl/MicrofluidicsModuleUsersGuide.pdf?utm_source=chatgpt.com)

5. COMSOL AB. *Intro to Modeling Microfluidic Devices in COMSOL Multiphysics® (Video).* Available at: [https://www.comsol.com/video/intro-to-modeling-microfluidic-devices-in-comsol-mph](https://www.comsol.com/video/intro-to-modeling-microfluidic-devices-in-comsol-mph?utm_source=chatgpt.com) [COMSOL](https://www.comsol.com/video/intro-to-modeling-microfluidic-devices-in-comsol-mph?utm_source=chatgpt.com)

6. (Tutorial for microchannel mixing) BME306 Purdue. *“Microchannel Mixing in COMSOL” (YouTube video).* Available at: [https://www.youtube.com/watch?v=TTJjVm3yfmw](https://www.youtube.com/watch?v=TTJjVm3yfmw&utm_source=chatgpt.com) [YouTube](https://www.youtube.com/watch?v=TTJjVm3yfmw&utm_source=chatgpt.com)

   
