# ScriptSentry
![ScriptSentry](ScriptSentry.png)

ScriptSentry finds misconfigured and dangerous logon scripts.

### Read the blog post
https://offsec.blog/hidden-menace-how-to-identify-misconfigured-and-dangerous-logon-scripts/

### Usage
```PowerShell
# Run ScriptSentry and display results on the console
IEX(Invoke-WebRequest 'https://raw.githubusercontent.com/techspence/ScriptSentry/main/Invoke-ScriptSentry.ps1')
Invoke-ScriptSentry

# Run ScriptSentry and save output to a text file
IEX(Invoke-WebRequest 'https://raw.githubusercontent.com/techspence/ScriptSentry/main/Invoke-ScriptSentry.ps1')
Invoke-ScriptSentry | Out-File c:\temp\ScriptSentry.txt

# Run ScriptSentry and save results to separate csv files in the current directory
IEX(Invoke-WebRequest 'https://raw.githubusercontent.com/techspence/ScriptSentry/main/Invoke-ScriptSentry.ps1')
Invoke-ScriptSentry -SaveOutput $true
```

### Example Output
```
 _______  _______  _______ _________ _______ _________ _______  _______  _       _________ _______
(  ____ \(  ____ \(  ____ )\__   __/(  ____ )\__   __/(  ____ \(  ____ \( (    /|\__   __/(  ____ )|\     /|
| (    \/| (    \/| (    )|   ) (   | (    )|   ) (   | (    \/| (    \/|  \  ( |   ) (   | (    )|( \   / )
| (_____ | |      | (____)|   | |   | (____)|   | |   | (_____ | (__    |   \ | |   | |   | (____)| \ (_) /
(_____  )| |      |     __)   | |   |  _____)   | |   (_____  )|  __)   | (\ \) |   | |   |     __)  \   /
      ) || |      | (\ (      | |   | (         | |         ) || (      | | \   |   | |   | (\ (      ) (
/\____) || (____/\| ) \ \_____) (___| )         | |   /\____) || (____/\| )  \  |   | |   | ) \ \__   | |
\_______)(_______/|/   \__/\_______/|/          )_(   \_______)(_______/|/    )_)   )_(   |/   \__/   \_/
                              by: Spencer Alessi @techspence
                                          v0.6
                                      __,_______
                                     / __.==---/ * * * * * *
                                    / (-'
                                    `-'
                            Setting phasers to stun, please wait..

########## Unsafe UNC folder permissions ##########

Type                      File                                User          Rights
----                      ----                                ----          ------
UnsafeUNCFolderPermission \\eureka-dc01\fileshare1            Everyone FullControl
UnsafeUNCFolderPermission \\eureka-dc01\fileshare1\accounting Everyone FullControl
UnsafeUNCFolderPermission \\eureka-dc01\fileshare1\IT         Everyone FullControl


########## Unsafe logon script permissions ##########

Type                        File                                                   User                                                  Rights
----                        ----                                                   ----                                                  ------
UnsafeLogonScriptPermission \\eureka.local\sysvol\eureka.local\scripts\elevate.vbs NT AUTHORITY\Authenticated Users ReadAndExecute, Synchronize
UnsafeLogonScriptPermission \\eureka.local\sysvol\eureka.local\scripts\run.vbs     NT AUTHORITY\Authenticated Users ReadAndExecute, Synchronize
UnsafeLogonScriptPermission \\eureka.local\sysvol\eureka.local\scripts\test.cmd    EUREKA\Domain Users                      Modify, Synchronize


########## Unsafe GPO logon script permissions ##########

Type                           File                             User                                        Rights
----                           ----                             ----                                        ------
UnsafeGPOLogonScriptPermission \\eureka-dc01\fileshare1\run.bat EUREKA\testuser Write, ReadAndExecute, Synchronize
UnsafeGPOLogonScriptPermission \\eureka-dc01\fileshare1\run.bat Everyone                               FullControl


########## Unsafe UNC file permissions ##########

Type                    File                                              User                                        Rights
----                    ----                                              ----                                        ------
UnsafeUNCFilePermission \\eureka-dc01\fileshare1\IT\securit360pentest.bat Everyone                               FullControl


########## Unsafe NETLOGON/SYSVOL permissions ##########

Type                 Folder                  User                                          Rights
----                 ------                  ----                                          ------
UnsafeNetlogonSysvol \\eureka.local\NETLOGON EUREKA\Domain Users              Modify, Synchronize
UnsafeNetlogonSysvol \\eureka.local\SYSVOL   NT AUTHORITY\Authenticated Users Modify, Synchronize

########## Plaintext credentials ##########

Type        File                                                   Credential
----        ----                                                   ----------
Credentials \\eureka.local\sysvol\eureka.local\scripts\ADCheck.ps1 $password = ConvertTo-SecureString -String "Password2468!" -AsPlainText -Force
Credentials \\eureka.local\sysvol\eureka.local\scripts\shares.cmd  net use f: \\eureka-dc01\fileshare1\it /user:itadmin Password2468!
Credentials \\eureka.local\sysvol\eureka.local\scripts\test.cmd    net use g: \\eureka-dc01\fileshare1 /user:user1 Password3355!
Credentials \\eureka.local\sysvol\eureka.local\scripts\test.cmd    net use h: \\eureka-dc01\fileshare1\accounting /user:userfoo Password5!
Credentials \\eureka.local\sysvol\eureka.local\scripts\logon.kix   Use X: "\\eureka-dc01\fileshare2" /USER:itadmin /P:Password2468!

########## Nonexistent Shares ##########

Type             Server             Share                                 Script                                                   DNS Exploitable Admins
----             ------             -----                                 ------                                                   --- ----------- ------
NonexistentShare CUHOLDING          \\CUHOLDING\QUICKBOOKS                \\eureka.local\sysvol\eureka.local\scripts\marketing.bat No  Potentially No    
NonexistentShare eureka-srvnotexist \\eureka-srvnotexist\NonExistingShare \\eureka.local\sysvol\eureka.local\scripts\test.cmd      No  Potentially No    
NonexistentShare NAS                \\NAS\PUBLIC                          \\eureka.local\sysvol\eureka.local\scripts\main.bat      No  Potentially No    
NonexistentShare NAS                \\NAS\SYMITAR                         \\eureka.local\sysvol\eureka.local\scripts\symregOLD.bat No  Potentially No    

########## Admins with logonscripts ##########

Type             User                                                      LogonScript
----             ----                                                      -----------
AdminLogonScript LDAP://CN=Administrator,CN=Users,DC=eureka,DC=local       run.vbs
AdminLogonScript LDAP://CN=it admin,OU=Admins,OU=Eureka,DC=eureka,DC=local elevate.vbs

########## Admins with logonscripts mapped from nonexistent share ##########

Type                   Server             Share                                 Script                                              DNS Exploitable Admins                                                                
----                   ------             -----                                 ------                                              --- ----------- ------                                                                
ExploitableLogonScript eureka-srvnotexist \\eureka-srvnotexist\NonExistingShare \\eureka.local\sysvol\eureka.local\scripts\test.cmd No  Yes  LDAP://eureka.local/CN=it admin,OU=Admins,OU=Eureka,DC=eureka,DC=local
ExploitableLogonScript eureka-srvnotexist \\eureka-srvnotexist\NonExistingShare \\eureka.local\sysvol\eureka.local\scripts\test.cmd No  Yes  LDAP://eureka.local/CN=user1,OU=Users,OU=Eureka,DC=eureka,DC=local  
```