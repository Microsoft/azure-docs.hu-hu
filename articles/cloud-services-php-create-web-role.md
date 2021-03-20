---
title: Azure-beli webes és feldolgozói szerepkörök létrehozása a PHP-hez
description: Útmutató a PHP-alapú webes és feldolgozói szerepkörök Azure Cloud Service-ben való létrehozásához és a PHP-futtatókörnyezet konfigurálásához.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95519846"
---
# <a name="create-php-web-and-worker-roles"></a>Webes és feldolgozói PHP-szerepkörök létrehozása

## <a name="overview"></a>Áttekintés

Ebből az útmutatóból megtudhatja, hogyan hozhat létre PHP-alapú webes vagy feldolgozói szerepköröket egy Windows-fejlesztési környezetben, kiválaszthatja a PHP adott verzióját az elérhető "beépített" verziók közül, módosíthatja a PHP-konfigurációt, engedélyezheti a bővítményeket, és végül üzembe helyezheti az Azure-ban. Azt is ismerteti, hogyan konfigurálhat webes vagy feldolgozói szerepkört egy olyan PHP-futtatókörnyezet használatára, amelyet Ön biztosít.

Az Azure három számítási modellt biztosít az alkalmazások futtatásához: Azure App Service, Azure Virtual Machines és Azure Cloud Services. Mindhárom modell támogatja a PHP-t. A webes és feldolgozói szerepköröket is tartalmazó Cloud Services *a szolgáltatásként szolgáló platformot (Pásti)* biztosítja. Egy felhőalapú szolgáltatásban a webes szerepkör egy dedikált Internet Information Services (IIS) webkiszolgálót biztosít az előtér-webalkalmazások üzemeltetéséhez. Egy feldolgozói szerepkör aszinkron, hosszan futó vagy örökös feladatokat futtathat a felhasználói interakciótól vagy bemenettől függetlenül.

