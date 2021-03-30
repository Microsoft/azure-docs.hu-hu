---
title: Tárolók naplóinak megtekintése az Azure Service Fabric
description: Ismerteti, hogyan lehet megtekinteni egy futó Service Fabric Container Services tároló-naplóit Service Fabric Explorer használatával.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75458184"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric tároló szolgáltatás naplófájljainak megtekintése
Az Azure Service Fabric egy tároló-Orchestrator, és támogatja a [Linux-és Windows-tárolókat](service-fabric-containers-overview.md)is.  Ez a cikk azt ismerteti, hogyan lehet megtekinteni egy futó tároló szolgáltatás vagy egy elhalt tároló tároló naplóit, így diagnosztizálhatja és elháríthatja a problémákat.

## <a name="access-the-logs-of-a-running-container"></a>Egy futó tároló naplóihoz való hozzáférés
A tároló naplói [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)használatával érhetők el.  Egy webböngészőben nyissa meg Service Fabric Explorer a fürt felügyeleti végpontján, ehhez navigáljon a következőre: `http://mycluster.region.cloudapp.azure.com:19080/Explorer` .  

A tároló-naplók azon a fürtcsomóponton találhatók, amelyen a Container Service-példány fut. Tegyük fel például, hogy beolvassa a [linuxos szavazási minta alkalmazás](service-fabric-quickstart-containers-linux.md)webes kezelőfelületének naplóit. A fanézetben bontsa ki a **cluster** > **Applications** > **VotingType** > **Fabric:/szavazás/azurevotefront** elemet.  Ezután bontsa ki a partíciót (ebben a példában a d1aa737e-f22a-e347-be16-eec90be24bc1), és ellenőrizze, hogy a tároló fut-e a fürtcsomópont *_lnxvm_0*.

A fanézetben keresse meg a *_lnxvm_0* csomóponton található kódot a **csomópontok** kibontásával > **_lnxvm_0** > **Fabric:/szavazó** > **azurevotfrontPkg** > **kód** > .  Ezután válassza a **tárolói naplók** lehetőséget a tároló naplófájljainak megjelenítéséhez.

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Egy halott vagy összeomlott tároló naplófájljainak elérése
A v 6.2-es verziótól kezdődően a [REST API](/rest/api/servicefabric/sfclient-index) -k vagy a [Service FABRIC CLI (SFCTL)](service-fabric-cli.md) parancsok használatával is beolvashatja a halott vagy összeomlott tároló naplóit.

### <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása
A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, a rendszer nem őrzi meg a tárolókat. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

A **RunInteractive** beállítás a Docker és a `--interactive` `tty` [Flags](https://docs.docker.com/engine/reference/commandline/run/#options)értéknek felel meg. Ha a jegyzékfájl értéke TRUE (igaz), akkor ezek a jelzők a tároló elindítására szolgálnak.  

### <a name="rest"></a>REST
A csomópont-művelethez tartozó [tároló-naplók beolvasása](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) paranccsal lekérheti egy összeomlott tároló naplóit. Adja meg annak a csomópontnak a nevét, amelyen a tároló fut, az alkalmazás neve, a szolgáltatás jegyzékfájljának neve és a kód csomag neve.  Adja meg a következőt: `&Previous=true`. A válasz tartalmazni fogja a kód csomag példányának Holt tárolójának tároló naplóit.

A kérelem URI-ja a következő formátumú:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Példa kérésre:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 válasz törzse:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Az összeomlott tároló naplófájljainak beolvasásához használja a [Get-Container-logs parancsot a sfctl szolgáltatásban](service-fabric-sfctl-service.md) .  Adja meg annak a csomópontnak a nevét, amelyen a tároló fut, az alkalmazás neve, a szolgáltatás jegyzékfájljának neve és a kód csomag neve. Határozza meg a `--previous` jelzőt.  A válasz tartalmazni fogja a kód csomag példányának Holt tárolójának tároló naplóit.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Válasz:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Következő lépések
- A [Linux Container Application alkalmazás létrehozásával foglalkozó oktatóanyagban](service-fabric-tutorial-create-container-images.md)dolgozhat.
- További információ a [Service Fabric és a tárolók](service-fabric-containers-overview.md) használatáról

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
