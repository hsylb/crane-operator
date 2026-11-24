# Crane Operator

## App overview

Build a self-contained, single-file tower-crane educational game for beginning engineering and construction management students. Players move one crane to carry fixed 4,000 lb, 8,000 lb, and 12,000 lb loads from the 50 ft staging point to delivery radii of 125 ft, 99 ft, and 74 ft, respectively. The game uses the assignment's conservative stepwise capacity chart, 1 ft controls at 10 ft/s, a 25 ft loaded-travel clearance rule, a 120-second timer, utilization warnings/tip-over, delivery scoring, and keyboard/touchscreen controls. It will be published as a static GitHub Pages `index.html`; no realistic crane safety tool, backend, or external runtime dependencies are intended.

## Purpose

Create a single-page interactive tower crane game that helps beginning engineering and construction management students understand how a crane's rated load capacity changes with operating radius.

The student moves three predetermined construction loads of 4,000, 8,000, and 12,000 lb from a staging area to a delivery area using the supplied load chart. The student monitors load utilization, recognizes the warning at 90% through 100% utilization, avoids exceeding 100% utilization, and delivers as many loads as possible within 120 seconds.

After using the app, students should be able to:

- Find the allowable capacity at a given radius using the supplied load chart.
- Calculate load utilization from the suspended load and allowable capacity.
- Explain how operating radius affects a crane's rated load capacity.
- Use utilization warnings and the 100% limit to make a lift decision in the simulation.

This application is a simplified educational simulation, not a tool for determining whether an actual crane lift is safe.

## Inputs

| Input | Units and allowable range | Default | Control |
|---|---|---|---|
| Trolley radius | 50–150 ft, in 1 ft increments | 50 ft, at the staging location | Left/right arrow keys or matching on-screen direction buttons |
| Hook height | 0–30 ft above ground, in 1 ft increments | 0 ft, at pickup height | Up/down arrow keys or matching on-screen direction buttons |
| Construction load | Fixed loads of 4,000, 8,000, and 12,000 lb, in that order | 4,000 lb is first at the staging location | Spacebar or on-screen pick-up/release button |
| Game timer | Fixed 120 seconds, counting down to 0; not adjustable | 120 seconds, stopped until Start | Start button begins the countdown |
| Game state | Ready, running, or ended | Ready | Start and Restart buttons; matching on-screen buttons are available on touchscreens |

Pressing a direction control moves the trolley or hook by 1 ft. Holding it continues movement at 10 ft per second in 1 ft increments. Movement stops when the control is released or a range limit is reached. The trolley cannot move below 50 ft or beyond 150 ft; the hook cannot move below 0 ft or above 30 ft. Opposing directions pressed at the same time cancel movement along that axis.

The initial ready state has the trolley at 50 ft, the hook at 0 ft, the 4,000 lb load waiting at staging, and the 120-second timer stopped. Pressing Start begins the countdown. Restart immediately restores this ready state, including the initial positions, load order, and timer. Restart does not require confirmation.

The staging location is at radius 50 ft and hook height 0 ft. The pickup tolerance is ±1 ft horizontally and vertically, intersected with the allowed ranges; therefore, the accepted pickup range is radius 50–51 ft and hook height 0–1 ft.

The delivery locations and accepted ranges are:

| Load | Delivery radius | Accepted radius (±1 ft) | Hook delivery height | Accepted hook height (±1 ft) |
|---:|---:|---:|---:|---:|
| 4,000 lb | 125 ft | 124–126 ft | 20 ft | 19–21 ft |
| 8,000 lb | 99 ft | 98–100 ft | 20 ft | 19–21 ft |
| 12,000 lb | 74 ft | 73–75 ft | 20 ft | 19–21 ft |

Use the spacebar or on-screen pick-up/release button to pick up the next load when the hook is within the staging tolerance, or to deliver a carried load when the trolley and hook are within that load's delivery tolerances. Only one load can be carried at a time. While carrying a load, the hook must be raised to at least 25 ft before horizontal trolley movement is allowed. At an invalid pickup or delivery position, the load remains in place and the app displays a brief instruction.

During play, the arrow keys and spacebar control the game rather than scrolling the page. Movement and pick-up/release controls are inactive before Start and after the game ends; Restart remains available.

## Calculations

### Variables and units

- \(R\): operating radius, the horizontal distance from the crane's centerline to the hook, in feet (ft). Valid values are 50–150 ft in 1 ft increments.
- \(h\): hook height above ground, in feet (ft). Valid values are 0–30 ft in 1 ft increments.
- \(W\): suspended load, in pounds (lb). \(W=0\) when no load is suspended; otherwise, it is the weight of the single suspended load.
- \(C(R)\): allowable load capacity at radius \(R\), in pounds (lb).
- \(U\): load utilization, in percent (%).
- \(t\): remaining game time, in seconds (s), from 120 s to 0 s.
- \(S\): score, in points, from 0 to 300.

### Allowable capacity

Use the following crane load chart provided in the assignment:

| Radius (ft) | Capacity (lb) |
|---:|---:|
| 50 | 20,000 |
| 75 | 13,000 |
| 100 | 9,000 |
| 125 | 7,000 |
| 150 | 5,500 |

