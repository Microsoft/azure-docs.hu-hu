---
title: Helyi telepítés és fejlesztés Azure Cosmos DB emulátorral
description: Ismerje meg, hogyan telepítheti és használhatja a Azure Cosmos DB Emulatort Windows-, Linux-, macOS-és Windows Docker-környezetekben. Az emulátor használatával ingyenesen fejlesztheti és tesztelheti alkalmazását az Azure-előfizetés létrehozása nélkül.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: 89e82e210dbf7c404ebd5978d87bf391d8565ee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033969"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>A Azure Cosmos DB Emulator telepítése és használata helyi fejlesztéshez és teszteléshez
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB Emulator helyi környezetet biztosít, amely az Azure Cosmos DB szolgáltatást emulálja a fejlesztéshez. Az Azure Cosmos DB Emulator használatával helyben fejlesztheti és tesztelheti alkalmazását, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. Ha meggyőződött arról, hogy az alkalmazás hogyan működik a Azure Cosmos DB Emulatorban, átválthat egy Azure Cosmos-fiók használatára a felhőben. Ez a cikk bemutatja, hogyan telepítheti és használhatja az emulátort Windows-, Linux-, macOS-és Windows Docker-környezetekben.

## <a name="download-the-emulator"></a>Az Emulator letöltése

