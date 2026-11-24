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
- Clear feedback for invalid actions, successful deliveries, winning, and losing. State the reason for a loss, such as time expiring or tip-over.
- A visible control guide for keyboard and touchscreen controls.
- A visible disclaimer: "Educational simulation only. This assignment load chart is not a real crane chart and must not be used for actual crane operations."
- Important warning and game-state changes available to assistive technology, not only visually.

## Look and feel

Use a clean, professional construction-site design with a yellow tower crane, a gray building under construction, a staging area, and clearly identified delivery targets. Use green, amber, and red status indicators accompanied by text and icons; do not rely on color alone.

On desktop, place the crane simulation beside the load chart and numerical readings. Put the application title, timer, score, and delivery progress in a header. Keep the keyboard control guide near the crane simulation.

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

The tests below define expected results for the completed application. Use the conservative capacity lookup in Calculations. Do not add test-only loads to the selectable game loads.

### Hand calculations

For each case, \(W=12{,}000\) lb and \(U=(W/C(R))\times100\).

| Test | Substitution and expected calculation | Expected result |
|---|---|---|
| 74 ft | \(C(74)=13{,}000\) lb; \(U=(12{,}000\text{ lb}/13{,}000\text{ lb})\times100=92.3077\ldots\%=92.31\%\) | Warning; no tip-over |
| 75 ft | \(C(75)=13{,}000\) lb; \(U=(12{,}000\text{ lb}/13{,}000\text{ lb})\times100=92.3077\ldots\%=92.31\%\) | Warning; no tip-over |
| 76 ft | \(C(76)=9{,}000\) lb; \(U=(12{,}000\text{ lb}/9{,}000\text{ lb})\times100=133.3333\ldots\%=133.33\%\) | Tip-over; game ends |

### Capacity lookup boundaries

Check exact table radii and the next 1 ft radius. Capacity must change according to the stepwise rule, without interpolation.

| Radius input | Expected allowable capacity |
|---:|---:|
| 50 ft | 20,000 lb |
| 51 ft | 13,000 lb |
| 75 ft | 13,000 lb |
| 76 ft | 9,000 lb |
| 100 ft | 9,000 lb |
| 101 ft | 7,000 lb |
| 125 ft | 7,000 lb |
| 126 ft | 5,500 lb |
| 150 ft | 5,500 lb |
| Attempt to move beyond 150 ft | Radius remains 150 ft; no lookup is performed outside the valid range |

### No-load and utilization thresholds

With no suspended load at \(R=100\) ft: \(W=0\) lb, \(C(R)=9{,}000\) lb, and \(U=0.00\%\). Expected: normal status, no warning, and no tip-over.

Test the threshold-classification logic directly with utilization percentages. These are isolated calculation tests, not additional selectable or simulated loads. Compare the unrounded percentage; display rounding must not change its classification.

| Utilization input | Expected displayed value | Expected status |
|---:|---:|---|
| 89.99% | 89.99% | Normal |
| 89.999% | 90.00% | Normal |
| 90.00% | 90.00% | Warning |
| 90.01% | 90.01% | Warning |
| 99.99% | 99.99% | Warning |
| 100.00% | 100.00% | Warning; no tip-over |
| 100.001% | 100.00% | Tip-over; game ends |
| 100.01% | 100.01% | Tip-over; game ends |

### Movement, pickup, and delivery

- At \(R=50\) ft, attempt inward movement; at \(R=150\) ft, attempt outward movement. At hook heights 0 ft and 30 ft, attempt movement beyond each limit. Expected: no position moves outside its allowed range.
- Verify one direction press moves 1 ft and a held direction moves at 10 ft/s in 1 ft increments. Verify that release, touchscreen cancellation, or browser-window focus loss stops held movement; opposing directions on the same axis cancel.
- With a load suspended at \(h=24\) ft, horizontal movement alone is blocked. With both axes moving, verify vertical movement updates first: from 24 ft, an upward step to 25 ft permits the horizontal step in that update. At \(h=25\) ft, horizontal movement is allowed.
- Verify pickup at the inclusive staging tolerance: \(R=50\)–51 ft and \(h=0\)–1 ft, when no load is suspended and a load remains. Verify pickup outside either tolerance, with a load already suspended, or after all loads are delivered is rejected without changing game state.
- Verify the inclusive delivery ranges: 4,000 lb at \(R=124\)–126 ft, 8,000 lb at \(R=98\)–100 ft, and 12,000 lb at \(R=73\)–75 ft; \(h=19\)–21 ft for each. Check both endpoints. Every accepted radius must remain within capacity.
- Attempt delivery outside a target's radius or height tolerance and at the wrong target. Expected: the load remains suspended and the score does not change.

### Game flow, scoring, and controls

- On first open, verify the ready state: \(R=50\) ft, \(h=0\) ft, no suspended load, 4,000 lb next, delivery progress 0/3, score 0 points, and the timer stopped at 120 s. Gameplay controls must not start play until Start is pressed.
- Deliver the three fixed loads in order at the centers of their delivery ranges, returning to staging between loads. The horizontal path is \(75+75+49+49+24=272\) ft; the required vertical path is 130 ft. Moving both axes sequentially at 10 ft/s takes approximately \((272+130)/10=40.2\) seconds, excluding interaction time. Expected: all three loads delivered before 120 s, progress 3/3, score 300 points, and a win.
- Verify one successful delivery adds exactly 100 points. Holding the pick-up/release control triggers only one action per press; repeated or invalid actions do not award duplicate points.
- Allow the timer to reach 0 s before all loads are delivered. Expected: timeout loss, no delivery accepted at the exact expiration time, and the score reflects only completed deliveries.
- Restart from a running game, a win, and a loss. Expected: ready state restored with \(R=50\) ft, \(h=0\) ft, \(W=0\) lb, score 0, timer stopped at 120 s, and the 4,000 lb load next.
- Verify keyboard and touchscreen controls produce the same movement and pickup/release behavior. Check that the touch dock remains usable without covering the crane simulation.

### Display and responsive layout

- At 74, 75, and 76 ft, compare the numerical capacity, utilization, and status with the hand calculations. Verify that the chart uses stepwise capacity, has labeled axes, and places its capacity and suspended-load markers at values matching the readouts.
- At 76 ft with a 12,000 lb load, verify tip-over ends play and preserves the final radius, load, capacity, utilization, and chart markers.
- Verify the active delivery target, load queue, delivery progress, timer, and score reflect the current game state.
- Check desktop and mobile layouts, including no horizontal scrolling, visible mobile radius/utilization/status near the simulation, readable chart labels, and controls that do not obscure the scene.
- Verify warning and win/loss messages use text and icons as well as color, and are available to assistive technology.

### Recording test results

For each test, record its ID or scenario, actual observed result, pass/fail status, and notes. For display or control tests, also record the browser/device and evidence when useful. All actual results and statuses are currently **Not run** because the application has not been built. Update them only after the corresponding test has genuinely been performed.

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