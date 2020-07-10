---
title: Azure-adatátviteli lehetőségek nagyméretű adatkészletek számára, közepes és nagy hálózati sávszélesség | Microsoft Docs
description: Megtudhatja, hogyan választhat ki Azure-megoldást adatátvitelre, ha a környezete mérsékelten nagy sávszélességgel rendelkezik, és nagy adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 900221bd27fe4020a927b3155c91aa8e494ca890
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203837"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Nagy méretű adathalmazok átvitele közepes vagy nagy hálózati sávszélességen
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha közepes és magas hálózati sávszélességgel rendelkezik a környezetében, és nagy adatkészletek átvitelét tervezi. A cikk az ajánlott adatátviteli beállításokat és a megfelelő kulcsfontosságú képességi mátrixot is ismerteti ehhez a forgatókönyvhöz.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintését itt találja: [válasszon egy Azure adatátviteli megoldást](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Forgatókönyv leírása

A nagyméretű adathalmazok a TBs és a PBs közötti adatméretekre hivatkoznak. A közepes és a nagy hálózati sávszélesség 100 Mbps és 10 GB közötti értékre utal.

## <a name="recommended-options"></a>Ajánlott beállítások

Az ebben a forgatókönyvben javasolt beállítások attól függnek, hogy a hálózati sávszélesség vagy a nagy hálózati sávszélesség van-e.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Mérsékelt hálózati sávszélesség (100 Mbps – 1 GB/s)

A mérsékelt hálózati sávszélességgel a hálózaton keresztüli adatátvitel időpontját kell megtervezni.

Az alábbi táblázat segítségével megbecsülheti az időtartamot, és attól függően választhatja az offline átvitelt vagy a hálózati átvitelt. A táblázat a hálózati adatátvitel tervezett időpontját jeleníti meg a különböző elérhető hálózati sávszélességekhez (feltéve, hogy 90%-os kihasználtságot).  

![Hálózati átvitel vagy offline átvitel](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Ha a hálózati átvitel túl lassú, akkor fizikai eszközt kell használnia. Ebben az esetben az ajánlott beállítások a saját lemezek használatával Azure Data Box családból vagy az Azure import/export szolgáltatásból származó offline átvitelű eszközök.

    - **Azure Data Box család offline átvitelhez** – a Microsoft által biztosított Data Box eszközökről származó eszközök használatával nagy mennyiségű adatok helyezhetők át az Azure-ba, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. Helyi Adatmásolás a Robocopy eszközzel. Az átvitelre szánt adatok méretétől függően Data Box Disk, Data Box vagy Data Box Heavy közül választhat.
    - **Azure import/export** – az Azure import/export szolgáltatás használata a saját lemezmeghajtók szállításával, hogy biztonságosan importáljon nagy mennyiségű adatmennyiséget az Azure Blob Storage-ba és a Azure Filesba. Ezzel a szolgáltatással adatok vihetők át az Azure Blob Storage-ból a lemezmeghajtóra és a helyszíni helyekre.

- Ha a hálózati átvitelt a rendszer ésszerűnek tekinti, akkor a [nagy hálózati sávszélességgel](#high-network-bandwidth)kapcsolatos alábbi eszközök bármelyikét használhatja.


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Nagy hálózati sávszélesség (1 GB/s – 100 GB/s)

Ha a rendelkezésre álló hálózati sávszélesség magas, használja az alábbi eszközök egyikét.

- **AzCopy** – ezzel a parancssori eszközzel könnyedén másolhatók az Azure-Blobok,-fájlok és-tárolók adatai az optimális teljesítmény érdekében. A AzCopy támogatja a párhuzamosságot és a párhuzamosságot, valamint lehetővé teszi a másolási műveletek folytatását megszakítás közben.
- **Azure Storage REST API-k/SDK** -k – egy alkalmazás létrehozásakor az Azure Storage REST API-kon keresztül fejlesztheti az alkalmazást, és használhatja a több nyelven elérhető Azure SDK-kat.
- **Azure Data Box család online átvitelekhez** – a Data Box Edge és a Data Box Gateway olyan online hálózati eszközök, amelyek az Azure-ba és az-ba helyezhetik át az adatátvitelt. Data Box Edge fizikai eszköz használata, ha a feltöltés előtt egyidejűleg szükség van a folyamatos betöltésre és az adatok előzetes feldolgozására. Data Box Gateway az eszköz virtuális verziója azonos adatátviteli képességekkel. Az adatátvitelt minden esetben az eszköz kezeli.
- **Azure Data Factory** – Data Factory kell használni az adatátviteli műveletek felskálázásához, és ha szükség van a koordinálásra és a nagyvállalati szintű figyelési lehetőségekre. A Data Factory használatával rendszeresen továbbíthatja a fájlokat több Azure-szolgáltatás, a helyszíni környezet vagy a kettő kombinációja között. a Data Factory használatával olyan adatvezérelt munkafolyamatokat hozhat létre és ütemezhet, amelyek különböző adattárakból származó adatok betöltésére, valamint az adatáthelyezés és az adatátalakítás automatizálására használhatók.

## <a name="comparison-of-key-capabilities"></a>A főbb képességek összehasonlítása

Az alábbi táblázatok összefoglalják az ajánlott lehetőségek főbb képességeinek különbségeit.

### <a name="moderate-network-bandwidth"></a>Mérsékelt hálózati sávszélesség

Ha kapcsolat nélküli adatátvitelt használ, a következő táblázat segítségével megismerheti a legfontosabb képességek különbségeit.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Adatméret**                    |    Akár 35 TBs                 |    Akár 80 TBs/eszköz                       |    Akár 800 TB/eszköz               |    Változó                            |
|    **Adattípus**                    |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    **Űrlap tényező**                  |    5 SSD/megrendelés             |    1 X 50 – lbs. asztali méretű eszköz/megrendelés    |    1 X ~ 500 – lbs. nagyméretű eszköz/megrendelés    |    Akár 10 HDD/SSD/megrendelés        |
|    **Kezdeti telepítési idő**               |    Alacsony <br>(15 perc)            |    Alacsony – mérsékelt <br> (<30 perc)               |    Mérsékelt<br>(1-2 óra)               |    Mérsékelt – nehéz<br>változó |
|    **Adatküldés az Azure-ba**           |    Igen                          |    Igen                                           |    Igen                                   |    Igen                                 |
|    **Adatexportálás az Azure-ból**           |    Nem                           |    Nem                                            |    Nem                                    |    Igen                                 |
|    **Titkosítás**                   |    AES 128 bites                  |    AES 256 bites                                   |    AES 256 bites                           |    AES 128 bites                         |
|    **Hardver**                     |     Microsoft által megadott          |    Microsoft által megadott                            |    Microsoft által megadott                    |    Ügyfél által megadott                   |
|    **Hálózati adapter**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegráció**          |    Néhány                         |    [Magas](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Magas](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Néhány                                |
|    **Shipping**                     |    Microsoft által felügyelt            |    Microsoft által felügyelt                             |    Microsoft által felügyelt                     |    Ügyfél által felügyelt                    |
| **Használat az adatáthelyezéskor**     |Egy kereskedelmi határon belül|Egy kereskedelmi határon belül|Egy kereskedelmi határon belül|Földrajzi határok között, például az EU felé|
|    **Díjszabás**                          |    [Díjszabás](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Díjszabás](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Ha online adatátvitelt használ, használja a következő szakaszban található táblázatot a nagy hálózati sávszélességhez.

### <a name="high-network-bandwidth"></a>Nagy hálózati sávszélesség

|                                     |    Eszközök AzCopy, <br>Azure PowerShell <br>Azure CLI             |    Azure Storage REST API-k, SDK-k                   |    Data Box Gateway vagy Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Adattípus**              |    Azure-Blobok, Azure Files, Azure-táblák    |    Azure-Blobok, Azure Files, Azure-táblák    |    Azure-Blobok, Azure Files                           |   70 + adatösszekötőt támogat az adattárakhoz és a formátumokhoz    |
|    **Űrlap tényező**            |    Parancssori eszközök                        |    Programozott felület                    |    A Microsoft biztosítja a virtuális <br>vagy fizikai eszköz     |    Szolgáltatás Azure Portal                                            |
|    **Kezdeti egyszeri telepítés** |    Könnyen               |    Mérsékelt                       |    Easy (<30 perc) – mérsékelt (1-2 óra)            |    Kiterjedt                                                          |
|    **Adatfeldolgozás előtti**          |    Nem                                        |    Nem                                        |    Igen (az Edge számítási feladatokkal)                               |    Igen                                                                |
|    **Átvitel más felhőkből**   |    Nem                                        |    Nem                                        |    Nem                                                    |    Igen                                                                |
|    **Felhasználó típusa**                    |    IT Pro vagy dev                                       |    Dev                                       |    IT-szakértő                                                |    IT-szakértő                                                             |
|    **Díjszabás**                      |    Ingyenes, a kimenő adatforgalomra vonatkozó díjak érvényesek         |    Ingyenes, a kimenő adatforgalomra vonatkozó díjak érvényesek         |    [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>További lépések

- [Megtudhatja, hogyan vihet át adatátvitelt importálási/exportálási szolgáltatással](/azure/storage/common/storage-import-export-data-to-blobs).
- Ismerje meg, hogyan

    - [Adatok átvitele Data Box Disksal](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Adatok átvitele Data Boxsal](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Adatok átvitele a AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Ismerje meg a következőket:
    - [Adatok átvitele Data Box Gatewaysal](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Adatok átalakítása Data Box Edge az Azure-ba való küldés előtt](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Megtudhatja, hogyan viheti át az adatátvitelt Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Adatok átvitele a REST API-k használatával

    - [A .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [A Javában](https://docs.microsoft.com/java/api/overview/azure/storage)
