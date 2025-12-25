FOTA UPDATE SETUP USING LOCAL PYTHON SERVER AND NGROK

This document explains how to host a firmware .pack file locally, expose it to the internet using ngrok, and perform a Firmware Over-The-Air (FOTA) update on the Quectel EC200U module using AT commands.

PREREQUISITES

->Python installed on your system

->ngrok downloaded and available in the working directory

->A valid firmware .pack file

->Active SIM with data (APN: airtelgprs.com)

->EC200U module connected and ready for AT commands

STEP 1: START LOCAL PYTHON HTTP SERVER

Navigate to the directory containing your firmware .pack file and run:

python.exe -m http.server 8000

This command hosts the current directory on port 8000.

IMPORTANT:
Always start the Python server first, then start the ngrok server.


STEP 2: CONFIGURE NGROK

2.1 SIGN UP FOR NGROK

Go to https://ngrok.com

Create a free account

Copy your auth token from the ngrok dashboard

2.2 AUTHENTICATE NGROK

Run the following command in the directory where ngrok.exe is located:

./ngrok.exe authtoken YOUR_AUTH_TOKEN

STEP 3: START NGROK HTTP TUNNEL

Expose the local Python server (port 8000) to the internet.

Windows (CMD / PowerShell):

.\ngrok.exe http 8000

Linux / macOS:

./ngrok http 8000

ngrok will display a public HTTPS URL similar to:

https://abcd1234.ngrok.io

STEP 4: FIRMWARE FILE PUBLIC URL

If your firmware file name is:

Update_EC200UCNAAR03A03-R03A13.pack

Then the public URL will be:

https://abcd1234.ngrok.io/Update_EC200UCNAAR03A03-R03A13.pack

STEP 5: TEST THE PUBLIC URL

Before starting FOTA:

Open the firmware URL in a browser

Use a device with cellular internet access

Confirm that the firmware file downloads successfully


STEP 6: FOTA AT COMMAND SEQUENCE

TEST AT COMMUNICATION

AT

Expected Response:
OK

CONFIGURE PDP CONTEXT (APN: airtelgprs.com)

AT+QICSGP=2,1,"airtelgprs.com","","",1

Expected Response:
OK

ASSIGN PDP CONTEXT FOR FOTA

AT+QCFG="fota/cid",2

Expected Response:
OK

INITIATE FOTA DOWNLOAD

AT+QFOTADL="https://drema-nonglandulous-celestina.ngrok-free.dev/Update_EC200UCNAAR03A03-R03A13.pack",1,50,100

Expected Responses:

OK
+QIND: "FOTA","HTTPSTART"
+QIND: "FOTA","DOWNLOADING",2
+QIND: "FOTA","DOWNLOADING",4
...
+QIND: "FOTA","DOWNLOADING",100
+QIND: "FOTA","HTTPEND",0

The module will:

Download the firmware

Verify the package

Apply the update

Reboot automatically (may take several minutes)

STEP 7: POST-UPDATE VERIFICATION

After reboot, verify the firmware version.

AT
AT+QGMR

Expected Result:
OK
<new firmware version>

NOTES AND BEST PRACTICES

Ensure stable cellular connectivity during FOTA

Do not power off the module during the update process

ngrok free URLs change when restarted; update the FOTA URL if needed

Always verify firmware URL accessibility before starting FOTA

SUMMARY FLOW

Start Python HTTP server

Start ngrok server

Generate public firmware URL

Test firmware download via cellular network

Execute FOTA AT commands

Verify firmware version after update
