---
title: Egyéni tároló konfigurálása
description: Megtudhatja, hogyan konfigurálhat egyéni tárolókat a Azure App Serviceban. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 1d1a1292bc7583e4934ac176c34d2768700d11c5
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036764"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Egyéni tároló konfigurálása az Azure App Service-hez

Ebből a cikkből megtudhatja, hogyan konfigurálhat egyéni tárolókat Azure App Service futtatásához.

::: zone pivot="container-windows"

Ez az útmutató a Windows-alkalmazások App Service-ben történő tárolókra bontás vonatkozó főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta Azure App Servicet, először kövesse az [Egyéni tároló](quickstart-custom-container.md) rövid [útmutatóját és az oktatóanyagot](tutorial-custom-container.md) .

::: zone-end

::: zone pivot="container-linux"

Ez az útmutató a Linux-alkalmazások App Service-ben történő tárolókra bontás kapcsolatos főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta Azure App Servicet, először kövesse az [Egyéni tároló](quickstart-custom-container.md) rövid [útmutatóját és az oktatóanyagot](tutorial-custom-container.md) . A [multi-Container app](quickstart-multi-container.md) rövid [útmutatója és oktatóanyaga](tutorial-multi-container-app.md)is rendelkezésre áll.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Támogatott szülői lemezképek

