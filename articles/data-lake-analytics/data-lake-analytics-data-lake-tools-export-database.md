---
title: U-SQL-adatbázis exportálása – Azure Data Lake eszközök a Visual studióhoz
description: Ismerje meg, hogyan exportálhat egy U-SQL-adatbázist a Visual studióhoz készült Azure Data Lake Tools használatával, és hogyan importálhatja automatikusan egy helyi fiókba.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219088"
---
# <a name="export-a-u-sql-database"></a>U-SQL-adatbázis exportálása

Ebből a cikkből megtudhatja, hogyan használhatja a [Visual studióhoz készült Azure Data Lake Tools eszközt](https://aka.ms/adltoolsvs) egy u-SQL-adatbázis egyetlen u-SQL-parancsfájlként való exportálásához és a letöltött erőforrásokhoz. Az exportált adatbázist egy helyi fiókba is importálhatja ugyanabban a folyamatban.

Az ügyfelek általában több környezetet tartanak fenn fejlesztési, tesztelési és éles környezetekben. Ezek a környezetek helyi fiókon, fejlesztői helyi számítógépen, valamint az Azure Azure Data Lake Analytics-fiókjában is futnak. 

A fejlesztési és tesztelési környezetekben a U-SQL-lekérdezések fejlesztésekor és finomhangolásakor a fejlesztőknek gyakran újra létre kell hozniuk a munkájukat egy éles adatbázisban. Az adatbázis-Exportálás varázsló segít felgyorsítani ezt a folyamatot. A varázsló segítségével a fejlesztők a meglévő adatbázis-környezetet és más Data Lake Analytics-fiókokat is megadhatják.

## <a name="export-steps"></a>Exportálási lépések

### <a name="step-1-export-the-database-in-server-explorer"></a>1. lépés: az adatbázis exportálása a Server Explorerben

Az összes olyan Data Lake Analytics fiók, amelyhez jogosultsága van, megjelenik a Server Explorerben. Az adatbázis exportálása:

1. A Server Explorerben bontsa ki az exportálni kívánt adatbázist tartalmazó fiókot.
2. Kattintson a jobb gombbal az adatbázisra, majd válassza az **Exportálás** lehetőséget. 
   
    ![Server Explorer – adatbázis exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Ha az **exportálási** menü nem érhető el, [frissítenie kell az eszközt az utolsó kiadásra](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>2. lépés: az exportálni kívánt objektumok konfigurálása

Ha csak kis méretű adatbázisra van szüksége, az exportálás varázslóban az exportálni kívánt objektumok egy részhalmazát is konfigurálhatja. 

Az exportálási művelet a U-SQL-feladatok futtatásával fejeződik be. Ezért az Azure-fiókból való exportálás némi költségekkel jár.

![Adatbázis-exportálási varázsló – objektumok exportálásának kiválasztása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>3. lépés: az objektumok listájának és más konfigurációknak a megtekintése

Ebben a lépésben ellenőrizheti a kijelölt objektumokat az **objektum exportálása listában** . Ha bármilyen hiba merül fel, az **előző** gombra kattintva visszatérhet, és helyesen állíthatja be az exportálni kívánt objektumokat.

Az exportálási cél egyéb beállításait is megadhatja. A konfigurációs leírások az alábbi táblázatban láthatók:

|Konfiguráció|Leírás|
|-------------|-----------|
|Cél neve|Ez a név jelzi, hogy hová szeretné menteni az exportált adatbázis erőforrásait. Ilyenek például a szerelvények, a további fájlok és a mintaadatok. A rendszer létrehoz egy ilyen nevű mappát a helyi adatgyökér mappában.|
|Projekt könyvtára|Ez az elérési út határozza meg, hogy hová szeretné menteni az exportált U-SQL-szkriptet. Ezen a helyen minden adatbázis-objektum definíciója mentve lesz.|
|Csak séma|Ha ezt a beállítást választja, a rendszer csak az adatbázis-definíciókat és erőforrásokat (például szerelvényeket és további fájlokat) exportálja.|
|Séma és adatok|Ha ezt a beállítást választja, a rendszer az adatbázis-definíciókat, erőforrásokat és az adatforrásokat exportálja. A táblázat első N sorát exportálja a rendszer.|
|Importálás a helyi adatbázisba automatikusan|Ha ezt a beállítást választja, az Exportálás befejezése után a rendszer automatikusan importálja az exportált adatbázist a helyi adatbázisba.|

![Adatbázis-exportálás varázsló – objektumok listájának exportálása és egyéb konfigurációk](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4. lépés: az Exportálás eredményeinek megtekintése

Ha elkészült az exportálással, az exportált eredményeket a varázsló napló ablakában tekintheti meg. Az alábbi példa bemutatja, hogyan találhatja meg az exportált U-SQL-parancsfájlokat és az adatbázis-erőforrásokat, beleértve a szerelvényeket, a további fájlokat és a mintaadatok:

![Adatbázis-exportálási varázsló – eredmények exportálása](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Az exportált adatbázis importálása helyi fiókba

Az exportált adatbázis importálásának legalkalmasabb módja az Exportálás **helyi adatbázisba automatikus** jelölőnégyzet bejelölése a 3. lépésben szereplő exportálási folyamat során. Ha nem jelölte be ezt a jelölőnégyzetet, először keresse meg az exportált U-SQL-parancsfájlt az exportálási naplóban. Ezután futtassa helyileg az U-SQL-szkriptet az adatbázis helyi fiókba történő importálásához.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Az exportált adatbázis importálása Data Lake Analytics-fiókba

Az adatbázis importálása a különböző Data Lake Analytics fiókba:

1. Töltse fel az exportált erőforrásokat, beleértve a szerelvényeket, a további fájlokat és a mintaadatok a Data Lake Analytics fiók alapértelmezett Azure Data Lake Store fiókjában, amelyet importálni szeretne. Az exportált erőforrás mappát a helyi adatgyökér mappában találja. Töltse fel a teljes mappát az alapértelmezett Data Lake Store fiók gyökerébe.
2. A feltöltés befejezésekor küldje el az exportált U-SQL-parancsfájlt arra a Data Lake Analytics-fiókra, amelyre importálni kívánja az adatbázist.

## <a name="known-limitations"></a>Ismert korlátozások

Ha a 3. lépésben a **séma és az adatok** lehetőséget választja, az eszköz egy U-SQL-feladatot futtat a táblákban tárolt adatok exportálásához. Emiatt az adatexportálási folyamat lassú lehet, és felmerülhet a költségek. 

## <a name="next-steps"></a>Következő lépések

* [Az U-SQL-adatbázisok megismerése](/u-sql/data-definition-language-ddl-statements) 
* [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


