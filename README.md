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