Egyéni Windows-rendszerkép esetén a kívánt keretrendszerhez ki kell választania a megfelelő [szülő-rendszerképet (alaprendszerkép)](https://docs.docker.com/develop/develop-images/baseimages/) :

- A .NET-keretrendszer alkalmazásai üzembe helyezéséhez használjon egy szülő rendszerképet a Windows Server Core [hosszú távú karbantartási csatorna (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) kiadása alapján. 
- A .NET Core-alkalmazások telepítéséhez a Windows Server Nano [féléves karbantartási csatorna (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kiadásán alapuló szülő lemezképet használjon. 

Az alkalmazás indításakor a szülőrendszerkép letöltése hosszabb időbe telhet. Az indítási időt azonban lecsökkentheti az alábbi, az Azure App Service-ben már gyorsítótárazott szülőrendszerképek egyikének használatával:

- [MCR.microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Egyéni tároló Docker-rendszerképének módosítása

Ha módosítani szeretné egy meglévő egyéni tároló alkalmazást a jelenlegi Docker-rendszerképből egy új képre, használja a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Rendszerkép használata privát beállításjegyzékből

Ha privát beállításjegyzékből (például Azure Container Registry) szeretne képet használni, futtassa a következő parancsot:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

A *\<username>* és a esetében *\<password>* adja meg a saját beállításjegyzék-fiókja bejelentkezési hitelesítő adatait.

## <a name="i-dont-see-the-updated-container"></a>Nem látom a frissített tárolót

Ha módosítja a Docker-tároló beállításait úgy, hogy az új tárolóra mutasson, eltarthat néhány percig, amíg az alkalmazás az új tárolóból származó HTTP-kérelmeket is felkínálja. Amíg az új tárolót lehúzta és elindítják, App Service továbbra is a régi tárolótól érkező kérelmeket szolgálja ki. Csak akkor, ha az új tároló elindult, és készen áll a kérelmek fogadására, App Service megkezdi a kérelmek küldését.

## <a name="how-container-images-are-stored"></a>A tároló-lemezképek tárolása

Amikor először futtat egy egyéni Docker-rendszerképet a App Serviceban, App Service a `docker pull` és az összes képréteget lekéri. Ezeket a rétegeket a lemez tárolja, például ha a helyszíni Docker-t használta. Az alkalmazás minden újraindításakor App Service a `docker pull` , de csak a módosított rétegeket kéri le. Ha nem történt változás, a App Service a helyi lemezen lévő meglévő rétegeket használja.

Ha az alkalmazás bármilyen okból módosítja a számítási példányokat, például az árképzési szintek fel-és leskálázásakor, App Service újra le kell húznia az összes réteget. Ugyanez érvényes, ha további példányok hozzáadásával bővíti a méretezést. Vannak olyan ritka esetek is, amikor az alkalmazás példányai méretezési művelet nélkül változhatnak.

## <a name="configure-port-number"></a>Portszám konfigurálása

Alapértelmezés szerint a App Service feltételezi, hogy az egyéni tároló figyeli a 80-es portot. Ha a tároló egy másik portot figyel, állítsa be az `WEBSITES_PORT` alkalmazás beállításait a app Service alkalmazásban. Megadhatja a [Cloud Shellon](https://shell.azure.com)keresztül. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service jelenleg lehetővé teszi, hogy a tároló csak egy portot tegyen elérhetővé HTTP-kérelmek esetén. 

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló olyan környezeti változókat használhat, amelyeket külsőleg kell megadni. Átadhatja őket a [Cloud Shellon](https://shell.azure.com)keresztül. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Az alkalmazás futtatásakor a rendszer automatikusan a folyamatba befecskendezi a App Service alkalmazás beállításait környezeti változókként. A tároló környezeti változóit az URL-cím segítségével ellenőrizheti `https://<app-name>.scm.azurewebsites.net/Env)` .

Ha az alkalmazás képeket használ egy privát beállításjegyzékből vagy a Docker hub-ból, a tárház eléréséhez szükséges hitelesítő adatok a következő környezeti változókban lesznek mentve: `DOCKER_REGISTRY_SERVER_URL` , `DOCKER_REGISTRY_SERVER_USERNAME` és `DOCKER_REGISTRY_SERVER_PASSWORD` . Biztonsági kockázatok miatt a fenntartott változók egyike sem lesz elérhető az alkalmazás számára.

::: zone pivot="container-windows"
Az IIS vagy a .NET-keretrendszer (4,0 vagy újabb) alapú tárolók esetében a rendszer a `System.ConfigurationManager` .NET-alkalmazás beállításait és a kapcsolatok karakterláncait app Service automatikusan befecskendezi. Minden más nyelv vagy keretrendszer esetében környezeti változókként vannak megadva a folyamathoz, a következő megfelelő előtagok egyikével:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Ez a módszer egytárolós alkalmazások vagy többtárolós alkalmazások esetén is működik, ahol a környezeti változók a *Docker-compose. YML* fájlban vannak megadva.

::: zone-end

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tároló használata

::: zone pivot="container-windows"

Az alkalmazás fájlrendszerében a *C:\home* Directory használatával megtarthatja a fájlokat az újraindítások között, és megoszthatja azokat a példányok között. Az `C:\home` alkalmazásban elérhetővé teszi a tároló alkalmazás számára az állandó tárterület elérését.

Ha az állandó tárterület le van tiltva, a rendszer nem őrzi meg az írásokat a `C:\home` könyvtárba. A [Docker-gazdagép naplófájljai és a tároló-naplók](#access-diagnostic-logs) egy alapértelmezett állandó megosztott tárolóba kerülnek, amely nincs a tárolóhoz csatolva. Ha az állandó tárterület engedélyezve van, a címtárba való összes írás megmarad, és a kibővített `C:\home` alkalmazás összes példánya elérhető, és a napló a következő címen érhető el: `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Az alkalmazás fájlrendszerében a */Home* Directory használatával megtarthatja a fájlokat az újraindítások között, és megoszthatja azokat a példányok között. Az `/home` alkalmazásban elérhetővé teszi a tároló alkalmazás számára az állandó tárterület elérését.

Ha az állandó tárterület le van tiltva, akkor a rendszer a címtárba való írást `/home` nem őrzi meg az alkalmazások újraindítása vagy több példánya között. Az egyetlen kivétel az a `/home/LogFiles` könyvtár, amely a Docker és a tároló naplóinak tárolására szolgál. Ha az állandó tárterület engedélyezve van, a címtárba való összes írás megmarad, és a kibővített `/home` alkalmazás összes példánya elérhetővé válik.

::: zone-end

Alapértelmezés szerint az állandó tárterület le van tiltva, és a beállítás nem érhető el az alkalmazás beállításaiban. Ennek engedélyezéséhez állítsa be az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazás beállításait a [Cloud Shellon](https://shell.azure.com)keresztül. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> [Saját állandó tárterületet is beállíthat](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service leállítja a TLS/SSL-t az előtér végén. Ez azt jelenti, hogy a TLS/SSL-kérések soha nem kapják meg az alkalmazást. Nem kell, és nem kell semmilyen támogatást biztosítani a TLS/SSL-hez az alkalmazásban. 

Az előtér-végpontok az Azure-adatközpontokon belül találhatók. Ha a TLS/SSL protokollt használja az alkalmazáshoz, az interneten keresztüli forgalom mindig titkosítva lesz.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>A ASP.NET-Beszúrás testreszabása

 A tároló indításakor a rendszer automatikusan generált kulcsokat fecskendez a tárolóba a ASP.NET titkosítási rutinok számára. Ezeket a [kulcsokat a tárolóban](#connect-to-the-container) a következő környezeti változók keresésével érheti el:,, `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

Az egyes újraindítások új kulcsai alaphelyzetbe állíthatják a ASP.NET űrlapos hitelesítését, és megtekinthetik az állapotot, ha az alkalmazás ezektől függ. A kulcsok automatikus újragenerálásának megakadályozásához [állítsa be őket manuálisan app Service alkalmazásbeállításokként](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Kapcsolódás a tárolóhoz

A Windows-tárolóhoz közvetlenül kapcsolódhat a diagnosztikai feladatokhoz, ha navigál a alkalmazáshoz `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Ez a következőképpen működik:

- A hibakeresési konzol lehetővé teszi az interaktív parancsok végrehajtását, például a PowerShell-munkamenetek indítását, a beállításkulcsok vizsgálatát és a teljes tároló fájlrendszerének megkeresését.
- A szolgáltatás a fenti grafikus böngészőtől függetlenül működik, amely csak a [megosztott tárolóban](#use-persistent-shared-storage)lévő fájlokat jeleníti meg.
- A kibővített alkalmazásokban a hibakeresési konzol csatlakozik az egyik tároló példányhoz. A felső menüben választhat egy másik példányt a **példány** legördülő listából.
- A tárolón belüli, a konzolon végrehajtott bármilyen módosítás *nem* marad meg az alkalmazás újraindításakor (kivéve a megosztott tárolóban történt változásokat), mert nem része a Docker-rendszerképnek. Ha továbbra is meg szeretné őrizni a módosításokat, például a beállításjegyzék beállításait és a szoftverek telepítését, tegye azokat a Docker.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

App Service naplózza a Docker-gazdagép műveleteit, valamint a tárolón belüli tevékenységeket. A Docker-gazdagépről (platform-naplók) származó naplók alapértelmezés szerint el vannak szállítva, de a tárolón belül az alkalmazás naplófájljait vagy webkiszolgálói naplóit manuálisan kell engedélyezni. További információ: az [alkalmazások naplózásának engedélyezése](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) és a [webkiszolgáló naplózásának engedélyezése](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

A Docker-naplók több módon is elérhetők:

- [Azure Portal](#in-azure-portal)
- [A kudu-konzolról](#from-the-kudu-console)
- [A kudu API-val](#with-the-kudu-api)
- [Naplók küldése az Azure monitornak](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure Portal

A Docker-naplók a portálon, az alkalmazás **tároló beállításai** lapján jelennek meg. A naplók csonkoltek, de a **Letöltés** lehetőségre kattintva letöltheti az összes naplót. 

### <a name="from-the-kudu-console"></a>A kudu-konzolról

`https://<app-name>.scm.azurewebsites.net/DebugConsole`A naplófájlok megjelenítéséhez keresse meg és kattintson a **naplófájlok** mappára. A teljes **naplófájlok** könyvtárának letöltéséhez kattintson a könyvtár nevétől balra található **Letöltés** ikonra. Ezt a mappát FTP-ügyfél használatával is elérheti.

A konzol-terminálon alapértelmezés szerint nem férhet hozzá a `C:\home\LogFiles` mappához, mert az állandó megosztott tárterület nincs engedélyezve. Ha engedélyezni szeretné ezt a viselkedést a konzol-terminálon, [engedélyezze az állandó megosztott tárolót](#use-persistent-shared-storage).

Ha olyan Docker-naplót próbál letölteni, amely jelenleg használatban van egy FTP-ügyféllel, a fájl zárolása miatt hibaüzenetet kaphat.

### <a name="with-the-kudu-api"></a>A kudu API-val

Navigáljon közvetlenül a-hoz a `https://<app-name>.scm.azurewebsites.net/api/logs/docker` Docker-naplók metaadatainak megtekintéséhez. Egyszerre több naplófájl is látható, és a `href` tulajdonsággal közvetlenül is letöltheti a naplófájlt. 

Az összes napló egyetlen ZIP-fájlban való letöltéséhez nyissa meg a következőt: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Tároló memóriájának testreszabása

Alapértelmezés szerint a Azure App Service összes telepített Windows-tárolója 1 GB RAM-ra van korlátozva. Ezt az értéket módosíthatja úgy, hogy az `WEBSITE_MEMORY_LIMIT_MB` [Cloud Shellon](https://shell.azure.com)keresztül megadja az alkalmazás beállítását. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Az érték MB-ban van definiálva, és a gazdagép teljes fizikai memóriájának meg kell egyeznie. A 8 GB RAM-mal rendelkező App Service-csomagban például az összes alkalmazás összesített összege `WEBSITE_MEMORY_LIMIT_MB` nem haladhatja meg a 8 GB-ot. Az egyes díjszabási szintekhez rendelkezésre álló memória mennyiségét a **Premium Container (Windows) csomag** szakaszban találja [app Service díjszabásban](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="customize-the-number-of-compute-cores"></a>A számítási magok számának testreszabása

Alapértelmezés szerint a Windows-tároló a választott díjszabási szinten elérhető összes maggal együtt fut. Előfordulhat például, hogy csökkenteni szeretné az átmeneti tárolóhely által használt magok számát. A tárolók által használt magok számának csökkentéséhez állítsa az `WEBSITE_CPU_CORES_LIMIT` alkalmazás beállítását a magok előnyben részesített számára. Megadhatja a [Cloud Shellon](https://shell.azure.com)keresztül. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Az Alkalmazásbeállítás frissítése elindítja az automatikus újraindítást, ami minimális állásidőt okoz. Éles alkalmazások esetén érdemes lehet egy átmeneti tárolóhelyre cserélni, módosítani az alkalmazás beállítását az átmeneti tárolóhelyen, majd visszacserélni az éles környezetbe.

Ellenőrizze a beállított számot a kudu-konzolon ( `https://<app-name>.scm.azurewebsites.net` ), majd írja be az alábbi parancsokat a PowerShell használatával. Mindegyik parancs egy számot ad eredményül.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

A processzorok többmagos vagy feleznie processzorok lehetnek. Az egyes díjszabási szintekhez elérhető magok száma a **Premium Container (Windows) csomag** szakaszban található [app Service díjszabásban](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="customize-health-ping-behavior"></a>Az állapot pingelése viselkedés testreszabása

App Service úgy véli, hogy egy tároló sikeresen elindul a tároló indításakor, és válaszol egy HTTP-pingelésre. A Health ping kérelem tartalmazza a fejlécet `User-Agent= "App Service Hyper-V Container Availability Check"` . Ha a tároló elindul, de bizonyos idő elteltével nem válaszol a pingelésre, App Service naplóz egy eseményt a Docker-naplóban, mondván, hogy a tároló nem indult el. 

Ha az alkalmazás erőforrás-igényes, előfordulhat, hogy a tároló nem válaszol a HTTP-ping időben. Ha a HTTP-pingelések meghiúsulnak, állítsa be az `CONTAINER_AVAILABILITY_CHECK_MODE` alkalmazás beállításait. Megadhatja a [Cloud Shellon](https://shell.azure.com)keresztül. A Bashben:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

A PowerShellben:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

A következő táblázat a lehetséges értékeket mutatja:

| Érték | Leírások |
| - | - |
| **Javítás** | A tároló újraindítása három egymást követő rendelkezésre állási ellenőrzés után |
| **ReportOnly** | Az alapértelmezett érték. Ne indítsa újra a tárolót, de jelentse a tároló Docker-naplóit három egymást követő rendelkezésre állási ellenőrzés után. |
| **Kikapcsolva** | Ne keressen rendelkezésre állást. |

## <a name="support-for-group-managed-service-accounts"></a>Csoportosan felügyelt szolgáltatásfiókok támogatása

A csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókokat-EK) jelenleg nem támogatottak a App Service lévő Windows-tárolókban.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni tároló támogassa az SSH-t, fel kell vennie magát a Docker-rendszerképbe.

> [!TIP]
> A App Service összes beépített Linux-tárolója hozzá lett adva az SSH-utasításokhoz a rendszerkép-tárházban. A [Node.js 10,14 adattárral](https://github.com/Azure-App-Service/node/blob/master/10.14) az alábbi utasításokat követve megtekintheti, hogyan engedélyezhető ott. A Node.js beépített rendszerképben lévő konfiguráció némileg eltérő, de alapvetően ugyanez a helyzet.

- Vegyen fel [egy sshd_config fájlt](https://man.openbsd.org/sshd_config) a tárházba, az alábbi példához hasonlóan.

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
    > Ez a fájl az OpenSSH-t konfigurálja, és tartalmaznia kell a következő elemeket:
    > - `Port` 2222 értékre kell állítani.
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- A Docker adja hozzá a következő parancsokat:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóval. A tároló 2222-es portja csak a privát virtuális hálózatok hálózati hidakon belül érhető el, és nem érhető el az interneten található támadók számára.

- A tároló indítási parancsfájljában indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Állandó tároló használata a Docker-összeállításban](#use-persistent-storage-in-docker-compose)
- [Előzetes verzió korlátozásai](#preview-limitations)
- [Docker-összeállítás beállításai](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Állandó tároló használata a Docker-összeállításban

A többtárolós alkalmazások, például a WordPress esetében állandó tárterületre van szükség a megfelelő működéshez. Az engedélyezéshez a Docker-összeállítás konfigurációjának a tárolón *kívüli* tárolási helyre kell mutatnia. A tárolón belüli tárolóhelyek nem tartanak fenn módosításokat az alkalmazás újraindítása után.

Engedélyezze az állandó tárterületet az Alkalmazásbeállítások beállításával az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) parancs használatával [Cloud Shellban](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

A *Docker-compose. YML* fájlban rendelje hozzá a ( `volumes` `${WEBAPP_STORAGE_HOME}` ) beállítást. 

A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Például:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Előzetes verzió korlátozásai

A multi-Container jelenleg előzetes verzióban érhető el. A következő App Service platform-funkciók nem támogatottak:

- Hitelesítés/engedélyezés
- Felügyelt identitások
- CORS

### <a name="docker-compose-options"></a>Docker-összeállítás beállításai

Az alábbi listában a támogatott és nem támogatott Docker-összeállítási beállítások láthatók:

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
- 80 és 8080 közötti (figyelmen kívül hagyott) portok

> [!NOTE]
> A rendszer figyelmen kívül hagyja a nem kifejezetten kinevezett egyéb beállításokat a nyilvános előzetes verzióban.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni szoftver átmigrálása Azure App Servicere egyéni tároló használatával](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)

::: zone-end

Vagy tekintse meg a további forrásokat:

[Tanúsítvány betöltése Windows/Linux-tárolókban](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
