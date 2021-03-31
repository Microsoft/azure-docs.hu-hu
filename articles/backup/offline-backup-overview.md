---
title: Az offline biztonsági mentés áttekintése
description: További információ az offline biztonsági mentés összetevőiről. Az Azure import/export szolgáltatás alapján Azure Data Box és offline biztonsági mentésen alapuló offline biztonsági mentést is tartalmaznak.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704135"
---
# <a name="overview-of-offline-backup"></a>Az offline biztonsági mentés áttekintése

Ez a cikk áttekintést nyújt az offline biztonsági mentésről.

Az Azure-hoz készült kezdeti teljes biztonsági mentés általában nagy mennyiségű adat online átvitelét és nagyobb hálózati sávszélességet igényel, ha a további, csak növekményes módosításokat átvevő biztonsági másolatokhoz hasonlít. Bizonyos földrajzi területeken a távoli irodák vagy adatközpontok nem mindig rendelkeznek elegendő hálózati sávszélességgel. Ezért ezek a kezdeti biztonsági másolatok több napig tartanak. Ebben az időszakban a biztonsági mentések folyamatosan ugyanazt a hálózatot használják, mint a helyszíni adatközpontban futó alkalmazások esetében.

Azure Backup támogatja az offline biztonsági mentést, amely a kezdeti biztonsági mentési adatmennyiséget offline állapotba helyezi a hálózati sávszélesség használata nélkül. Lehetővé teszi a biztonsági másolatok fizikai tárolóeszközökre másolásának mechanizmusát. Az eszközök ezután egy közeli Azure-adatközpontba kerülnek, és egy Recovery Services-tárolóba lesznek feltöltve. Ez a folyamat biztosítja a biztonsági mentési adatok robusztus átvitelét a hálózati sávszélesség használata nélkül.

## <a name="offline-backup-options"></a>Offline biztonsági mentési beállítások

Az offline biztonsági mentést a tárolóeszközök tulajdonlása alapján két módban kínáljuk:

- Offline biztonsági mentés Azure Data Box alapján (előzetes verzió)
- Offline biztonsági mentés az Azure import/export szolgáltatás alapján

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline biztonsági mentés Azure Data Box alapján (előzetes verzió)

