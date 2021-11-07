
**PMIC Version Check and Update**

You can now check the version of your PMIC programming and update to the latest version if
you want.

You Can Also See [Chapter 14]()

But before, you should be aware of : 

**WARNING! WARNING! WARNING!**

The Infineon PMICs only have 26 programming locations. Each time the devices are
reprogrammed, another location is consumed. Use with caution!

If you accidentally program a device with the wrong file (like the 0x14 device with the 0x13 file or
vice versa), you WILL damage your board, and it will NOT be covered under warranty. You
must program the correct file to the correct device.

**Permanent damage will occur otherwise**

1.First you should download [this image](http://avnet.me/ultra96-v2-oob), then flash sd-card with it.

2.PMIC Version Checking

```
# cd ~/pmic-prog
# ls -l pmic-configs/
```

The naming convention for the files is as follows.
<board>_<device>_<channel>_<datecode in YYMMDD>
<board> U96V2
<device> 5401 or 38060
<channel> 0x13, 0x14, or 0x15
<datecode>
191030 = 2019, October 30
  
3. Enter the ‘detect’ command on I2C bus 6
  ```
# ./pmic_prog detect 6
  ```
  
4. For the Rocky/5401 devices (0x13 and 0x14) a date code is stored at locations 0x2A
and 0x2B for each PMIC. You can see which revision you have by reading these two
registers. 0x2A has the day and 0x2B has the month of release.
  
  ```
  
  # ./pmic_prog read 6 0x13 0x002A
# ./pmic_prog read 6 0x13 0x002B
# ./pmic_prog read 6 0x14 0x002A
# ./pmic_prog read 6 0x14 0x002B
  
  ```
  
5.To perform a comparison against an existing configuration file, use the read-registers
command with an input file, as follows:
  
  ```
  
  # ./pmic_prog read-registers 6 0x13 -i ./pmic-configs/U96V2_5401_0x13_191030.txt
# ./pmic_prog read-registers 6 0x14 -i ./pmic-configs/U96V2_5401_0x14_191030.txt
# ./pmic_prog read-registers 6 0x15 -i ./pmic-configs/U96V2_38060_0x15_190301.txt
  ```
  
6. PMIC Reprogramming **WARNING!!!**
  
  To reprogram the 0x13 device, enter the following command, making sure that the two
highlighted numbers match!
  
 ``` 
# ./pmic_prog program 6 0x13 -i ./pmic-configs/U96V2_5401_0x13_191030.txt
  
  ```
  
  To reprogram the 0x14 device, enter the following command, making sure that the two
highlighted numbers match!
  ```
# ./pmic_prog program 6 0x14 -i ./pmic-configs/U96V2_5401_0x14_191030.txt
  
  ```
  
