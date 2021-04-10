---
title: Az Azure Storage áttelepítési útmutatója
description: A Storage áttelepítésének áttekintése – útmutató a tárolók áttelepítéséhez
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231590"
---
# <a name="azure-storage-migration-overview"></a>Az Azure Storage áttelepítésének áttekintése

Ez a cikk az Azure-ba történő tárterület-áttelepítésre összpontosít, és útmutatást nyújt a következő tárolási áttelepítési forgatókönyvekhez:

- Strukturálatlan adat, például fájlok és objektumok áttelepítése
- Blokk alapú eszközök, például lemezek és tárterület-hálózatok (SANs) áttelepítése

## <a name="migration-of-unstructured-data"></a>Strukturálatlan adat áttelepítése

A strukturálatlan adatok áttelepítése a következő forgatókönyveket tartalmazza:

- Fájl áttelepítése hálózati csatlakoztatott tárolóról (NAS) az egyik Azure-fájlra:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [független szoftvergyártói (ISV) megoldások](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Objektumok áttelepítése objektum-tárolási megoldásokból az Azure Object Storage platformra:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Áttelepítési fázisok

A teljes áttelepítés számos különböző fázisból áll: felderítés, értékelés és áttelepítés.

![Az áttelepítés felderítési, felmérési és áttelepítési fázisait bemutató ábra](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Felderítési fázis

A felderítési fázisban meg kell határoznia az összes áttelepíteni kívánt forrást, például az SMB-megosztásokat, az NFS-exportálásokat vagy az objektumok névtereit. Ezt a fázist manuálisan is elvégezheti, vagy használhat automatikus eszközöket.

#### <a name="assessment-phase"></a>Értékelési fázis

Az értékelési fázis kritikus fontosságú az áttelepítéshez rendelkezésre álló lehetőségek megismerése érdekében. Az áttelepítés során felmerülő kockázat csökkentése érdekében, valamint a gyakori buktatók elkerüléséhez kövesse az alábbi három lépést:

| Értékelési fázis lépései                     | Beállítások                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Cél tárolási szolgáltatás kiválasztása**            | – Azure Blob Storage és Data Lake Storage<br>– Azure Files<br>– Azure NetApp Files<br>– ISV-megoldások |
| **Áttelepítési módszer kiválasztása**                  | – Online<br>– Offline<br> – A kettő kombinációja                                  |
| **Válassza ki a feladatokhoz legmegfelelőbb áttelepítési eszközt** | – Kereskedelmi eszközök (Azure és ISV)<br> – Nyílt forráskód                             


Több kereskedelmi (ISV) eszköz is rendelkezésre áll, amelyek segíthetnek az értékelési fázisban. Tekintse meg az [összehasonlító mátrixot](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Cél tárolási szolgáltatás kiválasztása

A cél tárolási szolgáltatás kiválasztása az alkalmazástól vagy az adatokhoz hozzáférő felhasználóktól függ. A megfelelő választás a technikai és a pénzügyi szempontoktól függ. Először is végezzen technikai értékelést a lehetséges célok felméréséhez, és határozza meg, hogy mely szolgáltatások felelnek meg a követelményeknek. Ezután végezzen pénzügyi értékelést a legjobb választás meghatározásához.

Az áttelepítés céljára szolgáló tárolási szolgáltatás kiválasztásához az egyes szolgáltatások következő szempontjait kell kiértékelnie:

- Protokolltámogatás
- Teljesítmény jellemzői
- A cél tárolási szolgáltatás korlátai

Az alábbi ábra egy egyszerűsített döntési fa, amely segítséget nyújt az ajánlott Azure file Service-hez. Ha a natív Azure-szolgáltatások nem felelnek meg a követelményeknek, számos [független szoftvergyártói (ISV) megoldás](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) lesz.

Miután elvégezte a technikai értékelést, és kiválasztja a megfelelő célt, a költséghatékonyságot a legköltséghatékonyabb megoldás meghatározásához kell elvégeznie.

![Alapszintű döntési fa a megfelelő Fájlszolgáltatások kiválasztásához](./media/storage-migration-overview/files-decision-tree.png)

A döntési fa egyszerű megtartásához a cél tárolási szolgáltatás korlátai nem kerülnek be a diagramba. Ha többet szeretne megtudni a jelenlegi korlátokról, és annak megállapításához, hogy szükség van-e a beállítások módosítására, tekintse meg a következőt:

- [A Storage-fiók korlátai](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Blob Storage korlátok](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Az Azure Files méretezhetőségi és teljesítménycéljai](/azure/storage/files/storage-files-scale-targets)
- [Erőforrás-korlátok Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Ha bármelyik korlát egy szolgáltatás használatára blokkoló, az Azure számos olyan tároló-gyártót támogat, amely az Azure Marketplace-en kínálja megoldásait. További információ a Fájlszolgáltatások szolgáltatást nyújtó érvényesített ISV-partnerekről: [Azure Storage-partnerek elsődleges és másodlagos tároláshoz](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Áttelepítési módszer kiválasztása

Két alapszintű áttelepítési módszer áll rendelkezésre a tárolók áttelepítéséhez.

- **Online**. Az online módszer a hálózatot használja az adatáttelepítéshez. A nyilvános Internet vagy az [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) is használható. Ha a szolgáltatás nem rendelkezik nyilvános végponttal, a nyilvános internettel rendelkező VPN-t kell használnia.
- **Offline.** Az offline metódus a [Azure Data Box](https://azure.microsoft.com/services/databox/) eszközök egyikét használja.

Az online metódus és az offline metódus használatára vonatkozó döntés a rendelkezésre álló hálózati sávszélességtől függ. Az online módszer olyan esetekben ajánlott, amikor elegendő hálózati sávszélesség szükséges a Migrálás végrehajtásához a szükséges idővonalon belül.

Mindkét módszer kombinációját használhatja a kezdeti tömeges áttelepítéshez és a módosítások fokozatos áttelepítéséhez használható online módszerhez. A mindkét módszer egyidejű használata magas szintű koordinációt igényel, ezért nem ajánlott. Ha úgy dönt, hogy mindkét módszert használja, az online áttelepített adatkészletek elkülönítése az offline állapotú adatkészletekből.

A különböző áttelepítési módszerekkel és irányelvekkel kapcsolatos további információkért lásd: [Azure-megoldás kiválasztása adatátvitelhez](/azure/storage/common/storage-choose-data-transfer-solution) és [Migrálás az Azure-fájlmegosztást](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Válassza ki a feladatokhoz legmegfelelőbb áttelepítési eszközt

A Migrálás elvégzéséhez számos különböző áttelepítési eszköz használható. Néhány olyan nyílt forráskódú, mint a AzCopy, a Robocopy, az xcopy és az rsync, míg mások kereskedelmi jellegűek. Az elérhető kereskedelmi eszközök listája és összehasonlítása az [összehasonlítási mátrixban](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)érhető el.

A nyílt forráskódú eszközök jól illeszkednek a kisméretű áttelepítésekhez. A Windows-fájlkiszolgálók Azure Filesra való áttelepítéséhez a Microsoft a Azure Files natív képességének és a [Azure file Sync](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync)használatával való elindítását javasolja. A különböző forrásokból, nagy kapacitásokból vagy speciális követelményekből álló összetettebb áttelepítések, például a szabályozás vagy a naplózási képességekkel való részletes jelentéskészítés esetén a kereskedelmi eszközök a legjobb választás. Ezek az eszközök megkönnyítik az áttelepítést, és jelentős mértékben csökkentik a kockázatot. A legtöbb kereskedelmi eszköz a felderítést is elvégezheti, amely értékes adatokat biztosít az értékeléshez.

#### <a name="migration-phase"></a>Áttelepítési fázis

Az áttelepítési fázis a végső áttelepítési lépés, amely az adatáthelyezést és az áttelepítést végzi. Az áttelepítési fázist általában többször is át kell futtatnia, hogy könnyebb legyen az átállás. Az áttelepítési fázis a következő lépésekből áll:

1. **Kezdeti áttelepítés.** A kezdeti áttelepítési lépés áttelepíti a forrásról a célhelyre irányuló összes adatforrást. Ez a lépés áttelepíti az áttelepíteni kívánt adatmennyiséget.
2. **Újraszinkronizálás.** Az újraszinkronizálási művelet áttelepíti a kezdeti áttelepítési lépés után módosult összes adatmódosítást. Ezt a lépést többször is megismételheti, ha sok módosítás van. Több újraszinkronizálási művelet futtatásának célja, hogy csökkentse az utolsó lépéshez szükséges időt. Ha inaktív adatokra és olyan adatokra van szüksége, amelyek nem változnak (például biztonsági mentési vagy archiválási adatok), kihagyhatja ezt a lépést.
3. **Végső átállás**. A végső átállítási lépés a forrásról a cél felé irányuló adatok aktív használatát, majd a forrás kikapcsolását váltja ki.

A strukturálatlan adatok áttelepítésének időtartama több szemponttól függ. A választott módszeren kívül a legfontosabb tényezők az adatok és a fájlméret eloszlásának teljes mérete. Minél nagyobb a teljes adathalmaz, annál tovább az áttelepítési idő. Minél kisebb az átlagos fájlméret, annál nagyobb az áttelepítési idő. Ha nagy számú kis fájlból áll, érdemes lehet nagyobb fájlok (például. tar vagy. zip fájl) archiválását a teljes áttelepítési idő csökkentése érdekében.

## <a name="migration-of-block-based-devices"></a>Blokk alapú eszközök migrálása

A blokk alapú eszközök migrálása általában a virtuális gép vagy a fizikai gazdagép áttelepítésének részeként történik. Ez egy gyakori tévhit, amely a tárolási döntések késleltetését késlelteti a Migrálás után. Ha ezeket a döntéseket a munkaterhelési követelményekkel kapcsolatos megfelelő megfontolások alapján hozza előre, a felhőbe való zökkenőmentes áttelepítéshez vezethet.

Az áttelepítéshez és az áttelepítéshez szükséges munkaterhelések megismeréséhez tekintse meg a [Azure Disk Storage dokumentációját](/azure/virtual-machines/disks-types)és erőforrásait a [Disk Storage termék lapon](https://azure.microsoft.com/services/storage/disks/#resources). Megtudhatja, hogy mely lemezek felelnek meg a követelményeknek, valamint a legújabb képességeket, például a [lemezek](/azure/virtual-machines/disk-bursting)betöltését. További információ a virtuális gépek és a mögöttes blokk alapú eszközök áttelepítéséről: [Azure Migrate](/azure/migrate/) dokumentáció.

## <a name="see-also"></a>Lásd még

- [Azure-megoldás kiválasztása adatátvitelhez](/azure/storage/common/storage-choose-data-transfer-solution)
- [Kereskedelmi áttelepítési eszközök összehasonlítása](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrálás Azure-fájlmegosztásokba](/azure/storage/files/storage-files-migration-overview)
- [Migrálás Data Lake Storagera az Azure-hoz készült WANdisco LiveData platformmal](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Az Azure Storage-ba másolhatja vagy áthelyezheti az AzCopy](https://aka.ms/azcopy)
- [Nagyméretű adatkészletek migrálása az Azure Blob Storageba a AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)