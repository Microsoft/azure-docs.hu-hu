---
title: Kapcsolódás Azure Data Factoryhoz
description: Ez a cikk a Azure Data Factory és az Azure-beli kapcsolat összekapcsolását ismerteti az adatvonal nyomon követéséhez.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/24/2021
ms.openlocfilehash: c9f2a21a1183637ec4648868cccd6f343b003f0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026623"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>A Azure Data Factory és az Azure hatáskörébe való kapcsolódás

Ez a dokumentum ismerteti azokat a lépéseket, amelyek szükségesek ahhoz, hogy egy Azure Data Factory-fiókot egy Azure-fiókkal csatlakoztasson az adatvonal nyomon követéséhez. A dokumentum a lefedettségi hatókör és a támogatott Lineage-minták részleteit is beolvassa.

## <a name="view-existing-data-factory-connections"></a>Meglévő Data Factory kapcsolatok megtekintése

Több Azure-beli adat-előállító is csatlakozhat egyetlen Azure-beli hatáskörébe Data Catalog a leküldéses adatok leküldéséhez. A jelenlegi korlát lehetővé teszi, hogy egyszerre tíz Data Factory fiókot csatlakoztasson a hatáskörébe felügyeleti központból. A hatáskörébe Data Catalog csatlakoztatott Data Factory-fiókok listájának megjelenítéséhez tegye a következőket:

1. Válassza a **felügyeleti központ** lehetőséget a bal oldali navigációs ablaktáblán.
2. A **külső kapcsolatok** területen válassza a **Data Factory kapcsolat** lehetőséget.
3. Megjelenik a Data Factory kapcsolatok listája.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="A screen shot egy adatgyári kapcsolatok listáját jeleníti meg." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Figyelje meg a kapcsolatok **állapotának** különböző értékeit:

    - **Csatlakoztatva**: az adatfeldolgozó csatlakozik a adatkatalógushoz.
    - **Leválasztva**: az adatelőállító hozzáfér a katalógushoz, de egy másik katalógushoz van csatlakoztatva. Ennek eredményeképpen az adatbányászat nem lesz automatikusan jelenteni a katalógusban.
    - **CannotAccess**: az aktuális felhasználónak nincs hozzáférése az adat-előállítóhoz, így a kapcsolat állapota ismeretlen.
 >[!Note]
 >A Data Factory kapcsolatok megtekintéséhez hozzá kell rendelnie a hatáskörébe tartozó szerepkörök egyikét:
 >- Közreműködő
 >- Tulajdonos
 >- Olvasó
 >- Felhasználói hozzáférés rendszergazdája

## <a name="create-new-data-factory-connection"></a>Új Data Factory-kapcsolatok létrehozása

>[!Note]
>A Data Factory kapcsolatok hozzáadásához vagy eltávolításához hozzá kell rendelnie a hatáskörébe tartozó szerepkörök egyikét:
>- Tulajdonos
>- Felhasználói hozzáférés rendszergazdája
>
> Emellett megköveteli, hogy a felhasználók a "tulajdonos" vagy "közreműködő" legyenek. 

Kövesse az alábbi lépéseket egy meglévő Data Factory fióknak a hatáskörébe Data Catalog való összekapcsolásához.

1. Válassza a **felügyeleti központ** lehetőséget a bal oldali navigációs ablaktáblán.
2. A **külső kapcsolatok** területen válassza a **Data Factory kapcsolat** lehetőséget.
3. A **Data Factory-kapcsolatok** lapon válassza az **új** lehetőséget.

4. Válassza ki az Data Factory fiókot a listából, és kattintson az **OK gombra**. A lista korlátozásához az előfizetés neve alapján is szűrhet.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="A Azure Data Factory összekapcsolását bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Előfordulhat, hogy egyes Data Factory példányok le vannak tiltva, ha az adatok előállítója már csatlakoztatva van a jelenlegi hatáskörébe tartozó fiókhoz, vagy az adatok előállítójának nincs felügyelt identitása.

    Figyelmeztető üzenet jelenik meg, ha a kiválasztott adatüzemek bármelyike már csatlakoztatva van egy másik hatáskörébe tartozó fiókhoz. Ha az OK gombra kattint, a másik hatáskörébe tartozó fiókkal létesített Data Factory kapcsolat le lesz választva. További megerősítések nem szükségesek.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="A Azure Data Factory leválasztására vonatkozó figyelmeztetést bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Most már legfeljebb 10 adatgyár hozzáadását támogatjuk egyszerre. Ha egyszerre több mint 10 adatgyárat szeretne felvenni, küldjön egy támogatási jegyet.

