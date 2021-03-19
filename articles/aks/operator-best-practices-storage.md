---
title: Ajánlott eljárások a tároláshoz és a biztonsági mentéshez
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) a tároláshoz, az adattitkosításhoz és a biztonsági mentésekhez kapcsolódó ajánlott eljárásokat
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88653970"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök az Azure Kubernetes szolgáltatásban (ak) való létrehozásakor és kezelésekor az alkalmazásoknak gyakran tárterületre van szükségük. Fontos megérteni a hüvelyek teljesítménybeli igényeit és hozzáférési módszereit, hogy az alkalmazások számára biztosítható legyen a megfelelő tárterület. Az AK-csomópont mérete hatással lehet ezekre a tárolási lehetőségekre. Azt is meg kell tervezni, hogyan lehet biztonsági mentést készíteni és tesztelni a csatlakoztatott tároló visszaállítási folyamatát.

Ez az ajánlott eljárások a fürtszolgáltatások tárolási szempontjait ismertetik. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * Milyen típusú tárterület érhető el
> * AK-csomópontok helyes méretezése a tárolási teljesítmény érdekében
> * A kötetek dinamikus és statikus üzembe helyezése közötti különbségek
> * Az adatkötetek biztonsági mentésének és védelmének módjai

## <a name="choose-the-appropriate-storage-type"></a>Válassza ki a megfelelő tárolási típust

**Ajánlott eljárási útmutató** – az alkalmazás igényeinek megfelelően kiválaszthatja a megfelelő tárterületet. Használjon nagy teljesítményű, SSD-alapú tárolást az éles számítási feladatokhoz. Tervezze meg a hálózat alapú tárolást, ha több egyidejű kapcsolatra van szükség.

Az alkalmazások gyakran eltérő típusú és tárolási sebességet igényelnek. Az alkalmazásoknak olyan tárterületre van szükségük, amely az egyes hüvelyekhez csatlakozik, vagy több hüvelyben van megosztva? A tároló csak olvasási hozzáféréssel rendelkezik az adateléréshez, vagy nagy mennyiségű strukturált adatmennyiséget szeretne írni? A tárolási igényeknek meg kell határozniuk a legmegfelelőbb tárolási típust.

Az alábbi táblázat a rendelkezésre álló tárolási típusokat és azok képességeit ismerteti:

| Használati eset | Kötet beépülő modul | Egyszer írható/olvasható | Csak olvasható sok | Több olvasása/írása | A Windows Server-tároló támogatása |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Megosztott konfiguráció       | Azure Files   | Igen | Igen | Igen | Yes |
| Strukturált alkalmazásadatok        | Azure Disks   | Igen | Nem  | Nem  | Igen |
| Strukturálatlan adatok, fájlrendszerbeli műveletek | [BlobFuse][blobfuse] | Igen | Igen | Igen | Nem |

Az AK-beli kötetek két elsődleges tárolási típusát az Azure-lemezek vagy a Azure Files. A biztonság növelése érdekében a mindkét típusú tároló az Azure Storage Service Encryption (SSE) alapértelmezés szerint az inaktív adatok titkosítására használja. A lemezek jelenleg nem titkosíthatók Azure Disk Encryption használatával az AK csomópont szintjén.

A Azure Files és az Azure-lemezek egyaránt elérhetők a standard és a prémium szintű teljesítménnyel:

- A *prémium* szintű lemezeket nagy teljesítményű SSD-lemezekkel támogatja. A prémium szintű lemezeket minden éles számítási feladathoz ajánlott használni.
- A *standard szintű* lemezeket a normál fonású lemezek (HDD-k) végzik, és jó az archiváláshoz vagy a ritkán használt adateléréshez.

