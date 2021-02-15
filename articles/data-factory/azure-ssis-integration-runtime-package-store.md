---
title: Csomagok kezelése Azure-SSIS Integration Runtime Package Store-val
description: Ismerje meg, hogyan kezelheti a csomagokat Azure-SSIS Integration Runtime Package Store-ban.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393836"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Csomagok kezelése Azure-SSIS Integration Runtime Package Store-val

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A helyszíni SQL Server Integration Services (SSIS) számítási feladatok felhőbe való átváltásához & a Azure-SSIS Integration Runtime (IR) Azure Data Factory (ADF) használatával. További információ: [Azure-SSIS IR létesítése](./tutorial-deploy-ssis-packages-azure.md). Az Azure-SSIS IR a következőket támogatja:

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

A csomag-telepítési modell használata esetén kiválaszthatja, hogy szeretné-e kiépíteni a Azure-SSIS IRt a csomagok tárolói között. Biztosítanak egy csomagkezelő réteget az Azure SQL felügyelt példánya által üzemeltetett fájlrendszer, Azure Files vagy MSDB felett. A Azure-SSIS IR Package Store lehetővé teszi a csomagok importálását/exportálását, törlését és futtatását, valamint a csomagok futtatásának figyelését/leállítását SQL Server Management Studio (SSMS) használatával, hasonlóan a [régi SSIS-csomag tárolójához](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Kapcsolódás Azure-SSIS IRhoz

Ha a Azure-SSIS IR kiépítve, csatlakozhat hozzá, hogy megkeresse a csomagjait a SSMS.

![Kapcsolódás Azure-SSIS IRhoz](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

A SSMS **Object Explorer** ablakában válassza a **Azure-SSIS Integration Runtime** lehetőséget a **kapcsolat** legördülő menüben. Ezután jelentkezzen be az Azure-ba, és válassza ki a Package Store-ban kiépített megfelelő előfizetést, ADF-t és Azure-SSIS IR. A Azure-SSIS IR a **futó csomagok** és a **tárolt csomagok** csomópont alatt jelenik meg. Bontsa ki a **tárolt csomagok** csomópontot, és tekintse meg a csomagok tárolóit az alatt. Bontsa ki a csomagok tárolóit, hogy az alatta lévő mappák és csomagok megjelenjenek. Előfordulhat, hogy meg kell adnia a csomagok tárolóinak hozzáférési hitelesítő adatait, ha a SSMS nem tud automatikusan csatlakozni hozzájuk. Ha például kibővít egy MSDB a felső részén, akkor előfordulhat, hogy először csatlakoznia kell a felügyelt Azure SQL-példányhoz.

![Kapcsolódás az Azure SQL felügyelt példányához](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Mappák és csomagok kezelése

Miután kapcsolódott a Azure-SSIS IRhoz a SSMS-on, a jobb gombbal kattintson bármelyik csomag-áruházra, mappára vagy csomagra a menü felugró menüjében, majd válassza az **új mappa**, **csomag importálása**, **csomag exportálása**, **Törlés** vagy **frissítés** lehetőséget.

   ![Mappák és csomagok kezelése](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Válassza az **új mappa** lehetőséget az importált csomagok új mappájának létrehozásához.

   *  Válassza a **csomag importálása** lehetőséget, hogy csomagokat importáljon a **fájlrendszerből**, **SQL Server** (MSDB) vagy a régi **SSIS-Package áruházból** a csomag tárolójába.

      ![Csomag importálása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Attól függően, hogy melyik **csomagot** szeretné importálni, válassza ki a megfelelő **kiszolgálói** / **hitelesítési típust**, adja meg a hozzáférési hitelesítő adatokat, ha szükséges, válassza ki a **csomag elérési útját**, és adja meg az új **csomag nevét**. Csomagok importálásakor a védelmi szintje nem módosítható. A módosításhoz használja a SQL Server Data Tools (SSDT) vagy a `dtutil` parancssori segédprogramot.

      > [!NOTE]
      > A SSIS-csomagok Azure-SSIS IR Package Store-ba történő importálása csak egyszer hajtható végre, és egyszerűen másolja őket az alapul szolgáló MSDB/File System/Azure Filesba, miközben megőrzi a SQL Server/SSIS verzióját. 
      >
      > Mivel a Azure-SSIS IR jelenleg **SQL Server 2017**-es verzión alapul, az alacsonyabb verziójú csomagok futtatásával a rendszer a SSIS 2017-csomagokba frissíti őket. A magasabb verziójú csomagok végrehajtása nem támogatott.
      >
      > Továbbá, mivel a régi SSIS-csomagok tárolói meghatározott SQL Server-verzióhoz vannak kötve, és az adott verzióhoz csak a SSMS érhetők el, a régi SSIS-csomagok alacsonyabb verziójú csomagjait először a kijelölt SSMS-verzióval kell exportálni, mielőtt a SSMS 2019-as vagy újabb verziókkal importálják őket Azure-SSIS IR-csomagokba.
      >
      > Azt is megteheti, hogy több SSIS-csomagot is importál Azure-SSIS IR Package Store-ba a védelmi szint átváltásakor. a [dtutil](/sql/integration-services/dtutil-utility) parancssori segédprogramot a következő témakörben tekintheti meg: [több csomag telepítése dtutil](#deploying-multiple-packages-with-dtutil)használatával.

   *  Válassza a csomag **exportálása** lehetőséget a csomagok tárolóból való exportálásához a **fájlrendszerbe**, **SQL Server** (MSDB) vagy az örökölt **SSIS-csomag tárolóba**.

      ![Csomag exportálása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Az exportálandó **csomag helyétől** függően válassza ki a megfelelő **kiszolgálói** / **hitelesítési típust**, adja meg a hozzáférési hitelesítő adatokat, ha szükséges, majd válassza ki a **csomag elérési útját**. A csomagok exportálásakor, ha titkosítva vannak, adja meg azokat a jelszavakat, amelyeket először vissza kell fejteni, majd módosíthatja a védelmi szintet, például a bizalmas adatok tárolásának elkerüléséhez, vagy a felhasználói kulccsal vagy jelszóval rendelkező adatok titkosításához.

      > [!NOTE]
      > Az Azure-SSIS IR SSIS származó csomagok exportálása csak egyszer hajtható végre, és a védelmi szint átállítása nélkül egyszerűen másolja őket a SQL Server/SSIS verziójának megőrzése mellett, ellenkező esetben a rendszer a SSIS 2019-as vagy újabb verziójú csomagokat fogja frissíteni.
      >
      > Mivel a Azure-SSIS IR jelenleg **SQL Server 2017**-es verzión alapul, az alacsonyabb verziójú csomagok futtatásával a rendszer a SSIS 2017-csomagokba frissíti őket. A magasabb verziójú csomagok végrehajtása nem támogatott.
      >
      > Azt is megteheti, hogy Azure-SSIS IR Package Store-ból több SSIS-csomagot is exportál a védelmi szint átállítása közben. a [dtutil](/sql/integration-services/dtutil-utility) parancssori segédprogramot a következő témakörben tekintheti meg: [több csomag telepítése dtutil](#deploying-multiple-packages-with-dtutil)használatával.

   *  A **Törlés** lehetőség kiválasztásával törölheti a meglévő mappákat/csomagokat a csomag tárolójából.

   *  Válassza a **frissítés** lehetőséget, hogy megjelenjenek az újonnan hozzáadott mappák/csomagok a Package Store-ban.

## <a name="execute-packages"></a>Csomagok végrehajtása

Miután kapcsolódott a Azure-SSIS IR a SSMS-on, a jobb gombbal bármelyik tárolt csomagra kattintva megnyithatja a menüt, és kiválaszthatja a **csomag futtatása** lehetőséget.  Ekkor megnyílik a **Csomagfuttató** párbeszédpanel, amelyen beállíthatja, hogy a csomagok végrehajtása a Azure-SSIS IR SSIS-csomag tevékenységének végrehajtásához az ADF-folyamatokban.

![Csomagfuttató lapok 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Csomagfuttató Pages 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**Csomagfuttató** párbeszédpanel **általános**, **konfigurációk**, **végrehajtási beállításai** és **naplózási** lapjai megfelelnek a SSIS-csomag végrehajtása tevékenység **Beállítások** lapjának. Ezeken az oldalakon megadhatja a csomag titkosítási jelszavát, és elérheti a csomag konfigurációs fájljának hozzáférési információit. Megadhatja a csomag végrehajtási hitelesítő adatait és tulajdonságait, valamint a naplófájl elérési adatait is.  Az **Csomagfuttató** párbeszédablak **set Values (értékek beállítása** ) lapján a SSIS-csomag végrehajtása tevékenységben szereplő **Tulajdonságok felülbírálása lapra kerül** , ahol megadhatja a meglévő csomag tulajdonságait a felülbíráláshoz. További információ: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).

A **végrehajtás** gomb kiválasztásakor a rendszer automatikusan generálja és elindítja az új ADF-folyamatot a SSIS-csomag végrehajtásával. Ha már létezik azonos beállításokkal rendelkező ADF-folyamat, az újra lesz futtatva, és új folyamat nem jön létre. Az ADF-folyamat és a SSIS-csomag végrehajtása tevékenység a neve `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` , illetve a lesz.

![Csomagfuttató gomb](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS-csomag végrehajtása tevékenység](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>A csomagok futtatásának figyelése és leállítása

Miután kapcsolódott a Azure-SSIS IRhoz a SSMS-on, kibonthatja a **futó csomagok** csomópontot, hogy a jelenleg futó csomagok megjelenjenek.  Kattintson a jobb gombbal bármelyikre, és válassza a **Leállítás** vagy a **frissítés** lehetőséget.

   ![A csomagok futtatásának figyelése és leállítása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  A **Leállítás** gombra kattintva szakítsa meg a csomagot futtató aktuálisan futó ADF-folyamatot a SSIS-csomag végrehajtása tevékenységként.

   *  Válassza a **frissítés** lehetőséget, ha az újonnan futó csomagokat szeretné megjeleníteni a csomagok tárolójában.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>A csomagok Azure-SSIS IRának és szerkesztésének figyelése

Miután kapcsolódott a Azure-SSIS IR a SSMS-on, kattintson rá a jobb gombbal, és válassza ki a kívánt menüt, és válassza az **ugrás Azure Data Factory portálra vagy a** **frissítés** lehetőségre.

   ![Ugrás az ADF-portálra](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Válassza az **Ugrás a Azure Data Factory portálra** lehetőséget az ADF-figyelési központ **Integration Runtimes (integrációs** modulok) oldalának megnyitásához, ahol nyomon követheti a Azure-SSIS IR. A **Package Stores (csomagok tárolása** ) csempén láthatja a Azure-SSIS IRhoz csatolt csomagok tárolóinak számát.  Ha kiválasztja ezt a számot, megjelenik egy ablak, amelyen szerkesztheti az ADF-hez társított szolgáltatásokat, amelyek tárolják a csomagokhoz tartozó tárolók hozzáférési információit.

      ![Csomagok tárolóinak szerkesztése](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Válassza a **frissítés** lehetőséget, hogy megjelenjenek az újonnan hozzáadott mappák/csomagok a csomagban, és csomagokat futtassanak a csomagok tárolói között.

## <a name="deploying-multiple-packages-with-dtutil"></a>Több csomag üzembe helyezése a dtutil-mel

A helyszíni SSIS számítási feladatok SSIS való átváltásához az ADF-ben a régi csomag-telepítési modell fenntartása mellett telepítenie kell a csomagokat a fájlrendszerből, a SQL Server által üzemeltetett MSDB, vagy az örökölt SSIS-csomagok tárolóit a Azure Files, az Azure SQL felügyelt példánya által üzemeltetett MSDB vagy Azure-SSIS IR csomag tárolja. & Ugyanakkor ha még nem tette meg, akkor a titkosítási szintet a felhasználói kulcs titkosítása nélkül is át kell váltania titkosítatlan vagy titkosításra a jelszóval.

A SQL Server/SSIS telepítéséhez használható [dtutil](/sql/integration-services/dtutil-utility) parancssori segédprogramot több csomag kötegekben történő üzembe helyezéséhez is használhatja. Az adott SSIS-verzióhoz van kötve, így ha az alacsonyabb verziójú csomagokat a védelmi szint átállítása nélkül helyezi üzembe, akkor egyszerűen másolja őket, miközben megőrzi a SSIS verzióját. Ha ezt a lehetőséget használja az üzembe helyezéséhez és a védelmi szint egyidejű átállításához, akkor az a SSIS verziójára frissíti őket.

 Mivel a Azure-SSIS IR jelenleg **SQL Server 2017**-es verzión alapul, az alacsonyabb verziójú csomagok futtatásával a rendszer a SSIS 2017-csomagokba frissíti őket. A magasabb verziójú csomagok végrehajtása nem támogatott.

Ennek következtében, ha el szeretné kerülni a futásidejű frissítéseket, a csomagok üzembe helyezése Azure-SSIS IR a csomag üzembe helyezési modelljében a dtutil 2017-et kell használnia, amely a SQL Server/SSIS 2017 telepítéshez tartozik. Erre a célra letöltheti és telepítheti az ingyenes [SQL Server/SSIS 2017 fejlesztői kiadást](https://go.microsoft.com/fwlink/?linkid=853016) . A telepítést követően a dtutil 2017 a következő mappában található: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Több csomag telepítése a helyi fájlrendszerből Azure Filesba a dtutil

 Ha több csomagot kíván üzembe helyezni a fájlrendszerből a Azure Filesba, és egy időben szeretné átváltani a védelmi szintet, akkor a következő parancsokat futtathatja a parancssorban. Cserélje le az adott esetre jellemző összes karakterláncot.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

A fenti parancsok batch-fájlban való futtatásához cserélje le a következőt: `%f` `%%f` .

Ha több csomagot kíván üzembe helyezni a fájlrendszeren felül lévő örökölt SSIS-csomagokból a Azure Filesba, és a védelmi szintjét egyszerre kell átállítania, ugyanazokat a parancsokat használhatja, de a helyére a `YourLocalDrive:\...\YourPackageFolder` régi SSIS-csomagok által használt helyi mappák is használhatók: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Ha például a régi SSIS-csomag tárolója SQL Server 2016-hez van kötve, ugorjon a következő helyre: `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  A értéket az `YourSQLServerDefaultCompatibilityLevel` [SQL Server alapértelmezett kompatibilitási szintjeinek listájából](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)találja.

Ha Azure-SSIS IR csomag-áruházakat Azure Files-on felül konfigurálta, akkor a telepített csomagok a SSMS 2019-as vagy újabb verziójában a Azure-SSIS IRhoz való csatlakozáskor fognak megjelenni.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Több csomag üzembe helyezése a MSDB a helyszínen a MSDB az Azure-ban a dtutil

 Ha SQL Server vagy örökölt SSIS-MSDB által üzemeltetett MSDB több csomagot szeretne üzembe helyezni az Azure SQL felügyelt példányai által üzemeltetett MSDB, és a védelmi szintjét egy időben át szeretné kapcsolni, akkor a SSMS-on lévő SQL Serverhoz kattintson a jobb gombbal a `Databases->System Databases->msdb` csomópontra a SSMS **Object Explorer** , és  futtassa az alábbi T-SQL-szkriptet. Cserélje le az esethez tartozó összes karakterláncot:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Az Azure SQL felügyelt példányának magán/nyilvános végpontjának használatához cserélje le `YourSQLManagedInstanceEndpoint` a-t a következőre: `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` .

A parancsfájl dtutil hoz majd a MSDB összes olyan csomagjának, amelyet egyszerre több kiválaszthat, másolhat & beilleszthet, és futtathatja a parancsot a parancssorba.

![Dtutil-parancssorok előállítása](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Ha a MSDB-ben konfigurálta Azure-SSIS IR Package Stores-t, akkor a telepített csomagok akkor jelennek meg, amikor a SSMS 2019-es vagy újabb verziójában csatlakozik a Azure-SSIS IRhoz.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Több csomag üzembe helyezése a MSDB a helyszínen Azure Files a dtutil-ben

 Ha SQL Server vagy örökölt SSIS-MSDB által üzemeltetett MSDB több csomagot szeretne üzembe helyezni Azure Files és a védelmi szintjét egyszerre szeretné átváltani, akkor a SQL Serverhoz kapcsolódhat a SSMS, majd a jobb gombbal kattintson a `Databases->System Databases->msdb` SSMS **Object Explorer** csomópontra az **új lekérdezési** ablak megnyitásához, majd futtassa a következő T-SQL-szkriptet. Cserélje le az esethez tartozó összes karakterláncot:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

A parancsfájl dtutil hoz majd a MSDB összes olyan csomagjának, amelyet egyszerre több kiválaszthat, másolhat & beilleszthet, és futtathatja a parancsot a parancssorba.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Ha Azure-SSIS IR csomag-áruházakat Azure Files-on felül konfigurálta, akkor a telepített csomagok a SSMS 2019-as vagy újabb verziójában a Azure-SSIS IRhoz való csatlakozáskor fognak megjelenni.

## <a name="next-steps"></a>Következő lépések

Az automatikus létrehozott ADF-folyamatokat újrafuttathatja vagy szerkesztheti a SSIS-csomag tevékenységeivel, vagy újakat hozhat létre az ADF-portálon. További információ: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).