### <a name="how-does-the-authentication-work"></a>Hogyan működik a hitelesítés?

Ha a hatáskörébe tartozó felhasználó olyan Data Factory regisztrál, amelyhez hozzáférése van, a következő történik a háttérben:

1. A **Data Factory felügyelt identitás** bekerül a hatáskörébe RBAC szerepkörbe: a **hatáskörébe tartozó adatkurátor**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="A Azure Data Factory MSI-t bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. A Data Factory folyamatot újra kell végrehajtani, hogy a leszármazási metaadatok vissza legyenek küldve a hatáskörébe.
3. A végrehajtás után a Data Factory metaadatokat a rendszer leküldi a hatáskörébe.

### <a name="remove-data-factory-connections"></a>Az adatgyári kapcsolatok eltávolítása
Egy adatfeldolgozó-kapcsolatok eltávolításához tegye a következőket:

1. A **Data Factory kapcsolat** lapon kattintson a **Remove (Eltávolítás** ) gombra egy vagy több adatfeldolgozó-kapcsolat mellett.
2. Válassza a **megerősítés** lehetőséget az előugró ablakban a kiválasztott adatfeldolgozó-kapcsolatok törléséhez.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="A kapcsolatok eltávolítására szolgáló adatüzemek kiválasztását bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-integration-runtime-to-collect-lineage"></a>Saját üzemeltetésű Integration Runtime konfigurálása a Lineage összegyűjtéséhez

