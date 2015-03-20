# RTIMULib-Arduino - a versatile 9-dof and 10-dof IMU library for the Arduino

RTIMULib-Arduino is the simplest way to connect a 9-dof or 10-dof IMU to an Arduino (Uno or Mega) and obtain fully fused quaternion or Euler angle pose data.

Select the IMU in use by editing libraries/RTIMULib/RTIMULibDefs.h and uncommenting one of the supported IMUs like this:

	#define MPU9150_68                      // MPU9150 at address 0x68
	//#define MPU9150_69                      // MPU9150 at address 0x69
	//#define MPU9250_68                      // MPU9250 at address 0x68
	//#define MPU9250_69                      // MPU9250 at address 0x69
	//#define LSM9DS0_6a                      // LSM9DS0 at address 0x6a
	//#define LSM9DS0_6b                      // LSM9DS0 at address 0x6b
	//#define GD20HM303D_6a                   // GD20H + M303D at address 0x6a
	//#define GD20HM303D_6b                   // GD20H + M303D at address 0x6b
	//#define GD20M303DLHC_6a                 // GD20 + M303DLHC at address 0x6a
	//#define GD20M303DLHC_6b                 // GD20 + M303DLHC at address 0x6b
	//#define GD20HM303DLHC_6a                // GD20H + M303DLHC at address 0x6a
	//#define GD20HM303DLHC_6b                // GD20H + M303DLHC at address 0x6b

Once this has been done, all example sketches will build for the selected IMU.

To enable a pressure sensor, uncomment one of the following lines in libraries/RTIMULib/RTIMULibDefs.h:

	//#define BMP180                          // BMP180
	//#define LPS25H_5c                       // LPS25H at standard address
	//#define LPS25H_5d                       // LPS25H at option address
	//#define MS5611_76                       // MS5611 at standard address
	//#define MS5611_77                       // MS5611 at option address


The actual RTIMULib and support libraries are in the library directory. The other top level directories contain example sketches.

*** Important note ***
It is essential to calibrate the magnetometers or else very poor results will obtained, especially with the MPU-9150 and MPU-9250. If odd results are being obtained, suspect the magnetometer calibration! 

## The Example Sketches

### Build and run

To build and run the example sketches, start the Arduino IDE and use File --> Preferences and then set the sketchbook location to:

	.../RTIMULib-Arduino

where "..." represents the path to the RTIMULib-Arduino directory. The directory is set up so that there's no need to copy the libraries into the main Arduino libraries directory although this can be done if desired.

### ArduinoMagCal

This sketch can be used to calibrate the magnetometers and should be run before trying to generate fused pose data. It also needs to be rerun at any time that the configuration is changed (such as different IMU or different IMU reference orientation). Load the sketch and waggle the IMU around, making sure all axes reach their minima and maxima. The display will stop updating when this occurs. Then, enter 's' followed by enter into the IDE serial monitor to save the data.

### ArduinoIMU

ArduinoIMU is the main demo sketch. It configures the IMU based on settings in RTIMUSettings.cpp. Change these to alter any of the parameters. The display is updated only 3 times per second regardless of IMU sample rate.

Note that, prior to version 2.2.0, the gyro bias is being calculated during the first 5 seconds. If the IMU is moved during this period, the bias calculation may be incorrect and the code will need to be restarted. Starting at version 2.2.0 this is no longer a problem and gyro bias will be reported as valid after the required number of stable samples have been obtained.

### ArduinoIMU10

This is exactly the same as ArduinoIMU except that it adds support for a pressure sensor. One of the pressure sensors in libraries/RTIMULib/RTIMULibDefs.h must be uncommented for this sketch to run. It will display the current pressure and height above standard sea level in addition to pose information from the IMU.


### ArduinoAccel

This is similar to ArduinoIMU except that it subtracts the rotated gravity vector from the accelerometer outputs in order to obtain the residual accelerations - i.e. those not attributable to gravity.

### RTArduLinkIMU

This sketch sends the fused data from the IMU over the Arduino's USB serial link to a host computer running either RTHostIMU or RTHostIMUGL (whcih can be found in the main RTIMULib repo). Basically just build and download the sketch and that's all that needs to be done. Magnetometer calibration can be performed either on the Arduino or within RTHostIMU/RTHostIMUGL.

Check out www.richards-tech.com for more details, updates and news.

## Release history

Note that any older release can be obtained via the Releases tab on the repo's GitHub page.

### March 20 2015 - 2.8.1

Added support for the MS5611 pressure sensor. Also extended the MPU-9150 driver so that it can also support the MPU-6050.

### February 4 2015 - 2.7.1

RTArduLink now compiles for Leonardo.

### December 19 2014 - 2.7.0

Added support for pressure sensors.

