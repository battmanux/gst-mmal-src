This fork adds a few option such as vertiacal / horizontal flip.
It is done by reading source code from [Raspicam](https://github.com/raspberrypi/userland/blob/master/host_applications/linux/apps/raspicam/RaspiCamControl.c)

# Camera acquisition on Raspberry Pi, shutter, ISO and exposure controls and sink video/x-raw in GStreamer pipeline

**GStreamer element : *gstmmalsrc***

This element gets frames from camera using MMAL API.


### How to install GStreamer

First you have to install required packages. If you use Debian 6, you can
install them with the provided script

`./scripts/install_prereq.sh`

Otherwise, look for each package in your package manager and install them.

### How to compile the project

First make sure you have the *cmake* package installed on your system.

To compile the *gstplugins*, you can run these commands :

```
source scripts/env.sh
rm -rf build
mkdir -p build
cd build
cmake ..
make
```

Then you should see the plugin `libgstmmal.so` in your build directory.
You can check that this is a correct GStreamer plugin

`gst-inspect-1.0 ${PWD}/libgstmmal.so`

### How to install the plugin

To install it with other plugins

`cp libgstmmal.so /usr/lib/arm-linux-gnueabihf/gstreamer-1.0/`

To force detection/parsing of GStreamer plugin, you need to delete the cache.
Note that the name of the registry may change depending on your system.

`rm ~/.cache/gstreamer-1.0/registry.arm.bin`

Then you can check that the plugin is usable

`gst-inspect-1.0 mmalsrc`

### How to use the plugin

The *mmalsrc* is a source element. You can test it with gst-launch

```
source scripts/env.sh
gst-launch-1.0 mmalsrc shutter-activation=on shutter-period=10000 exposure=on ISO=400 \
    ! video/x-raw,format=RGBA,width=640,height=360,framerate=30/1 \
	! videoconvert \
    ! fbdevsink
```

To display debug message from this element, use the environment variable

`export GST_DEBUG="mmalsrc:5"`