For a radius between listed values, use the capacity at the next larger listed radius. Do not interpolate between table values. At an exact table radius, use the capacity listed for that radius.

| Operating radius \(R\) | Allowable capacity \(C(R)\) |
|---|---:|
| \(R=50\) ft | 20,000 lb |
| \(50<R\le75\) ft | 13,000 lb |
| \(75<R\le100\) ft | 9,000 lb |
| \(100<R\le125\) ft | 7,000 lb |
| \(125<R\le150\) ft | 5,500 lb |

The displayed load chart must show these stepwise capacity ranges using the same lookup rule. It must not imply linear interpolation between table values. A radius outside 50–150 ft is invalid and must not be used for a capacity lookup.

### Load utilization and overload behavior

Calculate load utilization as:

\[
U=\frac{W}{C(R)}\times100
\]

When no load is suspended, \(W=0\), so \(U=0\%\). The capacity remains determined by the current radius; no warning or tip-over occurs.

For a suspended load:

- \(U<90\%\): normal operation.
- \(90\%\le U\le100\%\): display a warning; the crane does not tip over.
- \(U>100\%\): the crane tips over and the game ends immediately.

Compare the unrounded utilization to the thresholds. Round only the displayed percentage to two decimal places. Recalculate utilization immediately after pickup, successful delivery, restart, and every 1 ft radius movement. If a movement step causes utilization to exceed 100%, tip-over occurs on that step.

### Movement and load handling

- A direction press moves the trolley or hook by 1 ft. While held, it continues at 10 ft per second in 1 ft increments (one increment every 0.1 s).
- Movement stops at the 50–150 ft trolley-radius limits and 0–30 ft hook-height limits. Movement past a limit has no effect; it does not wrap.
- Opposing directions on the same axis cancel. Horizontal and vertical movement may occur simultaneously. When both axes move, update the vertical position first, then check the clearance rule before applying horizontal movement.
- While carrying a load, horizontal movement is allowed only when \(h\ge25\) ft. A blocked horizontal step has no effect; vertical movement remains available.
- Stop held movement when its key or touchscreen control is released, canceled, or the browser window loses focus.
- A spacebar press or touchscreen pick-up/release activation triggers only one action until released; holding it does not repeat the action.

A load may be picked up only when no load is suspended, a load remains in the fixed sequence, and the hook is within the staging tolerance: radius 50–51 ft and height 0–1 ft. A load may be delivered only at its matching destination and within that destination's horizontal and vertical tolerances:

| Load | Accepted radius (ft) | Accepted hook height (ft) | Capacity throughout accepted radius |
|---:|---:|---:|---:|
| 4,000 lb | 124–126 | 19–21 | At least 5,500 lb |
| 8,000 lb | 98–100 | 19–21 | 9,000 lb |
| 12,000 lb | 73–75 | 19–21 | 13,000 lb |

An invalid pickup or delivery leaves the load, score, and game state unchanged and displays a brief instruction. A successful delivery releases the load and awards points only once for that load.

### Score, timer, and game state

- \(S=0\) at the start of a new game. Add 100 points to \(S\) for each correctly delivered load. The maximum score is 300 points; invalid actions do not change the score.
- The timer starts at 120 s when Start is pressed and counts down continuously while the game is running. It does not pause during a warning.
- Win when all three loads are correctly delivered before \(t\) reaches 0 s. Stop the timer on a win.
- Lose if the timer reaches 0 s before all loads are delivered, or if a tip-over occurs. When the timer reaches 0 s, timeout takes precedence over an action at that exact instant; a delivery must be completed before time expires.
- Restart immediately restores the ready state: \(R=50\) ft, \(h=0\) ft, \(W=0\) lb, \(S=0\) points, and \(t=120\) s with the timer stopped. The 4,000 lb load is next in the sequence. Recalculate utilization on restart.

## Outputs

- An animated 2D side view showing the tower crane, trolley, cable, and hook.
- A construction-site scene showing the staging area, the three loads labeled 4,000 lb, 8,000 lb, and 12,000 lb, a building under construction, and three designated delivery targets.
- A visual highlight and label for the delivery target of the currently suspended load, or the next load in the fixed sequence.
- Current operating radius in feet (ft) and hook height in feet (ft).
- Current suspended load in pounds (lb), or "No load suspended."
- Allowable load capacity in pounds (lb) and load utilization in percent (%), displayed to two decimal places.
- A load chart with labeled axes: operating radius (ft) and load/capacity (lb). Show the conservative stepwise capacity ranges without interpolation. Mark the current radius and allowable capacity; when a load is suspended, also mark its weight at that radius. Label the markers so capacity and suspended load are distinguishable.
- A visible operating status: normal below 90% utilization; a warning from 90% through exactly 100%; and an overload/tip-over state above 100%.
- A warning message that states the condition and utilization. Use text and a visual indicator, such as an icon or shape; do not rely on color alone.
- A tip-over animation and a visible message that the crane exceeded capacity and the game has ended. After tip-over, preserve the final operating radius, suspended load, allowable capacity, and utilization readings, and keep the chart markers at their final values until Restart.
- Remaining time in seconds (s) and current score in points.
- Delivery progress showing how many of the three loads have been delivered, with each load's status identified.
- Clear feedback for invalid actions and successful deliveries during play.
- A prominent results panel when play ends. Use “Time's Up! — Game Over.” for timeout, “Crane Overloaded! — Game Over.” for overload, and “Congratulations! — You Win!” for successful completion. Show the reason play ended, final score in points, completed deliveries out of three, and remaining time in seconds. On overload, also show the final radius, allowable capacity, suspended load, and utilization in the panel. Provide a Play Again button that performs the existing Restart behavior.
- A visible control guide for keyboard and touchscreen controls.
- A visible disclaimer: "Educational simulation only. This assignment load chart is not a real crane chart and must not be used for actual crane operations."
- Important warning and game-state changes available to assistive technology, not only visually.

