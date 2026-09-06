# Known limitations

## No position feedback

The motor does not provide actual position feedback to this gateway.

Position is therefore estimated from:

- last known position,
- movement direction,
- configured total travel time,
- calibration curve.

## Original remote causes drift

If a physical Idemo remote moves the shutter, the gateway is not aware of that movement because the sniffer/receiver has been removed from the normal configuration.

A full open/close command from Home Assistant can restore a known endpoint.

## Calibration is installation-specific

Different:

- shutter heights,
- slat geometries,
- motor speeds,
- friction,
- loads,

can change the relationship between run time and visible opening.

The default curves are starting values only.

## Shared radio

All six shutters share one CC1101 transmitter. RF commands are serialised.

## Reverse-engineered protocol

Only the behaviour tested during this project should be considered confirmed. Other IDRM remotes, motors or firmware revisions may differ.

## Transmitter identity

The development transmitter ID is hard-coded in the current YAML. Community users should review it and understand pairing before installation.

## Safety

This project controls moving shutters. Avoid automatic movement where it could trap people, pets or objects. Follow the motor manufacturer's installation and safety requirements.
