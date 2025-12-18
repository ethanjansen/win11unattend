# Windows 11 Unattended Setup

From the OOBE Screen press Shift + F10

```
curl -L ethanjansen.github.io/win11unattend | powershell -Command -
```

This will skip the entire OOBE and perform an unattended setup for Windows 11, only asking a few questions for computer name (early in setup) and local account creation (towards end of process).
Entire process requires internet (not just for downloading the script).
Please wait for all Powershell windows to close after creating user, then log out and back in, and wait again for all Powershell windows to close (~30 seconds) before using your computer.
Remember to check for Windows updates.
Enjoy!

Recommend using with a fresh install of Windows 11 Pro or Enterprise (LTSC/LTSC IoT).
Should also work with any install of Windows 11 (including from an OEM), but may not remove all bloatware.
This may also work with Windows 10 (unsupported/untested).
DO NOT use outside of OOBE.

unattend.xml generated using [schneegans.de unattend generator](https://schneegans.de/windows/unattend-generator).

## Configuration:

  - Generalize: (this is a bit jank, but required to run specialize steps)
    - Persist device installs
  - Specialize:
    - **\[Prompt\]** Set computer name
      - Does not check for valid computer name
      - Use alphanumeric characters or hyphens, must contain one letter, 1-15 character limit
    - Remove Windows packages:
      - New Outlook for Windows
      - Windows Feedback Hub
      - Get Help
      - Tips
      - Skype
      - Solitaire Collection
      - Xbox Apps
        - Also explicitly disables GameDVR
    - Set local account passwords to never expire
    - Disable start menu ads
    - Remove default icons from start menu
    - Enable long paths support
    - Set Powershell execution policy to RemoteSigned
    - Disable fast startup
    - Prevent BitLocker encryption
    - Hide Edge first run experience
    - Disable Edge background mode and startup
    - Disable Sticky Keys
    - Add ICMP v4/v6 rules to firewall to allow ping response
    - Set default apps on taskbar:
      - File Explorer
      - Chrome
      - Notepad++
    - Show file extensions
    - Show hidden items
    - Set taskbar to left aligned
    - Disable enhanced pointer precision
    - Disable Bing search from start menu search
  - OOBE:
    - Set language, locale, and keyboard to en-US 
    - Enable Administrator account (will disable later in setup)
    - Skip OOBE, disabling all diagnostic data, personalized input, and location history sent to Microsoft
  - First Logon (Administrator):
    - Remove C:\Windows.old if empty
    - Install apps:
      - Chocolatey
      - Google Chrome
      - VLC
      - Sumatra PDF
      - Notepad++
    - Set [default app associations](./appassociations.xml) (this cannot set a default browser)
    - **\[Prompt\]** Set username and password
      - Does not check invalid characters
      - Accepts blank passwords
      - Does not allow using Administrator account
      - Add user to Administrators group
    - Disable Administrator account
    - Remove unattend.xml and unattend-original.xml (scripts and logs are kept in C:\Windows\Setup\Scripts)
  - First Logon (created user):
    - Revert right-click context menu to Windows 10 style
    - Set File Explorer to launch to This PC
    - Set taskbar search box to icon-only
    - Set desktop icons:
      - Recycle Bin
      - Google Chrome
      - VLC
      - Sumatra PDF
    - Set folders in start menu:
      - Personal folder
      - File Explorer
      - Settings

## Making Your Own

This project is intended to be modified for your own use.
If you want to build your own:

  - Fork the repo.
  - Enable GitHub Pages for main branch.
  - Find and replace all references to `ethanjansen.github.io/win11unattend` with your own GitHub Pages URL (there should be ~8 including this one).
  - Find and replace all references to `github.com/ethanjansen/win11unattend` with your own GitHub repo URL (there should be 2 including this one).
  - Modify [unattend.xml](./unattend.xml) to suite your needs. Use [schneegans.de unattend generator](https://schneegans.de/windows/unattend-generator) (a direct link is at the top of [unattend.xml](./unattend.xml).
  - Configure apps to be preinstalled within [chocopackages.conf](./chocopackages.config). Ensure they are intended to be installed for all users.
  - Configure default app associations within [appassociations.conf](./appassociations.xml). Generate your own using `Dism /Online /Export-DefaultAppAssociations:"C:\appassociations.xml"` from a preconfigured Windows 11 install.
  - All other configurations are within [unattend.xml](./unattend.xml).

## Inspiration

This project was inspired by ChrisTitusTech’s original [bypassnro](https://github.com/ChrisTitusTech/bypassnro) concept.
I decided to detach my fork as this repository is a complete rewrite, does not contain original code, and I would like proper licensing.
This project is not affiliated with or endorsed by ChrisTitusTech.