## Look and feel

Use a clean, professional construction-site design with a yellow tower crane, a gray building under construction, a staging area, and clearly identified delivery targets. Use green, amber, and red status indicators accompanied by text and icons; do not rely on color alone.

On desktop, place the crane simulation beside the load chart and numerical readings. Put the application title, timer, score, and delivery progress in a header. Keep the keyboard control guide near the crane simulation.

Keep the results panel visible as soon as play ends on desktop and mobile, even if the player has scrolled. Place it clear of the crane scene so the tip-over animation remains visible, and use the mobile control dock's space after play ends. Use a clear heading and text to identify the outcome, move keyboard focus to the results for assistive technology, and return focus to Start after Play Again. Keep the results stable with reduced motion enabled.

On mobile, stack the panels vertically without horizontal scrolling. Use a compact header and place a simplified crane scene near the top. Keep the persistent bottom control dock compact and include live operating-radius and hook-height readings, the suspended-load/next-load value, and the active delivery objective with its target letter, radius, and height. Reserve a visible utilization and operating-status/warning area in the dock for Stage 4; label those readings inactive until then. Keep these readings and the directional pad, pick-up/release, Start, and Restart controls visible without scrolling while the player operates the crane. Reserve enough space above the dock that it never covers the crane scene; keep the chart and detailed supporting readings below the operating area.

Reduce mobile scene clutter: identify the three delivery markers by letter (A = 4,000 lb at 125 ft; B = 8,000 lb at 99 ft; C = 12,000 lb at 74 ft), clearly highlight the active marker, and show its full delivery information in the live dock rather than in large overlapping SVG callout boxes. Use a tighter mobile SVG viewBox to crop non-operational scenery and enlarge the crane and operating area, while retaining the entire 50–150 ft operating range, staging point, building, delivery markers, and rulers. Remove duplicate scale annotations and decorative remnants of hidden callouts. Preserve the construction-site illustration and approved geometry.

Keep all scene labels, target callouts, pickup information, and measurement annotations legible and non-overlapping on desktop. Arrange the three delivery callouts in separate clear areas with non-crossing leaders, and keep the pickup label separate from the crane tower, load queue, and pickup load tag. Preserve the simplified A/B/C delivery markers on mobile. When the staging queue has no queued loads, hide its heading or replace it with a clear empty/completion message. After a successful delivery, show the delivered material at that target's delivery location without obscuring any undelivered target.

On mobile, make the detailed instrument readings collapsible and initially collapsed because the essential operating readings are in the persistent dock. Keep the required load chart as a separate panel directly accessible below the instrument panel. On desktop, keep the detailed instrument readings expanded beside the chart.

Use one consistent drawing scale so the trolley position represents operating radius and the hook position represents hook height. In the scene coordinate system, use 2 SVG units per foot, with the crane centerline at \(x=110\) and ground at \(y=366\): \(x=110+2R\) and \(y=366-2h\). Resize or crop the illustration uniformly for different screen sizes without changing this relationship. The cable must remain a single visible path connecting the moving trolley at \(x=110+2R,\ y=155\) to the hook at \(x=110+2R,\ y=352-2h\); update its path geometry whenever the radius or hook height changes. Show exactly one trolley graphic, and ensure it follows the cable. Show a radius measurement guide from the crane centerline to the hook, labeled in feet. Mark operating radii 50, 75, 100, 125, and 150 ft at evenly spaced positions on the scene radius ruler. Keep the active delivery target easy to identify.

Keep the illustrative building roof no higher than 15 ft above ground (\(y\ge336\) in this scene coordinate system). Keep the suspended-load illustration no taller than 6.5 ft (13 SVG units) below the hook. At the 25 ft loaded-clearance height (\(y=316\)), its bottom must remain at or above \(y=329\), at least 3.5 ft above the roof at \(y=336\). Show delivery targets as markers at the approved hook height of 20 ft (\(y=326\)), not as a solid platform that extends the building into the loaded-travel corridor.

Use a vertical hook-height ruler with consistently spaced and aligned labels at 0, 10, 20, and 30 ft (\(y=366\), \(346\), \(326\), and \(306\), respectively). Show the 25 ft loaded-travel clearance at \(y=316\) as a separate, clearly labeled dashed guide, not as another crowded ruler label. Place the ruler title apart from the labels. Keep the ground line and other scene artwork from obscuring the 0 ft label or any ruler markings. Draw the current-radius guide and its numeric label below the ground line, fully visible and separate from the radius ruler labels. End all tower bracing and supports at or above ground level (\(y\le366\)); no structural stroke may visibly extend below ground.