Első lépésként töltse le és telepítse a Azure Cosmos DB Emulator legújabb verzióját a helyi számítógépen. Az [emulátor kibocsátási megjegyzései](local-emulator-release-notes.md) című cikk felsorolja az összes elérhető verziót és az egyes kiadásokban végrehajtott szolgáltatás-frissítéseket.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[A Azure Cosmos DB Emulator letöltése](https://aka.ms/cosmosdb-emulator)**

Az [SQL](local-emulator.md#sql-api), a [Cassandra](local-emulator.md#cassandra-api), a [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), a [Gremlin](local-emulator.md#gremlin-api)és a [Table](local-emulator.md#table-api) API-fiókokkal alkalmazásokat fejleszthet Azure Cosmos db Emulator használatával. Jelenleg az emulátorban található adatkezelő teljes mértékben támogatja az SQL-adatmegjelenítést; a MongoDB, Gremlin/Graph és Cassandra ügyfélalkalmazások használatával létrehozott adatai jelenleg nem tekinthetők meg. További információ: a különböző API-kkal való [Kapcsolódás az emulátor-végponthoz](#connect-with-emulator-apis) .

## <a name="how-does-the-emulator-work"></a>Hogyan működik az emulátor?

Az Azure Cosmos DB Emulator az Azure Cosmos DB szolgáltatás kiváló minőségű emulációját nyújtja. Támogatja az egyenértékű funkciókat, mint a Azure Cosmos DB, amely magában foglalja az adatlétrehozást, az adatlekérdezést, a tárolók kiosztását és méretezését, valamint a tárolt eljárások és triggerek végrehajtását. Az Azure Cosmos DB Emulator használatával fejlesztheti és tesztelheti az alkalmazásokat, és a Azure Cosmos DB-kapcsolódási végpont frissítésével üzembe helyezheti azokat az Azure-ban globális szinten.

Bár az Azure Cosmos DB emulációja valósághű, az emulátor implementálása eltér a szolgáltatásétól. Az emulátor például szabványos operációsrendszer-összetevőket használ, például a helyi fájlrendszert az adatmegőrzéshez és a HTTPS-protokollvermet a kapcsolatokhoz. Az olyan funkciók, amelyek az Azure-infrastruktúrára, például a globális replikálásra támaszkodnak, az olvasási/írási műveletek egyszámjegyű ezredmásodperces késése és a hangolt konzisztencia-szintek nem alkalmazhatók az emulátor használata esetén.

A [Azure Cosmos db adatáttelepítési eszköz](https://github.com/azure/azure-documentdb-datamigrationtool)használatával áttelepítheti az Azure Cosmos db Emulator és a Azure Cosmos db szolgáltatás közötti adatátvitelt.

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Az emulátor és a felhőalapú szolgáltatás közötti különbségek

Mivel a Azure Cosmos DB Emulator egy emulált környezetet biztosít, amely a helyi fejlesztői munkaállomáson fut, az emulátor és a felhőben található Azure Cosmos-fiók működésének számos eltérése van:

* Az emulátorban jelenleg az **adatkezelő** panel teljes mértékben támogatja az SQL API-ügyfeleket. Azure Cosmos DB API-k, például a MongoDB, a Table, a Graph és a Cassandra API-k **adatkezelő** nézete és műveletei nem teljes mértékben támogatottak.

* Az emulátor csak egyetlen rögzített fiókot és egy jól ismert elsődleges kulcsot támogat. A Azure Cosmos DB emulátor használatakor nem lehet újragenerált kulcsot használni, azonban a [parancssori](emulator-command-line-parameters.md) kapcsolóval módosíthatja az alapértelmezett kulcsot.

* Az emulátor használatával csak a [kiépített átviteli sebességű](set-throughput.md) módban hozhat létre Azure Cosmos-fiókot; jelenleg nem támogatja a [kiszolgáló](serverless.md) nélküli üzemmódot.

* Az emulátor nem méretezhető szolgáltatás, és nem támogatja nagy mennyiségű tároló használatát. A Azure Cosmos DB emulátor használatakor alapértelmezés szerint legfeljebb 25 rögzített méretű tárolót hozhat létre a 400 RU/s (csak Azure Cosmos DB SDK-k használata esetén támogatott) vagy 5 korlátlan tárolók használatával. Az érték módosításával kapcsolatos további információkért tekintse meg [a PartitionCount értékének beállítása](emulator-command-line-parameters.md#set-partitioncount) című cikket.

* Az emulátor nem biztosít különböző [Azure Cosmos db konzisztencia-szinteket](consistency-levels.md) , például a Cloud Service-t.

* Az emulátor nem nyújt [többrégiós replikálást](distribute-data-globally.md).

* Mivel előfordulhat, hogy a Azure Cosmos DB Emulator másolata nem mindig naprakész a Azure Cosmos DB szolgáltatás legutóbbi változásaival, mindig tekintse meg az [Azure Cosmos db Capacity Plannert](estimate-ru-with-capacity-planner.md) , hogy pontosan megbecsülje az alkalmazás átviteli sebességének (RUs) szükségleteit.

* Az emulátor támogatja a maximális azonosító tulajdonság 254 karakteres méretet.

## <a name="install-the-emulator"></a>Az emulátor telepítése

Az emulátor telepítése előtt győződjön meg arról, hogy rendelkezik az alábbi hardver-és szoftver-követelményekkel:

* A szoftverre vonatkozó követelmények:
  * Jelenleg a Windows Server 2016, a 2019 vagy a Windows 10 gazdagép operációs rendszer támogatott. A gazdagép operációs rendszerének Active Directory engedélyezése jelenleg nem támogatott.
  * 64 bites operációs rendszer esetén:

* Minimális hardverkövetelmények:
  * 2 GB RAM
  * 10 GB szabad merevlemez-terület

* Az Azure Cosmos DB Emulator telepítéséhez, konfigurálásához és futtatásához rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. Az emulátor felvesz egy tanúsítványt, és beállítja a tűzfalszabályok beállításait a szolgáltatások futtatásához. Ezért rendszergazdai jogosultságok szükségesek ahhoz, hogy az emulátor végre tudja hajtani ezeket a műveleteket.

Első lépésként töltse le és telepítse a [Azure Cosmos db Emulator](https://aka.ms/cosmosdb-emulator) legújabb verzióját a helyi számítógépen. Ha az emulátor telepítésekor problémákba ütközik, tekintse meg az [emulátor hibaelhárítása](troubleshoot-local-emulator.md) című cikket.

A rendszerkövetelményektől függően a jelen cikk következő fejezeteiben leírtak szerint futtathatja az emulátort [a Windows, a](#run-on-windows) [Linux vagy a MacOS](#run-on-linux-macos) rendszerhez készült [Docker](#run-on-windows-docker)használatával.

## <a name="check-for-emulator-updates"></a>Emulátor frissítéseinek keresése

Az Emulator minden egyes verziója tartalmaz néhány szolgáltatás-frissítést vagy hibajavítást. Az elérhető verziók megtekintéséhez olvassa el az [Emulator kibocsátási megjegyzéseit](local-emulator-release-notes.md) ismertető cikket.

Ha az alapértelmezett beállításokat használta, a telepítés után a rendszer a%LOCALAPPDATA%\CosmosDBEmulator helyen menti az emulátornak megfelelő adatmennyiséget. Másik helyet is beállíthat az adatelérési út opcionális beállításainak használatával; Ez a `/DataPath=PREFERRED_LOCATION` [parancssori paraméter](emulator-command-line-parameters.md). Az Azure Cosmos DB Emulator egyik verziójában létrehozott adatmennyiség nem garantált, hogy más verzió használata esetén nem érhető el. Ha hosszú távon is meg kell őriznie az adatait, azt javasoljuk, hogy a Azure Cosmos DB Emulator helyett egy Azure Cosmos-fiókban tárolja az adatait.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Az emulátor használata Windows rendszeren

A Azure Cosmos DB Emulator alapértelmezés szerint telepítve van a `C:\Program Files\Azure Cosmos DB Emulator` helyen. A Azure Cosmos DB Emulator Windows rendszeren való elindításához kattintson a **Start** gombra, vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Cosmos DB Emulator** kifejezést, majd válassza ki az emulátort az alkalmazások listájából.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Kattintson a Start gombra, vagy nyomja le a Windows billentyűt, kezdje beírni Azure Cosmos DB emulátort, és válassza ki az emulátort az alkalmazások listájából.":::

Amikor az emulátor elindult, megjelenik egy ikon a Windows tálca értesítési területén. Az URL `https://localhost:8081/_explorer/index.html` -cím URL-címe automatikusan megnyitja az Azure Cosmos adatkezelőt a böngészőben.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="A helyi emulátor Azure Cosmos DB értesítése":::

Az emulátort a parancssori vagy a PowerShell-parancsokkal is elindíthatja és leállíthatja. További információt a [parancssori eszköz dokumentációjában](emulator-command-line-parameters.md) talál.

Az Azure Cosmos DB Emulator alapértelmezés szerint a 8081-es porton figyelő helyi gépen („localhost”) fut. A cím a következőképpen jelenik meg: `https://localhost:8081/_explorer/index.html`. Ha bezárja az Adatkezelőt, és később újra meg szeretné nyitni, megnyithatja az URL-t a böngészőben, vagy elindíthatja azt a Windows tálca Azure Cosmos DB Emulator ikonjával az alább látható módon.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos helyi emulátor adatkezelő-indító":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>A Windows rendszerhez készült Docker-emulátor használata

A Azure Cosmos DB Emulatort a Windows Docker-tárolón futtathatja. További információért tekintse meg a Docker-lekérési parancs és a [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) a [Docker hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) -t `Dockerfile` . Az emulátor jelenleg nem működik a Oracle Linux Docker-ben. Az alábbi utasítások segítségével futtathatja az emulátort a Windows Docker szolgáltatásban:

1. Miután telepítette a [Windows Docker](https://www.docker.com/docker-windows) szolgáltatást, váltson a Windows-tárolók elemre, és kattintson a jobb gombbal a Docker ikonjára az eszköztáron, és válassza a **váltás Windows-tárolóra** lehetőséget.

1. Ezután kérje le az emulátor rendszerképét a Docker Hubról. Ehhez futtassa a következő parancsot azon a felületen, amelyet használni szokott.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. A rendszerkép elindításához futtassa a következő parancsokat a parancssorból vagy a PowerShell-környezettől függően:

   # <a name="command-line"></a>[Parancssor](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Előfordulhat, hogy a Windows-alapú Docker-lemezképek általában nem kompatibilisek minden Windows-gazdagép operációs rendszerével. Az alapértelmezett Azure Cosmos DB Emulator-rendszerkép például csak a Windows 10 és a Windows Server 2016 rendszerrel kompatibilis. Ha olyan rendszerképre van szüksége, amely kompatibilis a Windows Server 2019-mel, futtassa a következő parancsot:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   A válasz a következőhöz hasonlóan néz ki:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Ha a parancs végrehajtásakor `docker run` ütközési hiba jelenik meg (azaz ha a megadott port már használatban van), adja át az egyéni portot a portszámok módosításával. Megváltoztathatja például a "-p 8081:8081" paramétert a "-p 443:8081" értékre.

1. Most használja az emulátor végpontját és az elsődleges kulcsot a válaszból, és importálja a TLS/SSL-tanúsítványt a gazdagépre. A TLS/SSL-tanúsítvány importálásához futtassa a következő lépéseket egy rendszergazdai parancssorból:

   # <a name="command-line"></a>[Parancssor](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Ha az emulátor elindítása után bezárja az interaktív felületet, a rendszer leállítja az emulátor tárolóját. Az adatkezelő megnyitásához nyissa meg a következő URL-címet a böngészőben. Az emulátor végpontja a fent látható válaszüzenetben van megadva.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Ha a .NET-ügyfélalkalmazás Linux Docker-tárolón fut, és ha Azure Cosmos DB emulátort futtat egy gazdagépen, a következő szakaszban található utasítások segítségével importálja a tanúsítványt a Linux Docker-tárolóba.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Emulator-tanúsítványok újralétrehozása Docker-tárolón való futtatáskor

Amikor az emulátort egy Docker-tárolóban futtatja, az emulátorhoz társított tanúsítványokat a rendszer minden alkalommal újragenerálta, amikor leállítja és újraindítja a megfelelő tárolót. Ezért az egyes tárolók elindítása után újra importálnia kell a tanúsítványokat. A korlátozás megkerülése érdekében a Docker-összeállítási fájllal a Docker-tárolót egy adott IP-címhez és egy tároló-rendszerképhez kell kötni.

Használhatja például a következő konfigurációt a Docker-összeállítási fájlon belül, hogy a követelmény szerint formázza azt: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Az emulátor használata Linux vagy macOS rendszeren

Jelenleg a Azure Cosmos DB Emulator csak Windows rendszeren futtatható. Ha Linux vagy macOS rendszert használ, akkor futtathatja az emulátort egy olyan Windows rendszerű virtuális gépen, amely egy olyan hypervisorban üzemel, mint a Parallels vagy a VirtualBox.

> [!NOTE]
> Minden alkalommal, amikor újraindul a hypervisorban üzemeltetett Windows rendszerű virtuális gép, újra kell importálnia a tanúsítványt, mert a virtuális gép IP-címe megváltozik. A tanúsítvány importálása nem szükséges abban az esetben, ha a virtuális gépet úgy konfigurálta, hogy megőrizze az IP-címet.

A következő lépésekkel használhatja az emulátort Linux vagy macOS rendszerű környezetekben:

1. Futtassa a következő parancsot a Windows rendszerű virtuális gépről, és jegyezze fel az IPv4-címeket:

   ```bash
   ipconfig.exe
   ```

1. Az alkalmazásban módosítsa a végpont URL-címét úgy, hogy az a helyett a által visszaadott IPv4-címet használja `ipconfig.exe` `localhost` .

1. A Windows rendszerű virtuális gépen indítsa el a Azure Cosmos DB Emulatort a parancssorból a következő lehetőségek használatával. A parancssor által támogatott paraméterek részleteiért tekintse meg az [Emulator parancssori eszköz dokumentációját](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Végül fel kell oldania a tanúsítvány megbízhatósági folyamatát a Linux vagy Mac környezetben futó alkalmazás és az emulátor között. A tanúsítvány feloldásához a következő két lehetőség közül választhat:

   1. [Importálja az EMULÁTOR TLS/SSL-tanúsítványát a Linux vagy a Mac környezetbe](#import-certificate) , vagy
   2. [A TLS/SSL-érvényesítés letiltása az alkalmazásban](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>1. lehetőség: az emulátor TLS/SSL-tanúsítványának importálása

A következő részben bemutatjuk, hogyan importálhatja az emulátor TLS/SSL-tanúsítványát Linux-és macOS-környezetbe.

#### <a name="linux-environment"></a>Linux-környezet

Ha Linux rendszeren dolgozik, a .NET-továbbítókat az OpenSSL-ben végezheti el az ellenőrzés elvégzéséhez:

1. [Exportálja a tanúsítványt pfx formátumban](local-emulator-export-ssl-certificates.md#export-emulator-certificate). A PFX beállítás akkor érhető el, ha a titkos kulcs exportálását választja.

1. Másolja a PFX-fájlt a linuxos környezetbe.

1. PFX-fájl konvertálása CRT-fájlba

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Másolja a CRT-fájlt arra a mappára, amely a Linux-disztribúcióban egyéni tanúsítványokat tartalmaz. A Debian-disztribúciók általában a következő helyen találhatók: `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Frissítse a TLS/SSL-tanúsítványokat, amelyek frissítik a `/etc/ssl/certs/` mappát.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS-környezet

Ha Mac gépen dolgozik, kövesse az alábbi lépéseket:

1. [Exportálja a tanúsítványt pfx formátumban](local-emulator-export-ssl-certificates.md#export-emulator-certificate). A PFX beállítás akkor érhető el, ha a titkos kulcs exportálását választja.

1. Másolja a PFX-fájlt a Mac-környezetbe.

1. Nyissa meg a *kulcstartó-hozzáférési* alkalmazást, és importálja a pfx-fájlt.

1. Nyissa meg a tanúsítványok listáját, és azonosítsa a nevet `localhost` .

1. Nyissa meg az adott elemhez tartozó helyi menüt, válassza az *elem beolvasása* és a *megbízhatóság* a  >  *tanúsítvány használatakor* lehetőséget, majd válassza a *mindig megbízható* lehetőséget. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Nyissa meg az adott elemhez tartozó helyi menüt, válassza az elem beolvasása és a megbízhatóság alatt – ezen tanúsítvány használata esetén válassza a mindig megbízható lehetőséget.":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>2. lehetőség: az SSL-érvényesítés letiltása az alkalmazásban

Az SSL-ellenőrzés letiltása csak fejlesztési célokra javasolt, ezért nem végezhető el éles környezetben való futtatáskor. Az alábbi példák bemutatják, hogyan tilthatja le a .NET-és Node.js-alkalmazások SSL-érvényesítését.

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

A .NET Standard 2,1-as vagy újabb verzióval kompatibilis keretrendszerben futó alkalmazások esetében a következőket tudjuk használni `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2,0](#tab/ssl-netstd20)

Bármely, a .NET Standard 2,0-kompatibilis keretrendszerben futó alkalmazás esetében a következőket tudjuk használni `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Node.js alkalmazások esetében a fájl módosításával `package.json` beállíthatja az `NODE_TLS_REJECT_UNAUTHORIZED` alkalmazás indításának idejét:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Az emulátor elérésének engedélyezése helyi hálózaton

Ha több géppel is rendelkezik egyetlen hálózattal, és ha az emulátort egy gépen állítja be, és más gépről szeretné elérni. Ilyen esetben engedélyeznie kell az emulátor elérését egy helyi hálózaton.

Egy helyi hálózaton futtathatja az emulátort. A hálózati hozzáférés engedélyezéséhez írja be a `/AllowNetworkAccess` parancsot a [parancssorba](emulator-command-line-parameters.md), amelyhez a vagy a paramétert is meg kell adni `/Key=key_string` `/KeyFile=file_name` . A használatával létrehozhat `/GenKeyFile=file_name` egy véletlenszerű kulccsal rendelkező fájlt. Ezt követően átadhatja a következőt: `/KeyFile=file_name` vagy `/Key=contents_of_file` .

A hálózati hozzáférés első alkalommal történő engedélyezéséhez a felhasználónak le kell állítania az emulátort, és törölnie kell az emulátor adatkönyvtárának *%LOCALAPPDATA%\CosmosDBEmulator*.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Kapcsolatok hitelesítése emulátor használatakor

A felhőbeli Azure Cosmos DB-hez hasonlóan hitelesíteni kell minden olyan kérést, amelyet az Azure Cosmos DB Emulatorhoz végez. A Azure Cosmos DB-emulátor csak a biztonságos kommunikációt támogatja a TLS-n keresztül. Az Azure Cosmos DB-emulátor egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat az elsődleges kulcsos hitelesítéshez. Az Azure Cosmos DB Emulatorral csak a fiók és a kulcs hitelesítő adatok használhatók. Ezek a következők:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Az Azure Cosmos DB-emulátor által támogatott elsődleges kulcs kizárólag az emulátorhoz használható. Nem használhatja az éles Azure Cosmos DB-fiókot és -kulcsot az Azure Cosmos DB Emulatorral.

> [!NOTE]
> Ha elindította az emulátort a/Key kapcsolóval, akkor az alapértelmezett kulcs helyett a generált kulcsot használja `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . A/Key beállítással kapcsolatos további információkért lásd a [parancssori eszköz dokumentációját.](emulator-command-line-parameters.md)

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Kapcsolódás különböző API-khoz az emulátorral

### <a name="sql-api"></a>SQL API

Amikor az Azure Cosmos DB Emulator fut az asztalon, bármelyik támogatott [Azure Cosmos DB SDK-t](sql-api-sdk-dotnet-standard.md) vagy [Azure Cosmos DB REST API-t](/rest/api/cosmos-db/) használhatja az emulátor használatához. A Azure Cosmos DB Emulator tartalmaz egy beépített adatkezelőt is, amely lehetővé teszi tárolók létrehozását az SQL API-hoz vagy a Mongo DB API-hoz Azure Cosmos DB. Az adatkezelő használatával bármilyen kód írása nélkül megtekintheti és szerkesztheti az elemeket.

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Miután futtatta az Azure Cosmos DB emulátort az asztalon, használhatja a [Azure Cosmos db API](mongodb-introduction.md) -ját a MongoDB, hogy együttműködjön az emulátorral. Indítsa el az emulátort a [parancssorból](emulator-command-line-parameters.md) rendszergazdaként a "/EnableMongoDbEndpoint" parancs futtatásával. Ezután használja a következő kapcsolati karakterláncot a MongoDB API-fiókhoz való kapcsolódáshoz:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Table API

Miután futtatta az Azure Cosmos DB emulátort az asztalon, az [Azure Cosmos DB Table API SDK](./tutorial-develop-table-dotnet.md) használatával kommunikálhat az emulátorral. Indítsa el az emulátort a [parancssorból](emulator-command-line-parameters.md) rendszergazdaként a "/EnableTableEndpoint" parancs futtatásával. Ezután futtassa a következő kódot a Table API-fiókhoz való kapcsolódáshoz:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

Indítsa el az emulátort egy rendszergazdai [parancssorból](emulator-command-line-parameters.md) a "/EnableCassandraEndpoint" paranccsal. Másik lehetőségként beállíthatja a környezeti változót is `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` .

1. [A Python 2,7 telepítése](https://www.python.org/downloads/release/python-2716/)

1. [A Cassandra CLI/CQLSH telepítése](https://cassandra.apache.org/download/)

1. Futtassa a következő parancsokat egy normál parancssori ablakban:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. A CQLSH-rendszerhéjban futtassa a következő parancsokat a Cassandra-végponthoz való kapcsolódáshoz:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin API

Indítsa el az emulátort egy rendszergazdai [parancssorból](emulator-command-line-parameters.md)a "/EnableGremlinEndpoint" paranccsal. Másik lehetőségként beállíthatja a környezeti változót is `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. Az [Apache-tinkerpop-Gremlin-Console-3.3.4 telepítése](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. Az emulátor adatkezelője hozzon létre egy "db1" adatbázist és egy "coll1" gyűjteményt; a partíciós kulcshoz válassza a "/Name" elemet.

1. Futtassa a következő parancsokat egy normál parancssori ablakban:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. A Gremlin-rendszerhéjban futtassa a következő parancsokat az Gremlin-végponthoz való kapcsolódáshoz:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>A helyi emulátor eltávolítása

Az emulátor eltávolításához kövesse az alábbi lépéseket:

1. Lépjen ki a helyi emulátor összes nyitott példányáról úgy, hogy a tálcán a jobb gombbal a **Azure Cosmos db Emulator** ikonra kattint, majd kiválasztja a **Kilépés** lehetőséget. Az összes példány bezárása egy percig is eltarthat.

1. Írja be a Windows keresőmezőbe az **alkalmazások & szolgáltatások** elemet, majd válassza az **alkalmazások & szolgáltatások (Rendszerbeállítások)** eredményt.

1. Az alkalmazások listájában görgessen a **Azure Cosmos db emulátorhoz**, válassza ki azt, kattintson az **Eltávolítás** gombra, és erősítse meg, majd válassza újra az **Eltávolítás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a helyi emulátort az ingyenes helyi fejlesztéshez. Most folytassa a következő cikkekkel:

* [A Azure Cosmos DB Emulator-tanúsítványok exportálása Java-, Python-és Node.js-alkalmazásokkal való használatra](local-emulator-export-ssl-certificates.md)
* [Az emulátor vezérléséhez használjon parancssori paramétereket és PowerShell-parancsokat](emulator-command-line-parameters.md)
* [Hibakeresési problémák az emulátorral](troubleshoot-local-emulator.md)
