# LSC_Smart-Connect_Indoor_Camera_Hack
Hack to enable the ONVIF (and RTSP) on the LSC Smart connect indoor camera from Action

## Tuto de base 
https://github.com/guino/BazzDoorbell/issues/2

## Rooter la caméra
- Placer le ficher [ppsFactoryTool.txt](https://github.com/n3odym3/LSC_Smart-Connect_Indoor_Camera_Hack/blob/main/ToRoot) dans une carte SD (FAT32)
- Setup le SSID et PASSWORD du WiFi
- Placer la carte SD dans la caméra
- Lancer la caméra
- Trouver son adresse ip avec [ONVIF Device Manager](https://sourceforge.net/projects/onvifdm/)
- Réaliser les requêtes HTTP Suivantes
  - http://admin:admin@CAMERA.IP:8090/devices/deviceinfo => Réponse : ppstrong-a3-tuya2_lsc-4.0.6.20210311
  - http://admin:admin@CAMERA.IP:8090/proc/cmdline => Sauvegarder réponse
  - http://admin:admin@CAMERA.IP:8090/proc/self/root/etc/init.d/S90PPStrong => S'assurer que MTDNUM=5 est bien décommenté (sans #)
- Si tout est OK éteindre la caméra et récupérer la carte SD
- Placer les fichier de [ToRoot](https://github.com/n3odym3/LSC_Smart-Connect_Indoor_Camera_Hack/blob/main/ToRoot) sur la SD
  - env
  - initrun.sh
  - ppsMmcTool.txt
- Placer la SD dans la caméra
- Appuyer sur le boutton reset
- Brancher la caméra (en gardant le boutton reset enfoncé)
- Maintenir le boutton enfoncé pendant 5 secondes (la caméra devrait clignotter entre rouge et bleu)
- La caméra devrait ensuite booter
- Tester si le hack a fonctionné avec la requête HTTP suivante 
  - http://admin:admin@CAMERA.IP:8090/proc/self/root/mnt/mmc01/hack => Réponse : done
 
## Hacker la caméra

- Placer les fichier de ToHack sur le carte SD
  - cgi-bin
  - busybox
  - custom.sh
  - dropbearmulti
  - httpd.conf <-- password http
  - index.html
  - jpeg-arm
  - passwd <-- password telnet
  - set
  - upload.html
- Copier le fichier ppsapp présent dans /home/app
- Sassurer que le md5 correspond à celui présent [ici](https://github.com/guino/ppsapp-rtsp/issues/1) (chercher ppstrong-a3-tuya2_lsc-4.0.6.20210311) 
- Aller sur https://www.marcrobledo.com/RomPatcher.js/
- Sélectionner le fichier ppsapp
- Sélectionner le patch téléchargé [ici](https://github.com/guino/ppsapp-rtsp/files/6880255/ppsapp-onvif.zip)
- Renommer le fichier ppsapp.txt téléchargé après le patch en ppsapp
- Placer le fichier ppsapp patché à la racine de la carte SD
- Placer la carte SD dans la caméra et brancher la caméra
- La caméra devrait booter deux fois !
- Bravo la caméra est patchée
- Essayer d'ouvrir le flux RTSP dans ONVIF Device Manager

## telnet
for telnet access either set the password hash in passwd file (get a hash ( https://unix4lyfe.org/crypt/ ) usin the calculate 'DES' button and copy the value)

## Cas particulier
ppstrong-a3-tuya2_lsc-5.2.4.20211015	M16S_A2_V10_MIS	ea82f8dee86047a82404b9bdc715ae75	Mini 16S
This firmware does NOT need patching to use ONVIF/RTSP 

To recreate, I followed all the steps until patching ppsapp then did the following:

telnet into camera
modify /home/cfg/tuya_config.json
(a) change onvif_enable=0 to onvif_enable=1
(b) change onvif_pwd to the password you wish (plaintext)
run the following command:
set onvif_enable 1
reboot by running the reboot command
That's it, once it rebooted you can access the rtsp stream.

FLUX HD => rtsp://admin:admin@IP:8554/Streaming/Channels/101
FLUX SD => rtsp://admin:admin@IP:8554/Streaming/Channels/102