On touchscreens, each directional and action control target must be at least 48 by 48 CSS pixels. At phone widths, keep the crane scene, live radius/height/load/objective readings, reserved utilization/status area, and controls visible together as much as the viewport permits, without the dock obscuring the scene or requiring scrolling to see the current operating state.

When the app first opens, show the crane at its initial position, the 4,000 lb load at the pickup point, and the 8,000 lb and 12,000 lb loads in a small ordered staging-area queue. Label each load so they do not visually overlap. When a load is delivered, move the next available load from the queue to the pickup point and highlight its delivery target. Show a stationary timer at 120 seconds, a score of zero, and delivery progress of 0 out of 3. Display Start prominently; gameplay must not begin until Start is pressed.

Show a visible control guide for keyboard and touchscreen controls and the educational-use disclaimer during gameplay. Use a dramatic tip-over animation when utilization exceeds 100%, while keeping the final status and readings understandable without relying on motion. Respect reduced-motion preferences.

Keep the app in one `index.html` file. Prefer inline vector drawings and CSS over external images, libraries, or other dependencies.

## Validation

### Stage 5 test record

New local validation was run on 2026-11-23 in Google Chrome 158.0.8127.88 (headless Chromium on macOS). The viewport matrix was selected before layout testing. It used 320 × 568, 390 × 844, 430 × 932, and 780 × 844 CSS px with touch emulation, and 781 × 844 and 1440 × 1000 CSS px with desktop input. The 780/781 px pair checks both sides of the mobile breakpoint.

The automated results below are from this Stage 5 run. The user’s later desktop walkthrough is recorded under Manual results. A temporary copy of the HTML exposed the existing calculation functions and allowed clock control for exact-boundary tests. The real-clock win and timeout, touch tests, responsive layout and offline checks used the application file without instrumentation. The test harness stayed outside the project.

Earlier Stage 4 evidence remains in [the prompt log](prompts.md). It includes Codex's automated tests, independent Chromium and source checks reported by the user, and the user's personal approval of the corrected end-of-game feedback. Those records were kept separate from the new Stage 5 results.

### Hand calculations

For each case, \(W=12{,}000\) lb and \(U=(W/C(R))\times100\).

| Radius | Substitution and expected calculation | Expected outcome | Actual in play with 12,000 lb | Status |
|---|---|---|---|---|
| 74 ft | \(C(74)=13{,}000\) lb; \(U=(12{,}000\text{ lb}/13{,}000\text{ lb})\times100=92.3077\ldots\%=92.31\%\) | Warning, no tip-over | 13,000 lb, 92.31%, warning, still running | Pass |
| 75 ft | \(C(75)=13{,}000\) lb; \(U=(12{,}000\text{ lb}/13{,}000\text{ lb})\times100=92.3077\ldots\%=92.31\%\) | Warning, no tip-over | 13,000 lb, 92.31%, warning, still running | Pass |
| 76 ft | \(C(76)=9{,}000\) lb; \(U=(12{,}000\text{ lb}/9{,}000\text{ lb})\times100=133.3333\ldots\%=133.33\%\) | Tip-over ends play | 9,000 lb, 133.33%, overload ended play on the 75 to 76 ft step | Pass |

### Capacity lookup boundaries

Check exact table radii and the next 1 ft radius. Capacity must change according to the stepwise rule, without interpolation.

| Radius input | Expected allowable capacity | Actual | Status |
|---|---:|---|---|
| 50 ft | 20,000 lb | 20,000 lb | Pass |
| 51 ft | 13,000 lb | 13,000 lb | Pass |
| 75 ft | 13,000 lb | 13,000 lb | Pass |
| 76 ft | 9,000 lb | 9,000 lb | Pass |
| 100 ft | 9,000 lb | 9,000 lb | Pass |
| 101 ft | 7,000 lb | 7,000 lb | Pass |
| 125 ft | 7,000 lb | 7,000 lb | Pass |
| 126 ft | 5,500 lb | 5,500 lb | Pass |
| 150 ft | 5,500 lb | 5,500 lb | Pass |
| Move outward at 150 ft | Hold 150 ft without looking up an out-of-range value | Stayed at 150 ft and 5,500 lb | Pass |

The lookup also matched the expected step for all 101 whole-foot radii. Direct calls at 49, 151, and 50.5 ft were rejected.

### No-load and utilization thresholds

Test classification with raw percentages before rounding. The threshold cases used the existing classifier directly and added no game loads.

| Input | Expected display | Expected status | Actual display and classification | Status |
|---|---:|---|---|---|
| No suspended load at 100 ft | 0.00% | Normal operation at 9,000 lb capacity | 0.00%, 9,000 lb, Normal, game continued | Pass |
| 89.99% | 89.99% | Normal | 89.99%, Normal | Pass |
| 89.999% | 90.00% | Normal | 90.00%, Normal | Pass |
| 90.00% | 90.00% | Warning | 90.00%, Warning | Pass |
| 90.01% | 90.01% | Warning | 90.01%, Warning | Pass |
| 99.99% | 99.99% | Warning | 99.99%, Warning | Pass |
| 100.00% | 100.00% | Warning, no tip-over | 100.00%, Warning | Pass |
| 100.001% | 100.00% | Overload | 100.00%, Overload | Pass |
| 100.01% | 100.01% | Overload | 100.01%, Overload | Pass |

