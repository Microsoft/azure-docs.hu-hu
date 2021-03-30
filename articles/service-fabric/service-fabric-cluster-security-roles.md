---
title: 'Service Fabric a fürt biztonsága: ügyfelek szerepkörei'
description: Ez a cikk a két ügyfél-szerepkört és a szerepkörökhöz megadott engedélyeket ismerteti.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75451900"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Szerepköralapú hozzáférés-vezérlés Service Fabric ügyfelek számára
Az Azure Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz csatlakozó ügyfelekhez: rendszergazda és felhasználó. A hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző felhasználói csoportok esetében korlátozza a hozzáférést bizonyos fürt műveleteihez, így a fürt biztonságosabbá válik.  

A **rendszergazdák** teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az írási/olvasási képességeket is). Alapértelmezés szerint a **felhasználók** csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és a szolgáltatások megoldásához.

A fürt létrehozásakor a két ügyfél-szerepkört (rendszergazdát és ügyfelet) kell megadnia, ha külön tanúsítványokat biztosít mindegyikhez. A biztonságos Service Fabric-fürt beállításával kapcsolatos részletekért tekintse meg a [Service Fabric a fürt biztonsága](service-fabric-cluster-security.md) című témakört.

## <a name="default-access-control-settings"></a>Alapértelmezett hozzáférés-vezérlési beállítások
A rendszergazdai hozzáférés-vezérlési típus teljes hozzáféréssel rendelkezik az összes FabricClient API-hoz. A Service Fabric-fürtön bármilyen olvasást és írást végrehajthat, beleértve a következő műveleteket is:

### <a name="application-and-service-operations"></a>Alkalmazás-és szolgáltatási műveletek
* **CreateService**: szolgáltatás létrehozása                             
* **CreateServiceFromTemplate**: szolgáltatás létrehozása sablonból                             
* **Updateservice művelet**: szolgáltatások frissítései                             
* **DeleteService művelet**: szolgáltatás törlése                             
* **ProvisionApplicationType**: alkalmazás típusának kiépítés                             
* **Létrehozás**: alkalmazás létrehozása                               
* **Alkalmazástörlés**: alkalmazás törlése                             
* **UpgradeApplication**: az alkalmazások frissítéseinek elindítása vagy megszakítása                             
* **UnprovisionApplicationType**: az alkalmazás típusa nem kiépítés                             
* **MoveNextUpgradeDomain**: az alkalmazások frissítésének folytatása explicit frissítési tartománnyal                             
* **ReportUpgradeHealth**: az alkalmazások frissítésének folytatása a jelenlegi frissítési folyamattal                             
* **ReportHealth**: jelentéskészítés állapota                             
* **PredeployPackageToNode**: előtelepítési API                            
* **CodePackageControl**: kódok újraindítása                             
* **Recoverpartition művelet fejeződött**: partíció helyreállítása                             
* **RecoverPartitions**: partíciók helyreállítása                             
* **Recoverservicepartitions művelet fejeződött**: a szolgáltatási partíciók helyreállítása                             
* **Recoversystempartitions művelet**: rendszerszolgáltatási partíciók helyreállítása                             

