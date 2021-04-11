---
title: A Microsoft Azure Data Box Gateway áttekintése | Microsoft Docs
description: A cikk ismerteti az Azure Data Box Gateway virtuálisberendezés-tároló megoldást, amellyel adatokat vihet át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 2adbefff2a0154268157c7d1735d83e70aa92ee8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563589"
---
# <a name="what-is-azure-data-box-gateway"></a>Mi az Azure Data Box Gateway?

Az Azure Data Box Gateway egy tárolási megoldás, amellyel zökkenőmentesen vihet át adatokat az Azure-ba. Ez a cikk áttekintést nyújt az Azure Data Box Gateway megoldásról, annak előnyeiről, főbb képességeiről és azokról a forgatókönyvekről, amelyekben üzembe helyezheti ezt az eszközt.

A Data Box Gateway egy virtuális eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen alapul. A virtuális eszköz a helyszínen található, és Ön írja rá az adatokat NFS és SMB protokoll használatával. Az eszköz ezután átviszi az adatait az Azure-beli blokkblobba, lapblobba vagy az Azure Filesba.

## <a name="use-cases"></a>Használati esetek

A Data Box Gatewayt használhatja felhőbe történő adatátvitelre, például felhőben történő archiválásra, vészhelyreállításra, vagy ha felhőszinten kell feldolgoznia az adatait. Itt találhatók azok a különböző forgatókönyvek, amelyekben a Data Box Gateway használata javasolt az adatátvitelhez.

- **Felhőben történő archiválás** – A Data Box Gateway segítségével több száz TB-nyi adatot másolhat át biztonságosan és hatékonyan az Azure Storage-ba. Az archiválási forgatókönyvek keretében az adatokat betöltheti egy alkalommal vagy folyamatosan is.

- **Folyamatos adatfeldolgozás** – az adatmérettől függetlenül folyamatosan betöltenek adatot az eszközre a felhőbe való másoláshoz. Ahogy az adatok az átjáró eszközre íródnak, az eszköz feltölti az Azure Storage-ba.  

- A **kezdeti tömeges átvitelt a növekményes átvitel** , valamint az offline módban (kezdeti mag), valamint a növekményes átvitelek (folyamatos adatcsatorna) Data Box Gateway a hálózaton keresztül történő növekményes átvitelhez Data Box.

További információ: [Azure Data Box Gateway használati esetek](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Előnyök

A Data Box Gateway az alábbi előnyökkel jár:

- **Egyszerű adatátvitel** – Használatával olyan könnyedén helyezhet át adatokat az Azure Storage-ból vagy az Azure Storage-ba, mintha csak egy helyi hálózati megosztással dolgozna.  
- **Nagy teljesítmény** – Nagy teljesítményű adatátvitelével problémamentessé válik a hálózati adatátvitel az Azure-ban.
- **Gyors hozzáférés és magas adatfeldolgozási díjak** a munkaidő alatt – Data Box Gateway helyi gyorsítótárral rendelkezik, amelyet a virtuális eszköz kiépítésekor a helyi kapacitás méretében határozhat meg. A [virtuális eszköz minimális követelményeinek](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)megfelelően meg kell adni az adatlemez méretét. A helyi gyorsítótár a következő előnyöket biztosítja:
    - A helyi gyorsítótár nagy arányban teszi lehetővé az adatfeldolgozást. Ha a munkaidőben nagy mennyiségű adatot töltenek be, a gyorsítótár az adatok tárolására és a felhőbe való feltöltésére szolgál.
    - A helyi gyorsítótár lehetővé teszi a gyors olvasási hozzáférést egy bizonyos küszöbértékig. Amíg az eszköz 50-60%-ban megtelt, az eszközről beolvasott összes olvasás a gyorsítótárból gyorsabban elérhetővé válik. Miután az eszközön a felhasznált terület túllépi ezt a küszöbértéket, az eszköz elkezdi eltávolítani a helyi fájlokat.
 
- **Korlátozott sávszélesség-használat** – Az Azure-ba akkor is lehet adatot írni, amikor a hálózat használata a csúcsidő során korlátozva van.  

## <a name="key-capabilities"></a>Főbb képességek

A Data Box Gateway az alábbi képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Sebesség     | Teljesen automatizált és nagymértékben optimalizált adatátvitel és sávszélesség.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> További információt a támogatott verziókkal kapcsolatban [a Data Box Gateway rendszerkövetelményeiben](data-box-gateway-system-requirements.md) találhat.|
|Az adatok elérése     | Ha az eszköz által elküldett adatok a felhőben vannak, akkor a Felhőbeli API-k közvetlen elérésével tovább módosítható.|
|Gyors hozzáférés     | Az eszközön helyi gyorsítótár található, amellyel gyorsabban elérhetők a legutóbb használt fájlok.|
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Rugalmasság     | Beépített hálózati rugalmasság        |


## <a name="specifications"></a>Specifikációk

A Data Box Gateway virtuális eszköz az alábbi műszaki adatokkal rendelkezik:

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |
| Memória  |Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek|Operációsrendszer-lemez: 250 GB <br> Adatlemez: legalább 2 TB, dinamikusan kiosztott és SSD-másolatokkal kiegészített|
| Hálózati adapterek |1 vagy több virtuális hálózati adapter|
| Natív fájlmegosztási protokollok|SMB és NFS  |
| Biztonság|Hitelesítés az eszköz és az adatok hozzáférésének feloldásához <br> Átvitel alatt álló adatok titkosítása AES-256 bites titkosítás használatával|
| Kezelés|Helyi webes felhasználói felület – kezdeti beállítás, diagnosztika és az eszköz energiagazdálkodása <br> Azure Portal – a Data Box Gateway-eszközök napi szintű felügyelete       |

## <a name="components"></a>Összetevők

A Data Box Gateway megoldás a Data Box Gateway-erőforrásból, a Data Box Gateway-virtuáliseszközből és egy helyi webes felhasználói felületből áll.

- **Data Box Gateway virtuális eszköz** – Egy eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen fut, és lehetővé teszi az adatok Azure-ba történő küldését.
    
- **Data Box Gateway-erőforrás** – Az Azure Portal erőforrása, amely lehetővé teszi a Data Box Gateway-eszközök kezelését egy webes felületről, amelyet eltérő földrajzi helyekről is elérhet. Az Data Box Gateway erőforrás segítségével megtekintheti és kezelheti az eszközöket, a megosztásokat, a felhasználókat és a riasztásokat. További információ: a [Azure Portal használatának kezelése](data-box-gateway-manage-shares.md).

- **Helyi webes felhasználói felület Data Box** – a helyi webes kezelőfelületen futtathatja a diagnosztikát, leállíthatja és újraindíthatja az eszközt, előállíthat egy támogatási csomagot, vagy megkérheti a Microsoft ügyfélszolgálatat a szolgáltatási kérelmek beküldéséhez. További információ: Hogyan [kezelhetők a helyi webes felhasználói felület használatával](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Régiónkénti elérhetőség

Data Box Gateway eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – az összes olyan régió listájához, ahol a Azure Data Box Gateway erőforrás elérhető, keresse fel az [Azure-termékeket régiónként](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). Data Box Gateway a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](../azure-government/documentation-government-welcome.md)

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők.

    A Data Box adatait tároló tárfiókok régióinaknak az eszköz közelében kell lennie az optimális teljesítmény elérése érdekében. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.


## <a name="next-steps"></a>Következő lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.
