---
title: Kapcsolódás Azure Data Factoryhoz
description: Ez a cikk a Azure Data Factory és az Azure-beli kapcsolat összekapcsolását ismerteti az adatvonal nyomon követéséhez.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 0e993cb1e53645f7081a20fc6a2785b8cfef1cce
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954182"
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

1. A **Data Factory MSI** bekerül a hatáskörébe RBAC szerepkörbe: a **hatáskörébe tartozó adat-kurátor**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="A Azure Data Factory MSI-t bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. A Data Factory folyamatot újra kell végrehajtani, hogy a leszármazási metaadatok vissza legyenek küldve a hatáskörébe.
3. A végrehajtás után a Data Factory metaadatokat a rendszer leküldi a hatáskörébe.

### <a name="remove-data-factory-connections"></a>Az adatgyári kapcsolatok eltávolítása
Egy adatfeldolgozó-kapcsolatok eltávolításához tegye a következőket:

1. A **Data Factory kapcsolat** lapon kattintson a **Remove (Eltávolítás** ) gombra egy vagy több adatfeldolgozó-kapcsolat mellett.
2. Válassza a **megerősítés** lehetőséget az előugró ablakban a kiválasztott adatfeldolgozó-kapcsolatok törléséhez.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="A kapcsolatok eltávolítására szolgáló adatüzemek kiválasztását bemutató képernyőkép." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Saját üzemeltetésű integrációs modul konfigurálása a Lineage SQL-ből való adatgyűjtéshez

A Data Factory másolási tevékenységhez a helyi SQL-adatbázisok esetében van lehetőség. Ha saját üzemeltetésű integrációs modult futtat az adatátvitelhez Azure Data Factory és az Azure-beli hatáskörébe szeretné rögzíteni a Lineage-t, győződjön meg róla, hogy a verzió 4.8.7418.1 vagy újabb. További információ a saját üzemeltetésű integrációs modulról: [saját üzemeltetésű integrációs modul létrehozása és konfigurálása](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Támogatott Azure Data Factory tevékenységek

Az Azure hatáskörébe a következő Azure Data Factory tevékenységekből származó futtatókörnyezetet rögzíti:

- Adatok másolása
- Adatfolyam
- SSIS-csomag végrehajtása

> [!IMPORTANT]
> Ha a forrás vagy a cél nem támogatott adattárolási rendszer használatát használja, az Azure hatáskörébe csökken.

A Data Factory és a hatáskörébe való integráció a következő szakaszokban leírtak szerint csak a Data Factory által támogatott adatrendszerek egy részhalmazát támogatja.

### <a name="data-factory-copy-data-support"></a>Data Factory Adatok másolása-támogatás

| Adattárolási rendszerek | Forrásként támogatott | Fogadóként támogatott |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Yes | Igen (csak nem bináris másolat) |
| ADLS Gen2 | Igen | Yes |
| Azure-blob | Igen | Yes |
| Azure Cosmos DB (SQL API) | Igen | Yes |
| Azure Cosmos DB (Mongo API) | Igen | Yes |
| Azure Cognitive Search | Igen | Yes |
| Azure Data Explorer | Igen | Yes |
| Azure Database for Maria DB \* | Igen | Yes |
| MYSQL-hez készült Azure-adatbázis \* | Igen | Yes |
| Azure Database for PostgreSQL \* | Igen | Yes |
| Azure File Storage | Igen | Igen |
| Azure Table Storage | Igen | Yes |
| Azure SQL Database \* | Igen | Yes |
| Azure SQL MI \* | Igen | Yes |
| Azure szinapszis Analytics (korábban SQL DW) \* | Igen | Yes |
| SQL Server helyszíni (szükséges) \* | Igen | Yes |
| Amazon S3 | Igen | Igen |
| Teradata | Igen | Yes |
| SAP S4 Hana | Igen | Yes |
| SAP ECC | Igen | Yes |
| Hive | Igen | Yes |

> [!Note]
> A Lineage funkció Data Factory másolási tevékenységben bizonyos teljesítménybeli terheléssel rendelkezik. Azok számára, akik az adat-előállítói kapcsolatokat a hatáskörébe tartoznak, előfordulhat, hogy bizonyos másolási feladatok végrehajtása tovább tart. A hatás többnyire nem elhanyagolható. Ha a másolási feladatok a szokásosnál hosszabb ideig tartanak, lépjen kapcsolatba az ügyfélszolgálattal.

### <a name="data-factory-data-flow-support"></a>Adatfolyam-támogatás Data Factory

| Adattárolási rendszerek | Támogatott |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Yes |
| ADLS Gen2 | Yes |
| Azure-blob | Yes |
| Azure SQL Database \* | Yes |
| Azure szinapszis Analytics (korábban SQL DW) \* | Yes |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory SSIS-csomag támogatásának végrehajtása

| Adattárolási rendszerek | Támogatott |
| ------------------- | ------------------- | ----------------- |
| Azure-blob | Yes |
| ADLS Gen1 | Yes |
| ADLS Gen2 | Yes |
| Azure SQL Database \* | Yes |
| Azure SQL MI \*| Yes |
| Azure szinapszis Analytics (korábban SQL DW) \* | Yes |
| SQL Server helyszíni \* | Yes |
| Azure File Storage | Yes |

*\* Az SQL (Azure és a helyszíni) forgatókönyvek esetében az Azure-beli környezet nem támogatja a tárolt eljárásokat vagy parancsfájlokat a leszármazás vagy a vizsgálat során. A Lineage csak a táblákra és a források megtekintésére korlátozódik.*

> [!Note]
> Az Azure Data Lake Storage Gen2 mostantól általánosan elérhető. Javasoljuk, hogy már ma kezdje el használni. További információkért tekintse meg a [termék oldalát](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Támogatott Lineage-minták

Az Azure-beli hatáskörébe több különböző kifejlődési minta is van. A generált Lineage-információ a Data Factory tevékenységekben használt forrás-és fogadó típusán alapul. Bár a Data Factory támogatja a több mint 80 forrást és a nyelőket, az Azure hatáskörébe csak egy részhalmazt támogat a [támogatott Azure Data Factory tevékenységekben](#supported-azure-data-factory-activities)felsoroltak szerint.

A Data Factory beállítása a Lineage-adatok küldéséhez: [Bevezetés a Lineage használatába](catalog-lineage-user-guide.md#get-started-with-lineage).

A leszármazási nézetben további információk is megtalálhatók, például a következő módon:

- A **Lineage** (kivonás) lapon vigye az alakzatokra, és tekintse meg az eszközre vonatkozó további információkat az elemleírásban.
- Válassza ki a csomópontot vagy a szegélyt, hogy megjelenjen az eszköz típusa, vagy az eszközök átváltása.
- Az adatkészlet oszlopai a **Lineage** lap bal oldalán jelennek meg. További információ az oszlop szintű leszármazásról: [oszlop szintű Lineage](catalog-lineage-user-guide.md#column-level-lineage).

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
