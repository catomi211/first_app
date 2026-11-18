# Project Specification

This project is a single-file web app. The app entry point is `index.html` at the repository top level.

# Beam Designer

## Purpose
Beam Designer is an interactive, single-page preliminary structural analysis tool for simply supported and cantilever beams. It helps users enter beam geometry and loading, calculate reactions and internal forces, estimate elastic deflection, and assess bending strength and stiffness.

The app displays the beam, shear/moment/deflection diagrams, governing values, and pass/fail results using US customary units. It is intended for educational and preliminary design use, not as a substitute for a code-compliant structural analysis or engineering review.

Analysis assumes linear elastic behavior, prismatic members, small deflection theory, and loads that do not cause second-order effects. Continuous beams, buckling, shear, torsion, connections, dynamic effects, and second-order effects are outside scope.

## Inputs
### Beam configuration
- Support type: simply supported or cantilever
- Span in feet
- Simply supported beams have a pin support at the left end and a roller support at the right end
- Cantilever beams have a fixed support at the left end

### Loads
- The user may add or remove multiple point loads
- Each point load includes:
  - Magnitude in kips
  - Position along the beam in feet from the left support
- The user may add or remove multiple uniformly distributed loads
- Each uniform load includes:
  - Magnitude in kip/ft
  - Start position and end position in feet from the left support
- Loads act downward
- Uniform loads may cover the full span
- The beam self-weight may be included or excluded, and the selected assumption must be stated clearly
- Load magnitudes must represent a consistent treatment of individual loads or load combinations

### Member
- The user selects either a steel W-shape or a lumber size
- Steel W-shapes use a built-in designation and provide section modulus, elastic modulus, and allowable bending stress
- Lumber sizes use a selected species and grade and provide section modulus, elastic modulus, and allowable bending stress
- Each member selection must provide the properties needed for bending stress and deflection calculations

### Units and defaults
- Use feet and kips for beam geometry and loading
- Use inches and ksi for section properties
- Use kip-ft for bending moment and inches for deflection
- Updates to loads or member properties should refresh the beam drawing and calculations
- Open with one valid example beam that has loads and a member selection already applied

### Input validation
- Span must be greater than zero
- Point-load positions must be between 0 ft and the beam span
- Distributed-load start and end positions must be within the span
- Distributed-load start position must be less than its end position
- Point loads and distributed-load magnitudes must be positive
- All required member properties must be available
- Incomplete or invalid inputs must show a clear message and prevent results from being calculated or displayed

## Calculations
### Reactions
- Determine support reactions using static equilibrium
- For simply supported beams, calculate vertical reactions using moment equilibrium and vertical force equilibrium
- For cantilever beams, calculate the fixed-end vertical reaction and fixed-end moment
- Assume vertical downward loads and neglect axial effects

### Shear and moment diagrams
- Construct shear and moment diagrams along the full beam
- Combine point-load, distributed-load, and support effects
- Use consistent sign conventions for positive shear and positive moment
- At a point load, shear changes by the load magnitude and moment remains continuous
- Within a distributed-load segment, shear varies linearly and moment varies quadratically
- Report maximum positive and maximum negative shear and moment values when applicable
- Report the governing absolute bending moment as |M|max

### Deflection
- Assume prismatic members and linear-elastic Euler-Bernoulli beam theory
- Calculate deflection using EI, where E is the elastic modulus and I is the moment of inertia about the bending axis
- Use superposition or direct integration with the proper support boundary conditions
- For simply supported beams, enforce zero vertical deflection at both supports
- For cantilever beams, enforce zero deflection and rotation at the fixed end
- Report the maximum absolute deflection in inches
- Obtain I from the built-in steel or lumber section data

### Bending stress
- Calculate bending stress as fb = |M|max / S, with consistent unit conversion
- M is the governing absolute bending moment
- S is the section modulus about the bending axis
- Compare fb with the member allowable bending stress:
  - Pass when fb is less than or equal to the allowable bending stress
  - Fail when fb is greater than the allowable bending stress
- Identify the location on the beam that governs the bending check

### Deflection check
- Calculate the allowable deflection as L/360, with L converted to inches
- Compare the maximum absolute deflection with L/360:
  - Pass when actual deflection is less than or equal to L/360
  - Fail when actual deflection is greater than L/360

### Numerical and reporting details
- Recalculate after every valid input change
- Perform calculations in a consistent internal unit system, then convert results for display
- Round moment, stress, deflection, and reaction results to a stated precision without rounding intermediate values
- Prevent stale or invalid results from remaining visible after an invalid edit
- Report calculations in this sequence: inputs, reactions, shear/moment, deflection, and pass/fail checks

