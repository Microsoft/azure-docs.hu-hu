---
title: Gyakori indítási feladatok a Cloud Serviceshoz (klasszikus) | Microsoft Docs
description: Néhány példát mutat be a Cloud Services webes szerepkörben vagy feldolgozói szerepkörben esetlegesen elvégzendő gyakori indítási feladatokra.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f55b225e615a3e7a5fbcf56b405054883d3b5413
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741196"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Gyakori Cloud Service-(klasszikus) indítási feladatok

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk néhány példát ismertet a Cloud Service-ben elvégzendő gyakori indítási feladatokra. Az indítási feladatokkal műveleteket hajthat végre a szerepkörök elkezdése előtt. A végrehajtani kívánt műveletek közé tartozik például az összetevők telepítése, a COM-összetevők regisztrálása, a beállításkulcsok beállítása vagy a hosszú ideig futó folyamat elindítása. 

[Ebből a cikkből](cloud-services-startup-tasks.md) megtudhatja, hogyan működnek az indítási feladatok, és konkrétan hogyan hozhatók létre az indítási feladatot meghatározó bejegyzések.

> [!NOTE]
> Az indítási feladatok nem alkalmazhatók Virtual Machinesra, csak a Cloud Service webes és feldolgozói szerepköreire.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Környezeti változók definiálása a szerepkör elindítása előtt
Ha egy adott feladathoz meghatározott környezeti változókra van szüksége, használja a [Task] elem [környezeti] elemét.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

A változók [érvényes Azure XPath-értéket](cloud-services-role-config-xpath.md) is használhatnak az üzemelő példányra vonatkozó információkra való hivatkozáshoz. Az attribútum használata helyett `value` adjon meg egy [RoleInstanceValue] gyermek elemet.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>AZ IIS-indítás konfigurálása AppCmd.exe
Az [AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) parancssori eszköz használatával az Azure-beli indításkor kezelheti az IIS-beállításokat. *AppCmd.exe* kényelmes, parancssori hozzáférést biztosít a konfigurációs beállításokhoz az Azure-beli indítási feladatokban való használathoz. A *AppCmd.exe* használatával a webhely beállításai hozzáadhatók, módosíthatók vagy eltávolíthatók az alkalmazásokhoz és a webhelyekhez.

Azonban van néhány dolog, amit a *AppCmd.exe* indítási feladatként való használatakor kell megnéznie:

* Az indítási feladatok többször is futtathatók az újraindítások között. Például ha egy szerepkör újrahasznosításra kerül.
* Ha egy *AppCmd.exe* műveletet többször hajtanak végre, a hiba léphet fel. Előfordulhat például, hogy egy szakaszt kétszer kell hozzáadnia *Web.config* egy hibát eredményez.
* Az indítási feladatok meghiúsulnak, ha nullától eltérő kilépési kódot vagy **errorlevel** értéket adnak vissza. Ha például *AppCmd.exe* hibát generál.

Javasoljuk, hogy a *AppCmd.exe* meghívása után ellenőrizze az **errorlevel** -t, ami könnyen elvégezhető, ha egy *. cmd* fájllal becsomagolja a hívást a *AppCmd.exeba* . Ha egy ismert **errorlevel** -választ érzékel, figyelmen kívül hagyhatja, vagy visszaküldheti azt.

A *AppCmd.exe* által visszaadott errorlevel a Winerror. h fájlban szerepel, és az [MSDN](/windows/desktop/Debug/system-error-codes--0-499-)-ben is látható.

### <a name="example-of-managing-the-error-level"></a>Példa a hiba szintjének kezelésére
Ez a példa egy tömörítési szakaszt és egy, a JSON-hoz tartozó tömörítési bejegyzést tartalmaz a *Web.config* fájlhoz, a hibakezelés és a naplózás használatával.

Itt jelennek meg a [ServiceDefinition. csdef] fájl megfelelő részei, amelyek közé tartozik a [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) attribútum beállítása, hogy `elevated` *AppCmd.exe* megfelelő engedélyeket adjon a *Web.config* fájlban lévő beállítások módosításához:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

A *Startup. cmd* batch-fájl a *AppCmd.exe* használatával felvesz egy tömörítési szakaszt és egy, a JSON-hoz tartozó tömörítési bejegyzést a *Web.config* fájlba. A várt 183-es **errorlevel** értéke nulla a VERIFY.EXE parancssori program használatával. Váratlan errorlevelek vannak naplózva StartupErrorLog.txtba.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Tűzfalszabályok hozzáadása
Az Azure-ban gyakorlatilag két tűzfal van. Az első tűzfal vezérli a virtuális gép és a külső világ közötti kapcsolatokat. Ezt a tűzfalat a [ServiceDefinition. csdef] fájl [végpontok] eleme vezérli.

