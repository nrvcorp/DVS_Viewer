# DVS Viewer for Linux

DVS Viewer is a Linux application for real-time streaming from NRV hardware cameras, including Delta-Series camera.

The viewer can display live DVS data, save unparsed raw data, save parsed frame images, and record video output. It also supports playback of previously saved DVS data.

## Environment Setting

### Recommended environment

This release is built and tested on the following environment. Using the same
versions avoids shared-library issues (see Troubleshooting):

| Component | Recommended version | Notes |
|-----------|---------------------|-------|
| OS        | **Ubuntu 22.04 LTS** | Ships the expected library versions by default |
| OpenCV    | **4.5.x** (SONAME `.so.4.5d`) | Provided by `libopencv-dev` on Ubuntu 22.04 |
| Qt        | **Qt 6** (any 6.x) | Major version must be 6 |
| libusb    | **libusb-1.0** (any 1.0.x) | Major version must be 1.0 |

> **Important:** OpenCV encodes the *minor* version into its library name
> (`libopencv_core.so.4.5d`), so the OpenCV version must match. Newer
> distributions (e.g. Ubuntu 24.04) install OpenCV 4.6.0 instead of 4.5.x, which
> causes a "shared library not found" error. On Ubuntu 22.04 the correct 4.5.x is
> installed automatically. On other distributions, see
> [OpenCV version mismatch](#opencv-version-mismatch-libopencv_-so45d-not-found)
> in Troubleshooting.
>
> Qt and libusb only encode the *major* version into their library names
> (`libQt6Core.so.6`, `libusb-1.0.so.0`), so any 6.x / 1.0.x release works.

Install the required Linux packages:

**Linux Bash**

```bash
sudo apt update
sudo apt install -y \
  libusb-1.0-0 \
  libopencv-dev \
  qt6-base-dev \
  libqt6svg6 \
  libhdf5-103 \
  xdg-utils
```

Optional packages:

**Linux Bash**

```bash
sudo apt install -y nautilus dbus-x11
```

- `libqt6svg6` is required for SVG-based UI icons, such as radio buttons and check boxes.
- `libhdf5-103` is the HDF5 runtime, required for exporting events to `.h5` files.
- `xdg-utils` is required for opening save directories from the viewer.
- `nautilus` is useful when the system does not already provide a Linux file manager.
- `dbus-x11` can help reduce DBus/GIO warnings in WSL or minimal desktop environments.

## USB Connection

On a native Linux system, connect the camera and check that it is visible:

**Linux Bash**

```bash
lsusb
```

Expected Cypress device IDs:

```text
Delta-01 : 04b4:00f1
Delta-10 : 04b4:00f0
```

If device permission is denied, run once with `sudo` or add a udev rule:

**Linux Bash**

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

**Linux Bash**

```bash
cd DVS_Viewer/Linux/x64
```

Allow execution if needed:

**Linux Bash**

```bash
chmod +x DVS_Viewer
```

Run the viewer:

**Linux Bash**

```bash
./DVS_Viewer
```

## Troubleshooting


### Shared library not found

If a library cannot be found:

**Linux Bash**

```bash
ldd ./DVS_Viewer
```

If needed, run with:

**Linux Bash**

```bash
LD_LIBRARY_PATH=. ./DVS_Viewer
```

### OpenCV version mismatch (`libopencv_*.so.4.5d` not found)

On distributions other than Ubuntu 22.04, `apt install libopencv-dev` may install a
different OpenCV version (for example, Ubuntu 24.04 installs OpenCV 4.6.0). Because
OpenCV encodes its minor version into the library name, the viewer fails to start:

```text
./DVS_Viewer_FX20: error while loading shared libraries:
libopencv_core.so.4.5d: cannot open shared object file: No such file or directory
```

Note: the `d` in `4.5d` is Ubuntu 22.04's ABI tag, **not** a debug build.

**Recommended fix:** use Ubuntu 22.04 LTS, which provides OpenCV 4.5.x
(`libopencv_core.so.4.5d`) automatically.

**Workaround (other distributions):** OpenCV keeps backward ABI compatibility across
minor releases, so you can link the installed version to the expected `.4.5d` name.

First, check which OpenCV version is actually installed:

**Linux Bash**

```bash
ldconfig -p | grep libopencv_core
# e.g. libopencv_core.so.4.6.0  -> the installed version is 4.6.0
```

Then create the links (replace `4.6.0` with the version reported above):

**Linux Bash**

```bash
cd /usr/lib/x86_64-linux-gnu

sudo ln -sf libopencv_core.so.4.6.0      libopencv_core.so.4.5d
sudo ln -sf libopencv_imgproc.so.4.6.0   libopencv_imgproc.so.4.5d
sudo ln -sf libopencv_imgcodecs.so.4.6.0 libopencv_imgcodecs.so.4.5d
sudo ln -sf libopencv_videoio.so.4.6.0   libopencv_videoio.so.4.5d

sudo ldconfig
```

Then run the viewer again:

**Linux Bash**

```bash
./DVS_Viewer
```

### UI icons are missing

Install the Qt SVG runtime:

**Linux Bash**

```bash
sudo apt install -y libqt6svg6
```

### Open Directory does not work

Install `xdg-open` support:

**Linux Bash**

```bash
sudo apt install -y xdg-utils
```

If no file manager is available:

**Linux Bash**

```bash
sudo apt install -y nautilus
```

### DBus or GIO warnings

Some WSL or minimal Linux environments may show DBus/GIO warnings when opening file dialogs or directories. If the viewer works normally, these warnings can usually be ignored.

To reduce them:

**Linux Bash**

```bash
sudo apt install -y dbus-x11
dbus-run-session ./DVS_Viewer
```