The 12,000 lb run confirmed that overload classification ends play in the application.

### Movement, pickup, and delivery

| Test | Expected | Actual | Status and notes |
|---|---|---|---|
| Movement bounds | Radius stays within 50–150 ft and height within 0–30 ft | Attempts past 50, 150, 0, and 30 ft stopped at those values without wrapping | Pass |
| Tap, hold, and release | A tap moves 1 ft, holding repeats at 10 ft/s in 1 ft increments, and release stops motion | A tap moved 1 ft. A 350 ms keyboard or touch hold moved 4 ft including the initial step, then stopped | Pass |
| Cancel, blur, and opposing directions | Touch cancellation and window focus loss stop movement. Opposing directions cancel on that axis | An emulated touch cancel and synthetic window blur stopped their respective holds. Opposing held keys produced no further movement along that axis | Pass |
| Loaded clearance and order of movement | At 24 ft, a horizontal step is blocked. With both axes active, raising to 25 ft permits a horizontal step in that update | Loaded radius stayed at 50 ft at 24 ft. The simultaneous update changed height to 25 ft and radius to 51 ft. Emulated touch below clearance left radius unchanged | Pass |
| Pickup tolerance | The 4,000 lb load can be picked up at any corner of 50–51 ft radius and 0–1 ft height | All four accepted corners picked up 4,000 lb without changing score | Pass |
| Invalid pickup or extra load | A pickup outside either tolerance, while carrying, or after the final delivery cannot take another load | At (52, 0) and (50, 2), no load attached and pickup instructions appeared. Using the control while carrying at staging kept the original load attached. After the win, the control left the 300-point result and empty hook unchanged | Pass |
| Delivery tolerances | For 4,000, 8,000, and 12,000 lb respectively, accept 124–126, 98–100, and 73–75 ft at 19–21 ft height. All accepted radii remain within capacity | Lower endpoints at 124/98/73 ft and 19 ft, upper endpoints at 126/100/75 ft and 21 ft, and all centers delivered successfully. The lowest capacities across the respective ranges were 5,500, 9,000, and 13,000 lb | Pass |
| Invalid delivery | A wrong target or position outside the radius or height tolerance leaves the load suspended and score unchanged | With 4,000 lb, radius 123 or 127 ft at 20 ft, height 18 or 22 ft at 125 ft, and target B at 99 ft and 20 ft were rejected with delivery instructions and score 0 | Pass |

### Game flow, scoring, and controls

| Test | Expected | Actual | Status and notes |
|---|---|---|---|
| Ready state | Radius 50 ft, hook 0 ft, no suspended load, 4,000 lb next, 0/3 deliveries, 0 points, and 120 s stopped. Only Start begins play | The opening readouts and controls matched. Attempts to move or pick up before Start left the initial state intact and the countdown stayed at 120 s. Restart restored the same stopped clock | Pass |
| Full real-clock win | Deliver at 125, 99, and 74 ft at 20 ft height in order before 120 s. Finish at 3/3 and 300 points. Nominal sequential path is 272 ft horizontal plus 130 ft vertical, about 40.2 s at 10 ft/s | Held keyboard movement completed all three deliveries. The “Congratulations! — You Win!” panel showed 300 points and 3/3, with 80 s on the stopped countdown. Time continued decreasing during the third-load warning | Pass |
| Points and one action per press | Each delivery adds 100 points once. Held or invalid actions cannot award duplicate points | Cumulative score was 100, 200, then 300. Repeated keydown events during a single held Space press counted once, as did a held emulated touch release. Invalid and post-win input awarded no points | Pass |
| Natural timeout | At 0 s, play ends, gameplay controls disable, and only completed deliveries count | A 390 × 844 run with one delivery reached zero on the unmodified clock. The poll observed “Time's Up! — Game Over.” at about 121 s after Start, with 100 points and 1/3. The timer remained at 0 and play controls were disabled. The panel appeared at the existing scroll position, also fit when resized to 1440 × 1000, and could be restarted by touch | Pass |
| Exact deadline precedence | An otherwise valid final delivery just before the deadline wins. At the deadline, timeout takes precedence over an otherwise valid delivery | A controlled clock accepted the third delivery at deadline minus 1 ms, froze at the displayed 1 s, and ended with a win and 300 points. A valid second-delivery input at the exact deadline was rejected, leaving the load suspended and the score at 100 | Pass |
| Tip-over ending | Overload stops movement and time immediately. Play controls disable and earned score remains | The 75 to 76 ft step ended play. An isolated clock run retained 200 points and 16 displayed seconds, and further actions did not move or deliver the load | Pass |
| Restart and Play Again | Reset a running game or any result to the complete ready state | Restart while running and restart after win, timeout, or tip-over restored radius 50 ft, hook 0 ft, 20,000 lb capacity, 0.00% utilization, empty hook, 4,000 lb next, 0/3, zero score, and stopped 120 s. Keyboard and emulated touch Play Again hid the results and focused Start | Pass |
| Keyboard and emulated touch parity | Both input routes move, pick up, and deliver under the same clearance and scoring rules | An all-touch run at 320 × 568 started, picked up, raised to 25 ft, moved to 125 ft, lowered to 20 ft and delivered for 100 points. Holding pickup/release left the score at 0 until release, which counted once. Separate touch checks confirmed blocked travel below clearance, cancellation, and restart | Pass in Chromium emulation |

