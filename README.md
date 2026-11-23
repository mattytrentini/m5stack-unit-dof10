# M5Stack Unit DoF10 for MicroPython

This `mip` bundle installs the three drivers for the [M5Stack Unit DoF10](https://docs.m5stack.com/en/unit/Unit_DoF10). It adds no wrapper module and does not run anything at boot.

| Sensor | I²C address | Driver | License |
| --- | --- | --- | --- |
| BMI270 accelerometer and gyroscope | `0x68` or `0x69` (switch positions `0` or `1`) | [micropython-lib](https://github.com/micropython/micropython-lib/tree/master/micropython/drivers/imu/bmi270) | MIT |
| BMM350 magnetometer | `0x14` | [Soldered Electronics](https://github.com/SolderedElectronics/Soldered-BMM350-MicroPython-Library) | GPL-3.0 |
| SPA06-003 pressure and temperature | `0x76` | [micropython-spa06-003](https://github.com/mattytrentini/micropython-spa06-003) | MIT |

## Install

```sh
mpremote mip install github:mattytrentini/m5stack-unit-dof10
```

Or, on a network-connected MicroPython board:

```python
import mip
mip.install("github:mattytrentini/m5stack-unit-dof10")
```

## Read the sensors

Connect the unit to an I²C Grove port. Power the port and pass the same
initialized I²C bus to all three drivers. Set the BMI270 switch to `1` on
CoreMatrix: the onboard BMI270 already uses `0x68`. The example below uses
`0x69`; use `0x68` only if the switch is at `0` and that address is free.

```python
from bmi270 import BMI270
from bmm350 import BMM350, BMM350_OK
from spa06_003 import SPA06_003

imu = BMI270(i2c, address=0x69)
mag = BMM350(i2c)
baro = SPA06_003(i2c)

print("Acceleration (g):", imu.accel())
print("Angular velocity (deg/s):", imu.gyro())
if mag.get_sensor_data() != BMM350_OK:
    raise OSError(mag.status_string())
print("Magnetic field (uT):", (mag.data.mag_x, mag.data.mag_y, mag.data.mag_z))
print("Temperature (C), pressure (hPa):", baro.read())
```

The [CoreMatrix app](https://github.com/mattytrentini/m5stack-core-matrix-app)
provides a board-specific demo that manages Grove Port A power.