További információ ezekről a lehetőségekről: az [Azure által biztosított számítási üzemeltetési beállítások](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>A PHP-hez készült Azure SDK letöltése

A [PHP-hez készült Azure SDK](https://github.com/Azure/azure-sdk-for-php) számos összetevőből áll. Ez a cikk kettőt használ: Azure PowerShell és az Azure-emulátorok. Ez a két összetevő a Microsoft webplatform-telepítő használatával telepíthető. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/) foglalkozó témakörben talál.

## <a name="create-a-cloud-services-project"></a>Cloud Services projekt létrehozása

A PHP webes vagy feldolgozói szerepkör létrehozásának első lépése egy Azure-szolgáltatási projekt létrehozása. Az Azure szolgáltatási projekt a webes és feldolgozói szerepkörök logikai tárolója, amely tartalmazza a projekt szolgáltatás- [definícióját (. csdef)] és a [szolgáltatás konfigurációs (. cscfg)] fájljait.

Új Azure-szolgáltatási projekt létrehozásához futtassa a Azure PowerShell rendszergazdaként, és hajtsa végre a következő parancsot:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Ezzel a paranccsal létrehozhat egy új könyvtárat ( `myProject` ), amelyhez webes és feldolgozói szerepköröket adhat hozzá.

## <a name="add-php-web-or-worker-roles"></a>PHP webes vagy feldolgozói Szerepkörök hozzáadása

PHP webes szerepkör projekthez való hozzáadásához futtassa a következő parancsot a projekt gyökérkönyvtárában:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Feldolgozói szerepkör esetén használja a következő parancsot:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> A `roleName` paraméter megadása nem kötelező. Ha nincs megadva, a rendszer automatikusan létrehozza a szerepkör nevét. Ekkor létrejön az első webes szerepkör, a második pedig a következő lesz: `WebRole1` `WebRole2` . Az első feldolgozói szerepkör a következő lesz, a második pedig a következő lesz: `WorkerRole1` `WorkerRole2` .
>
>

## <a name="use-your-own-php-runtime"></a>Saját PHP-futtatókörnyezet használata

Bizonyos esetekben előfordulhat, hogy a beépített PHP-futtatókörnyezet kiválasztása és a fent leírt módon történő konfigurálása helyett érdemes megadnia a saját PHP-futtatókörnyezetét. Használhatja például ugyanazt a PHP-futtatókörnyezetet egy webes vagy feldolgozói szerepkörben, amelyet a fejlesztési környezetben használ. Ez megkönnyíti annak biztosítását, hogy az alkalmazás ne változzon meg az éles környezetben.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Webes szerepkör konfigurálása saját PHP-futtatókörnyezet használatára

Ha egy webes szerepkört úgy szeretne konfigurálni, hogy az Ön által megadott PHP-futtatókörnyezetet használja, kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-szolgáltatási projektet, és adjon hozzá egy PHP-alapú webes szerepkört a témakörben korábban ismertetett módon.
2. Hozzon létre egy `php` mappát a `bin` webes szerepkör gyökérkönyvtárában található mappában, majd adja hozzá a PHP-futtatókörnyezetet (az összes bináris fájlt, a konfigurációs fájlokat, az almappákat stb.) a `php` mappához.
3. VÁLASZTHATÓ Ha a PHP-futtatókörnyezet a [Windowshoz készült Microsoft-illesztőprogramokat használja a SQL Serverhoz][sqlsrv drivers], akkor konfigurálnia kell a webes szerepkört, hogy telepítse a [SQL Server Native Client 2012][sql native client] -et a kiépítés során. Ehhez adja hozzá a [sqlncli.msi x64 telepítőt] a `bin` webes szerepkör gyökérkönyvtárában lévő mappához. A következő lépésben leírt indítási parancsfájl csendesen futtatja a telepítőt a szerepkör kiépítés után. Ha a PHP-futtatókörnyezet nem a Microsoft Windows-illesztőprogramokat használja a SQL Serverhoz, akkor a következő lépésben látható parancsfájlból eltávolíthatja a következő sort:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definiáljon egy indítási feladatot, amely úgy konfigurálja [Internet Information Services (IIS)][iis.net] , hogy a PHP-futtatókörnyezetet használja a lapokra vonatkozó kérelmek kezeléséhez `.php` . Ehhez nyissa meg a `setup_web.cmd` fájlt (a `bin` webes szerepkör gyökérkönyvtárában) a szövegszerkesztőben, és cserélje le a tartalmát a következő parancsfájlra:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adja hozzá az alkalmazás fájljait a webes szerepkör gyökérkönyvtárához. Ez lesz a webkiszolgáló gyökérkönyvtára.
6. Tegye közzé az alkalmazást az alábbi, az [alkalmazás közzététele](#publish-your-application) című szakaszban leírtak szerint.

> [!NOTE]
> A `download.ps1` parancsfájl (a `bin` webes szerepkör gyökérkönyvtárában) a saját php-futtatókörnyezet használatára vonatkozó fenti lépéseket követve törölhető.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Feldolgozói szerepkör konfigurálása saját PHP-futtatókörnyezet használatára

Ha egy feldolgozói szerepkört úgy szeretne konfigurálni, hogy az Ön által megadott PHP-futtatókörnyezetet használja, kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure-szolgáltatási projektet, és adjon hozzá egy PHP-feldolgozói szerepkört a témakörben korábban ismertetett módon.
2. Hozzon létre egy `php` mappát a feldolgozói szerepkör gyökérkönyvtárában, majd adja hozzá a PHP-futtatókörnyezetet (az összes bináris fájlt, a konfigurációs fájlokat, az almappákat stb.) a `php` mappához.
3. VÁLASZTHATÓ Ha a PHP-futtatókörnyezet [Microsoft-illesztőprogramokat használ a PHP-hez a SQL Serverhoz][sqlsrv drivers], akkor konfigurálnia kell a feldolgozói szerepkört, hogy telepítse a [SQL Server Native Client 2012][sql native client] -et a kiépítés után. Ehhez adja hozzá a [sqlncli.msi x64 telepítőt] a feldolgozói szerepkör gyökérkönyvtárához. A következő lépésben leírt indítási parancsfájl csendesen futtatja a telepítőt a szerepkör kiépítés után. Ha a PHP-futtatókörnyezet nem a Microsoft Windows-illesztőprogramokat használja a SQL Serverhoz, akkor a következő lépésben látható parancsfájlból eltávolíthatja a következő sort:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definiáljon egy indítási feladatot, amely hozzáadja a `php.exe` végrehajtható fájlt a feldolgozói SZEREPKÖR PATH környezeti változóhoz a szerepkör kiépítés során. Ehhez nyissa meg a `setup_worker.cmd` fájlt (a feldolgozói szerepkör gyökérkönyvtárában) egy szövegszerkesztőben, és cserélje le a tartalmát a következő parancsfájlra:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adja hozzá az alkalmazás fájljait a feldolgozói szerepkör gyökérkönyvtárához.
6. Tegye közzé az alkalmazást az alábbi, az [alkalmazás közzététele](#publish-your-application) című szakaszban leírtak szerint.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Alkalmazás futtatása a számítási és tárolási emulátorokban

Az Azure-emulátorok olyan helyi környezetet biztosítanak, amelyben tesztelheti az Azure-alkalmazást a felhőbe való üzembe helyezés előtt. Vannak különbségek az emulátorok és az Azure-környezet között. Ennek jobb megismeréséhez tekintse meg [a fejlesztéshez és teszteléshez az Azure Storage Emulator használatát](storage/common/storage-use-emulator.md)ismertető témakört.

Vegye figyelembe, hogy a PHP-t helyileg kell telepíteni a Compute Emulator használatához. A Compute Emulator a helyi PHP-telepítést fogja használni az alkalmazás futtatásához.

A projekt emulátorokban való futtatásához hajtsa végre a következő parancsot a projekt gyökérkönyvtárában:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

A következőhöz hasonló kimenet jelenik meg:

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Az emulátorban futó alkalmazást úgy tekintheti meg, ha megnyit egy webböngészőt, és megkeresi a kimenetben megjelenített helyi címeket ( `http://127.0.0.1:81` a fenti példában szereplő kimenetben).

Az emulátorok leállításához hajtsa végre a következő parancsot:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Az alkalmazás közzététele

Az alkalmazás közzétételéhez először importálnia kell a közzétételi beállításokat az [import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) parancsmag használatával. Ezután közzéteheti az alkalmazást a [publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) parancsmag használatával. A bejelentkezéssel kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

## <a name="next-steps"></a>Következő lépések

További információ: [php fejlesztői központ](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[szolgáltatás definíciója (. csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[szolgáltatás konfigurációja (. cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64-es telepítő]: https://go.microsoft.com/fwlink/?LinkID=239648