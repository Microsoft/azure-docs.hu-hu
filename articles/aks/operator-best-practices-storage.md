---
title: Ajánlott eljárások a tároláshoz és a biztonsági mentéshez
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) a tároláshoz, az adattitkosításhoz és a biztonsági mentésekhez kapcsolódó ajánlott eljárásokat
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104915"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök az Azure Kubernetes szolgáltatásban (ak) való létrehozásakor és kezelésekor az alkalmazásoknak gyakran tárterületre van szükségük. Győződjön meg arról, hogy megérti a pod teljesítményre vonatkozó szükségleteit és hozzáférési módszereit, így kiválaszthatja az alkalmazásának legmegfelelőbb tárhelyét. Az AK-csomópont mérete hatással lehet a tárolási lehetőségekre. Tervezze meg a csatlakoztatott tároló visszaállítási folyamatának biztonsági mentésének és tesztelésének módját.

Ez az ajánlott eljárások a fürtszolgáltatások tárolási szempontjait ismertetik. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * Milyen típusú tárterület érhető el.
> * Az AK-csomópontok helyes méretezése a tárolási teljesítmény érdekében.
> * A kötetek dinamikus és statikus üzembe helyezése közötti különbségek.
> * Az adatkötetek biztonsági mentésének és védelmének módjai.

## <a name="choose-the-appropriate-storage-type"></a>Válassza ki a megfelelő tárolási típust

> **Útmutatás az ajánlott eljárásokhoz**
> 
> Ismerje meg az alkalmazás igényeit, hogy kiválassza a megfelelő tárolót. Használjon nagy teljesítményű, SSD-alapú tárolást az éles számítási feladatokhoz. Tervezze meg a hálózat alapú tárolást, ha több egyidejű kapcsolatra van szüksége.

Az alkalmazások gyakran eltérő típusú és tárolási sebességet igényelnek. A következő kérdések megadásával határozza meg a legmegfelelőbb tárolási típust. 
* Az alkalmazásoknak olyan tárterületre van szükségük, amely az egyes hüvelyekhez csatlakozik?
* Az alkalmazásoknak több hüvely között kell megosztaniuk a tárhelyet? 
* A tároló csak olvasási hozzáféréssel rendelkezik az adateléréshez?
* A tárterület nagy mennyiségű strukturált adattal írható? 

Az alábbi táblázat a rendelkezésre álló tárolási típusokat és azok képességeit ismerteti:

| Használati eset | Kötet beépülő modul | Egyszer írható/olvasható | Csak olvasható sok | Több olvasása/írása | A Windows Server-tároló támogatása |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Megosztott konfiguráció       | Azure Files   | Igen | Igen | Igen | Yes |
| Strukturált alkalmazásadatok        | Azure Disks   | Igen | Nem  | Nem  | Igen |
| Strukturálatlan adatok, fájlrendszerbeli műveletek | [BlobFuse][blobfuse] | Igen | Igen | Igen | Nem |

Az AK két elsődleges típusú biztonságos tárterületet biztosít az Azure-lemezek vagy Azure Files által támogatott kötetek számára. Mindkettő az alapértelmezett Azure Storage Service Encryption (SSE) használja, amely az inaktív adatok titkosítására használható. A lemezek nem titkosíthatók Azure Disk Encryption használatával az AK csomópont szintjén.

A Azure Files és az Azure-lemezek egyaránt elérhetők a standard és a prémium szintű teljesítménnyel:

- *Prémium* szintű lemezek
    - Nagy teljesítményű SSD-lemezekkel támogatva. 
    - Minden éles munkaterheléshez ajánlott.
- *Standard szintű* lemezek
    - A normál fonású lemezek (HDD-k) támogatottak.
    - Archiválásra vagy ritkán használt adatelérésre alkalmas.

