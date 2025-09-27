
# Installation der Open Bike Dashcam auf dem Raspberry Pi 5

Das CM5 hat kein Display. Daher erfolgt die Installation ohne direkt angeschlossenes Display. 

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
13) Kamera einrichten:
  
    13.1 Kameraerkennung aktivieren:  
        `sudo nano /boot/firmware/config.txt` (Datei wird geöffnet)  
  
    13.2 folgende Zeile suchen:  
        `camera_auto_detect=1`  
        diese Zeile löschen oder auskommentieren und statt dessen folgendes eingeben:  
        
        ```
        start_x=1
        camera_auto_detect=0
        dtoverlay=imx708,cam1
        ```
        
    13.3 danach strg-o, Enter, strg-x (speichern, schließen)  
    13.4 pi neu starten  
    13.5 Kameras anzeigen lassen
		   libcamera-hello --list-cameras
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
		    libcamera-hello

       hier sollte sich kurz ein live-Vorschaubild der Kamera öffnen
	
 15) RGPIO installieren (in der venv) 
		   pip install rpi-lgpio
15)weitere Pakete installieren
		15.1 sudo apt-get install python3-libcamera python3-kms++  (Standard Terminal Fenster ohne venv)  
    15.2 sudo apt-get install libcamera-dev (außerhalb der venv)
	  15.3  pip install rpi-libcamera  (in der venv - kann lange dauern)

# Sensor xm125 installieren
5) qwiic anschließen

| Farbe   | Funktion | Pin [Board nummeriert] |
| ------- | -------- | ---------------------- |
| schwarz | GND      | Pin  6                 |
| Rot     | 5V       | Pin 4                  |
| Blau    | SDA      | Pin 3 (GPIO2)          |
| Gelb    | SCL      | Pin 5 (GPIO3)          |
2) ic2 aktivieren
   sudo raspi-config --> interface options -> i2c
3) i2c tools installieren
   sudo apt update
   sudo apt install -y i2c-tools
4) prüfen, ob der Sensor erkannt wird
   i2cdetect -y 1
   --> sollte Adresse 0x52 erkennen
5) optional: falls man mit dem Sensor und der Arudino IDE rumspielen möchte ARduino IDE installieren
   ```
   sudo apt update
   sudo apt install arduino
   ```
6) Venv anlegen
   python3 -m venv --system-site-packages venv
7) venv aktivieren
   source venv/bin/activate
8) sudo apt install python3-pip
   pip3 install smbus2
## Sensor Einrichten für Distance_Detector
1) https://pypi.org/project/acconeer-exptool/ herunterladen
2) Entpacken
3) update.bat ausführen (dauert lange)
4) run.bat ausführen - exploration Tool startet
5) auf reiter flash wechseln (links)
6) Sensor vorbereiten: Boot gedrückt halten - reset kurz drücken - boot loslassen
7) com port des sensors aus gerätemanager suchen (z.B. com7)
    Falls der Sensor beim Einstecken **nicht als "USB Serial Port"** im **Geräte-Manager** auftaucht, installiere den passenden **CH340-USB-Treiber**:
    
    - [SparkFun CH340 Treiber Download](https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/all)
        

8) aus dem sdk-ordner-out die i2c_detector_distance.bin Datei suchen
9) im browse menü die .bin datei auswählen
10) Sensor einstellen: und auf flashen drücken --> firmware wird auf den sensor geladen
11) reset button 1x drücken
12) sensor vom strom nehmen. beim neustart startet die neue firmware
