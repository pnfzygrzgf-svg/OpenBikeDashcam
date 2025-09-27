
# Installation der Open Bike Dashcam auf dem Raspberry Pi 5

Das CM5 hat kein Display. Daher erfolgt die Installation ohne direkt angeschlossenes Display. 

# Raspberry Pi einrichten 
1) OS Full 64-bit (inkl. Desktop) mit dem Raspberry Pi Imager installieren https://www.raspberrypi.com/software/
2) Pi Starten, warten bis er sich mit dem Wlan verbunden hat und z.B. über die FritzBox Weboberfläche die IP des Pi finden.
3) über putty oder ssh in Windows mit Pi verbinden (hierfür die IP des Pi notwendig)
4) VNC aktivieren:  
    4.1 `sudo raspi-config`  
    4.2 Interface -> VNC -> yes  
5) Rasperry neu starten. mit RealVC oder TigerVNC den auf die Weboberfläche zugreifen
6) alle updates installieren  (Terminal öffnen):  
   `sudo apt-get update`
7) notwendige Systempakete installieren:  
`sudo apt install -y python3-pip python3-dev python3-venv libopenblas-dev liblapack-dev libatlas-base-dev gfortran libjpeg-dev libtiff-dev libpng-dev libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libx264-dev libx265-dev libgtk2.0-dev libcanberra-gtk3-module libblas-dev libhdf5-dev libimath-dev libopenexr-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libx11-dev libxext-dev libxtst-dev libgl1-mesa-glx libglu1-mesa-dev`  
8) `sudo apt-get install libavformat-dev libavcodec-dev libavdevice-dev libavutil-dev libavfilter-dev libswscale-dev libswresample-dev`
9) virtuelle Umgebung (venv) im Projektordner anlegen:  
	   `python3 -m venv --system-site-packages venv`
10) virtuelle Umgebung aktivieren:  
	   `source venv/bin/activate`
11) Pip aktualisieren:  
	   `pip install --upgrade pip`

ab hier: alles, was mit "pip" startet passiet im Terminal, in dem die Venv aktiv ist. Parallel zweites Terminal ohne Venv öffnen. Dort wird alles andere insalliert.

12) notwendige Python Bibliotheken installieren:  
    in dieser Reihenfolge nacheinander:  
      `pip install numpy`  
      `pip install opencv-python`  
      `sudo apt install libcap-dev`  (im Standard Terminal)  
      `pip install picamera2 lgpio RPi.GPIO`
## Kamera einrichten
14) Kamera einrichten:
  
    13.1 Kameraerkennung aktivieren:  
        `sudo nano /boot/firmware/config.txt` (Datei wird geöffnet)  
  
    13.2 folgende Zeile suchen:  
        `camera_auto_detect=1`  
        diese Zeile löschen oder auskommentieren und statt dessen folgendes eingeben:  
        
        start_x=1
        camera_auto_detect=0
        dtoverlay=imx708,cam1
        
    13.3 danach strg-o, Enter, strg-x (speichern, schließen)  
    13.4 pi neu starten  
    13.5 Kameras anzeigen lassen
    	`libcamera-hello --list-cameras`  
		Ausgabe sollte wie folgt sein: 
		   
		   pi@RaspberryCM5:~ $ libcamera-hello --list-cameras
		   Available cameras
		   -----------------
		   0 : imx708_wide [4608x2592 10-bit RGGB
		   (/base/axi/pcie@1000120000/rp1/i2c@70000/imx708@1a)
		   Modes: 'SRGGB10_CSI2P' : 1536x864 [120.13 fps - (768, 432)/3072x1728 crop]
                             2304x1296 [56.03 fps - (0, 0)/4608x2592 crop]
                             4608x2592 [14.35 fps - (0, 0)/4608x2592 crop]

    13.6 Kamera testen
		    `libcamera-hello`

       hier sollte sich kurz ein live-Vorschaubild der Kamera öffnen  
	
 15) RGPIO installieren (in der venv):
     14.1 Venv wieder aktivieren: `source venv/bin/activate`
		   `pip install rpi-lgpio`  
 15)weitere Pakete installieren:
	15.1 `sudo apt-get install python3-libcamera python3-kms++`  (Standard Terminal Fenster ohne venv)
    15.2 `sudo apt-get install libcamera-dev` (außerhalb der venv)
	15.3  `pip install rpi-libcamera`  (in der venv - kann lange dauern)

