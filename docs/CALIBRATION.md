# Calibration

The project separates two concepts:

- **Physical position (F):** the visible opening percentage presented in Home Assistant.
- **Conceptual position (C):** the internal time-domain percentage used to calculate how long the motor should run.

## Why the curves are directional

The visible opening is not perfectly linear with motor run time. In a roller shutter, slats stack/unstack and the geometry differs while opening and closing.

Therefore the project keeps:

- one curve for **raising/opening**,
- one curve for **lowering/closing**.

## Current defaults

### Raising

```text
25:25|50:70|70:90|80:100
```

### Lowering

```text
25:3|50:7|75:45|85:60|90:75
```

These are not universal values.

## Format

```text
C:F|C:F|C:F
```

Example:

```text
25:25|50:70|70:90|80:100
```

means:

- conceptual 25 % → physical 25 %
- conceptual 50 % → physical 70 %
- conceptual 70 % → physical 90 %
- conceptual 80 % → physical 100 %

The code automatically uses 0:0 and 100:100 as endpoints where required.

## Editing from Home Assistant

Each channel exposes:

- `Tiempo subida`
- `Tiempo bajada`
- `Curva Subida C:F`
- `Curva Bajada C:F`

Values use `restore_value`, so changes made from Home Assistant survive reboot.

## Recommended calibration procedure

1. Fully close the shutter.
2. Measure the complete opening time.
3. Fully open the shutter.
4. Measure the complete closing time.
5. Enter the measured travel times in Home Assistant.
6. Test several requested positions while raising.
7. Record the actual physical opening.
8. Build a raising curve.
9. Repeat while lowering.
10. Refine only where error is significant.

More calibration points can improve a strongly non-linear zone, but too many points make calibration harder to maintain.

## Interpolation

The gateway uses piecewise-linear interpolation.

When Home Assistant requests a physical position, the code computes the inverse of the curve for the required direction and derives a conceptual position. Motor run time is then calculated from the conceptual distance and configured total travel time.

## After using the original remote

The gateway does not receive motor position feedback. If the original RF remote moves the shutter, the internal Home Assistant position can drift.

Perform a full open or full close operation from Home Assistant to restore a known endpoint.
