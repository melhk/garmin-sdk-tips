# Garmin watch face tips

## Using Garmin SDK on Fedora and latest Linux distributions

Garmin provides an SDK Manager to download the SDKs and devices needed for simulation, and a simulator to test your app on different devices.

Garmin SDKs aren't officially supported on Fedora, and both the SDK Manager and the simulator depend on the older WebKitGTK 4.0 API (`libwebkit2gtk-4.0.so.37` and `libjavascriptcoregtk-4.0.so.18`), which is no longer available on recent Linux distributions: Ubuntu dropped it after 22.04, and Fedora dropped its equivalent package starting with Fedora 43. Here's how I got it working on Fedora 44; it should also work on recent Ubuntu releases.

### SDK Manager

Use [distrobox](https://distrobox.it) with an Ubuntu 22 image to run the Garmin SDK Manager:

```bash
distrobox create --image ubuntu:22.04 --name garmin-dev
distrobox enter garmin-dev
sudo apt install curl unzip libsecret-1-0 libexpat1 libxext6 libwebkit2gtk-4.0-37 libsm6
```

Download the desired SDK and devices from inside the container. distrobox shares your `$HOME` with the container by default, so the downloaded SDK ends up at the same path whether you look at it from the host distribution or the container.

### Editor

Install the Monkey C extension for VS Code following Garmin's instructions on [developer.garmin.com/connect-iq/sdk](https://developer.garmin.com/connect-iq/sdk/). `monkeyc` (the compiler) has no WebKit dependency and runs natively on Fedora regardless of how you got the SDK.

### Simulator

For the simulator itself, the easiest way is to find an AppImage packed with the downgraded dependencies, made by the community. I used the one made by Paul Colby, selecting the latest release (Connect IQ Simulator 9.2.0, [pcolby/connectiq-sdk-manager](https://github.com/pcolby/connectiq-sdk-manager/tree/main)).
