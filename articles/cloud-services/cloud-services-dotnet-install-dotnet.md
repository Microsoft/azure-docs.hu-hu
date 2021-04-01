---
title: A .NET telepítése Azure Cloud Services (klasszikus) szerepkörökre | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan telepítheti manuálisan a .NET-keretrendszert a Cloud Service webes és feldolgozói szerepkörein
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99061851"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>A .NET telepítése Azure Cloud Services (klasszikus) szerepkörökre

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk azt ismerteti, hogyan telepíthet olyan .NET-keretrendszer-verziókat, amelyek nem az Azure vendég operációs rendszerhez tartoznak. A .NET-kiszolgáló és a feldolgozói szerepkörök konfigurálásához használhatja a .NET-et a vendég operációs rendszeren.

A .NET-keretrendszer 4.6.2 telepíthető például a vendég operációs rendszer 4-es verziójára, amely a .NET-keretrendszer 4,6-es verziójának semmilyen kiadására nem alkalmas. (A vendég operációs rendszer 5. termékcsaládja a .NET-keretrendszer 4,6-es verziójával érhető el.) Az Azure vendég operációs rendszer kiadásaival kapcsolatos legfrissebb információkért tekintse meg az [Azure vendég operációs rendszer kiadási hírei](cloud-services-guestos-update-matrix.md)című témakört. 

>[!IMPORTANT]
>Az Azure SDK 2,9 korlátozást tartalmaz a .NET-keretrendszer 4,6 telepítéséhez a vendég operációsrendszer-család 4-es vagy korábbi verziójában. A korlátozáshoz tartozó javítás a [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) webhelyen érhető el.

Ha a .NET-et a webes és feldolgozói szerepkörökön szeretné telepíteni, foglalja bele a .NET web Installert a Cloud Service-projekt részeként. Indítsa el a telepítőt a szerepkör indítási feladatainak részeként. 

## <a name="add-the-net-installer-to-your-project"></a>A .NET-telepítő hozzáadása a projekthez
A .NET-keretrendszerhez készült web Installer letöltéséhez válassza ki a telepíteni kívánt verziót:

