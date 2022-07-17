# ConfigureDefender

A small utility for configuring Windows Defender Antivirus.

ConfigureDefender is a small GUI application to view and configure important Defender Antivirus settings.
It uses PowerShell cmdlets (with a few exceptions) to change Windows Defender settings.
Furthermore the user can apply one of three pre-defined protection levels: DEFAULT, HIGH, INTERACTIVE, and MAX.
Changing one of the protection levels requires a reboot in order to take effect.

### Supported operating systems

- Windows 10 32-bit and 64-bit version 1809 or later
- Windows 11 64-bit
- Windows Server 2019+

The [ASR](https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/attack-surface-reduction-rules-reference?view=o365-worldwide)
rule "Block persistence through WMI event subscription" requires Windows version 1903 or later.

### Installation

Download the latest binary from [Releases](https://github.com/szepeviktor/ConfigureDefender/releases).
ConfigureDefender is a portable application, no installation is needed.

### Documentation from Microsoft

- https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/attack-surface-reduction-rules-reference?view=o365-worldwide
- https://medium.com/palantir/microsoft-defender-attack-surface-reduction-recommendations-a5c7d41c3cf8
- https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-atp/attack-surface-reduction
- https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/bg-p/MicrosoftDefenderATPBlog/label-name/Demystifying%20ASR%20rules

### About Maximum Protection Level

The MAX Protection Level blocks anything suspicious
via Attack Surface Reduction, Controlled Folder Access, SmartScreen (set to block)
and cloud level (set to block).
These settings are very restrictive and using them can produce many false positives
even in home environment.
Such a setup is not recommended in the business environment.
 
### For Advanced Users

Windows Defender Antivirus settings are stored in the Windows Registry
and most of them are not available form Windows Defender Security Center.

They can be managed via

- Group Policy Management Console: gpedit.msc is not available in Windows Home edition
- Direct via Registry editing: manually, via `*.reg` files or scripts
- PowerShell cmdlets: set-mppreference, add-mppreference, remove-mppreference, PowerShell 5.0
 
#### Windows Defender Antivirus Registry Keys

Normally Windows Defender Antivirus stores most settings under this key, owned by SYSTEM.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender`

The registry keys can be changed while using Defender Security Center or PowerShell cmdlets.

#### Group Policy Management Console (GPO)

Administrators can use Windows Group Policy Management Console (GPO) utility
to override certain Windows Defender Antivirus registry values.
Group Policy settings are stored under another key, owned by ADMINISTRATORS.

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender`

Keep in mind that GPOs do not delete the normal Defender settings!

#### Manually changing WD settings via registry 

Registry editing is usually made, under the second key (see below), the first requires system admin-rights. 
Applying Defender settings by directly manipulating the registry
is not recommended on Windows editions which officially support
Group Policy Management Console e.g. PRO & Enterprise editions.

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender`

- Those settings are not recognized by Group Policy Management Console.
- They can temporarily overwrite Group Policy Management Console setup in the Registry
  because they share the same Registry keys.
  Those changes are not permanent, because Group Policy configuration is not overwritten. 
- After some hours those settings are automatically and silently
  back-overwritten by Group Policy Refresh feature. 
- Those settings cannot be changed via Defender Security Center or PowerShell cmdlets
  even if they are visible like folders and applications related for Controlled Folder Access.
 
### Windows Home Editions

Under Windows Home editions, someone can configure Defender settings
(outside of the Defender Security Center)
when using PowerShell cmdlets or via the manual Registry editing method.
This may confuse some users, but ConfigureDefender utility can remove the settings made under the policy path.

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender`

This is required because those settings would override ConfigureDefender settings.

### ConfigureDefender utility and GPOs

ConfigureDefender utility can be used on Windows Professional & Enterprise editions
if an Administrator did not apply Defender policies via Group Policy Management Console.
Normally all those policies are by default set to 'Not configured'.
They can be found in Group Policy Management Console.

Computer configuration 👉 Policies 👉 Administrative templates 👉 Windows components 👉 Windows Defender Antivirus
 
The tabs: MAPS, MpEngine, Real-time Protection, Reporting, Scan, Spynet and Windows Defender Exploit Guard
should be inspected before using ConfigureDefender.
The corresponding policies have to be set to 'Not configured'.
If not then the GPO Refresh feature will override the settings applied via ConfigureDefender.
