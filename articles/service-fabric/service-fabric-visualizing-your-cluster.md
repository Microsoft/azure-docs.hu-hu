---
title: A fürt megjelenítése az Azure Service Fabric Explorer használatával
description: Service Fabric Explorer egy Microsoft Azure Service Fabric-fürtön futó felhőalapú alkalmazások és csomópontok vizsgálatára és felügyeletére szolgáló alkalmazás.
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a45aff305f97610cb2660c2e3f4b4427b905d7d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574055"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel

A Service Fabric Explorer (SFX) egy nyílt forráskódú eszköz az Azure Service Fabric-fürtök vizsgálatához és kezeléséhez. A Service Fabric Explorer asztali alkalmazások Windows, macOS és Linux rendszerekhez.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer Letöltés

Az alábbi hivatkozások segítségével töltheti le Service Fabric Explorer asztali alkalmazásként:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A Service Fabric Explorer asztali verziója több vagy kevesebb funkciót is tartalmazhat, mint a fürt támogatása. A szolgáltatás teljes kompatibilitásának biztosítása érdekében visszatérhet a fürtre központilag telepített Service Fabric Explorer-verzióra.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Service Fabric Explorer futtatása a fürtből

A Service Fabric Explorer egy Service Fabric-fürt HTTP-kezelési végpontján is fut. Ha az SFX-t egy webböngészőben szeretné elindítani, keresse meg a fürt HTTP-felügyeleti végpontját bármely böngészőből – például: https: \/ /clusterFQDN: 19080.

A fejlesztői munkaállomás beállításához nyissa meg a Service Fabric Explorert a helyi fürtön a (z) lehetőségre kattintva https://localhost:19080/Explorer . A [fejlesztési környezet előkészítéséhez](service-fabric-get-started.md)tekintse meg ezt a cikket.

> [!NOTE]
> Ha a fürtöt önaláírt tanúsítvány védi, a webböngésző "Ez a hely nem biztonságos" hibaüzenet jelenik meg. A figyelmeztetés felülbírálásával egyszerűen folytathatja a legtöbb modern böngészőt. Éles környezetben a fürtöt a köznapi név és a hitelesítésszolgáltató által kiadott tanúsítvány használatával kell védeni. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Kapcsolódás Service Fabric-fürthöz
Service Fabric-fürthöz való csatlakozáshoz a fürtök felügyeleti végpontja (FQDN/IP) és a HTTP-felügyeleti végpont portja (alapértelmezés szerint 19080) szükséges. Például: https \: //mysfcluster.westus.cloudapp.Azure.com:19080. A "kapcsolódás a localhost-hoz" jelölőnégyzet bejelölésével csatlakozhat egy helyi fürthöz a munkaállomáson.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
A Service Fabric-fürthöz tanúsítványokkal vagy Azure Active Directory (HRE) használatával is szabályozhatja az ügyfelek hozzáférését.

Ha egy biztonságos fürthöz próbál csatlakozni, a fürt konfigurációjától függően meg kell adnia egy ügyféltanúsítványt, vagy be kell jelentkeznie a HRE használatával.

## <a name="understand-the-service-fabric-explorer-layout"></a>A Service Fabric Explorer elrendezésének megismerése
A bal oldalon található fa használatával navigálhat Service Fabric Explorer. A fa gyökerében a fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását.