## Sensor Einrichten
16) Sensor XM125 einrichten:  
	16.1 anschließen wie folgt
    
	| Farbe   | Funktion | Pin [Board nummeriert] |
	| ------- | -------- | ---------------------- |
	| schwarz | GND      | Pin 6                  |
	| Rot     | 5V       | Pin 4                  |
	| Blau    | SDA      | Pin 3 (GPIO2)          |
	| Gelb    | SCL      | Pin 5 (GPIO3)          |  
  
	16.2 ic2 aktivieren:  
		sudo raspi-config --> interface options -> i2c  
	16.3 i2c tools installieren:  
		`sudo apt install -y i2c-tools`  
	16.4 prüfen, ob der Sensor erkannt wird:  
		`i2cdetect -y 1`  
   		--> sollte Adresse 0x52 erkennen  
	16.5 (optional): falls man mit dem Sensor und der Arudino IDE rumspielen möchte ARduino IDE installieren

		sudo apt update
		sudo apt install arduino


      16.5.1 pip3 install smbus2
   
## Sensor Einrichten für Distance_Detector
unter Windows den Sensor per USB anschließen:  
	16.6 https://pypi.org/project/acconeer-exptool/ herunterladen  
	16.7 Entpacken  
	16.8 update.bat ausführen (dauert lange)  
	16.9 run.bat ausführen - exploration Tool startet  
	16.10 auf reiter "flash" wechseln (links)  
	16.11 Sensor vorbereiten: Boot gedrückt halten - reset kurz drücken - boot loslassen  
	16.12 com-port des sensors aus gerätemanager suchen (z.B. com7)     
    	Falls der Sensor beim Einstecken **nicht als "USB Serial Port"** im **Geräte-Manager** auftaucht, installiere den passenden **CH340-USB-Treiber**: https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/all  
	16.13 aus dem sdk-ordner-out die i2c_detector_distance.bin Datei suchen 
	16.14 im browse menü die .bin datei auswählen  
	16.15 Sensor einstellen und auf flashen drücken --> firmware wird auf den sensor geladen  
	16.16 reset button 1x drücken  
	16.17 Sensor vom strom nehmen. beim Neustart startet die neue Firmware

# Dashcam Software einrichten (auf dem Raspberry):  
17) die folgenden Dateien/Ordner in den Ordner home/pi/venv verschieben:

    - Dashcam.py  
    - gps_receiver.py  
    - den Ordner "static"

19) den Ordner "settings" auf den USB Stick verschieben (ohne Unterverzeichnis)
20) Testen den Programms:
    20.1 venv aktivieren:

		source venv/bin/activate

	20.2 Programm starten:

		python3 venv/Dashcam.py

21) Datei im Autostart hinterlegen:  
	21.1 über folgenden Befehl einen sudo Dateiexplorer öffnen:  

		sudo pcmanfm

	21.2 dann die Datei "Dashcam.service" in den Ordner "/etc/systemd/system/" kopieren  
    21.3 Service-Datei aktivieren:
    
	- `sudo systemctl daemon-reload`   
	- `sudo systemctl enable Dashcam.service`  
	- optional: (Zum sofortigen Starten des Programms) `sudo systemctl start Dashcam.service`
 	- optional: (zum stoppen des Programms) `sudo systemctl stop Dashcam.service`

#RTC einrichten:
22) RTC einrichten:  
	22.1 `sudo nano /boot/firmware/config.txt` öffnen
	22.1 dort die folgende Zeil einfügen: 
	
		dtparam=rtc_bbat_vchg=3000000
	
