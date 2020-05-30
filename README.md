ADBKeyBoard 
===========

Android Virtual Keyboard Input via ADB

[<img src="https://f-droid.org/badge/get-it-on.png"
     alt="Get it on F-Droid"
     height="80">](https://f-droid.org/packages/com.android.adbkeyboard/)

ADBKeyBoard is a virtual keyboard that receives commands from system broadcast intents, which you can send text input using adb.

There is a shell command 'input', which can help you send text input to the Android system. 
<pre>
usage: input [text|keyevent]
  input text <string>
  input keyevent <event_code>
</pre>
  
But you cannot send unicode characters using this command, as it is not designed to use it this way.
<br />
Reference : http://stackoverflow.com/questions/14224549/adb-shell-input-unicode-character
<pre>
e.g.
adb shell input text '你好嗎' 
is not going to work.
</pre>

ADBKeyboard will help in these cases, especially in device automation and testings.

Build and install APK
---------------------

With one device or emulator connected, use these simple steps to install the keyboard:

 * Get source: `git clone https://github.com/senzhk/ADBKeyBoard.git`
 * Go into project dir `cd ADBKeyBoard`
 * Set Android SDK location: `export ANDROID_HOME=$HOME/Android/Sdk` or edit file `local.properties`
 * Build and install: `./gradlew installDebug`

How to Use
----------

 * Enable 'ADBKeyBoard' in the Language&Input Settings.
 * Set it as Default Keyboard OR Select it as the current input method of certain EditText view.
 * Sending Broadcast intent via Adb or your Android Services/Apps.

Usage Example:
<pre>
1. Sending text input
adb shell am broadcast -a ADB_INPUT_TEXT --es msg '你好嗎? Hello?'

* This may not work for Oreo/P, am/adb command seems not accept utf-8 text string anymore

1.1 Sending text input (base64) if (1) is not working.

* For Mac/Linux, you can use the latest base64 input type with base64 command line tool:
adb shell am broadcast -a ADB_INPUT_B64 --es msg `echo '你好嗎? Hello?' | base64`

* For Windows, please try this script (provided by ssddi456): 
https://gist.github.com/ssddi456/889d5e8a2571a33e8fcd0ff6f1288291

* Sample python script to send b64 codes (provided by sunshinewithmoonlight):
import os
import base64
chars = '的广告'
charsb64 = str(base64.b64encode(chars.encode('utf-8')))[1:]
os.system("adb shell am broadcast -a ADB_INPUT_B64 --es msg %s" %charsb64)

2. Sending keyevent code  (67 = KEYCODE_DEL)
adb shell am broadcast -a ADB_INPUT_CODE --ei code 67

3. Sending editor action (2 = IME_ACTION_GO)
adb shell am broadcast -a ADB_EDITOR_CODE --ei code 2

4. Sending unicode characters
To send 😸 Cat
adb shell am broadcast -a ADB_INPUT_CHARS --eia chars '128568,32,67,97,116'

5. Send meta keys
To send Ctrl+Space
adb shell am broadcast -a ADB_INPUT_MCODE --eia mcode '4096,62'

</pre>

Switch to ADBKeyBoard from adb (by [robertio](https://github.com/robertio)) :
<pre>
adb shell ime set com.android.adbkeyboard/.AdbIME   
</pre>

Switch back to original virtual keyboard: (swype in my case...)
<pre>
adb shell ime set com.nuance.swype.dtc/com.nuance.swype.input.IME  
</pre>

Check your available virtual keyboards:
<pre>
adb shell ime list -a  
</pre>

You can try the apk with my debug build: https://github.com/senzhk/ADBKeyBoard/raw/master/ADBKeyboard.apk

KeyEvent Code Ref: http://developer.android.com/reference/android/view/KeyEvent.html

Editor Action Code Ref: http://developer.android.com/reference/android/view/inputmethod/EditorInfo.html
