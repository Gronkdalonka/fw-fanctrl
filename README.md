# fw-fanctrl

This is a very simple script for Linux that pilots the FrameWork laptop's fan speed according to a configurable speed/temp curve.

Under the hood, it uses [fw-ectool](https://github.com/DHowett/fw-ectool) to change parameters in FrameWork's embedded controller (EC).

# Install

## Dependancies
This tool depends on `lm-sensors` to fetch CPU temperature:
```
sudo apt install lm-sensors
yes | sudo sensors-detect
```

You can either use the pre-compiled executable of `fw-ectool` in this repo, or recompile one from [this repo](https://github.com/DHowett/fw-ectool) and copy it in `./bin`.

Then, simply run:
```
sudo ./install.sh
```

This bash script is going to create and enable a service that runs this repo's main script, `fanctrl.py`.
It will copy `fanctrl.py` and `./bin/ectool` to `/usr/local/bin` and create a config file in `/home/<user>/.config/fw-fanctrl/config.json`

# Uninstall
```
sudo ./install.sh remove
```

# Configuration

There is a single `config.json` file where you can configure the service. You need to run the install script again after editing this config, or you can directly edit the installed config at `/home/<user>/.config/fw-fanctrl/config.json`

- **SpeedCurve**:

    This is the curve points for `f(temperature) = fan speed`

    `fw-fanctrl` measures the CPU temperature, compute a moving average of it, and then find an appropriate `fan speed` value by interpolation on the curve.

- **FanSpeedUpdateFrequency**:

    Time interval between every update to the fan's speed. `fw-fanctrl` measures temperature every second and add it to its moving average, but the actual update to fan speed is made every 5s by default. This is for comfort, otherwise the speed is changed too often and it is noticeable and annoying, especially at low speed.
    For a more reactive fan, you can lower this setting.

- **MovingAverageInterval**:

    Number of seconds on which the moving average of temperature is computed. Increase it, and the fan speed will change more gradually. Lower it, and it will gain in reactivity. Defaults to 30 seconds.