### Display, accessibility, reduced motion, and standalone use

| Test | Expected | Actual | Status and notes |
|---|---|---|---|
| Chart steps and axes | Equal radius intervals, labeled axes, and horizontal conservative capacity steps with correct endpoints | Ticks at 50, 75, 100, 125, and 150 ft occupied x = 68, 150, 232, 314, and 396. The chart had the 50 ft endpoint and four horizontal segments with open left and closed right endpoints. The axis labels were “Operating radius (ft)” and “Load / capacity (lb)” | Pass |
| Numerical/chart agreement | At 74/75/76 ft, capacity, suspended-load markers, and condition agree with the hand cases | At all three radii the chart guide and markers matched the numerical readouts and chart labels. The capacity marker moved from 13,000 to 9,000 lb on the step to 76 ft. The load marker remained at 12,000 lb and was hidden after reset | Pass |
| Frozen overload display | Preserve radius, capacity, suspended load, utilization, and chart markers until reset | The panel and readouts retained 76 ft, 9,000 lb, 12,000 lb, and 133.33%. Chart marker coordinates and labels stayed fixed, as did the earned score | Pass |
| Live scene and progress | Active target, ordered load queue, labels, delivered materials, progress, time, and score follow play | Targets and staging advanced from A to B to C, the empty queue heading cleared, and delivered material appeared at each target. Progress advanced 0/3 to 3/3 and matched score. Delivered labels fit their desktop cards after the correction | Pass |
| Crane drawing geometry | Cable follows one trolley and hook at 2 SVG units/ft. Painted roof and load respect the 3.5 ft clearance at a 25 ft hook. Tower structure ends at or above ground | Cable and trolley/hook coordinates matched through the radius and height sweeps. Rendered SVG geometry after correction measured load bottom y=329, roof top y=336, and tower extent at most y=365.5 | Pass on retest |
| Desktop layouts | Side-by-side scene and readings/chart without horizontal overflow. Rulers, chart labels, callouts and guide are readable | Both desktop viewports fit with expanded readings, distinct callouts and labels, and no visible mobile dock. Inspected the 1440 × 1000 render and measured label bounds at both desktop sizes | Pass in Chromium |
| Mobile layouts | Stack panels, keep the live dock visible and clear of the scene, use controls ≥48 × 48 CSS px, and keep the chart separately accessible | All four mobile widths fit without horizontal overflow. Scene-to-dock gaps were 39.0, 272.7, 338.6, and 250.6 CSS px in test-matrix order. The dock displayed time, position, utilization, warning status and objective. The crop kept the operating range visible, instruments started collapsed and toggled, and the separate chart labels were unobstructed within the chart | Pass in Chromium emulation |
| Results layout and non-color cues | All three outcomes have the prescribed text, reason, and icon. Results fit without covering the crane animation, and Play Again is easily reached | Win and overload panels fit at all six viewports. The natural timeout fit at the tested mobile and resized desktop viewports. Warning used ⚠, both losses used ✕, and the win used ✓. Play Again measured at least 48 px high | Pass on retest |
| Programmatic accessibility and keyboard focus | Warning and outcomes are exposed to assistive technology. Controls have accessible names and meaningful focus | Chromium's accessibility tree exposed the live warning and named results region with its description, the chart, and named buttons. Results took focus, Tab reached Play Again, and Space activated it and returned focus to Start | Pass for browser accessibility tree and keyboard |
| Tip-over and reduced motion | The crane visibly tips around its base. Reduced motion shows the tipped pose and results without animation | Normal motion used the tip animation with the base fixed. Emulated reduced motion showed the static tipped pose, readable full results, and no crane animation at 320 × 568 | Pass in Chromium emulation |
| Self-contained application and disclaimer | The HTML works alone offline, and the educational-use disclaimer remains available during play | Opened a copy of only `index.html` with networking disabled. It drew the game and chart, reached an overload through normal actions, and reset with zero resource requests. The complete disclaimer and control guides were present | Pass |

### Fixes and retests

- Delivered desktop titles initially overran their cards by 8.8–50.7 SVG units. Reduced the delivered title size, then repeated both endpoint runs and verified every delivered title fit its card.
- Stage 4 results supplied text and color. Added the specified non-color outcome icons and rechecked the win and loss panels, keyboard/touch restart, and reduced motion.
- The previous roof stroke extended to y=333.5 and the suspended load stroke to y=330 at loaded travel height, leaving 1.75 ft of painted clearance. Clipped the roof stroke at y=336 and included the load's outline in its height limit. Repeated the geometry measurements, layout checks, overload, and full delivery sequence. The measured painted clearance was 3.5 ft.

