# laragon-multi-php-version-via-package-manager
Manage your php version via powershell package manager


For this sake ill be using https://github.com/mlocati/powershell-phpmanager) package manage to handle multi version
instead of download the zip file manually.
The reason why i want to using this package manager is because it easy for me to add new extension like how linux and mac do.
For example, installing imagick on windows is such a hassle. Not to mention if you keep switching your laptop or pc it could make it hard to just install all back without any semi or automated script laying around.


**Steps:**
1. Make sure that you already have install your laragon. For my example ill be using c:/laragon/bin/php as my php path. yours should be different depends on where you install laragon.
2. Follow the instruction how to get the shell installed part on https://github.com/mlocati/powershell-phpmanager

In my case i need 4 type of version available for my environment, if you are on the safe side of development just stick to
ThreadSafe and x64 (depends on your setup):
- PHP 5.6
- PHP 7.2
- PHP 7.3
- PHP 7.4

my command should be :
```
Install-Php -Version 5.6 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php56 -TimeZone Asia/Kuala_Lumpur	
Install-Php -Version 7.2 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php72 -TimeZone Asia/Kuala_Lumpur	
Install-Php -Version 7.3 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php73 -TimeZone Asia/Kuala_Lumpur	
Install-Php -Version 7.4 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php74 -TimeZone Asia/Kuala_Lumpur
```

**By default it will register all your folder path naming the same as the installation zip file. It will be something like **

Name | Value
---- | -----
5.6 | C:\laragon\bin\php\5.6-ts-x64
7.2 | C:\laragon\bin\php\7.2-ts-x64
7.3 | C:\laragon\bin\php\7.3-ts-x64
7.4 | C:\laragon\bin\php\7.4-ts-x64

**Next is you need to add aliases location where the file version can easily switch into, the Name part can be what you like:**

```
Add-PhpToSwitcher -Name php56 -Path C:\laragon\bin\php\php56
Add-PhpToSwitcher -Name php72 -Path C:\laragon\bin\php\php72
Add-PhpToSwitcher -Name php73 -Path C:\laragon\bin\php\php73
Add-PhpToSwitcher -Name php74 -Path C:\laragon\bin\php\php74
```

**see the result by run this command below.**
```(Get-PhpSwitcher).Targets```

**To switch version you like just type:**
```Switch-Php php73```

**Once you switch to the version you like for the first time, it will create a new linked folder called "current".** ```C:\laragon\bin\php\current```

**In your laragon system tray menu, switch your php version to "current" so that it will always follow your package manager switch php version all the time.**

**Check your current version :**
```Get-Php```

**To install extension just follow the package manager. My default extension to install is:**
```Install-PhpExtension imagick```

**To Enable existing extension:**
```
Enable-PhpExtension opcache
Enable-PhpExtension openssl
Enable-PhpExtension exif
Enable-PhpExtension curl
Enable-PhpExtension gmp
Enable-PhpExtension mbstring
Enable-PhpExtension fileinfo
Enable-PhpExtension pdo_pgsql
Enable-PhpExtension pdo_mysql
Enable-PhpExtension gd
```

**To Disable extension:**
```Disable-PhpExtension opcache```


By default your /current/php.ini file config mostly empty. If you doing alot of external access with outside of the world or other
package manager, it would recommend that you setting up your curl and ssl cert path

For Curl
go to this https://curl.haxx.se/docs/caextract.html and get the latest cert.

usually the latest one can be obtain here at https://curl.haxx.se/ca/cacert.pem

place those file to your current php folder, mine would be 
```C:\laragon\bin\php\current\extras\ssl\```

and in ```php.ini``` , add line below

```
[curl]
curl.cainfo ="C:/laragon/bin/php/current/extras/ssl/cacert.pem"
```
