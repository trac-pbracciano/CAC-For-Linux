# These are instructions on how to have your CAC working on Ubuntu
*These instructions have only been used on Ubuntu v 22.04 LTS using Google Chrome. I believe that you are able to get this working on Firefox but since Firefox is installed with snap, it isn't configured properly. There is an issue with adding a security device on Firefox* https://bugzilla.mozilla.org/show_bug.cgi?id=1734371

### Here are the list of required certificates that need to be installed
- DoD Root CA 3
- DoD Root CA 4
- DoD Root CA 5
- DoD Root CA 6

**EMAIL CERTIFICATES**
|   **Subject**   |   **Issuer**  |
| --------------- | ------------- |
| DOD EMAIL CA-59 | DoD Root CA 3 |
| DOD EMAIL CA-62 | DoD Root CA 3 |
| DOD EMAIL CA-63 | DoD Root CA 3 |
| DOD EMAIL CA-64 | DoD Root CA 3 |
| DOD EMAIL CA-65 | DoD Root CA 3 |
| DOD EMAIL CA-70 | DoD Root CA 6 |
| DOD EMAIL CA-71 | DoD Root CA 3 |
| DOD EMAIL CA-72 | DoD Root CA 6 |
| DOD EMAIL CA-73 | DoD Root CA 6 |


**ID CERTIFICATES**
| **Subject**  |   **Issuer**  |
| ------------ | ------------- |
| DOD ID CA-59 | DoD Root CA 3 |
| DOD ID CA-62 | DoD Root CA 3 |
| DOD ID CA-63 | DoD Root CA 3 |
| DOD ID CA-64 | DoD Root CA 3 |
| DOD ID CA-65 | DoD Root CA 3 |
| DOD ID CA-70 | DoD Root CA 6 |
| DOD ID CA-71 | DoD Root CA 3 |
| DOD ID CA-72 | DoD ROOT CA 6 |
| DOD ID CA-73 | DoD ROOT CA 6 |

**SW CERTIFICATES**
| **Subject**  |   **Issuer**  |
| ------------ | ------------- |
| DOD SW CA-60 | DoD ROOT CA 3 |
| DOD SW CA-61 | DoD ROOT CA 5 |
| DOD SW CA-66 | DoD ROOT CA 3 |
| DOD SW CA-67 | DoD ROOT CA 3 |
| DOD SW CA-68 | DoD ROOT CA 5 |
| DOD SW CA-69 | DoD ROOT CA 5 |
| DOD SW CA-74 | DoD ROOT CA 6 |
| DOD SW CA-75 | DoD ROOT CA 3 |
| DOD SW CA-76 | DoD ROOT CA 5 |
| DOD SW CA-77 | DoD ROOT CA 5 |


### First is to install the middleware

```bash
sudo apt-get install libpcsclite1 pcscd pcsc-tools
```

### Verify the OS is able to communicate with your CAC reader

```bash

sudo systemctl start pcscd

sudo systemctl enable pcscd
```

#### Plug in your CAC card

```bash
pcsc_scan
```

You should see something like this

```bash
pcsc_scan

Using reader plug'n play mechanism
Scanning present readers...
0: Virtual PCD 00 00
1: Virtual PCD 00 01
2: SCR3310 Smart Card Reader [CCID Interface] (53311514222847) 00 00
 
Tue Jul 18 11:27:47 2023
 Reader 0: Virtual PCD 00 00
  Event number: 0
  Card state: Card removed, 
 Reader 1: Virtual PCD 00 01
  Event number: 0
  Card state: Card removed, 
 Reader 2: SCR3310 Smart Card Reader [CCID Interface] (53311514222847) 00 00
  Event number: 0
  Card state: Card inserted, 
  ATR: 3B 7D 96 00 00 80 31 80 65 B0 75 49 17 0F 83 00 90 00

ATR: 3B 7D 96 00 00 80 31 80 65 B0 75 49 17 0F 83 00 90 00
+ TS = 3B --> Direct Convention
+ T0 = 7D, Y(1): 0111, K: 13 (historical bytes)
  TA(1) = 96 --> Fi=512, Di=32, 16 cycles/ETU
    250000 bits/s at 4 MHz, fMax for Fi = 5 MHz => 312500 bits/s
  TB(1) = 00 --> VPP is not electrically connected
  TC(1) = 00 --> Extra guard time: 0
+ Historical bytes: 80 31 80 65 B0 75 49 17 0F 83 00 90 00
  Category indicator byte: 80 (compact TLV data object)
    Tag: 3, len: 1 (card service data byte)
      Card service data byte: 80
        - Application selection: by full DF name
        - EF.DIR and EF.ATR access services: by GET RECORD(s) command
        - Card with MF
    Tag: 6, len: 5 (pre-issuing data)
      Data: B0 75 49 17 0F
    Tag: 8, len: 3 (status indicator)
      LCS (life card cycle): 00 (No information given)
      SW: 9000 (Normal processing.)

Possibly identified card (using /usr/share/pcsc/smartcard_list.txt):
3B 7D 96 00 00 80 31 80 65 B0 75 49 17 0F 83 00 90 00
3B 7D .. 00 00 80 31 80 65 B0 .. .. .. .. 83 .. 90 00
	IDClassic 3XX / Classic TPC (IXS, IS, IS V2, IS CC, IM, IM CC, IM CC V3) / MultiApp ID Cards
```


### Install the PKCS#11 interface

```bash
sudo apt-get install opensc
```

### Download the DoD Certificates

To find the most recent DoD PKI CA's I recommend to go to https://public.cyber.mil/pki-pke

As of 18 JUL 2023, these are the latest PKI CA's -> https://dl.dod.cyber.mil/wp-content/uploads/pki-pke/zip/unclass-certificates_pkcs7_DoD.zip

### Install the DoD Certificates

- Open up Google Chrome
- Click the three dots and click Settings
- On the left side click **Privacy and security**
- Click Security -> Manage device certificates
- Select Authorities and then Import
- Make sure to switch the viewing option to show All Files
- Go to your unzipped folder of the certificates and select the certificates_pkcs7_\<VERSION>\_dod_der.p7b

### Configure Google Chrome

**Make sure you are in your home directory!!**

```bash
modutil -dbdir sql:.pki/nssdb/ -add "CAC Module" -libfile /usr/lib/x86_64-linux-gnu/opensc-pkcs11.so
```

IF YOU MAKE ANY CHANGES WITH ADDING/REMOVING CERTIFICATES WITHIN THE GOOGLE CHROME SETTINGS, YOU HAVE TO RUN THE `modutil` COMMAND AGAIN.

### See if everything actually works :crossed_fingers:

Go to https://ohome.apps.mil and try to sign in using your CAC. The only difference is that you will type in your pin THEN you will select your certificate.