Ismerje meg az alkalmazás teljesítményére vonatkozó igényeket és a hozzáférési mintákat a megfelelő tárolási rétegek kiválasztásához. További információ a Managed Disks méretekről és a teljesítmény szintjeiről: az [Azure Managed Disks áttekintése][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tárolási osztályok létrehozása és használata az alkalmazás igényeinek meghatározásához

A használt tároló típusa a Kubernetes *Storage classs* használatával van definiálva. A tárolási osztályt ezután a pod vagy a telepítés specifikációja hivatkozik. Ezek a definíciók együttműködve alkotják a megfelelő tárolót, és összekapcsolják azt a hüvelyekkel. További információ: [tárolási osztályok az AK-ban][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>A csomópontok méretének méretezése a tárolási igényekhez

**Ajánlott eljárási útmutató** – az egyes csomópont-méretek maximális számú lemezt támogatnak. A különböző csomópont-méretek különböző mennyiségű helyi tárterületet és hálózati sávszélességet is biztosítanak. Tervezze meg, hogy az alkalmazás a megfelelő méretű csomópontok üzembe helyezését igényli.

Az AK-csomópontok Azure-beli virtuális gépekként futnak. A virtuális gépek különböző típusai és mérete elérhető. Minden virtuálisgép-méret különböző mennyiségű alaperőforrást biztosít, például a PROCESSZORt és a memóriát. Ezek a virtuálisgép-méretek maximális számú lemezt csatolhatnak. A tárolási teljesítmény a maximális helyi és a csatlakoztatott lemez IOPS (bemeneti/kimeneti műveletek száma másodpercenként) a virtuálisgép-méretektől függően is változhat.

Ha az alkalmazásai az Azure-lemezeket tárolási megoldásként igénylik, tervezze meg és válassza ki a megfelelő virtuálisgép-méretet. A virtuális gép méretének kiválasztásakor a CPU és a memória mennyisége nem az egyetlen tényező. A tárolási képességek szintén fontosak. Például a *Standard_B2ms* és *Standard_DS2_v2* virtuálisgép-méretek is hasonló mennyiségű processzor-és memória-erőforrást tartalmaznak. A lehetséges tárolási teljesítmény más, ahogy az alábbi táblázatban is látható:

| Csomópont típusa és mérete | vCPU | Memória (GiB) | Adatlemezek max. száma | Gyorsítótár nélküli lemez IOPS maximális száma | Gyorsítótárban lévő maximális átviteli sebesség (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

Itt a *Standard_DS2_v2* lehetővé teszi a csatlakoztatott lemezek számának kétszeres megadását, és a IOPS és a lemez átviteli sebességének három-négyszeresét biztosítja. Ha csak az alapvető számítási erőforrásokat és a költségek összevetését választotta, kiválaszthatja a *Standard_B2ms* virtuális gép méretét, és gyenge tárolási teljesítménnyel és korlátozásokkal rendelkezhet. Működjön együtt az alkalmazás fejlesztői csapatával, és Ismerje meg a tárolási kapacitását és a teljesítményre vonatkozó igényeit. Válassza ki a megfelelő virtuálisgép-méretet az AK-csomópontok számára a teljesítményre vonatkozó igények kielégítése érdekében. A virtuális gépek méretének igény szerinti módosítására szolgáló rendszeres alkalmazások.

További információ az elérhető virtuálisgép-méretekről: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Kötetek dinamikus kiépítése

**Ajánlott eljárási útmutató** – a felügyelet terhelésének csökkentése és a méretezés lehetővé tételéhez, ne statikusan hozzon létre és rendeljen állandó köteteket. Dinamikus kiépítés használata. A tárolási osztályok között adja meg a megfelelő visszaigénylési házirendet, hogy a felesleges tárolási költségeket a hüvelyek törlése után csökkentse.

Ha tárolót kell csatlakoztatnia a hüvelyekhez, állandó köteteket használ. Ezek az állandó kötetek hozhatók létre manuálisan vagy dinamikusan. Az állandó kötetek manuális létrehozása növeli a felügyeleti terhelést, és korlátozza a méretezési képességet. A dinamikus, állandó kötetek kiosztásával egyszerűsítheti a tárolók kezelését, és igény szerint növelheti és méretezheti az alkalmazásaikat.

![Állandó mennyiségi jogcímek egy Azure Kubernetes Services (ak) fürtben](media/concepts-storage/persistent-volume-claims.png)

Az állandó mennyiségi jogcím (PVC) lehetővé teszi, hogy szükség szerint dinamikusan hozza létre a tárolót. A mögöttes Azure-lemezek a hüvelyi kérelemként jönnek létre. A pod definíciójában egy kötetet kell létrehoznia, és csatolnia kell egy kijelölt csatlakoztatási útvonalhoz.

A kötetek dinamikus létrehozásával és használatával kapcsolatos fogalmakat lásd: az [állandó kötetek jogcímei][aks-concepts-storage-pvcs].

A kötetek működés közbeni megtekintéséhez tekintse meg az állandó kötetek [Azure-lemezekkel][dynamic-disks] vagy [Azure Files][dynamic-files]való dinamikus létrehozását és használatát ismertető témakört.

A tárolási osztályok definíciójának részeként állítsa be a megfelelő *reclaimPolicy*. Ez a reclaimPolicy az alapul szolgáló Azure Storage-erőforrás viselkedését szabályozza a pod törlésekor, és előfordulhat, hogy az állandó kötetre már nincs szükség. A mögöttes tárolási erőforrás törölhető vagy megtartható egy későbbi Pod-nal való használathoz. A reclaimPolicy megadható *vagy* *törölhető*. Ismerje meg az alkalmazás szükségleteit, és hajtson végre rendszeres ellenőrzéseket a tárolók számára, amelyekkel minimálisra csökkenthető a felhasznált és számlázott nem használt tárhely mennyisége.

További információ a tárolási osztályok beállításairól: [tárolási visszaigénylési házirendek][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Az adatai biztonságossá tétele és biztonsági mentése

**Ajánlott eljárások – útmutató** – az adatairól biztonsági másolatot készíthet a tárolási típusának megfelelő eszköz használatával, például Velero vagy Azure Backup. Ellenőrizze a biztonsági másolatok integritását és biztonságát.

Ha az alkalmazások lemezeken vagy fájlokban tárolt adatokat tárolnak és használnak fel, akkor rendszeres biztonsági mentést vagy pillanatképeket kell készítenie az adatairól. Az Azure-lemezek beépített pillanatkép-technológiákat használhatnak. Előfordulhat, hogy a pillanatkép-művelet végrehajtása előtt meg kell keresnie az alkalmazásokat a lemezre írások kiürítéséhez. A [Velero][velero] képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. Ha nem tudja [eltávolítani az állapotot az alkalmazásokból][remove-state], biztonsági másolatot készíthet az állandó kötetek adatairól, és rendszeresen teszteli a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak az ellenőrzéséhez.

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
