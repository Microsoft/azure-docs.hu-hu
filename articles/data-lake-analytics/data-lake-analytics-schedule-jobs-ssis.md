---
title: U-SQL-feladatok ütemezett Azure Data Lake Analytics a SSIS használatával
description: Ismerje meg, hogy a SQL Server Integration Services használatával hogyan ütemezhet U-SQL-feladatokat beágyazott parancsfájllal vagy U-SQL-lekérdezési fájlokkal.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221451"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>U-SQL-feladatok ütemezett SQL Server Integration Services (SSIS) használatával

Ebből a dokumentumból megtudhatja, hogyan hangolhat össze és hozhat létre U-SQL-feladatokat SQL Server Integration Service (SSIS) használatával. 

## <a name="prerequisites"></a>Előfeltételek

Az [integrációs szolgáltatásokhoz készült Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) biztosítja a [Azure Data Lake Analytics feladatot](/sql/integration-services/control-flow/azure-data-lake-analytics-task) és a [Azure Data Lake Analytics csatlakozáskezelőt](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) , amely segít a Azure Data Lake Analytics szolgáltatáshoz való kapcsolódásban. A feladat használatához telepítse a következőt:

- [A Visual studióhoz készült SQL Server Data Tools (SSDT) letöltése és telepítése](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Az Azure Feature Pack telepítése integrációs szolgáltatásokhoz (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics feladat

A Azure Data Lake Analytics feladat lehetővé teszi a felhasználóknak U-SQL-feladatok küldését a Azure Data Lake Analytics-fiókba. 

[Útmutató Azure Data Lake Analytics feladat konfigurálásához](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Azure Data Lake Analytics feladat a SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

A U-SQL-szkriptet különböző helyekről SSIS beépített függvények és feladatok használatával érheti el, az alábbi forgatókönyvek azt mutatják be, hogyan konfigurálhatja a U-SQL-parancsfájlokat a különböző felhasználói esetekhez.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>1. eset – beágyazott parancsfájl-hívási tvfs és tárolt proc-parancsok használata

Azure Data Lake Analytics Feladatütemezőben konfigurálja a **forrás típusa** -t **DirectInput**, és helyezze a U-SQL-utasításokat a **USQLStatement**-be.

Az egyszerű karbantartás és a kód kezelése érdekében a rendszer csak a rövid U-SQL-parancsfájlokat beágyazott parancsfájlokként helyezi üzembe, például meghívhatja a meglévő táblázat értékű függvényeket és tárolt eljárásokat a U-SQL-adatbázisokban. 

![Beágyazott U-SQL-parancsfájl szerkesztése a SSIS-feladatban](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Kapcsolódó cikk: paraméter átadása [tárolt eljárásokhoz](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>2. forgatókönyv – U-SQL-fájlok használata Azure Data Lake Store

A Azure Data Lake Store a U-SQL-fájlokat is használhatja az Azure Feature Pack **Azure Data Lake Store fájlrendszer feladatának** használatával. Ez a megközelítés lehetővé teszi a felhőben tárolt szkriptek használatát.

Kövesse az alábbi lépéseket a Azure Data Lake Store fájlrendszer feladatának és Azure Data Lake Analytics feladat közötti kapcsolat beállításához.

### <a name="set-task-control-flow"></a>Feladat-vezérlési folyamat beállítása

A SSIS-csomag Tervező nézetében adjon hozzá egy **Azure Data Lake Store fájlrendszerbeli feladatot**, egy **foreach loop-tárolót** és egy **Azure Data Lake Analytics feladatot** a foreach loop-tárolóban. A Azure Data Lake Store fájlrendszer feladat segít az U-SQL-fájlok letöltésében a ADLS-fiókban egy ideiglenes mappába. Az foreach loop tároló és a Azure Data Lake Analytics feladat segítséget nyújt az ideiglenes mappában lévő összes U-SQL-fájl küldéséhez U-SQL-feladatként az Azure Data Lake Analytics-fiókhoz.

![A foreach loop-tárolóba felvenni kívánt Azure Data Lake Store fájlrendszerbeli feladatot bemutató diagram.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store fájlrendszer feladat konfigurálása

1. Állítsa be a **műveletet** a **CopyFromADLS** értékre.
2. **AzureDataLakeConnection** beállítása, további információ a [Azure Data Lake Store-Csatlakozáskezelőről](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. **AzureDataLakeDirectory** beállítása. Mutasson arra a mappára, amely a U-SQL-parancsfájlokat tárolja. Relatív elérési út használata a Azure Data Lake Store fiók gyökérkönyvtárához képest.
4. Állítsa a **célhelyet** olyan mappára, amely gyorsítótárazza a letöltött U-SQL-parancsfájlokat. Ez a mappa elérési útja a U-SQL-feladatok beküldéséhez használt foreach loop-tárolóban lesz használatban. 

![Azure Data Lake Store fájlrendszer feladat konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[További információ a Azure Data Lake Store fájlrendszer feladatáról](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task).

### <a name="configure-foreach-loop-container"></a>Foreach hurok tárolójának konfigurálása

1. A **gyűjtemény** lapon állítsa az **enumerálást** a **foreach fájl enumerálására**.

2. A **mappa** beállítása a **számbavételi konfigurációs** csoportba a letöltött U-SQL-parancsfájlokat tartalmazó ideiglenes mappába.

3. Állítsa be a **fájlokat** a **számbavételi konfiguráció** területen `*.usql` úgy, hogy a hurok tárolója csak a-val végződő fájlokat kapja meg `.usql` .

    ![Képernyőkép, amely megjeleníti a foreach hurok-szerkesztőt, a "gyűjtemény" elemet, és kiemelte a számbavételi és enumerálási konfigurációs szakaszt.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. A **változó-hozzárendelések** lapon adjon hozzá egy felhasználó által definiált változót az egyes U-SQL-fájlok fájlnevének lekéréséhez. Állítsa az **indexet** 0-ra a fájl nevének lekéréséhez. Ebben a példában a nevű változót definiáljuk `User::FileName` . Ez a változó a U-SQL parancsfájl-kapcsolódás dinamikus beszerzésére és az U-SQL-feladat nevének Azure Data Lake Analytics feladatban való beállítására szolgál.

    ![A foreach hurok tárolójának konfigurálása a fájl nevének beolvasásához](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Azure Data Lake Analytics feladat konfigurálása 

1. Állítsa  a forrás típusa **FileConnection** értékre.

2. Állítsa be a **FileConnection** a foreach loop-tárolóból visszaadott fájl objektumra mutató kapcsolódási pontra.
    
    A kapcsolatfájl létrehozása:

   1. Válassza **\<New Connection...>** a FileConnection beállítást.
   2. Állítsa a **használati típust** **meglévő fájl** értékre, és állítsa a **fájlt** bármely létező fájl elérési útjára.

       ![Képernyőfelvétel: a "meglévő fájl" beállítással rendelkező fájlkezelő szerkesztő a "használat típusa" lehetőségre.](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. A **Csatlakozáskezelő** nézetben kattintson a jobb gombbal a most létrehozott fájlra, majd válassza a **Tulajdonságok** lehetőséget.

   4. A **Tulajdonságok** ablakban bontsa ki a **kifejezések** elemet, és állítsa a **ConnectionString** értéket a foreach loop-tárolóban definiált változóra, például: `@[User::FileName]` .

       ![Foreach hurok tárolójának konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Állítsa be a **AzureDataLakeAnalyticsConnection** arra a Azure Data Lake Analytics-fiókra, amelyhez feladatokat kíván küldeni. További információ a [Azure Data Lake Analytics Csatlakozáskezelőről](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Más feladatok konfigurációjának beállítása. [További információk](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

5. **Kifejezések** használata a U-SQL-feladatok nevének dinamikus beállításához:

    1. A **kifejezések** lapon adjon hozzá egy új Expression kulcs-érték párokat a **JobName**.
    2. Állítsa be a JobName értékét a foreach loop-tárolóban definiált változóra, például: `@[User::FileName]` .
    
        ![SSIS kifejezés konfigurálása U-SQL-feladatokhoz](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>3. forgatókönyv – U-SQL-fájlok használata az Azure-ban Blob Storage

A U-SQL-fájlokat az Azure Blob Storage Azure **blob letöltési feladatával** használhatja az Azure Feature packben. Ez a megközelítés lehetővé teszi a parancsfájlok használatát a felhőben.

A lépések a 2. [forgatókönyvhöz hasonlóak: a U-SQL-fájlok használata a Azure Data Lake Storeban](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Módosítsa a Azure Data Lake Store fájlrendszer feladatot az Azure Blob letöltési feladatra. [További információ az Azure Blob letöltési feladatáról](/sql/integration-services/control-flow/azure-blob-download-task).

A vezérlési folyamat az alábbihoz hasonló.

![U-SQL-fájlok használata a Azure Data Lake Storeban](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>4. forgatókönyv – U-SQL-fájlok használata a helyi gépen

A felhőben tárolt U-SQL-fájlok használata mellett a helyi gépen vagy a SSIS-csomagokban üzembe helyezett fájlokon is használhat fájlokat.

1. Kattintson a jobb gombbal a SSIS projektben található **kapcsolatkezelő** elemre, és válassza az **új Csatlakozáskezelő** elemet.

2. Válassza a **Fájltípus** lehetőséget, majd kattintson a **Hozzáadás...** elemre.

3. Állítsa a **használati típust** **meglévő fájl** értékre, és állítsa be a **fájlt** a helyi gépen lévő fájlra.

    ![Kapcsolatfájl hozzáadása a helyi fájlhoz](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. **Azure Data Lake Analytics** feladat hozzáadása és:
    1. Állítsa  a forrás típusa **FileConnection** értékre.
    2. Állítsa be a **FileConnection** a most létrehozott kapcsolatfájl számára.

5. Azure Data Lake Analytics feladat egyéb konfigurációinak befejezése.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>5. forgatókönyv – U-SQL-utasítás használata a SSIS változóban

Bizonyos esetekben előfordulhat, hogy dinamikusan kell létrehoznia a U-SQL-utasításokat. Az U-SQL-utasítás dinamikus létrehozásához használhatja a **SSIS változót** a **SSIS kifejezéssel** és más SSIS-feladatokkal, például a parancsfájl-feladattal.

1. A változók eszköz ablakának megnyitása a **SSIS > változók** felső szintű menüjében.

2. Adjon hozzá egy SSIS változót, és állítsa be közvetlenül az értéket, vagy használja a **kifejezést** az érték létrehozásához.

3. **Azure Data Lake Analytics feladat** hozzáadása és:
    1. Állítsa  a forrás típusa **változóra**.
    2. Állítsa be a **SourceVariable** a most létrehozott SSIS-változóra.

4. Azure Data Lake Analytics feladat egyéb konfigurációinak befejezése.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>6. forgatókönyv – paraméterek átadása U-SQL-parancsfájlba

Bizonyos esetekben érdemes lehet dinamikusan beállítani az U-SQL változó értékét a U-SQL-parancsfájlban. Az Azure Data Lake Analytics feladat **paraméter-hozzárendelési** funkciója segít ebben a forgatókönyvben. Általában két tipikus felhasználói eset létezik:

- A bemeneti és kimeneti fájl elérési útjának változóit az aktuális dátum és idő alapján dinamikusan állítsa be.
- Állítsa be a paramétert a tárolt eljárásokhoz.

[További információ a U-SQL-szkript paramétereinek megadásáról](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Következő lépések

- [SSIS-csomagok futtatása az Azure-ban](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Azure Feature Pack integrációs szolgáltatásokhoz (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [U-SQL-feladatok ütemezett Azure Data Factory használatával](../data-factory/transform-data-using-data-lake-analytics.md)