---
title: Azure-megoldás kiválasztása az időszakos adatátvitelhez | Microsoft Docs
description: Megtudhatja, hogyan választhat ki Azure-megoldást adatátvitelre az adatok rendszeres továbbításakor.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573918"
---
# <a name="solutions-for-periodic-data-transfer"></a>Időszakos adatátviteli megoldások
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, amikor rendszeresen továbbít adatátvitelt. Az időszakos adatátvitel a hálózaton keresztül rendszeres időközönként vagy folyamatos adatáthelyezéssel ismétlődőként is kategorizálható. A cikk az ajánlott adatátviteli beállításokat és a megfelelő kulcsfontosságú képességi mátrixot is ismerteti ehhez a forgatókönyvhöz.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintését itt találja: [válasszon egy Azure adatátviteli megoldást](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Ajánlott lehetőségek

Az időszakos adatátvitel javasolt beállításai két kategóriába sorolhatók attól függően, hogy az átvitel ismétlődő-e vagy folyamatos.

- **Megírt/programozott eszközök** – a rendszeres időközönként bekövetkezett adatátvitelhez használja a megírt és programozott eszközöket, például a AzCopy és az Azure Storage REST API-kat. Ezen eszközök esetében a célcsoportot az informatikai szakemberek és a fejlesztők alkotják.

    - **AzCopy** – ezzel a parancssori eszközzel könnyedén másolhatók az Azure-Blobok,-fájlok és-tárolók adatai az optimális teljesítmény érdekében. Az AzCopy támogatja az egyidejűséget és a párhuzamosságot, valamint lehetővé teszi a másolási műveletek folytatását egy esetleges megszakítás esetén.
    - **Azure Storage REST API-k/SDK** -k – egy alkalmazás létrehozásakor az Azure Storage REST API-kon keresztül fejlesztheti az alkalmazást, és használhatja a több nyelven elérhető Azure SDK-kat. A REST API-k az Azure Storage adatátviteli függvénytárát is kihasználhatják, amely különösen az Azure-ba irányuló és az adatok nagy teljesítményű másolására szolgál.

- **Folyamatos adatfeldolgozási eszközök** – folyamatos, folyamatos adatfeldolgozás esetén kiválaszthatja Data Box online átvitelű eszköz vagy Azure Data Factory egyikét. Az eszközök beállítását informatikai szakemberek végzik, és lehetővé teszik az adatátvitel átlátható automatizálását.

    - **Azure Data Factory** – Data Factory kell használni az adatátviteli műveletek felskálázásához, és ha szükség van a koordinálásra és a nagyvállalati szintű figyelési lehetőségekre. Az Azure Data Factory használatával létrehozhat egy olyan felhőalapú folyamatot, amely rendszeresen továbbítja a fájlokat több Azure- vagy helyszíni szolgáltatás, illetve a kettő kombinációja között. Az Azure Data Factoryvel olyan adatvezérelt munkafolyamatokat vezényelhet, amelyek különböző adattárakból töltenek be adatokat, illetve automatizálják az adatáthelyezést és az adatátalakítást.
    - **Azure Data Box család online átvitelhez** – a Data Box Edge és a Data Box Gateway olyan online hálózati eszközök, amelyek az Azure-ba és az-ba helyezhetik át az adatátvitelt. A Data Box Edge mesterséges intelligenciára (MI) épülő peremhálózati számítást alkalmaz az adatok feltöltés előtti feldolgozásához. A Data Box Gateway az eszköz virtuális verziója, amely ugyanazokkal az adatátviteli képességekkel rendelkezik.


## <a name="comparison-of-key-capabilities"></a>A főbb képességek összehasonlítása

Az alábbi táblázat a fő képességek közötti különbségeket összegzi.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Parancsfájlos/programozott hálózati adatátvitel

| Képesség                  | AzCopy                                 | Azure Storage REST API-k       |
|-----------------------------|----------------------------------------|-------------------------------|
| Űrlap tényező                 | A Microsoft parancssori eszköze       | Az ügyfelek a tárterületen dolgoznak <br> REST API-k az Azure ügyféloldali kódtárai használatával |
| Kezdeti egyszeri telepítés     | Minimális                                | Mérsékelt, változó fejlesztési tevékenység    |
| Adatformátum                 | Azure-Blobok, Azure Files, Azure-táblák | Azure-Blobok, Azure Files, Azure-táblák   |
| Teljesítmény                 | Már optimalizált                      | Optimalizálás a fejlesztés során                  |
| Díjszabás                     | Ingyenes, a kimenő adatforgalomra vonatkozó díjak érvényesek      | Ingyenes, a kimenő adatforgalomra vonatkozó díjak érvényesek        |

### <a name="continuous-data-ingestion-over-network"></a>Folyamatos adatfeldolgozás hálózaton keresztül

| Szolgáltatás                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Űrlap tényező                                   | Virtuális eszköz             | Fizikai eszköz          | Szolgáltatás Azure Portalban, helyszíni ügynök                                                            |
| Hardver                                      | A hypervisor            | Microsoft által biztosított    | NA                                                            |
| Kezdeti beállítási erőfeszítés                          | Alacsony (<30 perc.)            | Mérsékelt (~ pár óra) | Nagyméretű (~ nap)                                                 |
| Adatformátum                                   | Azure-Blobok, Azure Files   | Azure-Blobok, Azure Files | [70 + adatösszekötőt támogat az adattárakhoz és a formátumokhoz](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Adatfeldolgozás előtti                           | No                         | Igen, Edge-számításon keresztül    | Yes                                                           |
| Helyi gyorsítótár<br>(a helyszíni adattároláshoz)    | Igen                        | Igen                      | Nem                                                            |
| Átvitel más felhőkből                    | Nem                         | Nem                       | Igen                                                           |
| Díjszabás                                       | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Díjszabás](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Következő lépések

- [Adatok átvitele a AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [További információ a Storage REST API-kkal való adatátvitelről](/dotnet/api/overview/azure/storage).
- Ismerje meg a következőket:
    - [Adatok átvitele Data Box Gatewaysal](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Adatok átalakítása Data Box Edge az Azure-ba való küldés előtt](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Megtudhatja, hogyan viheti át az adatátvitelt Azure Data Factory használatával](../../data-factory/tutorial-bulk-copy-portal.md).