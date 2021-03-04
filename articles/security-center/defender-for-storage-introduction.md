---
title: Azure Defender for Storage – az előnyök és szolgáltatások
description: Ismerje meg az Azure Defender for Storage szolgáltatás előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42aa07ccf8d886dc7eb7109bc405c730331b2c3b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095629"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Az Azure Defender tároláshoz bemutatása

Az **Azure Defender for Storage** egy olyan Azure-alapú biztonsági intelligencia, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kihasználásához. A biztonsági AI és a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) speciális képességeinek kihasználásával környezetfüggő biztonsági riasztásokat és ajánlásokat biztosít.

A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a riasztások a Azure Security Centerba vannak integrálva, és e-mailben is elküldhető az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos javaslatokkal kapcsolatban.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A Storage-hoz készült **Azure Defender** számlázása [Security Center díjszabás](https://azure.microsoft.com/pricing/details/security-center/) szerint történik|
|Védett tárolási típusok:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) US Gov<br>![Nem](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Milyen előnyökkel jár az Azure Defender a Storage szolgáltatáshoz?

Az Azure Defender for Storage a következőket biztosítja:

- **Azure-natív biztonság** – az Azure-Blob, a Azure Files és az adattavakban tárolt adatvédelmet biztosító Defender for Storage védelmet nyújt. Az Azure-natív szolgáltatásként a Defender a Storage számára központosított biztonságot nyújt az összes Azure által kezelt adategységben, és integrálva van más Azure-beli biztonsági szolgáltatásokkal, például az Azure Sentinelrel.
- A Microsoft veszélyforrások felderítése által működtetett, **gazdag észlelési csomag** , amely a Defender for Storage szolgáltatás észleléseit fedi le, például a névtelen hozzáférést, a feltört hitelesítő adatokat, a közösségi mérnöki környezetet, a jogosultsági visszaéléseket és a rosszindulatú tartalmakat.
- A Scale-Security Center Automation-eszközeire **adott válasz** megkönnyíti az azonosított fenyegetések megelőzését és reagálását. További információ: [Security Center triggerekre adott válaszok automatizálása](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Az Azure Defender for Storage funkcióinak magas szintű áttekintése":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Milyen típusú riasztások biztosítják az Azure Defender for Storage szolgáltatást?

A biztonsági riasztások akkor aktiválódnak, ha:

- **Gyanús hozzáférési minták** – például sikeres hozzáférés egy Tor-kilépési csomópontból vagy egy olyan IP-címről, amely a Microsoft Threat Intelligence által gyanúsnak minősül
- **Gyanús tevékenységek** – például rendellenes Adatkiemelés vagy a hozzáférési engedélyek szokatlan módosítása
- **Rosszindulatú tartalom feltöltése** – például a lehetséges kártevő fájlok (a kivonatok elemzése alapján) vagy az adathalászat-tartalom üzemeltetése

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. A riasztások exportálhatók az Azure Sentinelbe vagy bármely más harmadik féltől származó SIEM vagy más külső eszközre.

> [!TIP]
> Javasoljuk, hogy az [Azure Defender for Storage](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) szolgáltatást az előfizetés szintjén konfigurálja, de az [Egyéni Storage-fiókok esetében is konfigurálható](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Mi a kivonatoló hírnevének elemzése kártevők számára?

Annak megállapításához, hogy a feltöltött fájl gyanús-e, az Azure Defender for Storage a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)által támogatott kivonat-elemzést használ. A veszélyforrások elleni védelem eszközei nem ellenőrzik a feltöltött fájlokat, hanem megvizsgálják a tárolási naplókat, és összehasonlítják az újonnan feltöltött fájlok kivonatait az ismert vírusok, trójaiak, kémprogramok és ransomware esetében. 

Ha egy fájl gyanúja szerint kártevőt tartalmaz, Security Center riasztást jelenít meg, és opcionálisan e-mailben is elküldheti a tároló tulajdonosának jóváhagyását a gyanús fájl törléséhez. Ha be szeretné állítani a fájlok automatikus eltávolítását, amely alapján a kivonatoló hírnevének elemzése a kártevőket tartalmazza, a Munkafolyamat-automatizálás üzembe helyezésével [aktiválhatja azokat a riasztásokat, amelyek "a Storage-fiókba feltöltött lehetséges kártevőket" tartalmaznak](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Az Security Center veszélyforrások elleni védelmi képességeinek engedélyezéséhez engedélyeznie kell az Azure Defender számára a megfelelő munkaterheléseket tartalmazó előfizetést.
>
> Az **Azure Defender a Storage szolgáltatáshoz** az előfizetés szintjén vagy az erőforrás szintjén is engedélyezhető.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Tesztelési riasztás elindítása az Azure Defender számára a Storage szolgáltatáshoz

Az Azure Defender által a környezetében tárolt biztonsági riasztások teszteléséhez a riasztás "hozzáférés egy Tor-kilépési csomópontból egy Storage-fiókba" a következő lépésekkel végezhető el:

1. Nyisson meg egy Storage-fiókot, amelyen engedélyezve van az Azure Defender.
1. Az oldalsávon válassza a "tárolók" lehetőséget, és nyisson meg egy meglévő tárolót, vagy hozzon létre újat.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="BLOB-tároló megnyitása egy Azure Storage-fiókból" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Töltsön fel egy fájlt a tárolóba.

    > [!CAUTION]
    > Ne töltsön fel bizalmas adatokat tartalmazó fájlt.

1. A feltöltött fájl helyi menüjéből válassza az "SAS-létrehozás" lehetőséget.

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Az SAS-beállítás létrehozása blob-tárolóban található fájlhoz":::

1. Hagyja meg az alapértelmezett beállításokat, és válassza az **sas-jogkivonat és URL-cím előállítása** lehetőséget.

1. Másolja a generált SAS URL-címet.

1. A helyi gépen nyissa meg a Tor böngészőt.

    > [!TIP]
    > A Tor-projekt webhelyéről letöltheti a Tor-t [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. A Tor-böngészőben navigáljon a SAS URL-címére.

1. Töltse le a 3. lépésben feltöltött fájlt.

    Két órán belül a következő biztonsági riasztást kapja Security Center:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Biztonsági riasztás a Tor kilépési csomópontról való hozzáférésre vonatkozóan":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az Azure Defender for Storage szolgáltatással kapcsolatos tudnivalókat.

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.
- [A speciális Defender engedélyezése a Storage-ban](../storage/common/azure-defender-storage-configure.md)
- [Az Azure Defender tárolási riasztások listája](alerts-reference.md#alerts-azurestorage)
- [A Microsoft fenyegetés-felderítési képességei](https://go.microsoft.com/fwlink/?linkid=2128684)