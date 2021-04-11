---
title: Ismerkedés a Python és az Azure Cloud Services (klasszikus) használatába | Microsoft Docs
description: Azure felhőszolgáltatások, például webes és feldolgozói szerepkörök létrehozása a Python Tools for Visual Studio eszközzel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2822f719928515efc70eeed3d7c182e347627418
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045518"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Python webes és feldolgozói szerepkörök a Visual Studio eszközzel

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk a Python webes és feldolgozói szerepkörök [Python Tools for Visual Studio][Python Tools for Visual Studio] eszközben történő használatát ismerteti. Megtudhatja, hogyan hozhat létre és telepíthet egy Pythont használó alapszintű felhőszolgáltatást a Visual Studióval.

## <a name="prerequisites"></a>Előfeltételek
* [Visual Studio 2013, 2015 vagy 2017](https://www.visualstudio.com/)
* [Python Tools for Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK Tools for VS 2013][Azure SDK Tools for VS 2013] vagy  
[Azure SDK Tools for VS 2015][Azure SDK Tools for VS 2015] vagy  
[Azure SDK Tools for VS 2017][Azure SDK Tools for VS 2017]
* [Python 2,7 32-bit][Python 2.7 32-bit] vagy [Python 3,8 32-bit][Python 3.8 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Mik a Python webes és feldolgozói szerepkörök?
Az Azure három számítási modellt biztosít az alkalmazások futtatásához: [Web Apps szolgáltatás az Azure App Service-ben][execution model-web sites], [Azure Virtual Machines][execution model-vms] és [Azure Cloud Services][execution model-cloud services]. Mindhárom modell támogatja a Python eszközt. A webes és feldolgozói szerepköröket is tartalmazó Cloud Services *platformszolgáltatást (PaaS)* kínál. A felhőszolgáltatásban a webes szerepkör egy külön Internet Information Services (IIS) webkiszolgálót biztosít az előtéri webalkalmazásokhoz, míg a feldolgozói szerepkör aszinkron, hosszan futó vagy folyamatos feladatokat futtat függetlenül a felhasználói műveletektől vagy bemenettől.

További információ: [Mi az a Cloud Service?].

> [!NOTE]
> *Egyszerű webhelyet szeretne készíteni?*
> Ha egy egyszerű webhely előterét kívánja futtatni, fontolja meg az Azure App Service egyszerűsített Web Apps szolgáltatásának használatát. Könnyedén frissíthet Cloud Service szolgáltatásra, ha a webhely növekszik és a követelmények változnak. A [Python fejlesztői központban](https://azure.microsoft.com/develop/python/) találhat az Azure App Service Web Apps szolgáltatásának fejlesztését ismertető cikkeket.
> <br />
> 
> 

## <a name="project-creation"></a>Projekt létrehozása
A Visual Studio eszközben válassza az **Azure Cloud Service** lehetőséget az **Új projekt** párbeszédpanelen a **Python** területen.

![Új projekt párbeszédpanel](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Az Azure Cloud Service varázslóban létrehozhat új webes és feldolgozói szerepköröket.

![Azure Cloud Service párbeszédpanel](./media/cloud-services-python-ptvs/new-service-wizard.png)

A feldolgozói szerepkör sablonja sablonkóddal rendelkezik, hogy csatlakozhasson egy Azure-tárfiókhoz vagy az Azure Service Bus szolgáltatáshoz.

![Cloud Service megoldás](./media/cloud-services-python-ptvs/worker.png)

Egy meglévő felhőszolgáltatáshoz bármikor hozzáadhat webes vagy feldolgozói szerepköröket.  Hozzáadhat a megoldásában már meglévő projekteket, vagy létrehozhat újakat.

![Szerepkör hozzáadása parancs](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

A felhőszolgáltatás tartalmazhat különböző nyelveken kialakított szerepköröket.  Például kialakíthat egy Python webes szerepkört Django, Python vagy C# feldolgozói szerepkör használatával.  A szerepkörök között egyszerűen kommunikálhat Service Bus üzenetsorok vagy tárolási sorok használatával.

## <a name="install-python-on-the-cloud-service"></a>A Python telepítése a felhőszolgáltatáson
> [!WARNING]
> A Visual Studióval települt telepítési parancsfájlok (a cikk utolsó frissítésekor) erre a célra nem használhatók. A megoldást ez a szakasz ismerteti.
> 
> 

A telepítési szkriptek fő problémája, hogy nem telepítik a Pythont. Először határozzon meg két [indítási feladatot](cloud-services-startup-tasks.md) a [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fájlban. Az első feladat (**PrepPython.ps1**) letölti és telepíti a Python-futtatókörnyezetet. A második feladat (**PipInstaller.ps1**) a pipet futtatja a vonatkozó függőségek telepítéséhez.

A következő parancsfájlok lettek megírva a Python 3,8-es verziójára. Ha a Python 2.x verzióját szeretné használni, állítsa a **PYTHON2** változófájlt **be** értékre a két indítási és a futtatókörnyezeti feladathoz: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

A **PYTHON2** és a **PYPATH** változókat hozzá kell adnia a feldolgozó indítási feladatához. A **PYPATH** változót csak akkor használja a rendszer, ha a **PYTHON2** változó **be** értékű.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Mintául szolgáló ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Ezután hozza létre a **PrepPython.ps1** és a **PipInstaller.ps1** fájlokat a szerepkör **./bin** mappájában.

#### <a name="preppythonps1"></a>PrepPython.ps1
Ez a parancsfájl telepíti a Pythont. Ha a **PYTHON2** környezeti változó **be értékre van állítva, akkor** a Python 2,7 telepítve van, ellenkező esetben a Python 3,8 telepítve van.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.8.8/python-3.8.8-amd64.exe"
        $outFile = "${env:TEMP}\python-3.8.8-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.18/python-2.7.18.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.18.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Ez a parancsfájl meghívja a pipet, és telepíti a **requirements.txt** fájlban található összes függőséget. Ha a **PYTHON2** környezeti változó be értékre van állítva, akkor a rendszer **a** Python 2,7-et használja, ellenkező esetben a Python 3,8-et használja.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>A LaunchWorker.ps1 módosítása
> [!NOTE]
> **Feldolgozói szerepkörű** projekt esetében az indítófájl végrehajtásához **LauncherWorker.ps1** fájl szükséges. **Webes szerepkörű** projekt esetében az indítófájl definiálása nem ebben a fájlban, hanem a projekt tulajdonságaiban történik.
> 
> 

A **bin\LaunchWorker.ps1** eredetileg azért jött létre, hogy előkészítési munkákat végezzen, de nem igazán működik. Cserélje le a fájl tartalmát a következő parancsfájllal.

A parancsfájl meghívja a **worker.py** fájlt a Python-projektből. Ha a **PYTHON2** környezeti változó be értékre van állítva, akkor a rendszer **a** Python 2,7-et használja, ellenkező esetben a Python 3,8-et használja.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
A Visual Studio-sablonoknak létre kellett hozniuk egy **ps.cmd** fájlt a **./bin** mappában. Ez a héjparancsfájl kiemeli a fenti PowerShell burkoló parancsfájlokat, és a meghívott PowerShell burkoló neve alapján biztosít naplózást. Ha a fájl nem jött létre, itt láthatja, minek kéne benne lennie. 

```cmd
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Helyi futtatás
Ha a felhőszolgáltatási projektet állítja be kiindulási projektnek, és lenyomja az F5 billentyűt, a felhőszolgáltatás a helyi Azure-emulátorban fut.

Bár a PVTS támogatja az emulátorban való indítást, a hibakeresés (például a töréspontok keresése) nem működik.

A webes és feldolgozói szerepkörök hibakereséséhez állítsa be a szerepkörprojektet kiindulási projektként, és végezzen azon hibakeresést  Beállíthat több kiindulási projektet is.  Kattintson a jobb gombbal a megoldásra, majd válassza az **Indítási projektek beállítása** lehetőséget.

![A megoldás kiindulási projektjének tulajdonságai](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Közzététel az Azure-ban
A közzétételhez kattintson a jobb gombbal a felhőszolgáltatási projektre a megoldásban, majd kattintson a **Közzététel** gombra.

![Bejelentkezés a Microsoft Azure közzétételhez](./media/cloud-services-python-ptvs/publish-sign-in.png)

Kövesse a varázsló utasításait. Szükség esetén engedélyezze a távoli asztal használatát. A távoli asztal akkor hasznos, amikor valamilyen okból hibakeresést kell végeznie.

Ha elkészült a beállítások konfigurálásával, kattintson a **Közzététel** gombra.

Egy folyamatjelző jelenik meg a kimeneti ablakban, majd megnyílik a Microsoft Azure-tevékenységnapló ablak.

![Microsoft Azure tevékenységnapló ablak](./media/cloud-services-python-ptvs/publish-activity-log.png)

Az üzembe helyezés néhány percet vesz igénybe, majd a webes és/vagy feldolgozói szerepkörök el is indulnak az Azure-ban!

### <a name="investigate-logs"></a>Naplók vizsgálata
A felhőszolgáltatás virtuális gépének elindulása és a Python telepítése után áttekintheti, hogy vannak-e a naplókban hibaüzenetek. Ezek a naplók a **C:\Resources\Directory \\ {role} \LogFiles** mappában találhatók. A **PrepPython.err.txt** fájlban legalább egy hiba található, amely akkor keletkezik, amikor a szkript megpróbálja észlelni, hogy a Python telepítve van-e. Az is előfordulhat, hogy a **PipInstaller.err.txt** jelzi a pip elavult verziójának használatát.

## <a name="next-steps"></a>Következő lépések
A PVTS dokumentációban további információkat találhat a webes és feldolgozói szerepkörök használatáról Python Tools for Visual Studio eszközben:

* [Cloud Service-projektek][Cloud Service Projects]

Ha további részletekre kíváncsi az Azure-szolgáltatások webes és feldolgozói szerepkörökből történő használatáról, például az Azure Storage vagy a Service Bus használatáról, olvassa el a következő cikkeket:

* [Blob Service][Blob Service]
* [Table Service][Table Service]
* [Queue szolgáltatás][Queue Service]
* [Service Bus várólisták][Service Bus Queues]
* [Service Bus témakörök][Service Bus Topics]

<!--Link references-->

[Mi az a Cloud Service?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: /visualstudio/python/
[Cloud Service Projects]: /visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.8 32-bit]: https://www.python.org/downloads/