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

