# DVS Viewer for NVIDIA Jetson Xavier NX

This package contains the ARM64 build of DVS Viewer for NVIDIA Jetson Xavier NX running JetPack 5.

## Supported Environment

| Component | Supported version |
|---|---|
| Device | NVIDIA Jetson Xavier NX |
| JetPack | JetPack 5 |
| OS | Ubuntu 20.04 LTS |
| Architecture | ARM64 (`aarch64`) |
| Qt | Qt 6.2.4, supplied with this package |
| OpenCV | Ubuntu 20.04 system package, normally 4.2.x |
| Ceres Solver | Ubuntu 20.04 system package, normally 1.14.x |
| HDF5 | Ubuntu 20.04 system package, normally 1.10.x |
| libusb | libusb 1.0 |

This release is intended for JetPack 5 on Jetson Xavier NX. It is not compatible with Linux x64 or Windows, and compatibility with other JetPack releases is not guaranteed.

## Package Contents

```text
JetPack5/
|-- Qt-6.2.4-Jetson-arm64.tar.gz
|-- README.md
`-- viewer/
    |-- DVS_Viewer
    |-- libDELTA_SDK.so
    |-- libcyusb.so
    |-- settings/
    |-- styles/
    |-- calibration/
    `-- Save/
```

Do not extract the Qt archive on Windows. Copy this entire directory to the Jetson and extract the archive on the Jetson so that Linux permissions and symbolic links are preserved.

## 1. Install System Dependencies

Connect the Jetson to the Internet and run:

```bash
sudo apt update && sudo apt install -y libusb-1.0-0 libopencv-dev libhdf5-103 libceres-dev libeigen3-dev libgoogle-glog-dev libgflags-dev libunwind-dev xdg-utils libxkbcommon-x11-0 libxcb-xinerama0 libxcb-cursor0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-render-util0
```

These packages provide the OpenCV, Calibration, HDF5 export, USB, desktop integration, and Qt XCB runtime dependencies used by DVS Viewer.

Qt 6 must not be installed with `apt` for this release. Use the supplied Qt 6.2.4 ARM64 archive as described below.

## 2. Install the Supplied Qt 6.2.4 Package

Open a terminal in the `JetPack5` directory containing `Qt-6.2.4-Jetson-arm64.tar.gz`, then run:

```bash
sudo mkdir -p /opt/Qt && sudo tar -xzf Qt-6.2.4-Jetson-arm64.tar.gz -C /opt/Qt
```

The resulting Qt installation directory must be `/opt/Qt/6.2.4`.

Register the Qt library directory with the system linker:

```bash
echo '/opt/Qt/6.2.4/lib' | sudo tee /etc/ld.so.conf.d/qt6-6.2.4.conf > /dev/null && sudo ldconfig
```

Verify the Qt installation:

```bash
/opt/Qt/6.2.4/bin/qmake6 --version
```

## 3. Configure USB Device Permission

Connect the camera and confirm that it is detected:

```bash
lsusb
```

Expected Cypress USB device IDs:

```text
Delta-01: 04b4:00f1
Delta-10: 04b4:00f0
```

Install a udev rule that allows DVS Viewer to access the camera without running the application as root:

```bash
echo -e 'SUBSYSTEM=="usb", ATTR{idVendor}=="04b4", ATTR{idProduct}=="00f0", MODE="0666"\nSUBSYSTEM=="usb", ATTR{idVendor}=="04b4", ATTR{idProduct}=="00f1", MODE="0666"' | sudo tee /etc/udev/rules.d/99-cypress-dvs.rules > /dev/null && sudo udevadm control --reload-rules && sudo udevadm trigger
```

Disconnect and reconnect the camera after installing the rule.

## 4. Run DVS Viewer

Open a terminal in the `JetPack5` directory and run:

```bash
cd viewer && chmod +x DVS_Viewer && QT_PLUGIN_PATH=/opt/Qt/6.2.4/plugins ./DVS_Viewer
```

## Troubleshooting

### Check for Missing Viewer Libraries

Run this command from the `JetPack5` directory:

```bash
cd viewer && ldd ./DVS_Viewer | grep 'not found' || echo 'Viewer dependencies OK'
```

### Check for Missing Qt XCB Libraries

```bash
ldd /opt/Qt/6.2.4/plugins/platforms/libqxcb.so | grep 'not found' || echo 'Qt XCB dependencies OK'
```

If either command prints a library followed by `not found`, install the corresponding Ubuntu 20.04 ARM64 package before starting the Viewer.

### Qt Platform Plugin Error

If the Viewer reports that the Qt `xcb` platform plugin cannot be found or initialized, run:

```bash
cd viewer && QT_QPA_PLATFORM=xcb QT_PLUGIN_PATH=/opt/Qt/6.2.4/plugins LD_LIBRARY_PATH=/opt/Qt/6.2.4/lib:$PWD ./DVS_Viewer
```

### Camera Permission Error

Confirm that the camera is visible and that the udev rule was installed:

```bash
lsusb && cat /etc/udev/rules.d/99-cypress-dvs.rules
```

Reconnect the camera after reloading the udev rules. Running the Viewer with `sudo` should only be used as a temporary diagnostic test.

### Open Save Directory Does Not Work

Install desktop integration support if it is not already present:

```bash
sudo apt install -y xdg-utils nautilus
```

## Dependency Notes

- `libusb-1.0-0` is required for Cypress camera USB communication.
- OpenCV is required by the Viewer, SDK, and Calibration functions.
- Ceres Solver, Eigen, glog, gflags, and libunwind are required by Calibration mode.
- HDF5 is required when exporting event data to `.h5` files.
- The supplied Qt archive contains Qt 6.2.4 libraries and the `libqxcb.so` platform plugin.
- XCB packages are installed from Ubuntu because the Qt platform plugin depends on system X11/XCB libraries.