### Manual results

The user reported these results on 2026-11-23 after reviewing the Stage 5 report. Browser version and viewport were unspecified.

| Test | Expected | Actual | Status |
|---|---|---|---|
| Personal desktop gameplay | Play the game in the intended desktop browser | The user completed a desktop gameplay walkthrough | Pass, user-reported |
| Results panel and outcome icons | The panel and icons display correctly | The user confirmed the panel and icons displayed correctly | Pass, user-reported |
| Delivered-load labels | Labels fit inside their callouts | The user confirmed delivered-load labels fit | Pass, user-reported |
| Keyboard focus after restart | Focus returns to Start after restarting | The user confirmed keyboard focus behaved correctly after restart | Pass, user-reported |

### Manual checks remaining

| Test | Expected | Actual | Status |
|---|---|---|---|
| Physical phone | Touch holds, release/cancel, and Play Again feel usable. Browser chrome and safe-area insets keep the scene, dock, chart and results readable | Chromium touch and viewport emulation were tested. No Stage 5 run on a physical phone was performed | Not run |
| Screen reader | Hear the warning and each game outcome, find the final stats, and restart with clear focus | Accessibility-tree exposure and keyboard focus were tested. No speech or actual screen-reader interaction was observed | Not run |
| Device reduced-motion setting | The tipped crane and final results remain understandable without animation | Chromium emulated the setting. No physical-device reduced-motion check was performed | Not run |

The mandatory core gameplay and all three hand-calculation cases passed local validation. The reported manual desktop checks passed. Physical-phone testing, screen-reader testing, the device reduced-motion check, and the separate Stage 5 commit remain pending.

## Out of scope

- Real-world crane operation, lift planning, or determining the safety of an actual lift.
- Actual crane manufacturer load charts or support for other cranes, charts, or operating standards.
- Three-dimensional simulation or detailed structural analysis.
- Realistic wind, rigging, ground-bearing pressure, outrigger, load-swing, or collision-physics calculations. Use only the simplified movement, clearance, capacity, and tip-over rules defined in this specification.
- Multiple cranes, game levels, randomized scenarios, or user-configurable load weights. The three fixed load deliveries are one game scenario.
- Instructor mode, user accounts, saved student results, advanced analytics, multiplayer, or online leaderboards. The required in-game score and delivery progress remain in scope.
- External APIs, backend servers, LMS integrations, remote assets, or unnecessary external dependencies.
- Native mobile applications or packaging beyond the web application.

The application must remain a self-contained `index.html` suitable for static publication on GitHub Pages.

### Additional feature requirements

- After all required functionality passes validation, implement at least two additional educational or usability features.
- At least one additional feature must be the student's original idea, not one of the examples suggested in the assignment.
- Describe both features in a `Features` section of this specification before implementing either one. Do not begin implementing additional features until all required functionality has passed validation.
- Build, test, and commit each additional feature separately. Keep the features simple and self-contained; they must not require excluded services or change the approved fixed loads, load chart, or game rules.

The application is an educational simulation, not a tool for determining whether an actual crane lift is safe.

## Features

Both features are readouts derived from the existing game state and conservative load chart. The fixed load sequence, movement and clearance rules, utilization thresholds, timer, scoring, and game-ending behavior stay the same. The tables below define expected results and record actual checks where available.

### Feature 1 · Maximum allowable radius

Display the greatest playable radius at which the existing chart can support the suspended load. For a suspended load of weight \(W>0\), calculate

\[
R_{\max}(W)=\max\{R\in\{50,51,\ldots,150\}\mid C(R)\ge W\}.
\]

Use the same stepwise capacity \(C(R)\) as gameplay. This value depends on the suspended load and does not change as the trolley moves. Display **N/A** when no load is suspended.

| Suspended load | Expected display | Actual on desktop and mobile | Status |
|---:|---:|---:|---|
| None | N/A | N/A | Pass |
| 4,000 lb | 150 ft | 150 ft | Pass |
| 8,000 lb | 100 ft | 100 ft | Pass |
| 12,000 lb | 75 ft | 75 ft | Pass |

Focused validation on 2026-11-23 used Chromium at 1440 × 1000 desktop and 320 × 568 emulated mobile viewports. The value stayed constant during trolley travel and returned to N/A on delivery and Play Again. Both runs ended with a 300-point win. The desktop readout fit in the operating-radius card. The mobile readout fit beside the suspended-load value, with at least 39 CSS px between the dock and crane scene. Neither viewport had horizontal overflow.

### Feature 2 · Remaining load capacity

Display the unused capacity at the current operating radius. Calculate \(M=C(R)-W\) in pounds, using \(W=0\) when no load is suspended. If \(M\ge 0\), display \(M\) as remaining capacity. If \(M<0\), display **Capacity exceeded by** \(-M\) **lb** alongside the final overload readings.