A második tűzfal a virtuális gép és a virtuális gép folyamatai közötti kapcsolatokat vezérli. Ezt a tűzfalat a parancssori eszköz is szabályozhatja `netsh advfirewall firewall` .

Az Azure tűzfal-szabályokat hoz létre a szerepkörökön belül elindított folyamatokhoz. Például egy szolgáltatás vagy program indításakor az Azure automatikusan létrehozza a szükséges tűzfalszabályok használatát, hogy a szolgáltatás kommunikáljon az internettel. Ha azonban olyan szolgáltatást hoz létre, amelyet a szerepkörön kívüli folyamat (például egy COM+ szolgáltatás vagy egy Windows ütemezett feladat) indít el, akkor manuálisan kell létrehoznia egy tűzfalszabályet, hogy engedélyezze a hozzáférést a szolgáltatáshoz. Ezek a tűzfalszabályok indítási feladat használatával hozhatók létre.

Egy tűzfalszabály létrehozására szolgáló indítási feladatnak egy **emelt szintű** [executionContext][feladattal] kell rendelkeznie. Adja hozzá a következő indítási feladatot a [ServiceDefinition. csdef] fájlhoz.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

A tűzfalszabály hozzáadásához az `netsh advfirewall firewall` indítási batch-fájlban a megfelelő parancsokat kell használnia. Ebben a példában az indítási feladathoz a 80-es TCP-port biztonsága és titkosítása szükséges.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Adott IP-cím blokkolása
Az IIS- **web.config** fájl módosításával korlátozhatja az Azure-beli webes szerepkörök hozzáférését a megadott IP-címek készletéhez. Emellett egy olyan parancsfájlt is kell használnia, amely feloldja a **ApplicationHost.config** fájl **ipSecurity** szakaszát.

A **ApplicationHost.config** fájl **ipSecurity** szakaszának feloldásához hozzon létre egy olyan parancsfájlt, amely a szerepkör indításakor fut. Hozzon létre egy mappát az **Indítás** nevű webes szerepkör gyökérszintű szintjén, és ezen a mappában hozzon létre egy **Startup. cmd** nevű batch-fájlt. Vegye fel ezt a fájlt a Visual Studio-projektbe, és állítsa be úgy a tulajdonságokat, hogy **mindig** a csomag része legyen.

Adja hozzá a következő indítási feladatot a [ServiceDefinition. csdef] fájlhoz.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adja hozzá ezt a parancsot a **Startup. cmd** fájlhoz:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Ez a feladat azt eredményezi, hogy az **indítási. cmd** batch-fájl a webes szerepkör inicializálása során mindig fut, így biztosítva, hogy a szükséges **ipSecurity** szakasz fel legyen oldva.

