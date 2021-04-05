---
title: SSIS-csomag futtatása a SSIS-csomag végrehajtása tevékenységgel
description: Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot egy Azure Data Factory-folyamatban a SSIS-csomag végrehajtása tevékenység használatával.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 40e7d5c11a2ebc62e59c3d5d20dfefe18a33f9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391609"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>SSIS-csomag futtatása az SSIS-csomag végrehajtása tevékenységgel az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot egy Azure Data Factory-folyamatban a SSIS-csomag végrehajtása tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre egy Azure-SSIS integrációs modult (IR), ha még nem rendelkezik az oktatóanyag részletes utasításait követve [: kiépítés Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Csomag futtatása a Azure Portal
Ebben a szakaszban a Data Factory felhasználói felületén (UI) vagy az alkalmazáson keresztül hozhat létre egy Data Factory folyamatot a SSIS-csomagot futtató végrehajtási SSIS-csomag tevékenységgel.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel
Ebben a lépésben a Data Factory felhasználói felületét vagy az alkalmazást használja egy folyamat létrehozásához. Vegyen fel egy végrehajtási SSIS-csomag tevékenységet a folyamatba, és konfigurálja úgy, hogy futtassa a SSIS-csomagot. 

1. A Azure Portal Data Factory áttekintés vagy Kezdőlap lapján válassza a **szerző & figyelő** csempét, hogy elindítsa a Data Factory felhasználói felületét vagy alkalmazását egy külön lapon. 

   ![Data Factory Kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. A **tevékenységek** eszközkészletben bontsa ki az **általános** elemet. Ezután húzzon egy **SSIS-csomag végrehajtása** tevékenységet a folyamat tervező felületére. 

   ![SSIS-csomag végrehajtása tevékenység áthúzása a tervező felületére](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Válassza az SSIS-csomag végrehajtása tevékenység objektumot az **általános**, a **Beállítások**, a **SSIS paramétereinek**, a **Csatlakozáskezelő** és a **Tulajdonságok felülbírálási** lapjainak konfigurálásához.

#### <a name="general-tab"></a>Általános lap

Az SSIS-csomag végrehajtása tevékenység **általános** lapján hajtsa végre a következő lépéseket.

![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. A **név** mezőben adja meg a SSIS-csomag végrehajtása tevékenység nevét.

   1. A **Leírás** mezőbe írja be a SSIS-csomag végrehajtása tevékenység leírását.

   1. Az **időtúllépés** mezőben adja meg azt a maximális időtartamot, ameddig a végrehajtás SSIS csomagjának tevékenysége futhat. Az alapértelmezett érték 7 nap, a formátum: D. HH: PP: MM.

   1. Az **újrapróbálkozáshoz** adja meg az újrapróbálkozási kísérletek maximális számát a SSIS-csomag végrehajtása tevékenységhez.

   1. Az **újrapróbálkozási időköz** mezőben adja meg a végrehajtási SSIS-csomag tevékenységének minden újrapróbálkozási kísérlete között eltelt másodpercek számát. Az alapértelmezett érték 30 másodperc.

   1. A **biztonságos kimenet** jelölőnégyzet bejelölésével kiválaszthatja, hogy ki szeretné-e zárni a végrehajtás SSIS-csomag tevékenység kimenetét a naplózásból.

   1. A **biztonságos bevitel** jelölőnégyzet bejelölésével kiválaszthatja, hogy ki szeretné-e zárni a végrehajtás SSIS-csomag tevékenységének bemenetét a naplózásból.

#### <a name="settings-tab"></a>Beállítások lap

A SSIS-csomag végrehajtása tevékenység **Beállítások** lapján hajtsa végre a következő lépéseket.

![Tulajdonságok beállítása a beállítások lapon – automatikus](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. **Azure-SSIS IR** esetén válassza ki a kijelölt Azure-SSIS IR a SSIS-csomag végrehajtása tevékenység futtatásához.

   1. A **Leírás** mezőbe írja be a SSIS-csomag végrehajtása tevékenység leírását.

   1. Jelölje be a **Windows-hitelesítés** jelölőnégyzetet annak kiválasztásához, hogy a Windows-hitelesítést kívánja-e használni az adattárakhoz, például az SQL-kiszolgálók/fájlmegosztás helyszíni vagy Azure files.
   
      Ha bejelöli ezt a jelölőnégyzetet, adja meg a csomag végrehajtási hitelesítő adatainak értékét a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Például a Azure Files, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó eléréséhez `<storage account key>` .

      Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Jelölje be a **32 bites futtatókörnyezet** jelölőnégyzetet annak kiválasztásához, hogy a csomaghoz szükség van-e a 32 bites futtatókörnyezet futtatására.

   1. A **csomag helye** területen válassza a **SSISDB**, a fájlrendszer **(csomag)**, a **fájlrendszer (projekt)**, a **beágyazott csomag** vagy a **csomag tároló** elemet. 

##### <a name="package-location-ssisdb"></a>Csomag helye: SSISDB

Ha a **SSISDB** automatikusan ki van választva, ha a Azure-SSIS IR Azure SQL Database kiszolgáló/felügyelt példány által ÜZEMELTETett SSIS-katalógussal (SSISDB) lett kiépítve, vagy kiválaszthatja saját maga is. Ha be van jelölve, hajtsa végre az alábbi lépéseket.

   1. Ha a Azure-SSIS IR fut, és a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, tallózással keresse meg és válassza ki a meglévő mappákat, projekteket, csomagokat és környezeteket a SSISDB. Válassza a **frissítés** lehetőséget az újonnan hozzáadott mappák, projektek, csomagok vagy KÖRNYEZETek SSISDB való beolvasásához, hogy elérhetők legyenek a böngészéshez és a kiválasztáshoz. A csomagok végrehajtásához szükséges környezetek tallózásához és kiválasztásához előre be kell állítania a projekteket, hogy a SSISDB alatt található azonos mappák hivatkozásaiként adja hozzá ezeket a környezeteket. További információ: [SSIS-környezetek létrehozása és leképezése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. A **naplózási szint** beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 

   1. Ha a Azure-SSIS IR nem fut, vagy a **manuális bejegyzések** jelölőnégyzet be van jelölve, adja meg a csomag-és környezeti elérési utakat a SSISDB közvetlenül a következő formátumokban: `<folder name>/<project name>/<package name>.dtsx` és `<folder name>/<environment name>` .

      ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Csomag helye: fájlrendszer (csomag)

Ha a Azure-SSIS IR SSISDB nélkül lett kiépítve **, a csomag helye** automatikusan ki van választva, vagy kiválaszthatja saját maga is. Ha be van jelölve, hajtsa végre az alábbi lépéseket.

![Tulajdonságok beállítása a beállítások lapon – fájlrendszer (csomag)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Adja meg a futtatni kívánt csomagot úgy, hogy a csomag `.dtsx` **elérési útja** mezőben univerzális elnevezési konvenció (UNC) szerinti elérési utat biztosít a csomagfájl számára. A csomag tallózásával és kiválasztásával megkeresheti és kiválaszthatja a csomagot **tallózással** vagy manuálisan is megadhatja. Ha például a csomagot Azure Filesban tárolja, annak elérési útja a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Ha a csomagot külön fájlban konfigurálja, akkor `.dtsConfig` a konfigurációs **elérési út** mezőben is meg kell adnia egy UNC elérési utat a konfigurációs fájlhoz (a). Tallózással és kiválasztással kiválaszthatja a konfigurációt a **file Storage tallózásával** , vagy manuálisan is megadhatja az elérési utat. Ha például Azure Files tárolja a konfigurációt, annak elérési útja a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Adja meg a csomag és a konfigurációs fájlok eléréséhez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag végrehajtási hitelesítő adataihoz tartozó értékeket ( **Windows-hitelesítéshez**), akkor a **csomagok végrehajtási hitelesítő adataival megegyező** jelölőnégyzet bejelölésével újra felhasználhatja őket. Ellenkező esetben adja meg a csomag hozzáférési hitelesítő adatainak értékét a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Ha például a csomagot és a konfigurációt Azure Files, a tartományt `Azure` , a felhasználónevet `<storage account name>` és a jelszót tárolja `<storage account key>` . 

      Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` . 

      Ezeket a hitelesítő adatokat a rendszer a csomagok végrehajtása tevékenységben a saját elérési úttal és a csomagban megadott egyéb konfigurációkra hivatkozó alárendelt csomagok elérésére is felhasználja. 

   1. Ha a **EncryptAllWithPassword** vagy a **EncryptSensitiveWithPassword** védelmi szintet használta a csomagnak a SQL Server Data Tools (SSDT) használatával történő létrehozásakor, adja meg a jelszó értékét a **titkosítási jelszó** mezőben. Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsot használja értékként (lásd fent).
      
      Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg a bizalmas értékeket a konfigurációs fájlokban, vagy a **SSIS paraméterek**, a **Csatlakozáskezelő** vagy a **tulajdonság felülbírálásai** lapokon (lásd alább).
      
      Ha a **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. Újra kell konfigurálnia a csomagot, hogy más védelmi szintet használjon a SSDT vagy a `dtutil` parancssori segédprogramon keresztül. 

   1. A **naplózási szint** beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 
   
   1. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási útvonal** mezőben adja meg az UNC elérési út megadásával a napló mappáját. A naplófájlok **tallózásával** tallózással és kiválaszthatja a log mappát, vagy megadhatja manuálisan az elérési utat. Ha például a naplókat a Azure Files tárolja, a naplózási útvonala a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . A rendszer létrehoz egy almappát ezen az elérési úton minden egyes csomaghoz, amely a SSIS-csomag futtatása tevékenység futtatási AZONOSÍTÓjának végrehajtása után lesz elnevezve, és a naplófájlokat a rendszer öt percenként hozza létre. 
   
   1. Adja meg a napló mappájának eléréséhez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag-hozzáférési hitelesítő adatok értékeit (lásd fent), akkor a **csomag-hozzáférési hitelesítő adatokkal megegyező** jelölőnégyzet bejelölésével újra felhasználhatja őket. Ellenkező esetben adja meg a naplózási hozzáférési hitelesítő adatok értékeit a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Ha például a naplókat Azure Filesban tárolja, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó `<storage account key>` . Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként (lásd fent).
   
Az összes korábban említett UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtár nevének 248 karakternél rövidebbnek kell lennie.

##### <a name="package-location-file-system-project"></a>Csomag helye: fájlrendszer (projekt)

Ha a csomag helyeként kiválasztja a **fájlrendszert (projekt)** , hajtsa végre az alábbi lépéseket.

![Tulajdonságok beállítása a beállítások lapon – fájlrendszer (projekt)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Adja meg a futtatni kívánt csomagot úgy, hogy a projekt `.ispac` **elérési útja** mezőben és a projekthez tartozó csomagfájl UNC elérési útját (a csomaggal együtt) adja meg `.dtsx` .  A projekt tallózásával és kiválasztásával megkeresheti és kiválaszthatja a **Tallózás a file Storage** használatával vagy az elérési utat manuálisan. Ha például a projektet Azure Filesban tárolja, annak elérési útja a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Adja meg a projekthez és a csomagok fájljaihoz való hozzáféréshez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag végrehajtási hitelesítő adataihoz tartozó értékeket ( **Windows-hitelesítéshez**), akkor a **csomagok végrehajtási hitelesítő adataival megegyező** jelölőnégyzet bejelölésével újra felhasználhatja őket. Ellenkező esetben adja meg a csomag hozzáférési hitelesítő adatainak értékét a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Ha például a projektet és a csomagot a Azure Files, a tartományt `Azure` , a felhasználónevet `<storage account name>` és a jelszót tárolja `<storage account key>` . 

      Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` . 

      A rendszer ezeket a hitelesítő adatokat használja arra is, hogy az ugyanazon projektből hivatkozott csomag végrehajtása tevékenységben lévő alárendelt csomagokat is hozzáférhessen. 

   1. Ha a **EncryptAllWithPassword** vagy a **EncryptSensitiveWithPassword** védelmi szintet használta a csomag SSDT-n keresztüli létrehozásakor, írja be a jelszó értékét a **titkosítási jelszó** mezőbe. Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsot használja értékként (lásd fent).
      
      Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg a bizalmas értékeket a **SSIS paraméterek**, a **Csatlakozáskezelő** vagy a tulajdonság- **felülbírálások** lapjain (lásd alább).
      
      Ha a **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. Újra kell konfigurálnia a csomagot, hogy más védelmi szintet használjon a SSDT vagy a `dtutil` parancssori segédprogramon keresztül. 

   1. A **naplózási szint** beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 
   
   1. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási útvonal** mezőben adja meg az UNC elérési út megadásával a napló mappáját. A naplófájlok **tallózásával** tallózással és kiválaszthatja a log mappát, vagy megadhatja manuálisan az elérési utat. Ha például a naplókat a Azure Files tárolja, a naplózási útvonala a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . A rendszer létrehoz egy almappát ezen az elérési úton minden egyes csomaghoz, amely a SSIS-csomag futtatása tevékenység futtatási AZONOSÍTÓjának végrehajtása után lesz elnevezve, és a naplófájlokat a rendszer öt percenként hozza létre. 
   
   1. Adja meg a napló mappájának eléréséhez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag-hozzáférési hitelesítő adatok értékeit (lásd fent), akkor a **csomag-hozzáférési hitelesítő adatokkal megegyező** jelölőnégyzet bejelölésével újra felhasználhatja őket. Ellenkező esetben adja meg a naplózási hozzáférési hitelesítő adatok értékeit a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Ha például a naplókat Azure Filesban tárolja, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó `<storage account key>` . Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként (lásd fent).
   
Az összes korábban említett UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtár nevének 248 karakternél rövidebbnek kell lennie.

##### <a name="package-location-embedded-package"></a>Csomag helye: beágyazott csomag

Ha a csomag helyeként a **beágyazott csomagot** választja, hajtsa végre a következő lépéseket.

![Tulajdonságok beállítása a beállítások lap beágyazott csomagjához](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Húzza át a csomagfájl (a `.dtsx` ) mappát, vagy **töltse fel** egy fájl mappájából a megadott mezőbe. A csomag automatikusan tömörítve és beágyazva lesz a tevékenység hasznos adataiba. A beágyazás után később is **letöltheti** a csomagot szerkesztésre. A beágyazott csomagot úgy is **parametrizálja** , hogy hozzárendeli azt egy olyan folyamat-paraméterhez, amely több tevékenységben is felhasználható, így optimalizálhatja a folyamat adattartalma méretét. A projektfájlok (és) beágyazásával `.ispac` jelenleg nem támogatott, így a beágyazott csomagok nem használhatnak SSIS-paramétereket és-összekötőket a Project szintű hatókörrel.
   
   1. Ha a beágyazott csomag nem minden titkosítva van, és felderítjük, hogy az alkalmazás végrehajtja a csomag végrehajtása (EPT) feladatot, a **csomag végrehajtása** jelölőnégyzet automatikusan ki lesz választva, és a rendszer automatikusan hozzáadja a fájlrendszer elérési útján hivatkozott gyermek csomagokat, így azt is beágyazhatja.
   
      Ha nem tudjuk felderíteni az EPT használatát, manuálisan kell kijelölnie a **csomag végrehajtása feladatot** , és fel kell vennie azokat a gyermek csomagokat, amelyekre a fájlrendszerbeli elérési út alapján hivatkozik, így azt is beágyazhatja. Ha a gyermek csomagok tárolása SQL Server adatbázisban (MSDB) történik, akkor nem ágyazhatja be őket, ezért biztosítania kell, hogy a Azure-SSIS IR hozzáférhessenek a MSDB, hogy azok a SQL Server-referenciáik használatával hozzáférjenek. A projektfájlok (és) beágyazásával `.ispac` jelenleg nem támogatott, ezért nem használhat Project-alapú referenciákat a gyermek csomagjaihoz.
   
   1. Ha a **EncryptAllWithPassword** vagy a **EncryptSensitiveWithPassword** védelmi szintet használta a csomag SSDT-n keresztüli létrehozásakor, írja be a jelszó értékét a **titkosítási jelszó** mezőbe. 
   
      Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsot használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg a bizalmas értékeket a konfigurációs fájlokban, vagy a **SSIS paraméterek**, a **Csatlakozáskezelő** vagy a **tulajdonság felülbírálásai** lapokon (lásd alább).
      
      Ha a **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. Újra kell konfigurálnia a csomagot, hogy más védelmi szintet használjon a SSDT vagy a `dtutil` parancssori segédprogramon keresztül.

   1. A **naplózási szint** beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 
   
   1. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási útvonal** mezőben adja meg az UNC elérési út megadásával a napló mappáját. A naplófájlok **tallózásával** tallózással és kiválaszthatja a log mappát, vagy megadhatja manuálisan az elérési utat. Ha például a naplókat a Azure Files tárolja, a naplózási útvonala a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . A rendszer létrehoz egy almappát ezen az elérési úton minden egyes csomaghoz, amely a SSIS-csomag futtatása tevékenység futtatási AZONOSÍTÓjának végrehajtása után lesz elnevezve, és a naplófájlokat a rendszer öt percenként hozza létre. 
   
   1. Adja meg a naplózási mappa eléréséhez szükséges hitelesítő adatokat a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben megadott értékek megadásával. Ha például a naplókat Azure Filesban tárolja, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó `<storage account key>` . Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként (lásd fent).
   
Az összes korábban említett UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtár nevének 248 karakternél rövidebbnek kell lennie.

##### <a name="package-location-package-store"></a>Csomag helye: Package Store

Ha a csomag helyeként a Package **Store** lehetőséget választja, hajtsa végre a következő lépéseket.

![Tulajdonságok beállítása a beállítások lapon – csomag tároló](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. A **Package Store neve** mezőben válasszon ki egy meglévő, a Azure-SSIS IRhoz csatolt csomagot.

   1. Adja meg a futtatni kívánt csomagot úgy, hogy a `.dtsx` **csomag elérési útja** mezőben megadja annak elérési útját (nélkül) a kiválasztott csomag tárolójából. Ha a kiválasztott csomagkezelő a fájlrendszer/Azure Files felett van, tallózással és kiválasztással kiválaszthatja a csomagot a **fájl tallózása Tallózás** lehetőség kiválasztásával, ellenkező esetben megadhatja az elérési útját a következő formátumban: `<folder name>\<package name>` . Az új csomagokat a [SSIS](/sql/integration-services/service/package-management-ssis-service)(SSMS) használatával is importálhatja a kiválasztott Package SQL Server Management Studio Store-ba. További információ: SSIS- [csomagok kezelése Azure-SSIS IR Package Stores szolgáltatással](./azure-ssis-integration-runtime-package-store.md).

   1. Ha a csomagot külön fájlban konfigurálja, meg kell adnia egy UNC elérési utat a konfigurációs fájlhoz (a) a `.dtsConfig` **konfigurációs elérési út** mezőben. Tallózással és kiválasztással kiválaszthatja a konfigurációt a **file Storage tallózásával** , vagy manuálisan is megadhatja az elérési utat. Ha például Azure Files tárolja a konfigurációt, annak elérési útja a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Jelölje be a **konfigurációs hozzáférési hitelesítő adatok** jelölőnégyzetet, és válassza ki, hogy szeretné-e megadni a konfigurációs fájl külön való eléréséhez szükséges hitelesítő adatokat. Erre akkor van szükség, ha a kiválasztott csomagkezelő a felügyelt Azure SQL-példányon üzemeltetett SQL Server adatbázison (MSDB) felül van, vagy nem tárolja a konfigurációs fájlt.
   
      Ha korábban már megadta a csomag végrehajtási hitelesítő adataihoz tartozó értékeket ( **Windows-hitelesítéshez**), akkor a **csomagok végrehajtási hitelesítő adataival megegyező** jelölőnégyzet bejelölésével újra felhasználhatja őket. Ellenkező esetben adja meg a konfigurációs hozzáférési hitelesítő adatok értékeit a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben. Ha például Azure Filesban tárolja a konfigurációt, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó `<storage account key>` . 

      Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Ha a **EncryptAllWithPassword** vagy a **EncryptSensitiveWithPassword** védelmi szintet használta a csomag SSDT-n keresztüli létrehozásakor, írja be a jelszó értékét a **titkosítási jelszó** mezőbe. Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsot használja értékként (lásd fent).
      
      Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg a bizalmas értékeket a konfigurációs fájlokban, vagy a **SSIS paraméterek**, a **Csatlakozáskezelő** vagy a **tulajdonság felülbírálásai** lapokon (lásd alább).
      
      Ha a **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. Újra kell konfigurálnia a csomagot, hogy más védelmi szintet használjon a SSDT vagy a `dtutil` parancssori segédprogramon keresztül. 

   1. A **naplózási szint** beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 
   
   1. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási útvonal** mezőben adja meg az UNC elérési út megadásával a napló mappáját. A naplófájlok **tallózásával** tallózással és kiválaszthatja a log mappát, vagy megadhatja manuálisan az elérési utat. Ha például a naplókat a Azure Files tárolja, a naplózási útvonala a következő: `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . A rendszer létrehoz egy almappát ezen az elérési úton minden egyes csomaghoz, amely a SSIS-csomag futtatása tevékenység futtatási AZONOSÍTÓjának végrehajtása után lesz elnevezve, és a naplófájlokat a rendszer öt percenként hozza létre. 
   
   1. Adja meg a naplózási mappa eléréséhez szükséges hitelesítő adatokat a **tartomány**, a **Felhasználónév** és a **jelszó** mezőkben megadott értékek megadásával. Ha például a naplókat Azure Filesban tárolja, a tartomány `Azure` , a Felhasználónév `<storage account name>` és a jelszó `<storage account key>` . Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként (lásd fent).
   
Az összes korábban említett UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtár nevének 248 karakternél rövidebbnek kell lennie.

#### <a name="ssis-parameters-tab"></a>SSIS paraméterek lap

A SSIS-csomag végrehajtása **SSIS paraméterek** lapján hajtsa végre a következő lépéseket.

![Tulajdonságok beállítása a SSIS Parameters lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Ha a Azure-SSIS IR fut, a **SSISDB** van kiválasztva a csomag helyeként, és a **Beállítások** lapon lévő **manuális bejegyzések** jelölőnégyzet nincs bejelölve, a kiválasztott PROJEKTben és csomagban lévő meglévő SSIS-paraméterek megjelennek a hozzájuk rendelt értékekhez. Ellenkező esetben megadhatja, hogy az egyes értékek manuálisan legyenek hozzárendelve. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. 
   
   1. Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** való létrehozásakor, a **fájlrendszer (projekt)**, a **beágyazott csomag** vagy a **Package Store** lehetőség van kiválasztva a csomag helyeként, a lapon lévő értékek hozzárendeléséhez újra meg kell adnia a bizalmas paramétereket is. 
   
Ha értékeket rendel hozzá a paraméterekhez, hozzáadhat dinamikus tartalmat kifejezések, függvények, Data Factory rendszerváltozók, valamint Data Factory folyamat paramétereinek vagy változóinak használatával.

Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Csatlakozáskezelő lap

Hajtsa végre a következő lépéseket a SSIS-csomag végrehajtása tevékenységben a **kapcsolatkezelő** lapon.

![Tulajdonságok beállítása a kapcsolatkezelő lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Ha a Azure-SSIS IR fut, a **SSISDB** van kiválasztva a csomag helyeként, és a **Beállítások** lapon lévő **manuális bejegyzések** jelölőnégyzet nincs bejelölve, a kiválasztott projektben és csomagban lévő, a SSISDB származó meglévő kapcsolatkezelő megjelennek az értékek a tulajdonságaihoz való hozzárendeléséhez. Ellenkező esetben megadhatja, hogy egy-egy értéket rendeljen hozzá a tulajdonságaihoz manuálisan. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. 
   
      A Csatlakozáskezelő megfelelő **hatókör**-, **név**-és **tulajdonságnév** beszerzéséhez nyissa meg a csomagot, amely a SSDT-on található. A csomag megnyitása után válassza ki a megfelelő Csatlakozáskezelőt, hogy megjelenjenek a SSDT **Tulajdonságok** ablakának összes tulajdonságának neve és értéke. Ezzel az információval felülbírálhatja a Csatlakozáskezelő-tulajdonságok értékeit futásidőben. 

      ![Csatlakozáskezelő tulajdonságainak beolvasása a SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Például anélkül, hogy módosítaná az eredeti csomagot a SSDT-on, átalakíthatja a helyszíni – helyszíni adatforgalmát, amely SQL Server fut a helyszíni – Felhőbeli adatforgalomra az ADF-ben futó SSIS-integráción keresztül, ha felülbírálja a **ConnectByProxy**-, **ConnectionString**-és **ConnectUsingManagedIdentity** -tulajdonságok értékeit a meglévő Ügyfélkapcsolat-kezelők esetében.
      
      Ezek a futásidejű felülbírálások engedélyezhetik Self-Hosted IR (SSIS) proxyként való használatát a helyszíni adatokhoz való hozzáférés során. lásd: a [konfigurálása a SSIS IR proxyként](./self-hosted-integration-runtime-proxy-ssis.md), valamint Azure SQL Database/felügyelt példányok kapcsolatai a legújabb MSOLEDBSQL illesztőprogrammal, amely lehetővé teszi a Azure Active Directory (HRE) hitelesítését az ADF által felügyelt identitással, lásd: [HRE hitelesítés konfigurálása az OLEDB-kapcsolatok esetében](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication).

      ![Tulajdonságok beállítása a SSDT a kapcsolatkezelő lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** való létrehozásakor, a **fájlrendszer (projekt)**, a **beágyazott csomag** vagy a **Package Store** lehetőség van kiválasztva a csomag helyeként, a lapon lévő értékek hozzárendeléséhez újra be kell írnia a bizalmas kapcsolatkezelő tulajdonságait is. 

Ha értékeket rendel a Csatlakozáskezelő tulajdonságaihoz, a kifejezések, függvények, Data Factory rendszerváltozók és Data Factory folyamat paramétereinek vagy változóinak használatával adhat hozzá dinamikus tartalmat. 

Azt is megteheti, hogy a Azure Key Vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be a mellette lévő **Azure Key Vault** jelölőnégyzetet. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki az értékhez tartozó titkos nevet és verziót. A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkos kulcsot közvetlenül a következő formátumban is megadhatja: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Tulajdonság-felülbírálások lap

Hajtsa végre a következő lépéseket a SSIS-csomag végrehajtása tevékenységének **Tulajdonságok felülbírálása** lapján.

![Tulajdonságok beállítása a tulajdonság-felülbírálások lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Adja meg a kiválasztott csomagban lévő meglévő tulajdonságok elérési útját, ha manuálisan szeretné hozzárendelni az értékeket. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. A felhasználói változó értékének felülbírálásához például adja meg az elérési útját a következő formátumban: `\Package.Variables[User::<variable name>].Value` . 

      A Package tulajdonság megfelelő **elérési útját** a SSDT-on található csomag megnyitásával szerezheti be. A csomag megnyitása után válassza ki a vezérlési folyamat és **konfigurációk** tulajdonságot a SSDT **Tulajdonságok** ablakában. Ezután a configurations ( **konfigurációk** ) tulajdonság melletti három pont (**..**.) gombra kattintva nyissa meg a csomag- **konfigurációk szervezőjét** , amelyet általában a csomag [-telepítési modellben való létrehozásához](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)használ. 

      ![Csomag tulajdonságainak beolvasása az SSDT-konfigurációk tulajdonságból](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      A csomag- **konfigurációk szervezője** lapon jelölje be a csomag konfigurációjának **engedélyezése** jelölőnégyzetet, és a **Hozzáadás...** gombot a **csomag konfigurálása varázsló** megnyitásához. 
      
      A **csomag konfigurációja varázslóban** válassza ki az **XML konfigurációs fájl** elemet a **konfiguráció típusa** legördülő menüből, és a **konfigurációs beállítások megadása közvetlenül** gombra, adja meg a konfigurációs fájl nevét, majd kattintson a **Next >(tovább** ) gombra. 

      ![Csomag tulajdonságainak beolvasása a SSDT-konfigurációk szervezőjének](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Végül válassza ki azt a csomag-tulajdonságokat, amelynek elérési útját és a **következő >** gombot.  Ekkor megtekintheti, másolhatja & beillesztheti a kívánt Package tulajdonság elérési útját, és mentheti azokat a konfigurációs fájlban. Ezzel az információval felülbírálhatja a csomagok tulajdonságainak értékeit futásidőben. 

      ![Csomag tulajdonságainak beolvasása a SSDT-konfiguráció varázslóból](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** való létrehozásakor, a **fájlrendszer (projekt)**, a **beágyazott csomag** vagy a **Package Store** lehetőség van kiválasztva a csomag helyeként, a lapon lévő értékek hozzárendeléséhez újra meg kell adnia a bizalmas csomag tulajdonságait is. 
   
Amikor értékeket rendel a csomag tulajdonságaihoz, hozzáadhat dinamikus tartalmat kifejezések, függvények, Data Factory rendszerváltozók, valamint Data Factory folyamat paramétereinek vagy változóinak használatával.

A konfigurációs fájlokban és a **SSIS paraméterek** lapon hozzárendelt értékek felülbírálása a **Csatlakozáskezelő** vagy a **tulajdonság felülbírálási** lapjaival lehetséges. A **kapcsolatkezelő** lapon megadott értékek felülbírálása a **Tulajdonságok** felülbírálásai lapon is megadható.

A folyamat konfigurációjának ellenőrzéséhez kattintson az **Érvényesítés** elemre az eszköztáron. A folyamat- **ellenőrzési jelentés** bezárásához válassza a elemet **>>** .

A folyamat Data Factory való közzétételéhez válassza az **összes közzététele** lehetőséget. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben elindítja a folyamat futtatását. 

1. A folyamat futtatásának elindításához válassza az **aktiválás** lehetőséget az eszköztáron, majd válassza az **aktiválás most** lehetőséget. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Megtekintheti a folyamat futását és állapotát, valamint egyéb információkat, például a **Futtatás kezdési** idejét. A nézet frissítéséhez válassza a **Frissítés** parancsot.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Csak egy tevékenység fut, mert a folyamat csak egy tevékenységet tartalmaz. Ez a SSIS-csomag végrehajtása tevékenység.

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Futtassa a következő lekérdezést az SQL Server SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomagok végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. A SSISDB végrehajtási AZONOSÍTÓját a folyamat által futtatott tevékenység kimenetében is lekérheti, és az azonosító használatával további részletes végrehajtási naplókat és hibaüzeneteket is megtudhat a SQL Server Management Studio.

   ![A végrehajtás AZONOSÍTÓjának beolvasása.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat beosztása triggerrel

Létrehozhat egy ütemezett triggert is a folyamathoz, hogy a folyamat ütemezésen, például óránként vagy naponta fusson. Példaként tekintse meg a következőt: [create a Factory-Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Csomag futtatása a PowerShell-lel
Ebben a szakaszban a Azure PowerShell használatával hozzon létre egy Data Factory folyamatot egy végrehajtási SSIS csomaggal, amely a SSIS-csomagot futtatja. 

Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésével és konfigurálásával kapcsolatos](/powershell/azure/install-az-ps)részletes útmutató lépéseit követve.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Hozzon létre egy adatgyárat Azure-SSIS IR
Használhat olyan meglévő adatelőállítót, amely már rendelkezik Azure-SSIS IR kiépítve, vagy létrehozhat egy új, Azure-SSIS IRkal rendelkező adatgyárat. Kövesse az oktatóanyag részletes utasításait [: SSIS-csomagok üzembe helyezése az Azure-](./tutorial-deploy-ssis-packages-azure-powershell.md)ban a PowerShell használatával.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel 
Ebben a lépésben létrehoz egy folyamatot a SSIS-csomag végrehajtása tevékenységgel. A tevékenység futtatja a SSIS-csomagot. 

1. Hozzon létre egy nevű JSON `RunSSISPackagePipeline.json` -fájlt a `C:\ADF\RunSSISPackage` mappában az alábbi példához hasonló tartalommal.

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le az Objektumnév, a leírások és az elérési utak, a tulajdonság vagy a paraméterek értékét, a jelszavakat és az egyéb változó értékeket. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   A fájlrendszerben vagy Azure Filesban tárolt csomagok végrehajtásához adja meg a csomag és a naplózási hely tulajdonságait a következőképpen:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   A fájlrendszerben vagy Azure Filesban tárolt projekteken belüli csomagok végrehajtásához adja meg a csomag tartózkodási helyének értékeit a következőképpen:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   A beágyazott csomagok végrehajtásához adja meg a csomag tartózkodási helyének értékeit a következőképpen:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   A Package Stores szolgáltatásban tárolt csomagok végrehajtásához adja meg a csomag és a konfigurációs hely tulajdonságainak értékét a következőképpen:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. A Azure PowerShellban váltson a `C:\ADF\RunSSISPackage` mappára.

3. A folyamat **RunSSISPackagePipeline** létrehozásához futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Itt látható a minta kimenete:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>A folyamat futtatása
A folyamat futtatásához használja a **meghívó-AzDataFactoryV2Pipeline** parancsmagot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja vagy illessze be a következő szkriptet a PowerShell-ablakba, majd kattintson az ENTER gombra. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

A folyamatot a Azure Portal használatával is nyomon követheti. Részletes útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat beosztása triggerrel
Az előző lépésben igény szerint futtatta a folyamatot. Létrehozhat egy ütemezett triggert is, amely a folyamat ütemezett futtatására szolgál, például óránként vagy naponta.

1. Hozzon létre egy nevű JSON-fájlt a `MyTrigger.json` `C:\ADF\RunSSISPackage` mappában az alábbi tartalommal: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. A Azure PowerShellban váltson a `C:\ADF\RunSSISPackage` mappára.
1. Futtassa a **set-AzDataFactoryV2Trigger** parancsmagot, amely létrehozza az triggert. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Alapértelmezés szerint az trigger leállított állapotban van. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. A **Get-AzDataFactoryV2Trigger** parancsmag futtatásával ellenőrizze, hogy elindult-e az trigger. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Futtassa a következő parancsot a következő óra után. Ha például az aktuális időpont 3:25 PM UTC, futtassa a parancsot 4 órakor UTC-kor. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Futtassa a következő lekérdezést az SQL Server SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő blogbejegyzést:
- [Az ETL/ELT-munkafolyamatok modernizálása és kiterjesztése a SSIS-tevékenységekkel Azure Data Factory-folyamatokban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)