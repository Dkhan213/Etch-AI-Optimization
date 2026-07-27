Compiled Data
If we are talking about the absolute hardware capabilities of the Oxford Instruments Plasmalab System 100 ICP 180, it is a highly capable tool that goes much higher than my previous constraints.
Here is the breakdown of the true hardware limits versus the practical AI search limits you should code into your optimization loop to avoid destroying your sample:
1. ICP Power (Source)
True Hardware Limit: The ICP generator can output up to 3000 W. Certain facility configurations cap this at a 600 W generator, but 3000 W is the system's upper design limit.
AI Search Limit (500.0 W – 1000.0 W): While the hardware handles 2000 W+, giving an AI free rein to push beyond 1000 W during an automated scoping run can severely overheat the chamber and the substrate.
2. RF Power (Substrate Bias)
True Hardware Limit: The substrate electrode RF power typically ranges from 5 W up to 300 W or 400 W, with some systems equipped to handle 600 W.
AI Search Limit (15.0 W – 60.0 W): If your pieces are mounted to the carrier wafer with adhesives like Crystalbond or Santovac, pushing the bias over 100 W will melt the thermal grease. This causes the sample to shift mid-etch and will quickly burn standard photoresist masks.
3. Chamber Pressure
True Hardware Limit: The system handles process pressures from 1 mTorr up to 60 mTorr or 100 mTorr.
AI Search Limit (10.0 mTorr – 40.0 mTorr): A plasma may not strike easily for low power levels. Conversely, letting the AI test pressures up to 100 mTorr will result in massive chemical undercutting of your silicon profile.
4. Gas Flows (SF6 / Ar / O2)
True Hardware Limit: Total gas flows can safely operate between 10 sccm and 200 sccm. Individual Mass Flow Controllers for gases like Argon and Oxygen are often rated for up to 100 sccm.
AI Search Limit (15.0 sccm – 45.0 sccm): Allowing the AI to max out the O2 controller at 100 sccm would cause complete passivation, entirely stopping the silicon etch. Maxing out the Argon controller would cause destructive physical sputtering.
By capping your search space parameters in Python to the practical thermal limits rather than the hardware ceilings, you ensure your active learning model generates recipes that are aggressive enough to map the boundaries, but safe enough to keep your sample intact.

—-------------------------------------------------------------------------------------

Because you cannot run hundreds of continuous iterations to let the AI randomly explore, you have a strict "budget" of physical runs. With your cleanroom training wrapping up this late in July, the window to execute these cycles is tight. If you give the AI five variables to play with, it will require 40+ runs just to map the basic trends. Team Etch-a-Sketch simply does not have the time or the wafer supply for that.
To force the AI to produce meaningful gradients within a highly limited number of runs, you must aggressively reduce the dimensionality of the search space.
Here is the strategy to constrain the AI so it learns efficiently without destroying your photoresist.
The "3-Variable" Constraint Strategy
Freeze the variables that merely "assist" the etch, and only allow the AI to control the three parameters that fundamentally dictate the physical-chemical balance.
1. The Frozen Variables (Set these in the Oxford software and do not change them):
Time: 120 seconds (Guarantees measurable depth, prevents thermal mask burning).
Argon Flow: 15.0 sccm (Keeps physical clearing constant).
ICP Power (Source): 800.0 W (Provides a dense, stable plasma cloud without maxing out the hardware).
2. The AI Search Space (Program these into your Python script):
By limiting the AI to just these three variables, a Bayesian model can begin predicting the response surface after only 4 or 5 initial runs.
Parameter
Narrowed AI Bound
Why this gives signal without destruction
RF Power
25.0 W – 60.0 W
25W is high enough to ensure the Argon ions actually punch through the passivation. 60W is low enough that your thermal grease won't boil and shift the sample. The AI will clearly see the correlation between this power bump and vertical etch rate.
SF_6 Flow
20.0 sccm – 40.0 sccm
Constraining this prevents the AI from trying a 100% chemical etch (which destroyed your last mask) or starving the plasma. This window guarantees etching occurs, but forces the AI to find the efficiency peak.
O_2 Flow
5.0 sccm – 15.0 sccm
5 sccm guarantees some sidewall protection. 15 sccm prevents the AI from causing "etch stop" (where too much oxygen totally seals the silicon). This narrow 10 sccm window is highly sensitive; the AI will see massive roughness and rate changes here.

How This Plays Out in the Lab
With this setup, the AI cannot physically suggest a recipe that will obliterate your sample.
If the AI suggests the "maximum" of this space (60W, 40 sccm SF_6, 5 sccm O_2), you will get a very fast, slightly rough etch.
If it suggests the "minimum" (25W, 20 sccm SF_6, 15 sccm O_2), you will get a slow, highly protected, very smooth etch.
Every single recipe generated within these bounds will yield a measurable trench depth and a measurable surface roughness for your profilometer. No destroyed wafers, no wasted runs, and 100% usable data for the model.
—--------------------------------------------------------------------------------------

This configuration establishes your frozen parameters (which you will never touch again) and sets your three AI variables to the exact center of your safe search space.
1. Top Left: Time & Pressure
STEP TIME: Set to 00 | 02 | 00 (This is your fixed 120-second limit).
APC CONTROLLER (Set Pressure): Type 20.0 (This keeps the chamber vacuum stable).
2. Center: Thermal Management
HELIUM BACKING (Pressure Controller): Leave at 10.0 (This is critical; it keeps your sample from burning).
CHILLER (Table Temperature): Leave at 0 or whatever the lab's default standby temperature is (usually 0°C to 20°C). Do not change this.
3. Bottom Row: Power Generators
RF GENERATOR (Bottom Left > Forward Power): Type 42.5 (This is AI Variable 1: Substrate Bias).
ICP GENERATOR (Bottom Center > Forward Power): Type 800 (This is your frozen plasma density).
4. Top Right: Process Gas Out
SF6 (Gas 1): Type 30.0 (This is AI Variable 2: Chemical Etchant).
C4F8 (Gas 2): 0.0
ARGON (Gas 3): Type 15.0 (This is your frozen physical hammer).
OXYGEN (Gas 4): Type 10.0 (This is AI Variable 3: Sidewall Passivation).
CHF3, BCL3, CL2: 0.0
—------------------------------------------------------------------------------------------------------------
