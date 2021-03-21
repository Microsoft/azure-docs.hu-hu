---
title: Az Azure szinapszis Analyticsbe való betöltési terabájt
description: Azt mutatja be, hogy az Azure szinapszis Analytics szolgáltatásban 1 TB-nyi adat hogyan tölthető le 15 percen belül Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5acae7c90efbf178fad199177fa6e0886e497fdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371209"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Töltsön be 1 TB-ot az Azure szinapszis Analytics szolgáltatásba 15 perc alatt Data Factory
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az adatok másolása az Azure szinapszis Analytics szolgáltatásba vagy az Data Factory használatával](../connector-azure-sql-data-warehouse.md)című témakört.


Az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővíthető adatbázis, amely nagy mennyiségű, a kapcsolatok és a nem kapcsolatok kezelésére képes.  A nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő Azure szinapszis Analytics a vállalati adattárház számítási feladataihoz van optimalizálva.  A felhő rugalmasságának köszönhetően rugalmasan méretezheti a tárolást és a számításokat egymástól függetlenül.

Az Azure szinapszis Analytics használatának első lépései mostantól minden eddiginél könnyebben **Azure Data Factory**.  A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely az Azure szinapszis Analytics és a meglévő rendszer adatainak feltöltésére használható, és értékes időt takaríthat meg az Azure szinapszis Analytics kiértékelése és az elemzési megoldások kiépítése során. Az alábbi főbb előnyökkel jár az Azure szinapszis Analytics szolgáltatásba való betöltés a Azure Data Factory használatával:

* **Könnyen beállítható**: 5 lépésből álló intuitív varázsló, amely nem igényel parancsfájlt.
* **Gazdag adattár-támogatás**: beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez.
* **Biztonságos és megfelelő**: az adatátvitel HTTPS-vagy ExpressRoute keresztül történik, és a globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt
* **Páratlan teljesítmény a Base használatával** – a Base használatával a leghatékonyabb módszer az adatok áthelyezése az Azure szinapszis analyticsbe. Az átmeneti blob funkcióval nagy terhelési sebességet érhet el az Azure Blob Storage melletti összes adattárolóból, amelyet alapértelmezés szerint a Base támogat.

Ebből a cikkből megtudhatja, hogyan használhatók a Data Factory másolási varázslóval az Azure-Blob Storage 1 TB-os adatok betöltésére az Azure szinapszis Analytics szolgáltatásba 15 percen belül, több mint 1,2 GB/s sebességnél.

Ez a cikk részletes útmutatást nyújt az adatáthelyezéshez az Azure szinapszis Analytics szolgáltatásba a másolás varázslóval.