| Radius | Suspended load | Chart capacity | Expected display | Actual on desktop and mobile | Status |
|---:|---:|---:|---|---|---|
| 50 ft | None | 20,000 lb | 20,000 lb remaining | 20,000 lb remaining | Pass |
| 150 ft | 4,000 lb | 5,500 lb | 1,500 lb remaining | 1,500 lb remaining | Pass |
| 100 ft | 8,000 lb | 9,000 lb | 1,000 lb remaining | 1,000 lb remaining | Pass |
| 74 ft | 12,000 lb | 13,000 lb | 1,000 lb remaining | 1,000 lb remaining | Pass |
| 75 ft | 12,000 lb | 13,000 lb | 1,000 lb remaining | 1,000 lb remaining | Pass |
| 76 ft | 12,000 lb | 9,000 lb | Capacity exceeded by 3,000 lb | Capacity exceeded by 3,000 lb | Pass |

Focused Chromium checks on 2026-11-23 used 1440 × 1000 desktop and 320 × 568 emulated mobile viewports. After the 4,000 lb delivery at 125 ft, the display rose to 7,000 lb remaining. Overload preserved 76 ft, 9,000 lb capacity, 12,000 lb suspended, 133.33% utilization, 200 points, and the final chart markers. Further input left the overload readings unchanged. Play Again restored 20,000 lb remaining. The readout and results fit without horizontal overflow or scene obstruction. The mobile dock remained at least 24.6 CSS px below the scene during play.

Core regression checks passed for loaded clearance, maximum allowable radius for all three loads, 92.31% warnings at 74 and 75 ft, tip-over at 76 ft, and a 300-point win. A controlled-clock attempt to deliver at the exact deadline showed the timeout panel at 0 s and preserved the 100 already-earned points.

## Build stages

1. **Mocking up the page layout** — Build the static page shell first: header, crane scene panel, live status strip, chart/readout panel, control guide, and mobile control dock. Use placeholder values and drawings only.  
   **Check:** Initial ready-state layout is clear; desktop uses a two-column arrangement; mobile stacks without horizontal scrolling; mobile radius/utilization/status and controls are visible without covering the scene. After these checks pass, commit Stage 1.

2. **Drawing the crane scene** — Create the inline vector construction site with the crane, trolley, cable, hook, staging pickup point and queue, building, labeled delivery targets, active-target highlight, and radius guide.  
   **Check:** One consistent scale maps radius and hook height to the drawing; the roof is no higher than 15 ft; delivery targets are at 20 ft; the 25 ft loaded-clearance corridor is visibly open; and the ruler marks 0/10/20/25/30 ft correctly. Loads do not overlap, and the next load and destination are unambiguous at desktop and mobile sizes. On mobile, use compact A/B/C target markers with the full active objective shown near the controls instead of large overlapping scene callouts. After these checks pass, commit Stage 2.

3. **Implementing movement and load handling** — Add keyboard/touch controls, 1 ft steps, 10 ft/s held movement, limits, focus/cancel stops, pickup/delivery tolerances, fixed load order, and vertical-first clearance handling.  
   **Check:** Boundary actions stay in range; horizontal movement is blocked below 25 ft while loaded; simultaneous movement follows vertical-first logic; invalid actions preserve state; controls work equivalently on keyboard and touch. Verify the cable stays connected to the single moving trolley and hook at the 50/150 ft radius and 0/30 ft height limits. At 320x568, 390x844, and 430x932 CSS px, verify the mobile crop preserves the entire operating range and approved scale, the height labels do not overlap, and the dock keeps live readings and accessible controls visible without covering the scene. The detailed instrument panel is initially collapsed on mobile while the load chart remains separately accessible; desktop readings remain expanded. After these checks pass, commit Stage 3.

4. **Implementing calculations and game flow** — Add the conservative chart lookup, unrounded utilization classifications, stepwise chart markers/readouts, warnings, tip-over, timer, score, win/loss states, and immediate restart.  
   **Check:** 74/75/76 ft hand cases and every chart boundary match; chart/readout values agree; the radius axis spaces equal radius intervals equally (including 50, 75, 100, 125, and 150 ft); 90–100% warns, >100% ends play; final overload readings persist; timer and scoring transitions match this specification. Correct the uneven radius-label spacing in the current static chart preview when building the actual chart. After these checks pass, commit Stage 4.

5. **Validating the required application locally** — Run the complete Validation checklist locally; check accessibility/status messaging and reduced motion; and verify desktop/mobile layouts. Record each actual result and pass/fail in Validation. Choose the exact browser/viewport matrix before layout checks. Do not publish or start additional features during this stage.  
   **Check:** All required cases pass locally, three deliveries can be completed within 120 seconds, and actual test results are recorded. After validation passes, make a separate commit for the validated core application.

6. **Adding the two additional features**
   - Both features must be described in a `Features` section of this specification before either feature is implemented. Do not start this stage until Stage 5 passes.
   - **6A — Adding the first feature:** Implement and test the first documented feature as a separate change, then commit it separately.
   - **6B — Adding the second feature:** Implement and test the second documented feature as a separate change, then commit it separately. At least one of the two features must be an original idea rather than an assignment example.
   - **Check:** Each feature has its own test results and commit, and the required core behavior remains passing.

After Stage 6B is complete and committed, publish the completed `index.html` to GitHub Pages and smoke-test the deployed site. Publication must occur only after both additional features have been implemented and tested.