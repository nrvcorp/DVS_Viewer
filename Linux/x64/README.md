# DVS Viewer for Linux

DVS Viewer is a Linux application for real-time streaming from NRV hardware cameras, including `RC1S_CX3` and `RC1S_FX10`.

The viewer can display live DVS data, save unparsed raw data, save parsed frame images, and record video output. It also supports playback of previously saved DVS data.

## Environment Setting

Install the required Linux packages:

```bash
sudo apt update
sudo apt install -y \
  libusb-1.0-0 \
  libopencv-dev \
  qt6-base-dev \
  libqt6svg6 \
  xdg-utils
```

Optional packages:

```bash
sudo apt install -y nautilus dbus-x11
```

- `libqt6svg6` is required for SVG-based UI icons, such as radio buttons and check boxes.
- `xdg-utils` is required for opening save directories from the viewer.
- `nautilus` is useful when the system does not already provide a Linux file manager.
- `dbus-x11` can help reduce DBus/GIO warnings in WSL or minimal desktop environments.

## USB Connection

On a native Linux system, connect the camera and check that it is visible:

```bash
lsusb
```

Expected Cypress device IDs:

```text
FX10 : 04b4:00f0
CX3  : 04b4:00f1
```

If device permission is denied, run once with `sudo` or add a udev rule:

```bash
sudo tee /etc/udev/rules.d/99-cypress-dvs.rules >/dev/null <<'EOF'
SUBSYSTEM=="usb", ATTR{idVendor}=="04b4", ATTR{idProduct}=="00f0", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="04b4", ATTR{idProduct}=="00f1", MODE="0666"
EOF

sudo udevadm control --reload-rules
sudo udevadm trigger
```

Reconnect the camera after applying the rule.

## How to Run

Move to the release directory:

```bash
cd DVS_Viewer_Linux_x64
```

Allow execution if needed:

```bash
chmod +x DVS_Viewer_FX20
```

Run the viewer:

```bash
./DVS_Viewer_FX20
```

## Troubleshooting

### Permission denied

If the executable cannot run:

```bash
chmod +x DVS_Viewer_FX20
```

### Shared library not found

If a library cannot be found:

```bash
ldd ./DVS_Viewer_FX20
```

If needed, run with:

```bash
LD_LIBRARY_PATH=. ./DVS_Viewer_FX20
```

### UI icons are missing

Install the Qt SVG runtime:

```bash
sudo apt install -y libqt6svg6
```

### Open Directory does not work

Install `xdg-open` support:

```bash
sudo apt install -y xdg-utils
```

If no file manager is available:

```bash
sudo apt install -y nautilus
```

### DBus or GIO warnings

Some WSL or minimal Linux environments may show DBus/GIO warnings when opening file dialogs or directories. If the viewer works normally, these warnings can usually be ignored.

To reduce them:

```bash
sudo apt install -y dbus-x11
dbus-run-session ./DVS_Viewer_FX20
```
