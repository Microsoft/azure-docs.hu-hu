---
title: Azure-adatátviteli lehetőségek nagyméretű adathalmazok számára alacsony vagy hálózati sávszélesség nélkül | Microsoft Docs
description: Ismerje meg, hogyan választhat Azure-megoldást adatátvitelre, ha a környezetében nincs hálózati sávszélesség, és nagy adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 6514b149b057d690d37551bd0489c63852addb1e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207496"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Nagy méretű adathalmazok átvitele alacsony hálózati sávszélességen vagy sávszélesség nélkül
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha a környezetében nincs hálózati sávszélesség, és nagy adatkészletek átvitelét tervezi. A cikk az ajánlott adatátviteli beállításokat és a megfelelő kulcsfontosságú képességi mátrixot is ismerteti ehhez a forgatókönyvhöz.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintését itt találja: [válasszon egy Azure adatátviteli megoldást](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline átvitel vagy hálózati átvitel

A nagyméretű adatkészletek azt feltételezik, hogy kevés a TBs, hogy kevés a PBs-adat. A hálózati sávszélességet nem, a hálózat lassú vagy megbízhatatlan. Lásd még:

- Az internetszolgáltató (ISP) hálózati átvitelének költségei korlátozottak.
- A biztonsági vagy szervezeti házirendek nem engedélyezik a kimenő kapcsolatokat a bizalmas adatok kezelésekor.

Az összes fenti példányban használjon egy fizikai eszközt, amely egyszeri tömeges adatátvitelt tesz elérhetővé. Válassza ki a Data Box Disk, Data Box, Data Box Heavy a Microsoft által biztosított eszközöket, vagy importálja/exportálja a saját lemezeit.

A következő táblázat segítségével ellenőrizheti, hogy a fizikai eszköz megfelelő-e. A hálózati adatátvitel tervezett időpontját mutatja a különböző rendelkezésre álló sávszélességek esetében (feltételezve, hogy 90%-os kihasználtságot). Ha a hálózati átvitel várhatóan túl lassú, akkor fizikai eszközt kell használnia.  

![Hálózati átvitel vagy offline átvitel](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Ajánlott beállítások

Az ebben a forgatókönyvben elérhető lehetőségek a Azure Data Box offline átvitelhez vagy az Azure import/export szolgáltatáshoz használható eszközök.

- **Azure Data Box család offline átvitelhez** – a Microsoft által biztosított Data Box eszközökről származó eszközök használatával nagy mennyiségű adatok helyezhetők át az Azure-ba, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. Helyi Adatmásolás a Robocopy eszközzel. Az átvitelre szánt adatok méretétől függően Data Box Disk, Data Box vagy Data Box Heavy közül választhat.
- **Azure import/export** – az Azure import/export szolgáltatás használata a saját lemezmeghajtók szállításával, hogy biztonságosan importáljon nagy mennyiségű adatmennyiséget az Azure Blob Storage-ba és a Azure Filesba. Ezzel a szolgáltatással adatok vihetők át az Azure Blob Storage-ból a lemezmeghajtóra és a helyszíni helyekre.

## <a name="comparison-of-key-capabilities"></a>A főbb képességek összehasonlítása

A következő táblázat összefoglalja a főbb képességek különbségeit.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Adatméret**                    |    Akár 35 TBs                 |    Akár 80 TBs/eszköz                       |    Akár 800 TB/eszköz               |    Változó                            |
|    **Adattípus**                    |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    **Űrlap tényező**                  |    5 SSD/megrendelés             |    1 X 50 – lbs. asztali méretű eszköz/megrendelés    |    1 X ~ 500 – lbs. nagyméretű eszköz/megrendelés    |    Akár 10 HDD/SSD/megrendelés        |
|    **Kezdeti telepítési idő**           |    Alacsony <br>(15 perc)            |    Alacsony – mérsékelt <br> (<30 perc)               |    Mérsékelt<br>(1-2 óra)               |    Mérsékelt – nehéz<br>változó |
|    **Adatküldés az Azure-ba**           |    Igen                          |    Igen                                           |    Igen                                   |    Igen                                 |
|    **Adatexportálás az Azure-ból**       |    Nem                           |    Nem                                            |    Nem                                    |    Igen                                 |
|    **Titkosítás**                   |    AES 128 bites                  |    AES 256 bites                                   |    AES 256 bites                           |    AES 128 bites                         |
|    **Hardver**                     |     Microsoft által megadott          |    Microsoft által megadott                            |    Microsoft által megadott                    |    Ügyfél által megadott                   |
|    **Hálózati adapter**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegráció**          |    Néhány                         |    [Magas](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Magas](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Néhány                                |
|    **Shipping**                     |    Microsoft által felügyelt            |    Microsoft által felügyelt                             |    Microsoft által felügyelt                     |    Ügyfél által felügyelt                    |
| **Használat az adatáthelyezéskor**     |Egy kereskedelmi határon belül|Egy kereskedelmi határon belül|Egy kereskedelmi határon belül|Földrajzi határok között, például az EU felé|
|    **Díjszabás**                      |    [Díjszabás](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan

    - [Adatok átvitele Data Box Disksal](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Adatok átvitele Data Boxsal](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Adatok átvitele importálással/exportálással](/azure/storage/common/storage-import-export-data-to-blobs).
