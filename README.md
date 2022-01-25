# Fear-Conditioning-Experiment-Designer
GUI-guided, Arduino based MATLAB app to interface with fear conditioning experiment hardware, design and run experiments

The Fear Conditioning Experiment Designer app allows users to create and execute a fear conditioning experiment with various auditory and visual stimuli, paired with or without shock or laser delivery. All of this is controlled within a MATLAB app that communicates with an Arduino to send TTL pulses to appropriate hardware.

The local timestamps (based on the computer clock) for all events (CS+, CS-, laser, and shock on and off) times are saved in a 'ts' (timestamp) struct. The save file is titled [Animal ID]_[YYYY-MM-DD-HH-SS].mat
# INITIAL SETUP
1.	Connect the Arduino to the computer, download and install the MATLAB Arduino Support package, and connect the Arduino to MATLAB using the guided prompts. 
2.	Note what port the Arduino is connected to. Commonly, this is COM4
3.	Configure the pins from the Arduino to the hardware. By default, the digital pins are programmed for:
- D3 = tone 
  - Note: the tone is produced by creating a custom sine wave within MATLAB and using the play MATLAB function to produce the tone, therefore no external hardware is required to make tones (beyond computer speakers). This pin is intended for an optional hardware pairing to be coincident with the tone (eg a houselight or visual confirmation of tone for recordings)
-	D4 = shocker
-	D6 = laser controller
  - Note:  this is a simple TTL trigger to turn on and off a designated controller for the laser. For constant light, this will work with TTL-compatible lasers. To shape the light pulse, you can use another Arduino running a custom script which then controls the laser.
-	D7 = light
-	D13 = Miniscope 
-	Ensure that the circuit is properly grounded.
-	Note that any of these outputs can be repurposed to trigger any TTL-controllable device.

4.	Install the Fear-Conditioning-Experiment-Designer (FCXD) app by running the .mlapp file. This will install the app under the ‘app’ tab in MATLAB.

# USAGE
## ENTERING EXPERIMENT PARAMETERS
1.	Open FCXD. You will be prompted to choose which COM port the Arduino is connected to. Select the appropriate port. The app then connects to the Arduino, which takes a few seconds. During this time, do not edit the app. Wait until the Arduino is successfully connected.
-	This only applies at launch, not at re-launching of experiments.
2.	Enter the experiment ID.
3.	Select which CS+ and CS- to use
-	Note: both CS+ and CS- can be paired with a shock. The naming is arbitrary.
-	Users can choose between pure tones, frequency modulated (FM) sweeps, pure light, or light pulses.
4.	Enter in experiment parameters.
-	Note: The laser and CS share a duration, such when CS and laser are paired, they are co-incident.
5.	Baseline period is a period after experiment launch, at which point the first event will occur.
6.	Inter-CS interval min and max indicate the range to randomly choose an inter-CS interval from (randomly chosen between each CS).
7.	Choose shock duration to co-terminate with the CS.
8.	Edit CS parameters
-	Select the tab to match the CS+ and CS- of choice (eg if CS+ is 'FM Sweep1' and CS- is 'Tone1', make changes to the 'FM1' tab and 'Tone1' tabs)
-	If using a pure tone, enter in the frequency
-	If using an FM sweep, enter the start and end frequencies, and sweep duration
-	Note: sweep duration must be evenly divisible by the CS duration. Eg for a CS duration of 30 seconds, a sweep duration of 1, 2, 3, and 5 seconds are all valid, but 4 seconds is invalid
9.	If using a pulsed light, enter the pulse frequency and duty cycle
-	A duty cycle of 0.5 indicates half on then half off. A duty cycle of 0.25 indicates 25% on, then 75% off.

## DESIGNING EXPERIMENT
1.	Once CS and their parameters have been selected, build the experiment by adding individual events in the order they'll be presented. Events consist of either a CS+, CS-, laser, or shock, in any combination.
-	 Choose whether to use the CS+, CS-, or no CS
-	Choose whether to pair with a co-terminating shock or not
-	Choose whether to pair with a co-incident laser or not
-	 If no CS is used, shock and laser will still use the same time line as if CS were present. Eg if CS duration is 30 seconds, and shock duration is 2 seconds, and an event is built with No CS, Yes shock, and Yes laser, the event will have laser on for 30 seconds, with the last 2 seconds having the shock on.
2.	 Once event details are selected, click 'Add Event' to add the event to the experiment timeline.
3.	 Repeat until experiment is designed.
-	 If you want to restart the design, click 'Clear all events' to remove all events from the timeline.
-	 Note: the experiment settings (CS dur, inter-CS interval, etc) apply to all events. Eg if you build 5 events with a CS duration of 30 seconds, then change CS duration to 60 seconds and add another event, the CS duration for all 6 events will be 60 seconds.
4.	 Click 'Launch Experiment' to begin the experiment. 
-	 Progress will be updated in the command window, including experiment completion.
-	If desired, the same experiment can be re-run by clicking 'Launch Experiment' again (usually changing the experiment name is desired).
5.	Results will be saved in a .mat file at the conclusion of the experiment.
-	Note:  results are only saved at the end of the experiment. If the experiment is interrupted or halted, no data will be saved.

## SAVED DATA
The data is saved as  [Animal ID]_[YYYY-MM-DD-HH-SS].mat
1.	The file contains the experimental parameters, including the order and timing of the events
2.	Events are saved in ‘xd’, which is a 4xN matrix, with rows representing the possible event (CS+, CS-, shock, laser) and columns representing the order of events. The identity of the events corresponds to to the names in ‘xd_labels’.
3.	Timestamps for all events are saved in the ‘ts’ struct. Within ts are separate variables denoting the start and stop times for all events. These are  stored in matrices of Nx6, with N number of events, and the six columns indicating [year][month][day][hour][minute][seconds]. 
