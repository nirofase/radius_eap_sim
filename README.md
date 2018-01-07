# radius_eap_sim

Introduction
EAP Subscriber Identity Module (EAP-SIM) use the subscriber identity module (sim) from Global System for Mobile Communications (GSM) for authentication and session key distribution. Also EAP-SIM can be used in a WPA enterprise network (WPA-802.1X). Our goal is to obtain access to a secure WiFi through our smartphone without a password by using EAP-SIM authentication method.  Our implementation consists of a freeradius server, an access point (netfaster 3) and an android 5.1 smartphone. 
 
Implementation Scheme

Authentication Triplets
In EAP-SIM the communication between the SIM card and the Authentication Centre (AuC)replaces the need for a pre-established password between the client and the AAA server. The AuC generates the authentication triplets (RAND, Kc, SRES), the RAND variable transferred in the sim card and in combination with the secret key Ki generates the SRES and the Kc using the A3 and A8 algorithms respectively. In order to authenticate with EAP-SIM we have to extract the authentication triplets from the sim card as we aren't a mobile network operator to have an AuC which knows the secret key Ki and generates the authentication triplets. The triplet consists of a key Kc, a random variable RAND and a variable SRES. We extracted the triplets with Osmocom Simtrace hardware board by reading the APDU communication but it's easier to use a smart card reader instead. The triplets should be hard-coded into freeradius server in order to authenticate the smartphone.
 
Osmocom Simtrace sample output
Configuration Files
users 
In this file, for every IMSI we should put at least three authentication triplets. The first line in the bellow image is made up of the IMSI (12XXXXX18), the Mobile Network Code (mnc), 000 is our provider ‘s code, the Mobile Country Code (mcc), 202 is our country ‘s code and the EAP type which is used (SIM).
 

eap
In this file we should define sim as the default EAP type.
default_eap_type = sim 
default
The main configuration file that calls the other files. 
 
We forced to insert the above lines, as the variable User-Name we were receiving from the AP, hasn’t the ending “org”, which exists in the variable Identity as we can see bellow.
 
And the freeradius server were terminating with the bellow error.
 
The next change we made in this file was to put under the files command the above code that will read the authentication triplets which correspond in the user ‘s IMSI who tries to connect.
 

clients.conf
In this file we should put the AP ‘s IP and a secret key which is shared between the radius server and the AP.
 
After that the only thing we have to do is to configure the AP to use 802.1X and the smartphone to use SIM as the EAP method when it tries to connect in the WiFi.
Router Configuration
 
 
Proof of Concept
Device that supports EAP-SIM authentication (Moto G Android 5.1 Smartphone in this case)
  



Security Considerations
IMSI leakage
Someone can see the users IMSI during the authentication process by simply monitoring the traffic.
 
 