A Data Factory másolási tevékenységének a helyi adattárak, például SQL-adatbázisok számára elérhető. Ha saját üzemeltetésű integrációs modult futtat az adatáthelyezéshez Azure Data Factory és az Azure-beli hatáskörébe kívánja rögzíteni a Lineage-t, győződjön meg arról, hogy a verzió 5,0-es vagy újabb verziójú. További információ a saját üzemeltetésű integrációs modulról: [saját üzemeltetésű integrációs modul létrehozása és konfigurálása](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Támogatott Azure Data Factory tevékenységek

Az Azure hatáskörébe a következő Azure Data Factory tevékenységekből származó futtatókörnyezetet rögzíti:

- [Adatok másolása](../data-factory/copy-activity-overview.md)
- [Adatfolyam](../data-factory/concepts-data-flow-overview.md)
- [SSIS-csomag végrehajtása](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> Ha a forrás vagy a cél nem támogatott adattárolási rendszer használatát használja, az Azure hatáskörébe csökken.

A Data Factory és a hatáskörébe való integráció a következő szakaszokban leírtak szerint csak a Data Factory által támogatott adatrendszerek egy részhalmazát támogatja.

### <a name="data-factory-copy-activity-support"></a>Data Factory másolási tevékenység támogatása

| Adattár | Támogatott | 
| ------------------- | ------------------- | 
| Azure Blob Storage | Igen |
| Azure Cognitive Search | Yes | 
| Azure Cosmos DB (SQL API) \* | Yes | 
| Azure Cosmos DB API-MongoDB \* | Yes |
| Azure-Adatkezelő \* | Igen | 
| Azure Data Lake Storage Gen1 | Igen | 
| Azure Data Lake Storage Gen2 | Igen | 
| Azure Database for Maria DB \* | Yes | 
| Azure Database for MySQL \* | Yes | 
| Azure Database for PostgreSQL \* | Yes |
| Azure File Storage | Yes | 
| Azure SQL Database \* | Yes | 
| Felügyelt Azure SQL-példány \* | Yes | 
| Azure szinapszis-elemzés \* | Igen | 
| Azure Table Storage | Igen |
| Amazon S3 | Yes | 
| Kaptár \* | Yes | 
| SAP ECC \* | Yes |
| SAP-tábla | Yes |
| SQL Server \* | Yes | 
| Teradata \* | Yes |

*\* Az Azure hatáskörébe jelenleg nem támogatott a lekérdezés vagy a tárolt eljárás a leszármazás vagy a vizsgálat során. A Lineage csak a táblákra és a források megtekintésére korlátozódik.*

> [!Note]
> A Lineage funkció Data Factory másolási tevékenységben bizonyos teljesítménybeli terheléssel rendelkezik. Azok számára, akik az adat-előállítói kapcsolatokat a hatáskörébe tartoznak, előfordulhat, hogy bizonyos másolási feladatok végrehajtása tovább tart. A hatás többnyire nem elhanyagolható. Ha a másolási feladatok a szokásosnál hosszabb ideig tartanak, lépjen kapcsolatba az ügyfélszolgálattal.

#### <a name="known-limitations-on-copy-activity-lineage"></a>A másolási tevékenység leszármazásának ismert korlátai

Jelenleg a következő másolási tevékenység-funkciók használata esetén a Lineage még nem támogatott:

- Az Adatmásolás Azure Data Lake Storage Gen1 bináris formátum használatával.
- Adatok másolása az Azure szinapszis Analyticsbe a Base vagy a COPY utasítás használatával.
- Tömörítési beállítás bináris, tagolt szöveg-, Excel-, JSON-és XML-fájlokhoz.
- A Azure SQL Database, az Azure SQL felügyelt példányának, az Azure szinapszis Analytics, a SQL Server és az SAP-táblázatnak a forrás partíciós beállításai.
- A forrásfájl-felderítési beállítás a fájl alapú tárolók esetében.
- Adatmásolás fájl alapú fogadóba fájlok maximális sorainak beállításával.
- További oszlopok hozzáadása a másolás során.

Az adategység sémája (az eszköz – > séma lapon) a következő összekötők esetében jelenik meg:

- CSV-és parketta-fájlok az Azure blobban, Azure File Storage, ADLS Gen1, ADLS Gen2 és Amazon S3
- Azure Adatkezelő, Azure SQL Database, Azure SQL felügyelt példány, Azure szinapszis Analytics, SQL Server, Teradata

### <a name="data-factory-data-flow-support"></a>Adatfolyam-támogatás Data Factory

| Adattár | Támogatott |
| ------------------- | ------------------- | 
| Azure Blob Storage | Igen |
| Azure Data Lake Storage Gen1 | Igen |
| Azure Data Lake Storage Gen2 | Igen |
| Azure SQL Database \* | Yes |
| Azure szinapszis-elemzés \* | Yes |

*\* Az Azure hatáskörébe jelenleg nem támogatott a lekérdezés vagy a tárolt eljárás a leszármazás vagy a vizsgálat során. A Lineage csak a táblákra és a források megtekintésére korlátozódik.*

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory SSIS-csomag támogatásának végrehajtása

| Adattár | Támogatott |
| ------------------- | ------------------- |
| Azure Blob Storage | Igen |
| Azure Data Lake Storage Gen1 | Igen |
| Azure Data Lake Storage Gen2 | Igen |
| Azure File Storage | Yes |
| Azure SQL Database \* | Yes |
| Felügyelt Azure SQL-példány \*| Yes |
| Azure szinapszis-elemzés \* | Yes |
| SQL Server \* | Yes |

*\* Az Azure hatáskörébe jelenleg nem támogatott a lekérdezés vagy a tárolt eljárás a leszármazás vagy a vizsgálat során. A Lineage csak a táblákra és a források megtekintésére korlátozódik.*

> [!Note]
> Az Azure Data Lake Storage Gen2 mostantól általánosan elérhető. Javasoljuk, hogy már ma kezdje el használni. További információkért tekintse meg a [termék oldalát](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Támogatott Lineage-minták

Az Azure-beli hatáskörébe több különböző kifejlődési minta is van. A generált Lineage-információ a Data Factory tevékenységekben használt forrás-és fogadó típusán alapul. Bár a Data Factory támogatja a több mint 80 forrást és a nyelőket, az Azure hatáskörébe csak egy részhalmazt támogat a [támogatott Azure Data Factory tevékenységekben](#supported-azure-data-factory-activities)felsoroltak szerint.

A Data Factory beállítása a Lineage-adatok küldéséhez: [Bevezetés a Lineage használatába](catalog-lineage-user-guide.md#get-started-with-lineage).

A leszármazási nézetben további információk is megtalálhatók, például a következő módon:

- A **Lineage** (kivonás) lapon vigye az alakzatokra, és tekintse meg az eszközre vonatkozó további információkat az elemleírásban.
- Válassza ki a csomópontot vagy a szegélyt, hogy megjelenjen az eszköz típusa, vagy az eszközök átváltása.
- Az adatkészlet oszlopai a **Lineage** lap bal oldalán jelennek meg. További információ az oszlop szintű leszármazásról: [adatkészlet oszlopának Lineage](catalog-lineage-user-guide.md#dataset-column-lineage).

### <a name="data-lineage-for-11-operations"></a>Adatbányászat 1:1 műveletekhez

Az adatvonal rögzítésének leggyakoribb mintája, hogy egyetlen bemeneti adatkészletből egyetlen kimeneti adatkészletbe helyezi át az adatokat, és a között egy folyamat van.

A minta példája a következő:

- 1 forrás/bemenet: *ügyfél* (SQL-tábla)
- 1 fogadó/kimenet: *Customer1.csv* (Azure Blob)
- 1 folyamat: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory másolási tevékenység)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Képernyőfelvétel: egy Data Factory másolási műveletének leszármazása." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Adatáthelyezés az 1:1 Lineage és a helyettesítő karakterek támogatásával

Egy másik gyakori forgatókönyv a Lineage rögzítéséhez, és egy helyettesítő karaktert használ a fájlok egyetlen bemeneti adatkészletből egyetlen kimeneti adatkészletbe való másolásához. A helyettesítő karakter lehetővé teszi, hogy a másolási tevékenység több fájlt is megegyezzen a fájl nevének közös részének használatával történő másoláshoz. Az Azure-beli hatáskörébe a megfelelő másolási tevékenység által másolt egyes fájlok esetében a fájl szintű vonal rögzítése.

A minta példája a következő:

* Forrás/bemenet: *CustomerCall \* . csv* (ADLS Gen2 útvonal)
* Fogadó/kimenet: *CustomerCall \* . csv* (Azure Blob-fájl)
* 1 folyamat: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory másolási tevékenység)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Képernyőfelvétel: egy-egy másolási művelet, helyettesítő karakteres támogatással." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Adatáthelyezés a n:1 Lineage szolgáltatással

Az adatfolyamatok tevékenységeit olyan adatműveletek elvégzésére használhatja, mint például az egyesítés, a csatlakozás stb. A cél adatkészletek létrehozásához több forrás adathalmaz is használható. Ebben a példában az Azure hatáskörébe az egyes bemeneti fájlokhoz tartozó, adatáramlási tevékenység részét képező SQL-táblára rögzíti a fájl szintű rendszervonalat.

A minta példája a következő:

* 2 forrás/bemenet: *Customer.csv*, *Sales. parquet* (ADLS Gen2 Path)
* 1 fogadó/kimenet: *vállalati adatokat* (Azure SQL-tábla)
* 1 folyamat: *DataFlowBlobsToSQL* (Data Factory adatfolyam-tevékenység)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Képernyőfelvétel: egy n és egy D F adatfolyam-művelet leszármazását ábrázoló kép." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Erőforrás-készletek Lineage

Az erőforrás-készlet egy olyan logikai objektum a katalógusban, amely a mögöttes tárolóban számos partíciós fájlt jelöl. További információ: az [erőforrás-készletek ismertetése](concept-resource-sets.md). Amikor az Azure hatáskörébe rögzíti a Azure Data Factory, a szabályok alapján normalizálja az egyes partíciós fájlokat, és egyetlen logikai objektumot hoz létre.

A következő példában egy Azure Data Lake Gen2-erőforrást állítunk elő egy Azure-Blobból:

* 1 forrás/bemenet: *alkalmazott \_management.csv* (Azure Blob)
* 1 fogadó/kimenet: *alkalmazott \_management.csv* (Azure Data Lake Gen 2)
* 1 folyamat: *CopyBlobToAdlsGen2 \_ RS* (Data Factory másolási tevékenység)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Az erőforrás-készlet kifejlődését ábrázoló képernyőkép." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Következő lépések

- [A katalógus Lineage felhasználói útmutatója](catalog-lineage-user-guide.md)
- [Hivatkozás az Azure-beli adatmegosztásra a Lineage számára](how-to-link-azure-data-share.md)
