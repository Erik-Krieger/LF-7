# Workshop - Temperature Sensor
## Step 1: Setup this Circuit
Connect the circuit as display below:
- the **red** wire is connect to **3.3V**
- the **black** wire is connected to **ground**
- the **blue** wire is connected to **GPIO 4**
- the **resistor** has a value of **4.7kΩ**
![image](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2023/07/raspberry-pi-ds18b20-wiring_bb.png?w=1090&quality=100&strip=all&ssl=1)
## Step 2: Enable the 1Wire Protocol
Execute the following command
```bash
sudo raspi-config
```
Select **Interface Options**
![image](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2023/07/rpi-interface-options.png?w=661&quality=100&strip=all&ssl=1)
Select **1-Wire**
![image](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2023/07/1-wire-rpi-configuration-tool.png?w=661&quality=100&strip=all&ssl=1)
And **Enable** it
![image](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2023/07/enable-1-wire-configuration-tool-rpi.png?w=661&quality=100&strip=all&ssl=1)
Finally, select **Finish**, and then **reboot** the Raspberry Pi.
![image](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2023/07/reboot-rpi-configuration-tools.png?w=661&quality=100&strip=all&ssl=1)
## Step 3:  Create a new file, import the following code and let it run
```python
import os
import glob
import time

os.system('modprobe w1-gpio')
os.system('modprobe w1-therm')

base_dir = '/sys/bus/w1/devices/'
one_wire_folder = glob.glob(base_dir + '28*')
if len(one_wire_folder) == 0:
	print("The 1-Wire bus is not enabled or the circuit is not connected")
	exit(1) 
device_folder = one_wire_folder[0]
device_file = device_folder + '/w1_slave'

def read_temp_raw():
	with open(device_file, 'r') as f:
		lines = f.readlines()
	return lines

def read_temp():
	lines = read_temp_raw()
	while lines[0].strip()[-3:] != 'YES':
		time.sleep(0.2)
		lines = read_temp_raw()
		
	equals_pos = lines[1].find('t=')

	if equals_pos != -1:
		temp_string = lines[1][equals_pos+2:]
		temp_c = float(temp_string) / 1000.0
		temp_f = temp_c * 9.0 / 5.0 + 32.0
		return f"{temp_c:.2f}°C, {temp_f:.2f}°F, {temp_c + 273.15:.2f}K"

while True:
	print(read_temp())
	time.sleep(1)
```
