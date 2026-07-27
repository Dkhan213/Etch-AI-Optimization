Phase 1: Load the Baseline
Open the Process Step Editor: Navigate to the recipe editor screen in the Oxford control software.

Select the Recipe: In the Step Name box at the top, load the LVC-Si Etch template.

Note: Because you have the "User" role, you cannot permanently save changes to this file. You will just edit the numbers on the screen and hit run.

Phase 2: Input Your AI's Recipe (The 4 Changing Variables)
Pull up the output from your Python script for this specific run, and change these four boxes:

APC CONTROLLER (Middle Left):

Set Pressure: Enter the AI's pressure (between 10.0 and 30.0).

Ensure the yellow light next to Pressure is on (not Position).

PROCESS GAS OUT (Center Right):

SF6 (Gas 1): Enter the AI's SF6 flow (between 20.0 and 40.0).

OXYGEN (Gas 4): Enter the AI's O2 flow (between 5.0 and 15.0).

RF GENERATOR (Bottom Left):

Forward Power: Enter the AI's RF Substrate Bias (between 25 and 60).

Phase 3: The Visual Safety Check (The "Do Not Touch" List)
Before you hit run, do a quick visual sweep of the screen to ensure the baseline loaded correctly and nobody messed with the static parameters.

Verify Time & Interlocks (Top Left):

STEP TIME: 00 02 00 (Exactly 120 seconds).

LOG INTERVAL: 00 00 05.

PUMP TO PRESSURE: 7.50e-09.

Verify Cooling (Top Center):

Pressure Controller (Helium): 10.0 Torr.

CRYO: 50.

CHILLER / Table Temperature: Leave at whatever default loads (typically 0, 20, or 25).

Verify Inactive Gases (Center Right):

ARGON (Gas 3): 15.0.

C4F8, CHF3, BCl3, CL2: All must be 0.0.

Verify Generators (Bottom):

ICP GENERATOR Forward Power: 800 (or your agreed-upon 500W-800W lab baseline).

RF AUTOMATCH & ICP AUTOMATCH: Both set to AUTO (yellow light on).

LF GENERATOR & PULSE PARAMETERS: All set to 0.

Phase 4: Execution
Once your 4 AI variables are typed in and the visual check is clear, click the green OK / RUN button at the top right of the editor.