## Outputs
- Show the beam, supports, and all loads
- Show shear, moment, and deflection diagrams
- Show reactions, maximum moment, maximum deflection, and bending stress
- Show clearly whether bending passes or fails
- Show clearly whether deflection passes or fails
- Clear or hide results when inputs are invalid

## Look and feel
- Use a clean, professional engineering-tool layout with pastel colors
- Use a pink page background
- Use purple as the primary accent color
- Use black text throughout for strong readability
- Place beam controls on the right side of the screen
- Place the beam drawing and diagrams on the main left side
- Open with a valid example beam and a complete set of example results
- Use pastel purple for active controls, selected states, and diagram accents
- Use a distinct pastel color for each diagram or load type without reducing text contrast
- Keep pass states green and fail states red, with text labels and icons so color is not the only indicator
- Use clear labels, readable chart axes, units, and gridlines
- Clearly separate controls, diagrams, and pass/fail results into labeled sections
- Support narrow screens by stacking the controls below the beam and diagrams
- Use clear focus, hover, disabled, validation-error, and valid-input states
- Maintain sufficient contrast, accessible labels, keyboard-operable controls, and a logical tab order
- Avoid dense or decorative content that distracts from the engineering results

## Validation
Calculations assume E = 29,000 ksi and I = 204 in4 for a W12x26.
Deflections use the full beam span and include only the applied loads.

- **Simply supported, uniform load:** A 20 ft beam with 1.0 kip/ft over its full length
  - Reactions: 10.0 kips at each support
  - Maximum moment: 50.0 kip-ft at midspan
  - Maximum deflection: 0.609 in at midspan
- **Simply supported, center point load:** A 20 ft beam with 10.0 kips at midspan
  - Reactions: 5.0 kips at each support
  - Maximum moment: 50.0 kip-ft at midspan
  - Maximum deflection: 0.487 in at midspan
- **Simply supported, partial uniform load:** A 20 ft beam with 1.0 kip/ft from 5 ft to 15 ft
  - Reactions: 5.0 kips at each support
  - Maximum moment: 37.5 kip-ft at midspan
  - Maximum shear: 5.0 kips
- **Cantilever, end point load:** A 10 ft beam with 10.0 kips at the free end
  - Fixed-end reaction: 10.0 kips
  - Maximum moment: 100.0 kip-ft at the fixed end
  - Maximum deflection: 0.974 in at the free end
- **Cantilever, uniform load:** A 10 ft beam with 1.0 kip/ft over its full length
  - Fixed-end reaction: 10.0 kips
  - Maximum moment: 50.0 kip-ft at the fixed end
  - Maximum deflection: 0.3653 in at the free end

## Out of scope
- Continuous, overhanging, and indeterminate beams
- Buckling, shear, torsion, and lateral-torsional stability
- Connections, support settlements, and second-order effects
- Dynamic loading, impact, fatigue, and temperature effects
- Code-compliant design or permit documentation

## Build stages

### Stage 1: Page layout mockup
Create the pink/pastel layout with a main diagram area on the left and controls on the right. Include placeholder beam, shear, moment, and deflection panels, input and results sections, example values, and pass/fail placeholders.

**Test when complete:** Resize the window and verify the layout remains readable. Confirm right-side controls, pink background, purple accents, black text, pass/fail colors, and example content match the Look and feel section.

### Stage 2: Interactive controls and validation
Add support, span, load, and member controls without real calculations. Support adding and removing point and uniform loads.

**Test when complete:** Add and remove loads, change support types and member selections, enter invalid span or load positions, and confirm clear validation messages. Invalid inputs must prevent placeholder results from appearing.

### Stage 3: Beam drawing and reactions, shear, and moment
Draw supports and load arrows, then calculate reactions and construct shear and moment diagrams. Start with one simple arrangement if needed, then expand to point loads, uniform loads, and cantilevers.

**Test when complete:** Compare reactions and maximum moment with the Validation cases. Verify shear jumps and moment continuity at point loads and linear/quadratic behavior for uniform-load segments.

### Stage 4: Deflection
Calculate elastic deflection using member E and I, correct boundary conditions, and linear-elastic Euler-Bernoulli beam theory. Add the deflection diagram.

**Test when complete:** Compare all deflection answers in the Validation cases. Verify simply supported deflection is zero at both supports and cantilever deflection and rotation are zero at the fixed end.

### Stage 5: Checks and polish
Calculate bending stress using M/S and deflection against L/360. Add governing locations, live updates, example defaults, chart labels, keyboard controls, and responsive behavior.

**Test when complete:** Verify pass and fail states with valid and failing cases. Compare final output with every Validation case and test keyboard navigation, resizing, invalid-input clearing, and the example displayed when the page opens.