![Service Fabric Explorer-fürt irányítópultja][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>A fürt elrendezésének megtekintése
Egy Service Fabric fürt csomópontjai a tartalék tartományok és a frissítési tartományok kétdimenziós rácsára vannak helyezve. Ez az elhelyezés biztosítja, hogy alkalmazásai továbbra is elérhetők legyenek a hardveres hibák és az alkalmazások frissítéseinek jelenlétében. Megtekintheti, hogyan határozza meg az aktuális fürtöt a fürt térképe alapján.

![Service Fabric Explorer fürt térképe][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Alkalmazások és szolgáltatások megtekintése
A fürt két alfáját tartalmaz: egyet az alkalmazásokhoz és egy másikat a csomópontokhoz.

Az alkalmazás nézet használatával navigálhat Service Fabric logikai hierarchiáján: alkalmazások, szolgáltatások, partíciók és replikák.

Az alábbi példában az alkalmazás **SajátPr** két szolgáltatásból, a **MyStatefulService** és a **webszolgáltatásból** áll. Mivel a **MyStatefulService** állapot-nyilvántartó, egy olyan partíciót tartalmaz, amely egy elsődleges és két másodlagos replikával rendelkezik. Ezzel szemben a WebSvcService állapot nélküli, és egyetlen példányt tartalmaz.

![Service Fabric Explorer alkalmazás nézet][sfx-application-tree]

A fa minden szintjén a fő ablaktábla az elemre vonatkozó információkat jeleníti meg. Megtekintheti például egy adott szolgáltatás állapotát és verzióját.

![Service Fabric Explorer Essentials panel][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>A fürt csomópontjainak megtekintése
A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton. Pontosabban láthatja, hogy mely replikák futnak jelenleg.

## <a name="actions"></a>Műveletek
A Service Fabric Explorer gyors módszert kínál a csomópontokon, alkalmazásokon és szolgáltatásokon belüli műveletek meghívására a fürtön belül.

Például egy alkalmazás-példány törléséhez válassza ki az alkalmazást a bal oldali fában, majd válassza az   >  **alkalmazás törlése** műveletet.

![Alkalmazás törlése Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Ugyanezeket a műveleteket az egyes elemek melletti három pontra kattintva is végrehajthatja.
>
> Az Service Fabric Exploreron keresztül elvégezhető minden művelet a PowerShell vagy egy REST API használatával is végrehajtható az automatizálás engedélyezéséhez.
>
>

A Service Fabric Explorer használatával is létrehozhat alkalmazás-példányokat egy adott alkalmazás típusához és verziójához. Válassza ki az alkalmazás típusát a fanézetben, majd kattintson az alkalmazás **létrehozása** hivatkozásra a jobb oldali ablaktáblán a kívánt verzió mellett.

![Alkalmazás-példány létrehozása Service Fabric Explorerban][sfx-create-app-instance]

> [!NOTE]
> Az Service Fabric Explorer nem támogatja a paramétereket az alkalmazás példányainak létrehozásakor. Az alkalmazás példányai az alapértelmezett paramétereket használják.
>
>

## <a name="event-store"></a>Event Store
A EventStore a platform által kínált olyan szolgáltatás, amely Service Fabric platformon elérhető eseményeket biztosít a Service Fabric Explorer és a REST APIon keresztül. Láthatja, hogy mi történik a fürtben az egyes entitások esetében, például a csomópont, a szolgáltatás, az alkalmazás és a lekérdezés az esemény időpontja alapján. További információt a EventStore a [EventStore áttekintése című](service-fabric-diagnostics-eventstore.md)témakörben talál.   

![Képernyőfelvétel: a csomópontok ablaktábla, ahol a kiválasztott események láthatók.][sfx-eventstore]

>[!NOTE]
>A Service Fabric 6,4-es verziójának megfelelően. A EventStore alapértelmezés szerint nincs engedélyezve, és a Resource Manager-sablonban engedélyezve kell lennie.

>[!NOTE]
>A Service Fabric 6,4-es verziójának megfelelően. a EventStore API-k csak az Azure-on futó Windows-fürtök esetén érhetők el. Dolgozunk ezen funkciónak a Linuxon és a különálló fürtökön való portolása során.

## <a name="image-store-viewer"></a>lemezképtároló megjelenítője
A képtárak megjelenítője olyan natív lemezképtároló használata, amely lehetővé teszi a képáruház aktuális tartalmának megtekintését és a fájl-és mappa információinak beolvasását, valamint a fájlok és mappák eltávolítását.

![A lemezképtároló-megjelenítőt megjelenítő képernyőkép.][sfx-imagestore]

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
A Service Fabric Explorer lehetővé teszi a kapcsolódást a [biztonsági mentéssel és visszaállítással](./service-fabric-reliable-services-backup-restore.md). Az SFX biztonsági mentési és visszaállítási funkcióinak megjelenítéséhez engedélyezni kell a speciális módot.

![Speciális mód engedélyezése][0]
 
A következő műveletek lehetségesek:

* Biztonsági mentési szabályzat létrehozása, szerkesztése és törlése.
* Egy alkalmazás, szolgáltatás vagy partíció biztonsági mentésének engedélyezése és letiltása.
* Alkalmazás, szolgáltatás vagy partíció biztonsági mentésének felfüggesztése és folytatása.
* Egy partíció biztonsági másolatának elindítása és nyomon követése.
* Egy partíció aktiválását és nyomon követését.

A biztonsági mentési és visszaállítási szolgáltatással kapcsolatos további információkért tekintse meg a [REST API referenciát](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Következő lépések
* [A Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric alkalmazás központi telepítése a PowerShell használatával](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
