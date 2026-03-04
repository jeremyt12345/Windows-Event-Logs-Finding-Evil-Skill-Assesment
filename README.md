# Windows-Event-Logs-Finding-Evil-Skill-Assesment
'Your SOC manager has assigned you the task of analyzing older attack logs and providing answers to specific questions"


Q1. "By examining the logs located in the "C:\Logs\DLLHijack" directory, determine the process responsible for executing a DLL hijacking attack. Enter the process name as your answer. Answer format: _.exe"


First attempt 
I ran a basic Get-ChildItem loop through the folder pulling all events. This worked but returned 8000+ results — too much to manually look through.
<img width="963" height="559" alt="image" src="https://github.com/user-attachments/assets/d41f1495-d6fb-4c27-b6b7-29b959551115" />


Second attempt
Filtering by suspicious keywords (temp, appdata, etc.) -Which I needed Claude AI help
I tried narrowing it down using Where-Object filtering on the message field. This hit the "maximum number of replacements" error — which happens when PowerShell tries to process the message string on thousands of events at once. It essentially choked on the volume.
<img width="1519" height="500" alt="image" src="https://github.com/user-attachments/assets/627a75d0-21a2-49f5-b44d-8646d0fb41f4" />


Third Attempt 
 Switched to XML parsing
Instead of filtering on the raw message string, I used ToXml() to parse each event properly and extract specific fields (Image = the process, ImageLoaded = the DLL). I also filtered OUT known legitimate paths like System32, Program Files, and WinSxS — leaving only the suspicious ones. Essentially through powershell Im using XML as I did before in previosuly labs instead of searching throghout the Notepad application>
<img width="1534" height="523" alt="image" src="https://github.com/user-attachments/assets/bc8efc7b-2c4d-439f-a5a7-35536e663052" />



Q2. " By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe"

This one I struggled with because I wasnt fully following the instructions. If you follow "C:\Logs\PowershellExec" it takes you to the actual Event Viewer saved log. 

<img width="784" height="180" alt="image" src="https://github.com/user-attachments/assets/af288b35-b439-433f-ac68-8c82e9f6e1d2" />


<img width="629" height="130" alt="image" src="https://github.com/user-attachments/assets/ccafcc3d-4b3e-4697-ab26-7780271e1346" />


Once we find the log we're then suppose to with our past knowledge of Event ID's. We'd be using Event 7, then we filter the log for "mscoree.dll"

<img width="694" height="326" alt="image" src="https://github.com/user-attachments/assets/7ef30880-c1b9-49c9-9d46-aaf3200b8722" />

That then takes us to the Event ID and based on the file path we want to correlate that with DLL's we know shouldnt be associated with Powershell like "clr.dll" but this also tells us the answer too.

<img width="1125" height="287" alt="image" src="https://github.com/user-attachments/assets/0f2d23af-0071-49b6-9634-73999f90be4f" />

Q3. "By examining the logs located in the "C:\Logs\PowershellExec" directory, determine the process that injected into the process that executed unmanaged PowerShell code. Enter the process name as your answer. Answer format: _.exe"

By first glancing I went searching through Event ID 7 again but the question is asking for what INJECTED the process, not the process itself. Think of it like a break-in — Calculator.exe is the building that got broken into, the injector is the burglar who did it. The building didn't choose to have someone inside it, it was forced. I then looked online to check for Injection Event ID.

<img width="1108" height="407" alt="image" src="https://github.com/user-attachments/assets/b872c536-1111-4e72-b704-a8daefe46ddb" />


Luckily there were only three events for Event ID 8.

<img width="750" height="342" alt="image" src="https://github.com/user-attachments/assets/4e3962e2-34d6-42d9-870b-2173016e1599" />


From there I located the injector

<img width="703" height="406" alt="image" src="https://github.com/user-attachments/assets/9145b75a-b245-460e-a255-1c5eff75cb78" />


Q4. "By examining the logs located in the "C:\Logs\Dump" directory, determine the process that performed an LSASS dump. Enter the process name as your answer. Answer format: _.exe"

Initally I checked the previous document on LSASS and found event ID 10 as a way to narrow my focus but I was having trouble digging deeper looking for lsass.exe in a longer search

<img width="987" height="651" alt="image" src="https://github.com/user-attachments/assets/9295df62-25c0-44c3-b36c-d190e660d075" />

But after that failed search I used Claude AI to use "cd C:\Tools\chainsaw> .\chainsaw_x86_64-pc-windows-msvc.exe search "lsass" "C:\Logs\Dump". But theat gave too many results. I later tried ".\chainsaw_x86_64-pc-windows-msvc.exe search "lsass" "C:\Logs\Dump" | Select-String "SourceImage" which worked. 

<img width="1005" height="275" alt="image" src="https://github.com/user-attachments/assets/9ee323ef-b4a6-40d5-8af0-6b7471dd3411" />