Ezt a módot jelenleg a Microsoft Azure Recovery Services (MARS) ügynök támogatja, előzetes verzióban. Ez a lehetőség kihasználja [Azure Data Box](https://azure.microsoft.com/services/databox/) a Microsoft által védett, biztonságos és illetéktelen hozzáférést biztosító adatátviteli berendezések szállításához az adatközpont vagy a távoli iroda USB-csatlakozójához. A biztonsági mentési adatai közvetlenül ezekre az eszközökre íródnak. Ez a lehetőség elmenti a saját Azure-kompatibilis lemezek és összekötők beszerzéséhez szükséges erőfeszítést, vagy átmeneti tárolási helyként kiépíteni az ideiglenes tárterületet. A Microsoft a végpontok közötti adatátviteli logisztikát is kezeli, amelyet nyomon követhet a Azure Portalon.

Itt látható egy architektúra, amely leírja a biztonsági másolatok áthelyezését ezzel a lehetőséggel.

![Azure Backup Data Box architektúra](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Az architektúra összefoglalása:

1. Azure Backup közvetlenül másolja a biztonsági mentési adatfájlokat az előre konfigurált eszközökre.
2. Ezután visszaszállíthatja ezeket az eszközöket egy Azure-adatközpontba.
3. A Azure Data Box egy ügyfél tulajdonában lévő Storage-fiókba másolja az adataikat.
4. Azure Backup automatikusan átmásolja a biztonsági mentési adatait a Storage-fiókból a kijelölt Recovery Services-tárba. A növekményes online biztonsági mentések ütemezve vannak.

Ha Azure Data Box alapján szeretné használni az offline biztonsági mentést, tekintse meg az [Offline biztonsági mentés Azure Data Box használatával](offline-backup-azure-data-box.md)című témakört.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline biztonsági mentés az Azure import/export szolgáltatás alapján

Ezt a beállítást Microsoft Azure Backup kiszolgáló (MABS), a System Center Data Protection Manager (DPM) DPM-A és a MARS-ügynök támogatja. Az [Azure import/export szolgáltatást](../import-export/storage-import-export-service.md)használja. A kezdeti biztonsági mentési adatok az Azure-ba a saját Azure-kompatibilis lemezek és összekötők használatával vihetők át. Ennek a megközelítésnek az a módja, hogy ideiglenes tárolót kell kiépíteni átmeneti helyként, és az előre elkészített segédprogramokkal formázza és másolja a biztonsági mentési adataikat az ügyfél tulajdonában lévő lemezekre.

Itt látható egy architektúra, amely leírja a biztonsági másolatok áthelyezését ezzel a lehetőséggel.

![Azure Backup importálási/exportálási szolgáltatás architektúrája](./media/offline-backup-overview/azure-backup-import-export.png)

Az architektúra összefoglalása:

1. Ahelyett, hogy a biztonsági mentési adatokat a hálózaton keresztül küldi, Azure Backup a biztonsági mentési adatokat egy átmeneti helyre írja.
2. Az átmeneti hely adatai egy vagy több SATA-lemezre íródnak egy egyéni segédprogram használatával.
3. Az előkészítő munka részeként a segédprogram létrehoz egy Azure importálási feladatot. A SATA-meghajtók a legközelebbi Azure-adatközpontba kerülnek, és az importálási feladatokra hivatkoznak a tevékenységek összekapcsolásához.
4. Az Azure-adatközpontban a lemezeken lévő adattárakat egy Azure Storage-fiókba másolja a rendszer.
5. Azure Backup átmásolja a biztonsági mentési adatait a Storage-fiókból a Recovery Services-tárolóba. A növekményes biztonsági mentések ütemezve vannak.

Ha az Azure import/export szolgáltatáson alapuló offline biztonsági mentést szeretné használni a MARS-ügynökkel, tekintse [meg a Azure Backup offline biztonsági mentési munkafolyamatát](./backup-azure-backup-import-export.md).

Ha a MABS vagy a DPM-A szolgáltatással együtt szeretné használni, tekintse meg a [DPM és a Azure Backup Server offline biztonsági mentési munkafolyamatát](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Offline biztonsági mentési támogatás összegzése

A következő táblázat összehasonlítja a két elérhető lehetőséget, így a forgatókönyv alapján elvégezheti a megfelelő döntéseket.

| **Megfontolandó**                                            | **Offline biztonsági mentés Azure Data Box alapján**                     | **Offline biztonsági mentés az Azure import/export szolgáltatás alapján**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup üzembe helyezési modellek                              | MARS-ügynök (előzetes verzió)                                              | MARS-ügynök, MABS, DPM-A                                           |
| Biztonsági másolatok maximális száma kiszolgálónként (MARS) vagy védelmi csoportonként (MABS, DPM-A) | [Azure Data Box lemez](../databox/data-box-disk-overview.md) – 7,2 TB <br> [Azure Data Box](../databox/data-box-overview.md) – 80 TB       | 80 TB (legfeljebb 10 lemez 8 TB-ig)                          |
| Biztonság (adategységek, eszközök és szolgáltatások)                           | Adataes 256 [bites titkosítású](../databox/data-box-security.md#data-box-data-protection) <br> [Eszköz](../databox/data-box-security.md#data-box-device-protection) – robusztus eset, tulajdonosi, hitelesítő adatokra épülő felület az adatok másolásához <br> Az Azure biztonsági szolgáltatásai által [védett szolgáltatások](../databox/data-box-security.md#data-box-service-protection) | Adatátvitel – BitLocker – titkosított                                 |
| Ideiglenes előkészítési hely kiépítés                     | Nem szükséges                                                | A biztonsági másolatok becsült méretének nagyobb vagy egyenlőnek kell lennie        |
| Támogatott régiók                                           | [Azure Data Box lemezterület](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box régiók](../databox/data-box-disk-overview.md#region-availability) | [Azure import/export szolgáltatás régiói](../import-export/storage-import-export-service.md#region-availability) |
| Országok közötti szállítás                                     | Nem támogatott  <br>    A forrás-és a cél Azure-adatközpontnak ugyanabban az országban/régióban kell lennie * | Támogatott                                                    |
| Adatátviteli logisztika (szállítás, szállítás, pickup)           | Teljes mértékben Microsoft által felügyelt                                     | Ügyfél által felügyelt                                            |
| Díjszabás                                                      | [Díjszabás Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box lemez díjszabása](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure import/export szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Ha az országa vagy régiója nem rendelkezik Azure-adatközpontgal, a lemezeket egy másik országban/régióban lévő Azure-adatközpontba kell szállítani.

## <a name="next-steps"></a>Következő lépések

- [Offline biztonsági mentés Azure Backup Azure Data Box használatával](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Offline biztonsági mentési munkafolyamat a Azure Backup](backup-azure-backup-import-export.md)
- [A DPM és a Azure Backup Server kapcsolat nélküli biztonsági mentési munkafolyamata](backup-azure-backup-server-import-export.md)