Ismerje meg az alkalmazás teljesítményére vonatkozó igényeket és a hozzáférési mintákat a megfelelő tárolási rétegek kiválasztásához. További információ a Managed Disks méretekről és a teljesítmény szintjeiről: az [Azure Managed Disks áttekintése][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tárolási osztályok létrehozása és használata az alkalmazás igényeinek meghatározásához

Adja meg a Kubernetes *tárolási osztályaival* használni kívánt tárterület típusát. A tárolási osztályt ezután a pod vagy a telepítés specifikációja hivatkozik. A tárolási osztályok definíciói együttműködve létrehozzák a megfelelő tárolót, és összekötik azt a hüvelyekkel. 

További információ: [tárolási osztályok az AK-ban][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>A csomópontok méretének méretezése a tárolási igényekhez

> **Útmutatás az ajánlott eljárásokhoz**
> 
> Az egyes csomópont-méretek maximális számú lemezt támogatnak. A különböző csomópont-méretek különböző mennyiségű helyi tárterületet és hálózati sávszélességet is biztosítanak. Az alkalmazás igényeinek megfelelően tervezze meg a csomópontok megfelelő méretének üzembe helyezését.

Az AK-csomópontok különböző Azure-beli VM-típusok és-méretek szerint futnak. Az egyes virtuálisgép-méretek a következőket biztosítják:
* Az alapvető erőforrások, például a processzor és a memória különböző mennyisége. 
* A csatolható lemezek maximális száma. 

A tárolási teljesítmény a maximális helyi és a csatlakoztatott lemez IOPS (bemeneti/kimeneti műveletek száma másodpercenként) a virtuálisgép-méretektől függően is változhat.

Ha az alkalmazásai az Azure-lemezeket tárolási megoldásként igénylik, a strategize a csomópontok megfelelő méretét. A tárolási képességek, valamint a processzor-és memória mennyisége jelentős szerepet játszik a virtuális gépek méretének meghatározásakor. 

Ha például a *Standard_B2ms* és *Standard_DS2_v2* virtuálisgép-méretek is hasonló mennyiségű processzor-és memória-erőforrást tartalmaznak, a lehetséges tárolási teljesítményük eltérő:

| Csomópont típusa és mérete | vCPU | Memória (GiB) | Adatlemezek max. száma | Gyorsítótár nélküli lemez IOPS maximális száma | Gyorsítótárban lévő maximális átviteli sebesség (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

Ebben a példában a *Standard_DS2_v2* kétszer annyi csatolt lemezt kínál, és a IOPS és a lemez átviteli sebességének háromszorosa. Ha csak az alapvető számítási erőforrások és a költségek összevetése mellett döntött, akkor lehetséges, hogy a *Standard_B2ms* virtuális gép méretét a gyenge tárolási teljesítménnyel és korlátozásokkal használta. 

Működjön együtt az alkalmazás fejlesztői csapatával, és Ismerje meg a tárolási kapacitását és a teljesítményre vonatkozó igényeit. Válassza ki a megfelelő virtuálisgép-méretet az AK-csomópontok számára a teljesítményre vonatkozó igények kielégítése érdekében. A virtuális gépek méretének igény szerinti módosítására szolgáló rendszeres alkalmazások.

További információ az elérhető virtuálisgép-méretekről: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Kötetek dinamikus kiépítése

> **Útmutatás az ajánlott eljárásokhoz** 
>
> A felügyelet terhelésének csökkentése és a skálázás engedélyezése érdekében kerülje az állandó kötetek statikus létrehozását és hozzárendelését. Dinamikus kiépítés használata. A tárolási osztályok között adja meg a megfelelő visszaigénylési házirendet, hogy a felesleges tárolási költségeket a hüvelyek törlése után csökkentse.

Ha tárolót szeretne csatolni a hüvelyekhez, használjon állandó köteteket. Az állandó kötetek hozhatók létre manuálisan vagy dinamikusan. Az állandó kötetek létrehozása manuálisan növeli a felügyeleti terhelést, és korlátozza a méretezési képességét. Ehelyett dinamikusan kiépítheti az állandó kötetet a tárolási felügyelet egyszerűsítése és az alkalmazások igény szerinti növelésének és méretezésének lehetővé tételéhez.

![Állandó mennyiségi jogcímek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volume-claims.png)

Az állandó mennyiségi jogcím (PVC) lehetővé teszi, hogy szükség szerint dinamikusan hozza létre a tárolót. A mögöttes Azure-lemezek létrehozása hüvelyként történik. A pod definíciójában hozzon létre egy kötetet, amely egy kijelölt csatlakoztatási útvonalhoz van csatolva.

A kötetek dinamikus létrehozásával és használatával kapcsolatos fogalmakat lásd: az [állandó kötetek jogcímei][aks-concepts-storage-pvcs].

A kötetek működés közbeni megtekintéséhez tekintse meg az állandó kötetek [Azure-lemezekkel][dynamic-disks] vagy [Azure Files][dynamic-files]való dinamikus létrehozását és használatát ismertető témakört.

A tárolási osztályok definíciójának részeként állítsa be a megfelelő *reclaimPolicy*. Ez a reclaimPolicy az alapul szolgáló Azure Storage-erőforrás viselkedését vezérli a pod törlésekor. A mögöttes tárolási erőforrás törölhető vagy megtartható a későbbi Pod-használathoz. Állítsa be a reclaimPolicy a *megőrzéshez* vagy a *törléshez*. 

Ismerje meg az alkalmazás szükségleteit, és hajtson végre rendszeres ellenőrzéseket a megőrzött tároláshoz, hogy minimálisra csökkentse a használaton kívüli és a számlázott tárolók mennyiségét.

További információ a tárolási osztályok beállításairól: [tárolási visszaigénylési házirendek][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Az adatai biztonságossá tétele és biztonsági mentése

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> A megfelelő eszköz használatával biztonsági másolatot készíthet az adatairól a tárolási típushoz, például Velero vagy Azure Backup. Ellenőrizze a biztonsági másolatok integritását és biztonságát.

Ha az alkalmazások lemezeken vagy fájlokban tárolt adatokat tárolnak és használnak fel, akkor rendszeres biztonsági mentést vagy pillanatképeket kell készítenie az adatairól. Az Azure-lemezek beépített pillanatkép-technológiákat használhatnak. Előfordulhat, hogy az alkalmazásoknak az írási művelet elvégzése előtt ki kell üríteniük az írásokat a lemezre. A [Velero][velero] képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. Ha nem tudja [eltávolítani az állapotot az alkalmazásokból][remove-state], biztonsági másolatot készíthet az állandó kötetek adatairól, és rendszeresen teszteli a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak az ellenőrzéséhez.

Ismerje meg az adatok biztonsági mentésének különböző módszereinek korlátozásait, és ha a pillanatkép előtt fokozatos leválasztása kell az adatait. Az adatbiztonsági másolatok nem feltétlenül lehetővé teszik a fürt üzembe helyezésének alkalmazási környezetének visszaállítását. További információ ezekről a forgatókönyvekről: [ajánlott eljárások az üzleti folytonosság és a vész-helyreállítás az AK-ban][best-practices-multi-region].

## <a name="next-steps"></a>Következő lépések

Ez a cikk a tárolási ajánlott eljárásokat ismerteti az AK-ban. További információ a Kubernetes tárolásával kapcsolatos alapismeretekről: [az AK-beli alkalmazások tárolási fogalmai][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
