---
title: Az Azure adatátviteli lehetőségei kis adatkészletekhez alacsony – közepes hálózati sávszélességgel | Microsoft Docs
description: Megtudhatja, hogyan választhat ki Azure-megoldást adatátvitelre, ha alacsony vagy közepes sávszélességgel rendelkezik a környezetében, és a kis adatkészletek átvitelét tervezi.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023227"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Kis méretű adathalmazok átvitele kis vagy közepes hálózati sávszélességen
 
Ez a cikk áttekintést nyújt az adatátviteli megoldásokról, ha alacsony vagy mérsékelt hálózati sávszélességgel rendelkezik a környezetében, és kis adatkészletek átvitelét tervezi. A cikk az ajánlott adatátviteli beállításokat és a megfelelő kulcsfontosságú képességi mátrixot is ismerteti ehhez a forgatókönyvhöz.

Az összes rendelkezésre álló adatátviteli lehetőség áttekintését itt találja: [válasszon egy Azure adatátviteli megoldást](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Forgatókönyv leírása

A kis méretű adathalmazok adatmérete a néhány gigabájtostól a néhány terabájtosig terjed. A kis vagy közepes hálózati sávszélesség 45 Mbps (T3-csatlakozás az adatközpontban) és 1 Gbps közötti sebességre utal.

- Ha csak néhány fájlt helyez át, és nem kell automatizálnia az adatátvitelt, akkor az eszközöket grafikus felülettel kell megfontolni.
- Ha jártas a rendszerfelügyelet terén, vegye fontolóra parancssori vagy a programozási/szkriptelési eszközök használatát.

## <a name="recommended-options"></a>Ajánlott lehetőségek

Az ebben a forgatókönyvben javasolt beállítások a következők:

- **Grafikus felületi eszközök** , például a Azure Storage Explorer és az Azure Storage a Azure Portalban. Ezek egyszerű módszert nyújtanak az adatok megtekintésére és néhány fájl gyors átvitelére.

    - **Azure Storage Explorer** – ez a platformfüggetlen eszköz lehetővé teszi az Azure Storage-fiókok tartalmának kezelését. Segítségével feltöltheti, letöltheti és kezelheti a blobokat, fájlokat, üzenetsorokat, táblákat, valamint az Azure Cosmos DB-entitásokat. A Blob Storage szolgáltatással együtt használva kezelheti a blobokat és a mappákat, valamint blobokat tölthet fel és tölthet le a helyi fájlrendszer és a Blob Storage, illetve különböző tárfiókok között.
    - **Azure Portal** – az Azure Storage in Azure Portal webalapú felületet biztosít a fájlok feltárásához és az új fájlok feltöltéséhez egyszerre. Ez akkor jó megoldás, ha nem szeretne eszközöket telepíteni és parancsokat végrehajtani a fájlok gyors tallózásához, vagy egyszerűen csak néhány új fájlt kell feltöltenie.

- **Parancsfájlok/programozott eszközök** , mint például a AzCopy/PowerShell/Azure CLI és az Azure Storage REST API-k.

    - **AzCopy** – ezzel a parancssori eszközzel könnyedén másolhatók az Azure-Blobok,-fájlok és-tárolók adatai az optimális teljesítmény érdekében. Az AzCopy támogatja az egyidejűséget és a párhuzamosságot, valamint lehetővé teszi a másolási műveletek folytatását egy esetleges megszakítás esetén.
    - **Azure PowerShell** – a rendszerfelügyelettel kényelmes felhasználók számára az adatok átviteléhez használja a Azure PowerShell Azure Storage modulját.
    - **Azure CLI** – ezen platformfüggetlen eszköz használatával kezelheti az Azure-szolgáltatásokat, és feltöltheti az Azure Storage-ba.
    - **Azure Storage REST API-k/SDK** -k – alkalmazás létrehozásakor az alkalmazást kifejlesztheti az Azure Storage REST API-jai/SDK-kkal, és használhatja a több nyelven elérhető Azure-beli ügyféloldali kódtárakat is.


## <a name="comparison-of-key-capabilities"></a>A főbb képességek összehasonlítása

Az alábbi táblázat a fő képességek közötti különbségeket összegzi.

| Szolgáltatás | Azure Storage Explorer | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API-k vagy SDK-k |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Rendelkezésre állás | Letöltés és telepítés <br>Önálló eszköz | Webalapú feltárási eszközök a Azure Portal | Parancssori eszköz |Programozható felületek a .NET, a Java, a Python, a JavaScript, a C++, a go, a Ruby és a PHP használatával |
| Grafikus felület | Igen | Igen | Nem | Nem |
| Támogatott platformok | Windows, Mac, Linux | Webes alapú |Windows, Mac, Linux |Minden platform |
| Engedélyezett blob Storage-műveletek<br>blobok és mappák esetén | Feltöltés<br>Letöltés<br>Kezelés | Feltöltés<br>Letöltés<br>Kezelés |Feltöltés<br>Letöltés<br>Kezelés | Igen, testreszabható |
| Engedélyezett Data Lake Gen1-tároló<br>fájlok és mappák műveletei | Feltöltés<br>Letöltés<br>Kezelés | No |Feltöltés<br>Letöltés<br>Kezelés                   | No |
| Engedélyezett file Storage-műveletek<br>fájlok és könyvtárak esetén | Feltöltés<br>Letöltés<br>Kezelés | Feltöltés<br>Letöltés<br>Kezelés   |Feltöltés<br>Letöltés<br>Kezelés | Igen, testreszabható |
| Engedélyezett Table Storage-műveletek<br>táblákhoz |Kezelés | No |Tábla-támogatás a AzCopy v7-ben |Igen, testreszabható|
| Engedélyezett üzenetsor-tárolás | Kezelés | Nem  |Nem | Igen, testreszabható|


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [viheti át az adatátvitelt Azure Storage Explorer](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md)használatával.
- [Adatok áthelyezése az AzCopyval](./storage-use-azcopy-v10.md)