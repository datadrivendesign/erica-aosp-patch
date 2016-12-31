ERICA AOSP Patch
=====

A patch to create the custom Android build used in the Erica project. This build allows capturing UI Hierarchy data with low latency on Nexus 6 devices. The included patch should be applied to the platforms/base repository of the AOSP source code intended for the Nexus 6 device (android-5.1.0_r3).

Publications
------------

* Biplab Deka, Zifeng Huang, and Ranjitha Kumar. 2016. [ERICA: Interaction Mining Mobile Apps](http://ranjithakumar.net/resources/deka-uist2016-erica.pdf). In Proceedings of the 29th Annual Symposium on User Interface Software and Technology (UIST '16). ACM, New York, NY, USA, 767-776. DOI: https://doi.org/10.1145/2984511.2984581

## Usage

### Setting Up the Build Environment
Instructions on the [AOSP website](https://source.android.com/source/initializing.html).

### Downloading AOSP source
```bash
mkdir ~/<dir name of your choice>
cd ~/<dir name of your choice>
repo init -u https://android.googlesource.com/platform/manifest -b android-5.1.0_r3
repo sync
```

### Patching the source files
```bash
cd frameworks/base
patch -p1 < <path to erica_patch.diff>
```

### Building and flashing
Follow the instructions on the AOSP website for [building the Android source](https://source.android.com/source/building.html) (needs downloading binaries for Nexus 6) and [flashing the build to a device](https://source.android.com/source/running.html).

### Getting view hierarchies
Once the device is successfully flashed, connect it using USB (make sure USB debugging is enabled and that it responds to adb commands).

* Start the custom view server on the phone.
```bash
adb shell dumpsys activity current-snap
```
* Use ADB to forward a port on your local machine to port 1679 on the phone.
```bash
adb forward tcp:<port> tcp:1679
```
* Connect to the local port using TCP. The example below uses a Linux utility
called nc.
```bash
nc localhost <port>
```
* Send the String ```a<identifier>\n``` to request a dump of the view hierarchy. Each response will contain the view hierarchy in XML format along with metadata at the beginning of the response. Each response begins with ```DDDG_XML_Serial:<identifier>```.

* Send the String ```c\n``` to stop the server.

* Alternatively, the command below stops the custom view server on the phone.
```bash
adb shell dumpsys activity current-cancel
```