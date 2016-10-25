# Raspberry Pi Adhan Clock
This project uses a python script which automatically calculates [adhan](https://en.wikipedia.org/wiki/Adhan) times every day and plays all five adhans at their scheduled time using cron. 

## Prerequisites
1. Raspberry Pi running Raspbian
  1. I would stay away from Raspberry Pi zero esp if you're new to this stuff since it doesn't come with a built in audio out port.
  2. Also, if you haven't worked with raspberry pi before, I would highly recommend using [these](https://www.raspberrypi.org/documentation/installation/noobs.md) instructions to get it up and running: https://www.raspberrypi.org/documentation/installation/noobs.md
2. Speakers
3. Auxiliary audio cable

## Instructions
1. Install git: Go to Raspberry Pi terminal (command line interface) and install `git`
  * `$ sudo apt-get install git`
2. Clone repo: Clone this repository in `home` directory. (Tip: run `$ cd ~` to go to your home directory)
  * `$ git clone git@github.com:achaudhry/adhan.git`
  * After doing that you should see an `adhan` direcotry in your `home` directory. 
3. Go into `adhan`: `$cd adhan`
4. Open `updateAzaanTimers.py` in your favorite editor. For instance, `nano` is a simple one: `$ nano updateAzaanTimers.py`

## Configuration
The original python script is super configurable. Please see the [manual](http://praytimes.org/manual) for advanced instructions. However, below are the three basic things you'll need to change to get it up and running.

* Set the latitude and longitude so it can calculate accurate prayer times for that location. You can use any online geocoding tool to get lat/long from an address e.g. [LatLong.net](http://www.latlong.net/convert-address-to-lat-long.html). Modify the following lines:
```
#Set latitude and longitude here
#--------------------
lat = 42.288788
long = -71.551678
```
* Set adhan time [calculation method](http://praytimes.org/manual#Set_Calculation_Method). Modify the following line:
```
PT.setMethod('ISNA')
```
* And finally, set the time-zone. In the following example it's set to Eastern time hence the `-5`. The `1` after `-5` is for daylight saving.
```
times = PT.getTimes((now.year,now.month,now.day), (lat, long), -5, 1) 
```

Save your changes by pressing `Control X` to exit and then `Y` to save changes.

## Run it for the first time
Run this command `$ python /home/pi/adhan/updateAzaanTimers.py`. If everythig worked, your output will look something like this:
```
03:56
12:53
16:52
20:13
21:49
<generator object find_command at 0x76a714e0>
56 3 * * * omxplayer -o local /home/pi/adhan/Adhan-Makkah-Fajr.mp3 > /dev/null 2>&1 # fajr
53 12 * * * omxplayer -o local /home/pi/adhan/Adhan-Makkah.mp3 > /dev/null 2>&1 # dhuhr
52 16 * * * omxplayer -o local /home/pi/adhan/Adhan-Makkah.mp3 > /dev/null 2>&1 # asr
13 20 * * * omxplayer -o local /home/pi/adhan/Adhan-Makkah.mp3 > /dev/null 2>&1 # maghrib
49 21 * * * omxplayer -o local /home/pi/adhan/Adhan-Makkah.mp3 > /dev/null 2>&1 # isha
0 1 * * * python /home/pi/adhan/updateAzaanTimers.py >> /home/pi/adhan/adhan.log 2>&1
@monthly truncate -s 0 /home/pi/adhan/adhan.log 2>&1
Script execution finished at: 2016-07-24 14:59:16.021888
```

If you look at the last 8 lines, you'll see that 5 adhan times have been scheduled. Then there is another line at the end which makes sure that at 1am every day the same script will run and calculate adhan times for that day. And lastly, there is a line to clear logs on a monthly basis so that your log file doesn't grow too big.

VOILA! You're done!! Plug in your speakers and enjoy!

## Tips:
1. You can see your currently scheduled jobs by running `crontab -l`
2. The output of the job that runs at 1am every night is being captured in `/home/pi/adhan/adhan.log`. This way you can keep track of all successful runs and any potential issues. This file will be truncated at midnight on first day of each month. To view the output type `$ cat /home/pi/adhan/adhan.log`

## Diclaimer
I can't take much credit for this. Most of the code I've used here is written by other developers (mentioned below). I've only cleaned up some pieces and have put plumming in place so that people can get this up and running with minimal effort. 

### Credits
I have made minor modifications / bug fixes but I've used the following as starting point:
* Python code to calculate adhan times: http://praytimes.org/code/ 
* Basic code to turn the above into an adhan clock: http://randomconsultant.blogspot.co.uk/2013/07/turn-your-raspberry-pi-into-azaanprayer.html
* Cron scheduler: https://pypi.python.org/pypi/python-crontab/ 