> [!NOTE]
>  Az adatok Azure szinapszis Analytics szolgáltatásba való áthelyezésének Data Factory képességeivel kapcsolatos általános információkért lásd: [adatok áthelyezése az Azure szinapszis Analytics szolgáltatásba és onnan az Azure Data Factory cikk használatával](data-factory-azure-sql-data-warehouse-connector.md) .
>
> A Visual Studióval, a PowerShell-lel és más szolgáltatásokkal is készíthet folyamatokat. Tekintse meg az [oktatóanyag: adatok másolása az Azure blobból Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy gyors útmutatóhoz, amely részletes útmutatást nyújt a másolási tevékenység Azure Data Factory-ban való használatához.  
>
>

## <a name="prerequisites"></a>Előfeltételek
* Azure Blob Storage: Ez a kísérlet az Azure Blob Storaget (GRS) használja a TPC-H tesztelési adatkészlet tárolásához.  Ha nem rendelkezik Azure Storage-fiókkal, Ismerje meg, [hogyan hozhat létre egy Storage](../../storage/common/storage-account-create.md)-fiókot.
* [TPC-h-](http://www.tpc.org/tpch/) adat: a TPC-h-t a tesztelési adatkészletként fogjuk használni.  Ehhez a `dbgen` TPC-H eszközkészletből kell használnia, amely segít az adatkészlet létrehozásában.  Le is töltheti a forráskódot a `dbgen` [TPC-eszközökről](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) , és lefordíthatja saját magát, vagy letöltheti a lefordított bináris fájlt a [githubról](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Futtassa a dbgen.exe a következő parancsokkal 1 TB-os Flat-fájl létrehozásához a `lineitem` táblázatos elosztott fájlok között 10 fájlban:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Most másolja a generált fájlokat az Azure-Blobba.  Tekintse át az adatok átvitele [a helyszíni fájlrendszerbe és a rendszerből a Azure Data Factory használatával](data-factory-onprem-file-system-connector.md) című témakört, amely az ADF-másolás használatával végezhető el.    
* Azure szinapszis Analytics: Ez a kísérlet a 6 000 DWU-mel létrehozott Azure szinapszis Analyticsbe tölti be az adatgyűjtést.

    Az Azure szinapszis Analytics-adatbázis létrehozásával kapcsolatos részletes utasításokért tekintse meg az [Azure szinapszis Analytics létrehozása](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) című témakört.  Annak érdekében, hogy a lehető leghatékonyabban lehessen betölteni az Azure szinapszis Analytics-elemzést a Base használatával, a teljesítmény beállításban engedélyezett maximális számú adatraktár-egység (DWU) közül választhat, amely 6 000 DWU.

  > [!NOTE]
  > Az Azure Blobból való betöltéskor az adatok betöltésének teljesítménye közvetlenül az Azure szinapszis Analytics szolgáltatáshoz konfigurált DWU számával arányos:
  >
  > Az 1 TB betöltése a 1 000-es DWU az Azure szinapszis Analytics 87 perc (~ 200 MBps átviteli sebesség) értékre vált az 1 TB-ot a 2 000 DWU Azure szinapszis 46 Analytics szolgáltatásba
  >
  >

    Ha a 6 000 DWU rendelkező dedikált SQL-készletet szeretne létrehozni, helyezze át a teljesítmény csúszkát jobbra:

    ![Teljesítmény csúszka](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Egy olyan meglévő adatbázis esetében, amely nem 6 000 DWU van konfigurálva, Azure Portal használatával méretezheti fel.  Navigáljon a Azure Portal található adatbázishoz, és van egy **méretezési** gomb az **Áttekintés** panelen az alábbi képen látható módon:

    ![Méretezés gomb](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kattintson a **skála** gombra a következő panel megnyitásához, mozgassa a csúszkát a maximális értékre, majd kattintson a **Save (Mentés** ) gombra.

    ![Méretezési párbeszédpanel](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ez a kísérlet az Azure szinapszis Analyticsbe az erőforrás-osztály használatával tölti be az adatgyűjtést `xlargerc` .

    A lehető legjobb átviteli sebesség eléréséhez a másolást az erőforrás osztályhoz tartozó Azure szinapszis Analytics-felhasználó használatával kell elvégezni `xlargerc` .  Ebből a témakörből megtudhatja, hogyan teheti ezt meg a [felhasználói erőforrás osztályra vonatkozó példa módosítása](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)után.  
* Hozzon létre egy céltáblabeli sémát az Azure szinapszis Analytics-adatbázisban a következő DDL-utasítás futtatásával:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Az előfeltételként szükséges lépések elvégzésével most már készen áll a másolási tevékenység konfigurálására a másolás varázslóval.

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. Kattintson az **erőforrás létrehozása** elemre a bal felső sarokban, kattintson az **intelligencia és Analitika** elemre, majd a **Data Factory** elemre.
3. Az **új adatgyár** panelen:

   1. Adja  meg a LoadIntoSQLDWDataFactory **nevet**.
       Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja: a **"LoadIntoSQLDWDataFactory" nem érhető el** az adatfeldolgozó neve, módosítsa az adatgyár nevét (például yournameLoadIntoSQLDWDataFactory), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
   2. Válassza ki az Azure- **előfizetését**.
   3. Az erőforráscsoportban hajtsa végre a következő lépések egyikét:
      1. Meglévő erőforráscsoport kiválasztásához kattintson a **Use existing** (Meglévő használata) elemre.
      2. Az erőforráscsoport nevének megadásához válassza ki a **Create new** (Új létrehozása) lehetőséget.
   4. Válassza ki a Data Factory **helyét**.
   5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.  
   6. Kattintson a **Létrehozás** lehetőségre.
4. A létrehozás befejezése után a **Data Factory** panel jelenik meg, ahogy az a következő képen látható:

   ![Data factory kezdőlap](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. A Data Factory kezdőlapján kattintson a **Copy data** (Adatok másolása) csempére a **Copy Wizard** (Másolás varázsló) elindításához.

   > [!NOTE]
   > Ha a böngésző azt jeleníti meg, hogy „Authorizing...” (Hitelesítés), és nem lép tovább, tiltsa le a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetet, illetve törölje a jelölését, vagy hagyja engedélyezve, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét a varázsló elindításával.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>1. lépés: az betöltési ütemterv konfigurálása
Az első lépés az betöltési ütemterv konfigurálása.  

A **Properties** (Tulajdonságok) oldalon:

1. Adja meg a **CopyFromBlobToAzureSqlDataWarehouse** a **feladat neveként**
2. Válassza a **Futtatás egyszer már** lehetőséget.   
3. Kattintson a **Tovább** gombra.  

    ![Másolás varázsló – Tulajdonságok lap](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2. lépés: a forrás konfigurálása
Ez a szakasz a forrás konfigurálásának lépéseit mutatja be: az Azure-Blob az 1 TB-os TPC-H soros elemek fájljait tartalmazza.

1. Válassza ki az **Azure Blob Storage** adattáraként, majd kattintson a **tovább** gombra.

    ![Másolási varázsló – forrás kiválasztása lap](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Adja meg az Azure Blob Storage-fiókhoz tartozó kapcsolatok adatait, és kattintson a **tovább** gombra.

    ![Másolás varázsló – forrásként szolgáló kapcsolatok adatai](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Válassza ki a TPC-H sor elem fájljait tartalmazó **mappát** , és kattintson a **tovább** gombra.

    ![Másolás varázsló – bemeneti mappa kiválasztása](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. A **tovább** gombra kattintás után a rendszer automatikusan észleli a fájlformátum beállításait.  Győződjön meg arról, hogy az oszlop elválasztója "|" az alapértelmezett vessző "," helyett.  Az adatmegjelenítés után kattintson a **tovább** gombra.

    ![Másolás varázsló – fájlformátum-beállítások](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3. lépés: a cél konfigurálása
Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a cél: `lineitem` Table táblát az Azure szinapszis Analytics-adatbázisban.

1. Válassza az **Azure szinapszis Analytics** lehetőséget célként szolgáló tárolóként, majd kattintson a **tovább** gombra.

    ![Másolási varázsló – cél adattár kiválasztása](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Adja meg az Azure szinapszis Analytics szolgáltatáshoz kapcsolódó információkat.  Győződjön meg arról, hogy a szerepkör tagja a felhasználónak `xlargerc` (lásd az **Előfeltételek** című szakaszt a részletes utasításokhoz), majd kattintson a **tovább** gombra.

    ![Másolási varázsló – cél kapcsolatok adatai](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Válassza ki a cél táblát, és kattintson a **tovább** gombra.

    ![Másolás varázsló – tábla-hozzárendelés lap](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. A séma-hozzárendelés lapon hagyja bejelölés nélkül az "oszlop-hozzárendelés alkalmazása" lehetőséget, és kattintson a **tovább** gombra.

## <a name="step-4-performance-settings"></a>4. lépés: a teljesítmény beállításai

Alapértelmezés szerint be van jelölve a " **Base** " jelölőnégyzet.  Kattintson a **Tovább** gombra.

![Másolás varázsló – séma leképezése lap](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5. lépés: a betöltési eredmények üzembe helyezése és figyelése
1. A telepítéshez kattintson a **Befejezés** gombra.

    ![Másolás varázsló – 1. összesítő oldal](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Az üzembe helyezés befejezése után kattintson a gombra a `Click here to monitor copy pipeline` Másolás futtatási folyamatának figyeléséhez. Válassza ki a létrehozott másolási folyamatot a **tevékenység Windows** listájában.

    ![Másolási varázsló – összefoglalás 2. lapja](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    A másolási művelet részleteit a jobb oldali panelen, a **tevékenység ablak-kezelőben** tekintheti meg, beleértve a forrásból beolvasott adatmennyiséget, valamint a célhelyre, az időtartamra és a Futtatás átlagos átviteli sebességére vonatkozó adatokat.

    Ahogy az alábbi képernyőképen is látható, az Azure-Blob Storageból az Azure szinapszis Analyticsbe való 1 TB-os adatmásolási szolgáltatás 14 percet vett igénybe, ami gyakorlatilag 1,22 GB/s sebesség elérését

    ![Másolás varázsló – sikeres párbeszédpanel](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Ajánlott eljárások
Íme néhány ajánlott eljárás az Azure szinapszis Analytics-adatbázis futtatásához:

* FÜRTÖZÖTT OSZLOPCENTRIKUS-INDEXbe való betöltéskor használjon nagyobb erőforrás-osztályt.
* A hatékonyabb összekapcsoláshoz érdemes lehet a kivonatoló eloszlást egy Select oszlop használatával használni az alapértelmezett ciklikus multiplexelés-eloszlás helyett.
* A gyorsabb betöltési sebesség érdekében érdemes lehet a kupacot használni az átmeneti adatértékekhez.
* Statisztikák létrehozása az Azure szinapszis Analyticsbe való betöltés befejezését követően.

További részletekért tekintse meg az [Azure szinapszis Analytics ajánlott eljárásai](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) című témakört.

## <a name="next-steps"></a>Következő lépések
* [Data Factory másolási varázsló](data-factory-copy-wizard.md) – ez a cikk a másolás varázsló részleteit tartalmazza.
* [Másolási tevékenység teljesítményének és finomhangolásának útmutatója](data-factory-copy-activity-performance.md) – ez a cikk a referenciák teljesítményének méréseit és hangolási útmutatóját tartalmazza.