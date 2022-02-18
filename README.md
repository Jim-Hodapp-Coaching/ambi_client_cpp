# Ambi Client (C++)

An Edge IoT ambient room environment sensor implementation for a [Particle Argon](https://docs.particle.io/argon/) based on the [Particle IoT Air Quality Monitoring Kit](https://store.particle.io/products/air-quality-monitoring-kit-wi-fi).

This client pairs with and pushes readings to a [Ambi web backend instance](https://github.com/Jim-Hodapp-Coaching/ambi).

## Compiling this firmware using the Particle CLI

When you're ready to compile this firmware, make sure you have the [Particle CLI installed](https://docs.particle.io/tutorials/developer-tools/cli/) and also make sure you have the Particle Argon device target selected and run `particle compile <platform>` in the CLI. Note: if you're on an Arm-based Mac, the default Particle CLI install doesn't seem to work right. Work around this by doing a more manual install by running `sudo npm install -g particle-cli`.

Also make sure that you have an account registered with the Particle cloud and you've logged in using `particle setup`.

To build:
`particle compile argon --saveTo ambi_client_cpp.bin`

If building succeeds, you'll see output similar to:

```
Compiling code for argon

Including:
    src/air_purity_sensor.h
    src/base_sensor.h
    src/dust_sensor.h
    src/http_client.h
    src/humidity_sensor.h
    src/pressure_sensor.h
    src/temperature_sensor.h
    src/sensorchi.ino
    src/air_purity_sensor.cpp
    src/base_sensor.cpp
    src/dust_sensor.cpp
    src/http_client.cpp
    src/humidity_sensor.cpp
    src/pressure_sensor.cpp
    src/sensorchi.cpp
    src/temperature_sensor.cpp
    project.properties

attempting to compile firmware
downloading binary from: /v1/binaries/621006549bdb2a2cea6cc276
saving to: ambi_client_cpp.bin
Memory use:
   text    data     bss     dec     hex filename
  34080     128    2324   36532    8eb4 /workspace/target/workspace.elf

Compile succeeded.
Saved firmware to: /Users/jhodapp/Projects/cpp/ambi_client_cpp/ambi_client_cpp.bin
```

The following files in project folder will be sent to the cloud-hosted compile service:

- Everything in the `/src` folder, including the `.ino` application file
- The `project.properties` project definition file
- All libraries stored under `lib/<libraryname>/src`

## Flashing firmware onto Argon target device

To flash your intended Particle Argon target device with the built firmware, first identify the name of the device registered to the Particle cloud by listing all devices:

`particle list`

You should see a list similar to:

```
ambi1 [e00fce68834a01fd162abace] (Argon) is online
ambi2 [e00fce68d002c229a830d87f] (Argon) is offline
  Functions:
    int digitalread (String args) 
    int digitalwrite (String args) 
    int analogread (String args) 
    int analogwrite (String args) 
```

Note: in this example each target already has a specific name (e.g. ambi1) because it was changed via the Particle mobile app. By default you'll have a more generic device target name if using a new Argon from the factory.

Now flash the firmware to your particle device target like so:

`particle flash ambi1 ambi_client_cpp.bin`

Note: again, make sure you replace `ambi1` with your particular device name.

*If for some reason over-the-air (OTA) flashing fails, you can flash over USB like so:*

`particle flash --usb ambi_client_cpp.bin`

# Changing the firmware to point to your Ambi instance

An important step is to make sure that the firmware points to the local IP address of your Ambi web backend. To do that, open the source file `src/sensorchi.ino` and change one or both of the byte arrays `dev_server` or `prod_server`. Note: at this time, full domain or hostnames are not supported and is future work to improve this firmware once Ambi is fully cloud-hosted.

Also make sure that if you're using a the `dev_server` IP address that `#define DEV` is left defined. If you want to use `prod_server`, then just comment out the line `#define DEV`.

Now rebuild and reflash your firmware with it pointing to your local Ambi instance. One tip is to either dedicate a small computer like a Raspberry Pi to host the Ambi backend, or host it in a VM or bare on your development computer. Set your WiFi router to always issue your Ambi host the same IP address based on its MAC address. This way you won't have to keep changing the dev/prod IP address.
