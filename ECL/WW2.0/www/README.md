BB10 Emergency Contact List v2
========

## Description

* __NEW!__ Ported to WebWorks 2.x 
* Complete installation instructions right here (now with automated plugin installation).
* Error messaging and return values from BES integrated into server push code (/server/)
* Now includes integrated compression routines. Compress your payload using LZMA and break
past the 8K limit for your contacts!

The Emergency Contact List (ECL) Application stores and displays the
latest emergency contact list.  It allows the user to display and
search the list and then call, email, or sms the appropriate contact.

The contact list is updated by a push to the device.  When a new list
is received, a local copy of that list is stored and a notification is
sent to the Notification Hub.  The user can start the application
manually or by selecting the hub notification.  Since the data is
stored locally it is available when offline.

## Data Transports and Upgrading to ECL 

This version of ECL uses JSON (JavaScript Object Notation) as a transport for efficiency.  
For those upgrading from earlier versions of ECL, you can simply cut-and-paste your Excel 
data as-is into the server/index.htm ECL Service included with this code.  As an alternative, 
you may do on-the-fly binary conversion of the Excel XLS format with a server-side tool like
Apache POI (http://poi.apache.org), but this solution would require a Java Application server 
and is beyond the scope of this document to set up.

There is a blog @ http://devblog.blackberry.com that goes into this migration path in 
greater detail.

## Server Component

The www/server/index.htm is a small test application that can be used to send requests
to the enterprise server to push an emergency contact list to a device/phone.
This is a standalone HTML file and can run directly from the BlackBerry filesystem,
on a simple webserver (like Apache or Nginx), or packaged and deployed as a hybrid app
itself on BlackBerry platforms.  It handles the Excel to JSON conversion.  It also now
supports compression.  Go ahead and try compressing your data and choosing different compression
values.  The client app handles both compressed or uncompressed data automatically.

__NOTE:__ You need to enable CORS to get feedback from the BES server or it will be blocked.  Run Chrome with "--disable-web-security" or install the very cool extension at: https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi.  More instructions in the www/server/index.htm file comments.

## Known Issues

None currently.

## How to Build

1. Ensure WebWorks 2.1 or greater is installed

2. Ensure signing keys are set up.

3. Change to the root directory of the project (eg., ~/BES10-WebWorks/ECL/WW2.0/), and run "webworks create ECL" - this will create the rest of the necessary platform files.  You may then delete the directory it creates "ECL"

4. Make sure you have added all appropriate plugins in the plugin directory, I have included a node script to do this for you automatically (anywhere inside the project directory):
  * node update_plugins.js
    OR manually: 
  * webworks plugin add com.blackberry.push 
  * webworks plugin add com.blackberry.app 
  * webworks plugin add com.blackberry.utils 
  * webworks plugin add com.blackberry.system
  * webworks plugin add com.blackberry.invoke
  * webworks plugin add com.blackberry.invoked
  * webworks plugin add com.blackberry.invoke.card
  * webworks plugin add com.blackberry.ui.contextmenu
  * webworks plugin add com.blackberry.identity
  * webworks plugin add com.blackberry.notification

__NOTE VERY IMPORTANT TO DO THE NEXT STEP for PUSH to WORK!__
5. Update the www/config.xml on ~ line 24 "widget id" to be your desired app id (from ecl_webworks_2)
Then modify the two "rim:invoke-target" lines 55 & 65 to be unique (from webworks2.ecl2.invoke.waa.*)
Then lastly modify the "push" value in www/js/pushhandler.js ~ line 23 to have the pushTarget identical to what you just defined in line 55 of config.xml.

6. Connect your device or simulator, type "webworks run".  
HINT: If your BES admin has enabled it, you may switch your device into the work perimeter and sign/deploy it directly there by typing  "webworks run --release -k SIGNING_PASSWORD"

7. Contact me on Twitter @latestlinux, or your Account Manager to get more help if you run into issues. 
And check back regularly to github, as the source is costantly being modified and improved.


##  File Structure
  * config.xml - WebWorks project file.  Contains application parameters and permissions.
  * index.html - Top level application file that initializes WebWorks and loadsthe 1st screen (contactlist.htm).
  * contactlist.htm - Skeleton markup of Emergency Contact List view
  * phonelist.htm - Skeleton markup of contact phone list view
  * details.htm - Skeleton markup of details view
  * bbui-min.js, bbui-min.css -  bbUI VERSION: 0.9.6.1
  * index.js - Top level Javascript file that defines app namespace.
  * contactlist.js - Creates and manages the Emergency Contact List view.
  * phonelist.js - Creates and manages the phone list view.
  * details.js - Creates and manages the details view.
  * eclmodel.js - Holds and saves the contact list.
  * pushhandlers.js - Handles the push messages.
  * utils.js - Utility functions.  
  * img - Contains the application icon and icons used in the application.

## More Info

The JS LZMA compression routines are by Nathan Rugg @ https://github.com/nmrugg/LZMA-JS
The JSON parser in JavaScript by Douglas Crockford @ http://javascript.crockford.com
The UI uses Webworks and BBUI.js