Added the ability to disable specific sensors from the fusion algorithm - setGyroEnable(), setAccelEnable and setCompassEnable() have been included in RTFusionRTQF.


### December 9 2014 - 2.6.0

Added the L3GD20H + LSM303DLHC combination used by the updated Adafruit 10-dof IMU. Some small fixes to other drivers. 

### December 4 2014 - 2.5.0

Added the ability to change IMU orientation - see libraries/RTIMULib/RTIMULibDefs.h for more details. Also see http://wp.me/p4qcHg-cO for more details of how to use this capability.

Some minor fixes to IMU drivers.

### November 8 2014 - 2.4.0

Added support for the InvenSense MPU-9250 using the I2C interface.

### October 9 2014 - 2.3.1

Updated to include revised RTArduLink with new config EEPROM signature.

### October 8 2014 - 2.3.0

Added new example sketch RTArduLinkIMU. This is a stripped-down version of the software that is intended for use with the RTHostIMU and RTHostIMUGL apps in the RTIMULib repo. Basically, RTArduLinkIMU just collects the sensor data from the IMU chip and sends it to the connected host. Tests with the MPU9150 IMU have shown that up to 200 samples per second is possible. Sensor fusion is performed on the host rather than the Arduino. See the notes in the RTIMULib repo for more information on how to use RTArduLinkIMU with the host applications.

RTArduLinkIMU uses RTArduLink to connect to the host. The default configuration is correct but if there's any need to change or review, the RTArduLink repo has a sketch called RTArduLinkConfig that can be used to set things up. Check the documentation in the RTArduLink repo for more details.

### October 4 2014 - 2.2.0

Added support for the L3GD20 + LSM303DLHC IMU combo and the L3GD20H + LSM303D combo. As a result, there are an increased number of #defines in RTIMULibDefs.h, only one of which should be un-commented in order to select the IMU in use. By default, it now looks like:

	#define MPU9150_68                      // MPU9150 at address 0x68
	//#define MPU9150_69                      // MPU9150 at address 0x69
	//#define LSM9DS0_6a                      // LSM9DS0 at address 0x6a
	//#define LSM9DS0_6b                      // LSM9DS0 at address 0x6b
	//#define GD20HM303D_6a                   // GD20H + M303D at address 0x6a
	//#define GD20HM303D_6b                   // GD20H + M303D at address 0x6b
	//#define GD20M303DLHC_6a                 // GD20 + M303DLHC at address 0x6a
	//#define GD20M303DLHC_6b                 // GD20 + M303DLHC at address 0x6b

Changed the gyro bias calculation to run automatically when the IMU is detected as being stable. This means
that the IMU no longer needs to be kept still for 5 seconds and gyro bias is continually tracked. IMUGyroBiasValid can be called to check if enough stable samples have been obtained for a reasonable bias calculation to be made. If the IMU is stable, this will normally occur within 1 second. If it never indicates a valid bias, the #defines RTIMU_FUZZY_GYRO_ZERO and/or RTIMU_FUZZY_ACCEL_ZERO may need to be increased if the gyro bias or accelerometer noise is unusually high. These should be set to be greater than the readings observed when the IMU is completely stable.

Updated I2Cdev to the latest version.

### September 26 2014 - 2.1.0

Added a new sketch - ArduinoAccel. This shows how to subtract a rotated gravity vector using quaternions
in order to obtain the residual accelerations. It's basically used in exactly the same way as ArduinoIMU but
displays the residual accelerations instead. Note that there's no accel calibration at the moment so there 
will be some residual accelerations indicated that aren't real. Also, as usual (!), it's essential to perform
magnetometer calibration or else results will be useless.

### September 26 2014 - 2.0.0

There have been significant changes in this version (V2) to reduce the memory footprint. Instead of the previous
autodetection system, the IMU and address is now selected using a #define in RTIMULibDefs.h in the libraries
subdirectory. By default it looks like this:

	#define MPU9150_68                      // MPU9150 at address 0x68
	//#define MPU9150_69                      // MPU9150 at address 0x69
	//#define LSM9DS0_6a                      // LSM9DS0 at address 0x6a
	//#define LSM9DS0_6b                      // LSM9DS0 at address 0x6b

Change the commenting as required to select the device and address. All other functionality has remained the same. V1 is still available via the GitHub repo release tab.

### May 5 2014 - 1.0.0

Fixed bug in MPU-9150 compass initialization - changed incorrect writeBytes to readBytes to get fuse ROM data.

### April 22 2014 - 0.9.0

#### First release

This version supports the InvenSense MPU-9150 and STM LSM9DS0 single chip IMUs. The fusion filter is RTFusionRTQF.

(Pre-V2 only) The software will automatically discover the IMU type in use and also the address being used. This can be overridden in RTIMUSettings.cpp if desired.

