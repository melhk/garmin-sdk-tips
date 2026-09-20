# Garmin SDK tips

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
## Project structure

* `bin`: contains binary and debug output from the app compilation
* `resources`: inputs to the resource compiler, such as layouts, images, fonts, strings, and language-specific resources
* `source`: contains the Monkey C source files, initially split into `App` and `View` files
* `manifest.xml`: application properties like the app id, the app type, and the targeted devices
## Fonts

### Using custom fonts

You can find free and non-free fonts on sites like dafont.com.

To avoid "moving text," choose a monospaced font. If you want to use this font to display time only, and want to be able to precisely align it on the screen, you can use a font creation program like FontForge to remove the space above and below reserved for characters like t or q. To do this, set Descent value to 0, and match the "BlueValues" to the Ascent value.

The resource compiler reads the font descriptor in text .fnt format, together with the .png image page(s). You can convert the font using BMFont or another BMFont-compatible generator. BMFont is a Windows application, but it runs fine under Wine on Linux.

Export your font in .ttf format from FontForge, then install it on your computer, or, if you are using BMFont with Wine, put it in the folder `~/.wine/drive_c/windows/Fonts`. Prior to export, ensure that BMFont's Font Settings specify the Unicode character set.
 
The generator produces two files:
- one .fnt metadata file
- one or more .png files (retro_0.png, retro_1.png, ...). BMFont splits characters across multiple pages when they don't all fit on a single texture, so larger character sets or bigger point sizes can produce several .png instead of just one.

Garmin expects font metadata such as:

```
info face="..." size=...
common lineHeight=... base=...
page id=0 file="retro_0.png"
chars count=...
char id=48 x=... y=... width=...
```
 
Put these files in the `fonts` folder (an optional subfolder is fine), then declare the font in `resources.xml`. The `filter` attribute is optional and restricts the font to the listed characters, which keeps the resource size down:
 
```xml
<font id="id_retro" filename="fonts/retro/retro.fnt" antialias="true" filter="0123456789"/>
```
## Garmin examples

Garmin code examples can be found in the `samples` folder downloaded with your SDK. This folder is located here:

```
~/.Garmin/ConnectIQ/Sdks/your-SDK/samples/Analog
```
## Garmin devices
### Garmin Venu 4 41 mm
- https://www.garmin.com/fr-CH/p/1613801/#specs
- screen size: round, 390 x 390 px