Végül módosítsa a [System. webserver szakaszt](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a webes szerepkör **web.config** fájljában a hozzáféréshez megadott IP-címek listájának hozzáadásához, ahogy az a következő példában látható:

Ez a minta-konfiguráció **lehetővé teszi** , hogy az összes IP-cím hozzáférhessen a kiszolgálóhoz, kivéve a két definiált

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Ez a minta-konfiguráció **letiltja** az összes IP-címet a kiszolgálóhoz való hozzáféréshez, kivéve a két definiált értéket.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>PowerShell indítási feladat létrehozása
A Windows PowerShell-parancsfájlok nem hívhatók közvetlenül az [ServiceDefinition. csdef] fájlból, de meghívhatók egy indítási batch-fájlból.

A PowerShell (alapértelmezés szerint) nem futtat aláíratlan parancsfájlokat. Ha nem írja alá a parancsfájlt, a PowerShellt úgy kell konfigurálnia, hogy aláíratlan parancsfájlokat futtasson. Az aláíratlan parancsfájlok futtatásához a **ExecutionPolicy** **korlátozás** nélküli értékre kell állítani. A használt **ExecutionPolicy** -beállítás a Windows PowerShell verzióján alapul.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Ha olyan vendég operációs rendszert használ, amely a PowerShell 2,0-es vagy 1,0-OS verzióját futtatja, akkor a 2. verziót kényszerítheti, és ha nem érhető el, használja az 1. verziót.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Helyi tárolóban lévő fájlok létrehozása indítási feladatból
A helyi tárolási erőforrással olyan fájlokat tárolhat, amelyeket az alkalmazás által később elérhető indítási feladat hozott létre.

A helyi tárolási erőforrás létrehozásához adjon hozzá egy [LocalResources] szakaszt a [ServiceDefinition. csdef] fájlhoz, majd adja hozzá a [LocalStorage] Child elemet. Adjon egyedi nevet és megfelelő méretet az indítási feladatnak a helyi tárolási erőforrás számára.

Ha helyi tárolási erőforrást szeretne használni az indítási feladatban, létre kell hoznia egy környezeti változót, amely a helyi tárolási erőforrás helyére hivatkozik. Ezután az indítási feladat és az alkalmazás képes fájlok olvasására és írására a helyi tároló-erőforrásba.

Az **ServiceDefinition. csdef** fájl megfelelő fejezetei itt láthatók:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

A **Startup. cmd** kötegfájl például a **PathToStartupStorage** környezeti változó használatával hozza létre a fájlt **MyTest.txt** a helyi tároló helyén.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Az Azure SDK-ból a [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metódus használatával férhet hozzá a helyi tároló mappához.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Futtatás az emulátorban vagy a felhőben
Az indítási feladat különböző lépéseket hajthat végre a felhőben való működéshez képest, ha az Compute Emulator van. Előfordulhat például, hogy az emulátorban való futtatáskor csak az SQL-adatainak friss példányát szeretné használni. Vagy érdemes lehet olyan teljesítmény-optimalizálást végezni a felhőhöz, amelyet nem kell tennie az emulátorban való futtatáskor.

A Compute Emulator és a felhő különböző műveleteinek elvégzésére a [ServiceDefinition. csdef] fájlban lévő környezeti változó létrehozásával van lehetőség. Ezt követően tesztelje a környezeti változót az indítási feladatban szereplő értékhez.

A környezeti változó létrehozásához adja hozzá a [] / [RoleInstanceValue] elem változót, és hozzon létre egy XPath-értéket `/RoleEnvironment/Deployment/@emulated` . A **(z)% ComputeEmulatorRunning%** környezeti változó értéke `true` , ha a Compute Emulator fut, és `false` Amikor a felhőben fut.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

A feladat most megtekintheti a **% ComputeEmulatorRunning%** környezeti változót úgy, hogy különböző műveleteket hajtson végre, attól függően, hogy a szerepkör a felhőben vagy az emulátorban fut-e. Itt található egy. cmd rendszerhéj-parancsfájl, amely a környezeti változót ellenőrzi.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Annak észlelése, hogy a feladat már fut
A szerepkör újraindítás nélkül újraindulhat, ami azt eredményezi, hogy az indítási feladatok újra futnak. Nincs jelölő, amely azt jelzi, hogy egy feladat már futott a gazdagépen futó virtuális gépen. Előfordulhat, hogy vannak olyan feladatai, amelyeken nem számít, hogy többször futnak. Előfordulhat azonban, hogy olyan helyzetbe kell kerülnie, amikor egy adott feladatot többször kell futtatni.

A feladat futtatásának legegyszerűbb módja, ha a feladat sikeressége után egy fájlt hoz létre a **% temp%** mappában, és megkeresi a feladat elején. Itt látható egy minta cmd rendszerhéj-szkript, amely az Ön számára.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>A feladat ajánlott eljárásai
Íme néhány ajánlott eljárás, amelyet a feladatok webes vagy feldolgozói szerepkörhöz való konfigurálásakor kell követni.

### <a name="always-log-startup-activities"></a>Az indítási tevékenységek naplózása mindig
A Visual Studio nem biztosít hibakeresőt a Batch-fájlok beléptetéséhez, ezért jó, ha a lehető legtöbb adatmennyiséget szeretné lekérni a Batch-fájlok működéséről. A Batch-fájlok ( **StdOut** és **stderr**) kimenetének naplózásával fontos információkat adhat meg a Batch-fájlok hibakereséséhez és javításához. Ha a **(z)% Temp%** környezeti változó által rámutatott könyvtárban lévő StartupLog.txt fájlra szeretné naplózni az **StdOut** és a **stderr** , adja hozzá a `>>  "%TEMP%\\StartupLog.txt" 2>&1` bejelentkezni kívánt sorok végéhez a szöveget. Például a **(z)% PathToApp1Install%** könyvtárban lévő setup.exe végrehajtásához: `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

Az XML leegyszerűsítése érdekében létrehozhat egy burkoló *cmd* -fájlt, amely az összes indítási feladatot meghívja a naplózással, és biztosítja, hogy az egyes alárendelt feladatok ugyanazokat a környezeti változókat tudják megosztva.

Előfordulhat, hogy bosszantó, ha `>> "%TEMP%\StartupLog.txt" 2>&1` az egyes indítási feladatok végén használja. A feladatok naplózását a naplózást kezelő burkoló létrehozásával kényszerítheti ki. Ez a burkoló hívja meg a futtatni kívánt valódi batch-fájlt. A cél batch-fájl minden kimenete át lesz irányítva a *Startuplog.txt* fájlra.

Az alábbi példa bemutatja, hogyan irányíthatja át az összes kimenetet egy indítási batch-fájlból. Ebben a példában a ServerDefinition. csdef fájl egy indítási feladatot hoz létre, amely meghívja a *logwrap. cmd* fájlt. a *logwrap. cmd* meghívja a *Startup2. cmd* fájlt, és átirányítja a kimenetet a következőre: **% temp% \\StartupLog.txt**.

ServiceDefinition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2. cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Minta kimenet a **StartupLog.txt** fájlban:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> A **StartupLog.txt** fájl a *C:\Resources\temp \\ {role Identifier} \RoleTemp* mappában található.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>ExecutionContext megfelelő beállítása indítási feladatokhoz
Állítsa be a megfelelő jogosultságokat az indítási feladathoz. Időnként előfordulhat, hogy a rendszerindítási feladatok futtatásához emelt szintű jogosultságok szükségesek, bár a szerepkör normál jogosultságokkal fut.

A [executionContext][Task] attribútum az indítási feladat jogosultsági szintjét állítja be. Az `executionContext="limited"` azt jelenti, hogy az indítási tevékenység a szerepkörével megegyező jogosultsági szinttel rendelkezik. `executionContext="elevated"`Az azt jelenti, hogy az indítási feladat rendszergazdai jogosultságokkal rendelkezik, így az indítási feladat rendszergazdai feladatokat hajthat végre anélkül, hogy rendszergazdai jogosultságokat adna a szerepkörhöz.

Emelt szintű jogosultságokat igénylő indítási feladat például az IIS konfigurálását **AppCmd.exe** használó indítási feladat. **AppCmd.exe** szükséges `executionContext="elevated"` .

### <a name="use-the-appropriate-tasktype"></a>A megfelelő taskType használata
A [taskType][feladat] attribútuma határozza meg az indítási feladat végrehajtásának módját. Három érték létezik: **egyszerű**, **háttér** és **előtér**. A háttér-és előtér-feladatok aszinkron módon vannak elindítva, és az egyszerű feladatok egyszerre lesznek végrehajtva.

Az **egyszerű** indítási feladatokkal beállíthatja, hogy a tevékenységek milyen sorrendben fussanak a feladatok a ServiceDefinition. csdef fájlban való listájának sorrendjében. Ha egy **egyszerű** feladat nem nulla kilépési kóddal végződik, akkor az indítási eljárás leáll, és a szerepkör nem indul el.

A **háttérben** futó indítási feladatok és az **előtér** -indítási feladatok közötti különbség az, hogy az **előtér** -feladatok megőrzik a szerepkört, amíg az **előtéri** feladat be nem fejeződik. Ez azt is jelenti, hogy ha az **előtéri** feladat lefagy vagy összeomlik, a szerepkör nem indul újra, amíg az **előtér** -feladat be nem zárul. Emiatt a **háttérben futó** feladatok aszinkron indítási feladatokhoz ajánlottak, kivéve, ha erre az **előtér** -feladatnak szüksége van.

### <a name="end-batch-files-with-exit-b-0"></a>A Batch-fájlok kilépési/B 0
A szerepkör csak akkor indul el, ha az egyszerű indítási feladat **errorlevel** értéke nulla. Nem minden program helyesen állította be az **errorlevel** (kilépési kódot), így a Batch-fájlnak a megfelelő futtatása után kell végződnie `EXIT /B 0` .

Az `EXIT /B 0` indítási batch-fájl végén hiányzik egy olyan gyakori ok, amely nem indul el.

> [!NOTE]
> Észrevettem, hogy a beágyazott batch-fájlok időnként nem válaszolnak a paraméter használatakor `/B` . Érdemes lehet meggyőződni arról, hogy ez a probléma nem fordul elő, ha egy másik batch-fájl meghívja az aktuális batch-fájlt, például ha a [log burkolót](#always-log-startup-activities)használja. Ebben az esetben kihagyhatja a `/B` paramétert.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Az indítási feladatok többszöri futtatása várható
Nem minden szerepkör-újraindítást tartalmaz, de az összes szerepkör-újraindítási művelet az indítási feladatok futtatását is magában foglalja. Ez azt jelenti, hogy az indítási feladatoknak a problémák nélkül többször is futtatniuk kell az újraindítások között. Ezt az [előző szakaszban](#detect-that-your-task-has-already-run)tárgyaljuk.

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Helyi tároló használata a szerepkörben elérhető fájlok tárolására
Ha olyan fájlt szeretne másolni vagy létrehozni az indítási feladatban, amely elérhető a szerepkör számára, akkor azt a fájlt helyi tárolóba kell helyeznie. Lásd az [előző szakaszt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Következő lépések
A Cloud [Service-modell és-csomag](cloud-services-model-and-package.md) áttekintése

További információ a [feladatok](cloud-services-startup-tasks.md) működéséről.

[Hozza létre és telepítse](cloud-services-how-to-create-deploy-portal.md) a Cloud Service-csomagot.

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Feladat]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Environment]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Változó]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Végpontok]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue