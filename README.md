# laragon-multi-php-version-via-package-manager
Manage your php version via Laragon PHP UI switcher with help of powershell package manager.


For this sake ill be using https://github.com/mlocati/powershell-phpmanager) package manage to handle multi version
instead of download the zip file manually.
The reason why i want to using this package manager is because it easy for me to add new extension like how linux and mac do.
For example, installing imagick on windows is such a hassle. Not to mention if you keep switching your laptop or pc it could make it hard to just install all back without any semi or automated script laying around.


**Disclaimer**
1. Make sure that you already have install your Laragon. For my example, ill be using **c:/laragon/bin/php** as my php path. yours could  be different depends on where you install your Laragon.
2. Steps below are just my word on how to install powershell-phpmanager which is available from their github page  https://github.com/mlocati/powershell-phpmanager

**Steps**
1. Open powershell as admininstrator and check current version ```$PSVersionTable.PSVersion.ToString()``` if the value is more than 5 it should be ok to go.
2. To avoid having command execution issue,we will run ```Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force```
3. Next run ```Install-Module -Name PhpManager -Repository PSGallery -Force -Scope CurrentUser``` to install the package for current user. 
4. Once done, you should have command ```Install-Php``` available and the this we will be focusing on later.

Im running on 64bit intel chip (typical configuration PC nowdays, both intel and amd should be the same)
In my case i need 4 type of version available for my environment, if you are on the safe side of development just stick to
ThreadSafe and x64 (depends on your setup):
- PHP 5.6
- PHP 7.4
- PHP 8.0
- PHP 8.1

my command should be :
```
Install-Php -Version 5.6 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php56 -AddToPath User -TimeZone Asia/Kuala_Lumpur
Install-Php -Version 7.4 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php74 -AddToPath User -TimeZone Asia/Kuala_Lumpur
Install-Php -Version 8.0 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php80 -AddToPath User -TimeZone Asia/Kuala_Lumpur
Install-Php -Version 8.1 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php81 -AddToPath User -TimeZone Asia/Kuala_Lumpur
Install-Php -Version 8.2 -Architecture x64 -ThreadSafe $true -Path C:\laragon\bin\php\php82 -AddToPath User -TimeZone Asia/Kuala_Lumpur
```

**By default it will register all your folder path naming the same as the installation zip file. It will be something like **

Name | Value
---- | -----
5.6 | C:\laragon\bin\php\php56
7.4 | C:\laragon\bin\php\php74
8.0 | C:\laragon\bin\php\php80
8.1 | C:\laragon\bin\php\php81
8.2 | C:\laragon\bin\php\php82

**Set current where PHP symlink to be pointed when we set which version of php need to be use**
```Initialize-PhpSwitcher -Alias C:\laragon\bin\php\current -Scope CurrentUser```

**Next is you need to add aliases location where the file version can easily switch into, the Name part can be what you like:**

```
Add-PhpToSwitcher -Name php56 -Path C:\laragon\bin\php\php56
Add-PhpToSwitcher -Name php74 -Path C:\laragon\bin\php\php74
Add-PhpToSwitcher -Name php80 -Path C:\laragon\bin\php\php80
Add-PhpToSwitcher -Name php81 -Path C:\laragon\bin\php\php81
Add-PhpToSwitcher -Name php82 -Path C:\laragon\bin\php\php82
```
**To switch version manually you like just type:**
```Switch-Php php82 -Force```

However we are going to combine Laragon UI php switch with 

**Then on laragon windows tray (bottom right), set the PHP version to "current"**

**see the result by run this command below.**
```(Get-PhpSwitcher).Targets```

**Once you switch to the version you like for the first time, it will create a new linked folder called "current".** ```C:\laragon\bin\php\current```

**In your laragon system tray menu, switch your php version to "current" so that it will always follow your package manager switch php version all the time.**

**Check your current version :**
```Get-Php```

**To install extension just follow the package manager. My default extension to install is:**
```
Install-PhpExtension imagick
Install-PhpExtension redis
Install-PhpExtension swoole
```

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
Enable-PhpExtension redis
Enable-PhpExtension sodium
Enable-PhpExtension xsl
Enable-PhpExtension gettext
Enable-PhpExtension zip
Enable-PhpExtension sockets
```

**To Disable extension:**
```Disable-PhpExtension opcache```


By default your /current/php.ini file config mostly empty. If you doing alot of external access with outside of the world or other
package manager, it would recommend that you setting up your curl and ssl cert path

For Curl
go to this https://curl.haxx.se/docs/caextract.html and get the latest cert.

usually the latest one can be obtain here at https://curl.haxx.se/ca/cacert.pem

Take not that you have to place **cacert.pem** file to all php version folder, file should be in each **<php_version>** folder: 
```C:\laragon\bin\php\**<php_version>**\extras\ssl\```

and inside ```C:\laragon\bin\php\**<php_version>**\php.ini``` , add line below

```
[curl]
curl.cainfo ="C:/laragon/bin/php/**<php_version>**/extras/ssl/cacert.pem"
```


[php.ini]
```
date.timezone=GMT+8
default_charset=UTF-8
extension_dir =  "C:/laragon/bin/php/**<php_version>**/ext"
max_execution_time = 10000
memory_limit= 2048M
upload_max_filesize=120M
post_max_size=120M
```

# Laragon Setting
Once you have start your laragon app, a small laragon system tray icon located at bottom on your screen should be visible.
Right click the icon, navigate to PHP > Version > and choose **current**

![image](https://user-images.githubusercontent.com/6977727/156331470-17cd92ff-98d1-4e36-a9e3-37276c20a479.png)


There is two ways to change PHP version, both will have its own different impact due to how the terminal pick which php version.
For default **Cmder** terminal installed with Laragon it will always pointing to laragon php path your set on laravel version icon tray,
while for system wide php version will always follow Powershell version.

It is recommend that you use system wide version (**current**) so it will be consistent across your setting.
I would recommend you create a batch shortcut for each php version for easy switch.

# Powershell PHP Switch Shortcut

To make life much simpler, having a shortcut file would be beneficial.
There is 2 part of this file; 1st would be the **actual script file** and the 2nd part is the **shortcut file**.

## Part 1: script file
The content of each file should be as follows:
php56.ps1 ``` Switch-Php php56 ```
php74.ps1 ``` Switch-Php php74 ```
php80.ps1 ``` Switch-Php php80 ```
php81.ps1 ``` Switch-Php php81 ```

![image](https://user-images.githubusercontent.com/6977727/156331887-b812b762-73fb-4be9-a3e2-3b8e8294a68b.png)

## Part 2: shortcut file
![image](https://user-images.githubusercontent.com/6977727/156332282-90aa532c-d579-4998-bc17-4a798929cc74.png)

Each shortcut file target is just an script of powershell executable with some argument attach to the shortcut file.
Basically you need to append these script (on Target file) as follows : ```powershell.exe -ExecutionPolicy Bypass -File <file path to actual file```
![image](https://user-images.githubusercontent.com/6977727/156332872-ef22f643-5425-42e1-a4ac-4fe8444b7d0c.png)