* [.NET-keretrendszer 4,8 web Installer](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [.NET-keretrendszer 4.7.2 web Installer](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET-keretrendszer 4.6.2 web Installer](https://www.microsoft.com/download/details.aspx?id=53345)

A telepítő hozzáadása *webes* szerepkörhöz:
  1. **Megoldáskezelő** a Cloud Service-projekt **szerepkörei** alatt kattintson a jobb gombbal a *webes* szerepkörre, és válassza az  >  **új mappa** hozzáadása lehetőséget. Hozzon létre egy **bin** nevű mappát.
  2. Kattintson a jobb gombbal a Bin mappára, és válassza a  >  **meglévő elem hozzáadása elemet**. Válassza ki a .NET-telepítőt, és adja hozzá a bin mappához.
  
A telepítő hozzáadása *feldolgozói* szerepkörhöz:
* Kattintson a jobb gombbal a *feldolgozói* szerepkörre, és válassza a   >  **meglévő elem** hozzáadása lehetőséget. Válassza ki a .NET-telepítőt, és adja hozzá a szerepkörhöz. 

Ha a fájlokat így hozzáadja a szerepkör-tartalom mappájához, azok automatikusan hozzáadódnak a Cloud Service-csomaghoz. Ezután a rendszer a virtuális gép egy konzisztens helyére telepíti a fájlokat. Ismételje meg ezt a folyamatot a felhőalapú szolgáltatás minden webes és feldolgozói szerepköre esetében, hogy minden szerepkör rendelkezik a telepítő másolatával.

> [!NOTE]
> A .NET-keretrendszer 4.6.2 a Cloud Service-szerepkörre kell telepítenie, még akkor is, ha az alkalmazás a .NET-keretrendszer 4,6-es példányát A vendég operációs rendszer tartalmazza a Tudásbázis [3098779-es frissítését](https://support.microsoft.com/kb/3098779) és a 3097997-es [frissítést](https://support.microsoft.com/kb/3097997). A .NET-alkalmazások futtatásakor problémák léphetnek fel, ha a .NET-keretrendszer 4,6 telepítve van a Tudásbázis frissítésein. A probléma elkerülése érdekében telepítse a .NET-keretrendszer 4.6.2-et a 4,6-es verzió helyett. További információt a [tudásbázis 3118750](https://support.microsoft.com/kb/3118750) -es és [4340191](https://support.microsoft.com/kb/4340191)-es cikkében talál.
> 
> 

![Szerepkörök tartalma a telepítő fájljaival][1]

## <a name="define-startup-tasks-for-your-roles"></a>Adja meg a szerepkörök indítási feladatait
Az indítási feladatokkal műveleteket hajthat végre a szerepkörök elkezdése előtt. A .NET-keretrendszer indítási feladat részeként történő telepítése biztosítja, hogy a keretrendszer telepítve legyen az alkalmazás kódjának futtatása előtt. Az indítási feladatokkal kapcsolatos további információkért lásd: [indítási feladatok futtatása az Azure-ban](cloud-services-startup-tasks.md). 

1. Adja hozzá a következő tartalmat a ServiceDefinition. csdef fájlhoz a **webrole** vagy a **WorkerRole** csomópontban az összes szerepkörhöz:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Az előző konfiguráció a konzol parancsát `install.cmd` rendszergazdai jogosultságokkal futtatja a .NET-keretrendszer telepítéséhez. A konfiguráció emellett létrehoz egy **NETFXInstall** nevű **LocalStorage** elemet is. Az indítási parancsfájl beállítja a Temp mappát a helyi tárolási erőforrás használatára. 
    
    > [!IMPORTANT]
    > A keretrendszer helyes telepítésének biztosításához állítsa az erőforrás méretét legalább 1 024 MB-ra.
    
    További információ az indítási feladatokról: [általános Azure Cloud Services indítási feladatok](cloud-services-startup-tasks-common.md).

2. Hozzon létre egy **install. cmd** nevű fájlt, és adja hozzá a következő telepítési parancsfájlt a fájlhoz.

   A parancsfájl ellenőrzi, hogy a .NET-keretrendszer adott verziója már telepítve van-e a számítógépen a beállításjegyzék lekérdezésével. Ha a .NET-keretrendszer verziója nincs telepítve, a .NET-keretrendszer web Installer megnyitása megtörtént. A hibák elhárítása érdekében a parancsfájl az összes tevékenységet naplózza a startuptasklog-(aktuális dátum és idő). txt fájlba, amely a **InstallLogs** helyi tárolójában van tárolva.
   
   > [!IMPORTANT]
   > A install. cmd fájl létrehozásához használjon egy egyszerű szövegszerkesztőt, például a Windows jegyzettömböt. Ha a Visual Studióval szövegfájlt hoz létre, és a kiterjesztést a. cmd fájlba módosítja, akkor a fájl továbbra is tartalmazhatja az UTF-8 bájtos sorrendet. Ez a jel hibát okozhat a parancsfájl első sorának futtatásakor. A hiba elkerüléséhez végezze el a parancsfájl első sorát, amely a byte Order Processing által kihagyható REM-utasítás. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Adja hozzá az install. cmd fájlt az egyes szerepkörökhöz   >  **meglévő elem** hozzáadása **megoldáskezelő** a témakör korábbi részében leírtak szerint. 

    A lépés befejezése után az összes szerepkörnek rendelkeznie kell a .NET Installer-fájllal és a install. cmd fájllal.

   ![Szerepkörök tartalma az összes fájllal][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Diagnosztika konfigurálása az indítási naplók blob Storage-ba történő átviteléhez
A telepítési hibák elhárításának leegyszerűsítése érdekében beállíthatja, hogy a Azure Diagnostics az indítási parancsfájl vagy a .NET-telepítő által az Azure Blob Storage-hoz generált naplófájlok átvitelére. Ezzel a megközelítéssel megtekintheti a naplókat úgy, hogy letölti a naplófájlokat a blob Storage-ból, ahelyett, hogy a Távoli asztalt a szerepkörbe helyezi.


A diagnosztika konfigurálásához nyissa meg a Diagnostics. wadcfgx fájlt, és adja hozzá a következő tartalmat a **címtárak** csomópontban: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Ez az XML úgy konfigurálja a diagnosztikát, hogy a **NETFXInstall** erőforrásban lévő naplófájlokat a **netfx-install** blob tárolóban lévő diagnosztikai Storage-fiókba vigye át.

## <a name="deploy-your-cloud-service"></a>A Cloud Service üzembe helyezése
A Cloud Service üzembe helyezésekor az indítási feladatok telepítik a .NET-keretrendszert, ha még nincs telepítve. A felhőalapú szolgáltatási szerepkörök *elfoglalt* állapotban vannak a keretrendszer telepítésekor. Ha a keretrendszer telepítése újraindítást igényel, előfordulhat, hogy a szolgáltatási szerepkörök is újraindulnak. 

## <a name="additional-resources"></a>További források
* [A .NET-keretrendszer telepítése][Installing the .NET Framework]
* [A .NET-keretrendszer telepített verzióinak meghatározása][How to: Determine Which .NET Framework Versions Are Installed]
* [A .NET-keretrendszer telepítésének hibaelhárítása][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



