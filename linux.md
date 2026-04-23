# Battery

```bash
upower -e | grep 'BAT'
```

```bash
upower -i /org/freedesktop/UPower/devices/battery_BAT0
```

or a one line
```bash
upower -i $(upower -e | grep BAT) | grep percentage
```