### <a name="cluster-operations"></a>Fürtműveletek
* **ProvisionFabric**: msi és/vagy a fürt jegyzékfájljának kiépítés                             
* **UpgradeFabric**: a fürt frissítéseinek indítása                             
* **UnprovisionFabric**: az MSI és/vagy a fürt jegyzékfájljának kiépítése                         
* **MoveNextFabricUpgradeDomain**: a fürt frissítéseinek folytatása explicit frissítési tartománnyal                             
* **ReportFabricUpgradeHealth**: a fürt frissítésének folytatása a jelenlegi frissítési folyamattal                             
* **StartInfrastructureTask**: infrastruktúra-feladatok indítása                             
* **FinishInfrastructureTask**: infrastrukturális feladatok befejezése                             
* **InvokeInfrastructureCommand**: infrastruktúra-feladatok kezelési parancsai                              
* **ActivateNode**: csomópont aktiválása                             
* **Deactivatenode művelet (**: csomópont inaktiválása                             
* **DeactivateNodesBatch**: több csomópont inaktiválása                             
* **RemoveNodeDeactivations**: inaktiválás visszaállítása több csomóponton                             
* **GetNodeDeactivationStatus**: inaktiválási állapot ellenőrzése                             
* **Nodestateremoved művelet**: jelentési csomópont állapota eltávolítva                             
* **ReportFault**: jelentéskészítési hiba                             
* **FileContent**: rendszerkép-tároló ügyfél-fájlátviteli (a fürtön kívül)                             
* **FileDownload**: rendszerkép-tároló – ügyféloldali fájl letöltése (a fürtön kívül)                             
* **InternalList**: a rendszerkép-tároló ügyfél-fájllista művelete (belső)                             
* **Törlés**: rendszerkép-tároló ügyfél-törlési művelete                              
* **Feltöltés**: rendszerkép-tároló ügyfél-feltöltési művelete                             
* **NodeControl**: csomópontok indítása, leállítása és újraindítása                             
* **MoveReplicaControl**: replikák áthelyezése egyik csomópontról a másikra                             

### <a name="miscellaneous-operations"></a>Egyéb műveletek
* **Ping**: ügyfél pingelése                             
* **Lekérdezés**: az összes engedélyezett lekérdezés
* **NameExists**: URI-azonosítók létezésének ellenőrzése                             

A felhasználói hozzáférés-vezérlés típusa alapértelmezés szerint a következő műveletekre korlátozódik: 

* **EnumerateSubnames**: névhasználati URI enumerálása                             
* **EnumerateProperties**: elnevezési tulajdonság enumerálása                             
* **PropertyReadBatch**: névhasználati tulajdonság – olvasási műveletek                             
* **GetServiceDescription**: hosszú távú lekérdezési szolgáltatás értesítései és olvasási szolgáltatás leírása                             
* **ResolveService**: panasztételi megoldáson alapuló szolgáltatás feloldása                             
* **ResolveNameOwner**: névhasználati URI tulajdonosának feloldása                             
* **ResolvePartition**: rendszerszolgáltatások feloldása                             
* **ServiceNotifications**: eseményvezérelt szolgáltatás értesítései                             
* **GetUpgradeStatus**: az alkalmazás frissítési állapotának lekérdezése                             
* **GetFabricUpgradeStatus**: a fürt frissítési állapotának lekérdezése                             
* **InvokeInfrastructureQuery**: infrastruktúra-feladatok lekérdezése                             
* **Lista**: a rendszerkép-tárolóban megjelenő ügyfél-fájllista művelet                             
* **ResetPartitionLoad**: feladatátvételi egység terhelésének alaphelyzetbe állítása                             
* **ToggleVerboseServicePlacementHealthReporting**: részletes szolgáltatás elhelyezési állapotának jelentése                             

A rendszergazdai hozzáférés-vezérlés szintén hozzáfér az előző műveletekhez.

## <a name="changing-default-settings-for-client-roles"></a>Az ügyfél szerepköreinek alapértelmezett beállításainak módosítása
A fürt jegyzékfájljának fájljában szükség esetén rendszergazdai képességeket biztosíthat az ügyfélnek. A [fürt létrehozásakor](service-fabric-cluster-creation-via-portal.md)a **háló beállításai** lehetőségre kattintva módosíthatja az alapértelmezett értékeket, és megadhatja az előző beállításokat a **név**, a **rendszergazda**, a **felhasználó** és az **érték** mezőkben.

## <a name="next-steps"></a>Következő lépések
[Service Fabric-fürt biztonsága](service-fabric-cluster-security.md)

[Service Fabric fürt létrehozása](service-fabric-cluster-creation-via-portal.md)

