---
title: Egyéni tároló konfigurálása
description: Megtudhatja, hogyan konfigurálhatja az egyéni tárolókat a Azure App Service. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: article
ms.date: 02/23/2021
ms.custom: devx-track-azurepowershell
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 48d2eeec1bdb1b9b4a393b4116092f043716077c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832032"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Egyéni tároló konfigurálása az Azure App Service-hez

Ez a cikk bemutatja, hogyan konfigurálhat egyéni tárolókat a Azure App Service.

::: zone pivot="container-windows"

Ez az útmutató a Windows-alkalmazások tárolóba való telepítésének alapvető fogalmait és utasításait App Service. Ha még soha nem használt Azure App Service az egyéni [tárolók rövid útmutatóját](quickstart-custom-container.md) és [](tutorial-custom-container.md) oktatóanyagát.

::: zone-end

::: zone pivot="container-linux"

Ez az útmutató a Linux-alkalmazások tárolóba való telepítésének alapvető fogalmait és utasításait App Service. Ha még soha nem használt Azure App Service az egyéni [tárolók rövid útmutatóját](quickstart-custom-container.md) és [](tutorial-custom-container.md) oktatóanyagát. Emellett egy többtárolós [alkalmazás gyorsútmutatója és](quickstart-multi-container.md) oktatóanyaga [is van.](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Támogatott szülő rendszerképek

Az egyéni Windows-rendszerképhez ki kell választania a megfelelő szülő rendszerképet [(alap rendszerképet)](https://docs.docker.com/develop/develop-images/baseimages/) a kívánt keretrendszerhez:

- Az .NET-keretrendszer üzembe helyezéséhez használjon a Windows Server Core [hosszú távú karbantartási csatorna (LTSC) kiadásán alapuló szülő](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) rendszerképet. 
- .NET Core-alkalmazások telepítéséhez használjon a Windows Server Nano féléves karbantartási [csatorna (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kiadásán alapuló szülő rendszerképet. 

Az alkalmazás indításakor a szülőrendszerkép letöltése hosszabb időbe telhet. Az indítási időt azonban lecsökkentheti az alábbi, az Azure App Service-ben már gyorsítótárazott szülőrendszerképek egyikének használatával:

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Egyéni tároló Docker-rendszerképének módosítása

Ha egy meglévő egyéni tárolóalkalmazást az aktuális Docker-rendszerképről egy új rendszerképre módosít, használja a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Rendszerkép használata privát beállításjegyzékből

Ha privát beállításjegyzékből(például adatbázisból) származó rendszerképet Azure Container Registry, futtassa a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

A és a számára meg kell megadnia a privát *\<username>* *\<password>* beállításjegyzék-fiók bejelentkezési hitelesítő adatait.

## <a name="i-dont-see-the-updated-container"></a>Nem látom a frissített tárolót

Ha úgy módosítja a Docker-tároló beállításait, hogy egy új tárolóra mutasson, eltarthat néhány percig, amíg az alkalmazás http-kéréseket fog kihozni az új tárolóból. Az új tároló lekérése és igénylése közben a App Service továbbra is kiszolgálja a régi tárolótól származó kéréseket. Csak akkor indítja el a kérések küldését, ha az új tároló elindult, és készen áll App Service a kérések fogadására.

## <a name="how-container-images-are-stored"></a>A tároló rendszerképének tárolása

Amikor először futtat egyéni Docker-rendszerképet a App Service, App Service le az összes `docker pull` rendszerképréteget. Ezek a rétegek lemezen vannak tárolva, például ha helyszíni Docker-et használ. Az alkalmazás minden újraindításakor a App Service, de csak a módosított `docker pull` rétegeket húzza le. Ha nem történt módosítás, a App Service a helyi lemez meglévő rétegeit használja.

Ha az alkalmazás bármilyen okból módosítja a számítási példányokat, például a tarifacsomagok fel- és leméretezését, a App Service le kell húznia az összes réteget. Ugyanez igaz akkor is, ha további példányok hozzáadásához skáláz fel horizontálisan. Vannak olyan ritka esetek is, amikor az alkalmazáspéldányok skálázható művelet nélkül változhatnak.

## <a name="configure-port-number"></a>Portszám konfigurálása

Alapértelmezés szerint a App Service feltételezi, hogy az egyéni tároló a 80-as porton figyel. Ha a tároló egy másik portot figyel, állítsa be az alkalmazásbeállítást a `WEBSITES_PORT` App Service alkalmazásban. A következővel állíthatja [be: Cloud Shell.](https://shell.azure.com) A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service a tároló jelenleg csak egy portot tesz elérhetővé a HTTP-kérések számára. 

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló használhat olyan környezeti változókat, amelyeket külsőleg kell szolgáltatni. A következőn keresztül használhatja [Cloud Shell.](https://shell.azure.com) A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Az alkalmazás futtatásakor a rendszer App Service környezeti változókként injektálja az alkalmazásbeállításokat a folyamatba. A tárolókörnyezet változóit a URL-cím használatával `https://<app-name>.scm.azurewebsites.net/Env)` ellenőrizheti.

Ha az alkalmazás privát beállításjegyzékből vagy adatbázisból származó rendszerképeket Docker Hub, az adattár eléréséhez szükséges hitelesítő adatokat környezeti változókba menti a rendszer: `DOCKER_REGISTRY_SERVER_URL` , `DOCKER_REGISTRY_SERVER_USERNAME` és `DOCKER_REGISTRY_SERVER_PASSWORD` . Biztonsági kockázatok miatt az alkalmazás egyik fenntartott változónevet sem teszi elérhetővé.

::: zone pivot="container-windows"
IIS- .NET-keretrendszer (4.0-s vagy magasabb) alapú tárolók esetén a rendszer automatikusan .NET-alkalmazásbeállításokként és kapcsolati sztringekként injektálja őket a `System.ConfigurationManager` App Service. Minden más nyelvhez vagy keretrendszerhez ezek a folyamat környezeti változóiként biztosítanak adatokat, a következő előtagok egyikével:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Ez a metódus az egytárolós és a többtárolós alkalmazások esetében is működik, ahol a környezeti változók a *docker-compose.yml fájlban vannak megadva.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tároló használata

::: zone pivot="container-windows"

Az alkalmazás *fájlrendszerében található C:\home* könyvtár használatával a fájlokat újraindítások között is megőrzheti, és megoszthatja őket a példányok között. Az `C:\home` alkalmazásában a van megtéve, hogy a tárolóalkalmazás hozzáfér az állandó tárolóhoz.

Ha az állandó tároló le van tiltva, a könyvtárba való `C:\home` írások nem maradnak meg. [A Docker-gazdagép naplóit](#access-diagnostic-logs) és a tárolónaplókat egy alapértelmezett, állandó megosztott tárolóba menti a rendszer, amely nincs a tárolóhoz csatolva. Ha az állandó tárolás engedélyezve van, a könyvtárba írt összes írás megmarad, és a horizontálisan felskálált alkalmazás összes példánya hozzáfér, a napló pedig a következő helyen `C:\home` érhető el: `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

A */home* könyvtárat használhatja az alkalmazás fájlrendszerében a fájlok újraindítások közötti megőrzéséhez és példányok közötti megosztásához. Az alkalmazásában elérhető, hogy a `/home` tárolóalkalmazás hozzáférjen az állandó tárolóhoz.

Ha az állandó tárolás le van tiltva, akkor a könyvtárba történő írások nem maradnak meg az alkalmazás újraindítása vagy több példány `/home` között. Az egyetlen kivétel a Docker- és tárolónaplók tárolására `/home/LogFiles` használt könyvtár. Ha az állandó tárolás engedélyezve van, a könyvtárba való összes írás megmarad, és a horizontálisan felskálált alkalmazás összes példánya számára `/home` elérhető lesz.

::: zone-end

Alapértelmezés szerint az állandó tároló le van tiltva, és a beállítás nem látható az alkalmazásbeállításokban. Az engedélyezéséhez állítsa be az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazásbeállítást a [következő Cloud Shell.](https://shell.azure.com) A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Saját állandó [tárolót is konfigurálhat.](configure-connect-to-azure-storage.md)

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service le a TLS/SSL-t az előoldalon. Ez azt jelenti, hogy a TLS-/SSL-kérések soha nem jutnak el az alkalmazáshoz. Nem kell és nem is kell támogatnia a TLS/SSL-t az alkalmazásában. 

Az előtér-alkalmazások Azure-adatközpontokban találhatók. Ha TLS/SSL-t használ az alkalmazással, az interneten keresztüli forgalom mindig biztonságosan titkosítva lesz.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>A ASP.NET kulcs injektálásának testreszabása

 A tároló elindulása során a rendszer automatikusan létrehozott kulcsokat injektál a tárolóba a titkosítási rutinok ASP.NET számítógépkulcsaiként. Ezeket a [kulcsokat a](#connect-to-the-container) tárolóban a következő környezeti változók keresésével találhatja meg: `MACHINEKEY_Decryption` , , , `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

Az új kulcsok minden újraindításkor alaphelyzetbe állíthatják ASP.NET űrlapos hitelesítést és megtekintési állapotot, ha az alkalmazás függ ezektől. A kulcsok automatikus újragenerálásának megakadályozásához manuálisan állítsa be [őket App Service alkalmazásbeállításként.](#configure-environment-variables) 

## <a name="connect-to-the-container"></a>Csatlakozás a tárolóhoz

A Windows-tárolóhoz közvetlenül csatlakozhat diagnosztikai feladatok elvégzéséhez, ha megnyitja a következőt: `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Ez a következőképpen működik:

- A hibakeresési konzolon interaktív parancsokat hajthat végre, például PowerShell-munkameneteket futtathat, megvizsgálhatja a beállításkulcsokat, és navigálhat a teljes tároló fájlrendszerében.
- A fenti grafikus böngészőtől elkülönítve működik, amely csak a megosztott [tárolóban lévő fájlokat jeleníti meg.](#use-persistent-shared-storage)
- A horizontálisan felskálált alkalmazásokban a hibakeresési konzol az egyik tárolópéldányhoz csatlakozik. Másik példányt is kiválaszthat a felső menü **Példány** legördülő menüjéből.
- A tároló konzolon végrehajtott módosítása nem  marad meg az alkalmazás újraindításakor (kivéve a megosztott tárolón végrehajtott módosításokat), mert az nem része a Docker-rendszerképnek. A módosítások , például a beállításjegyzék-beállítások és a szoftvertelepítés megőrzéséhez tegye őket a Dockerfile részét.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

App Service a Docker-gazdagép által végzett műveleteket, valamint a tárolón belüli tevékenységeket. A Docker-gazdagép naplói (platformnaplók) alapértelmezés szerint ki vannak szállítva, de a tárolóban található alkalmazásnaplókat vagy webkiszolgáló-naplókat manuálisan kell engedélyezni. További információ: Alkalmazásnaplózás [engedélyezése és](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) [Webkiszolgáló naplózásának engedélyezése.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

A Docker-naplók többféleképpen is elérhetőek:

- [A Azure Portal](#in-azure-portal)
- [A Kudu-konzolról](#from-the-kudu-console)
- [A Kudu API-val](#with-the-kudu-api)
- [Naplók küldése az Azure Monitorba](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>A Azure Portal

A Docker-naplók az alkalmazás Tárolóbeállítások lapján jelennek meg **a** portálon. A naplók csonkolódtak, de az összes napló letölthető a **Letöltés gombra kattintva.** 

### <a name="from-the-kudu-console"></a>A Kudu-konzolról

Navigáljon `https://<app-name>.scm.azurewebsites.net/DebugConsole` a **logFiles mappához,** és kattintson rá az egyes naplófájlok bejelentkezéskor. A teljes **LogFiles** könyvtár letöltéséhez kattintson a könyvtár nevétől balra található Letöltés ikonra.  Ezt a mappát FTP-ügyféllel is elérheti.

A konzolterminálon alapértelmezés szerint nem férhet hozzá a mappához, mert az állandó megosztott `C:\home\LogFiles` tároló nincs engedélyezve. Ha engedélyezni szeretné ezt a viselkedést a konzolterminálon, engedélyezze [az állandó megosztott tárolót.](#use-persistent-shared-storage)

Ha egy FTP-ügyféllel próbálja letölteni a jelenleg használatban lévő Docker-naplót, a fájlzárolás hibát jelezhet.

### <a name="with-the-kudu-api"></a>A Kudu API-val

Lépjen közvetlenül `https://<app-name>.scm.azurewebsites.net/api/logs/docker` a(z) elemhez a Docker-naplók metaadatainak megtekintéséhez. Előfordulhat, hogy egynél több naplófájlt lát a listában, és a tulajdonság lehetővé teszi `href` a naplófájl közvetlen letöltését. 

Ha az összes naplót egyetlen ZIP-fájlban töltse le, a következőt kell `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` elérnie: .

## <a name="customize-container-memory"></a>Tárolómemória testreszabása

Alapértelmezés szerint a Azure App Service üzembe helyezett összes Windows-tároló 1 GB RAM-ra van korlátozva. Ezt az értéket úgy módosíthatja, hogy az `WEBSITE_MEMORY_LIMIT_MB` alkalmazásbeállítást a [](https://shell.azure.com)következő Cloud Shell. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Az érték MB-ban van definiálva, és a gazdagép teljes fizikai memóriájában kisebbnek és egyenlőnek kell lennie. Például egy 8 GB APP SERVICE RAM-mal rendelkező csomag esetében az alkalmazások összesített összege nem haladhatja meg a `WEBSITE_MEMORY_LIMIT_MB` 8 GB-ot. Az egyes tarifacsomagok számára elérhető memóriával [](https://azure.microsoft.com/pricing/details/app-service/windows/)kapcsolatos információkat a prémium App Service **(Windows)** csomag szakaszában, a díjszabás szakaszban talál.

## <a name="customize-the-number-of-compute-cores"></a>Számítási magok számának testreszabása

Alapértelmezés szerint egy Windows-tároló a választott tarifacsomaghoz elérhető összes maggal fut. Előfordulhat például, hogy csökkenteni szeretné az előkészítési pont által használt magok számát. A tárolók által használt magok számának csökkentéséhez állítsa az alkalmazásbeállítást a kívánt számú `WEBSITE_CPU_CORES_LIMIT` magra. A következővel állíthatja [be: Cloud Shell.](https://shell.azure.com) A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Az alkalmazásbeállítás frissítése kiváltja az automatikus újraindítást, ami minimális állásidőt okoz. Éles alkalmazás esetén fontolja meg az előkészítési pontra való felcserélést, módosítsa az alkalmazásbeállítást az előkészítési ponton, majd cserélje le újra az éles környezetbe.

Ellenőrizze a módosított számot a Kudu-konzol ( ) megnyitásakor, és írja be a következő parancsokat `https://<app-name>.scm.azurewebsites.net` a PowerShell használatával. Minden parancs egy számot ad vissza.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

A processzorok lehetnek többmagosak vagy hiperszálas processzorok. Az egyes tarifacsomagok számára elérhető magokkal kapcsolatos információkat a [App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)a Prémium tároló **(Windows)** csomag szakaszában talál.

## <a name="customize-health-ping-behavior"></a>Állapot pingelési viselkedésének testreszabása

App Service úgy tekinti, hogy a tároló sikeresen elindul, amikor a tároló elindul, és válaszol egy HTTP-pingelésre. Az állapot pingelésére vonatkozó kérés tartalmazza a `User-Agent= "App Service Hyper-V Container Availability Check"` fejlécet. Ha a tároló elindul, de egy bizonyos idő után nem válaszol a pingelésre, a App Service egy eseményt naplóz a Docker-naplóban, amely szerint a tároló nem indul el. 

Ha az alkalmazás erőforrás-igényes, előfordulhat, hogy a tároló nem válaszol időben a HTTP-pingelésre. A HTTP-pingelés sikertelen műveletének szabályozása érdekében állítsa be az `CONTAINER_AVAILABILITY_CHECK_MODE` alkalmazásbeállítást. A következővel állíthatja [be: Cloud Shell.](https://shell.azure.com) A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

Az alábbi táblázat a lehetséges értékeket mutatja be:

| Érték | Leírások |
| - | - |
| **Javítás** | A tároló újraindítása három egymást követő rendelkezésre állási ellenőrzés után |
| **ReportOnly (Csak jelentés)** | Az alapértelmezett érték. Ne indítsa újra a tárolót, hanem három egymást követő rendelkezésre állási ellenőrzés után jelentsen a tároló Docker-naplóiban. |
| **Kikapcsolva** | Ne ellenőrizze a rendelkezésre állást. |

## <a name="support-for-group-managed-service-accounts"></a>Csoportosan felügyelt szolgáltatásfiókok támogatása

A csoportosan felügyelt szolgáltatásfiókok (gMSA-k) jelenleg nem támogatottak az App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni tároló támogassa az SSH-t, hozzá kell adni magát a Docker-rendszerképhez.

> [!TIP]
> A rendszerkép összes beépített Linux-App Service hozzá az SSH-utasításokat a rendszerkép-adattárakhoz. Az alábbi utasításokat a [Node.js 10.14-es](https://github.com/Azure-App-Service/node/blob/master/10.14) adattárban is végigveheti, hogy lássa, hogyan van engedélyezve. A beépített rendszerkép Node.js konfigurációja kissé eltérő, de elvben ugyanaz.

- Adjon [hozzá sshd_config fájlt](https://man.openbsd.org/sshd_config) az adattárhoz az alábbi példához hasonlóan.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Ez a fájl konfigurálja az OpenSSH-t, és a következő elemeket kell tartalmaznia:
    > - `Port` A beállításnak 2222-re kell esnie.
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- A Dockerfile-ban adja hozzá a következő parancsokat:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Ez a konfiguráció nem engedélyezi a tárolóhoz való külső kapcsolatokat. A tároló 2222-es portja csak egy privát virtuális hálózat hálózati hídhálózatán belül érhető el, internetes támadók számára nem.

- A tároló indítási szkriptjében indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Állandó tároló használata a Docker Compose-ban](#use-persistent-storage-in-docker-compose)
- [Az előzetes verzió korlátozásai](#preview-limitations)
- [Docker Compose-beállítások](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Állandó tároló használata a Docker Compose-ban

A többtárolós alkalmazásoknak, például a WordPressnek állandó tárolóra van szükségük a megfelelő működéshez. Az engedélyezéséhez a Docker Compose konfigurációjának a tárolón kívüli tárolási *helyre kell* mutasson. A tárolón belüli tárolási helyek nem megőrzési módosítások az alkalmazás újraindítása után.

Az állandó tárolás engedélyezéséhez állítsa be az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazásbeállítást az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal a [Cloud Shell.](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

A *docker-compose.yml fájlban* a kapcsolót a `volumes` következőre kell leképezni: `${WEBAPP_STORAGE_HOME}` . 

A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Például:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

A többtárolós tároló jelenleg előzetes verzióban érhető el. A következő App Service platform funkciói nem támogatottak:

- Hitelesítés /Engedélyezés
- Felügyelt identitások
- CORS

### <a name="docker-compose-options"></a>Docker Compose-beállítások

Az alábbi listák a támogatott és nem támogatott Docker Compose konfigurációs lehetőségeket mutatják be:

#### <a name="supported-options"></a>Támogatott beállítások

- command
- entrypoint
- környezet
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nem támogatott beállítások

- build (nem engedélyezett)
- depends_on (figyelmen kívül hagyva)
- networks (figyelmen kívül hagyva)
- secrets (figyelmen kívül hagyva)
- a 80-as és a 8080-as porttól (figyelmen kívül hagyva)

> [!NOTE]
> A nyilvános előzetes verzió figyelmen kívül hagyja a többi, explicit módon ki nem hívott lehetőséget.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni szoftver áttelepítése Azure App Service egyéni tároló használatával](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)

::: zone-end

Vagy tekintse meg a további erőforrásokat:

[Tanúsítvány betöltése Windows-/Linux-tárolókban](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
