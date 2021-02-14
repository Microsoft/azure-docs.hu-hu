---
title: SSIS-csomagok végrehajtása a SSDT-ből
description: Ismerje meg, hogyan hajthat végre SSIS-csomagokat az Azure-ban a SSDT-ból.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384928"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-csomagok végrehajtása az Azure-ban a SSDT-ből

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az Azure-kompatibilis SQL Server Integration Services-(SSIS-) projektek SQL Server Data Tools (SSDT) funkcióit ismerteti. Lehetővé teszi a SSIS-csomagok Felhőbeli kompatibilitásának felmérését és Azure-SSIS Integration Runtime (IR) Azure Data Factory (ADF) használatával történő futtatását. Ezzel a szolgáltatással tesztelheti a meglévő csomagokat, mielőtt megemeli & a váltást, vagy áttelepíti őket az Azure-ba, vagy új csomagokat fejleszt az Azure-ban.

Ezzel a funkcióval egy újonnan létrehozott/meglévő Azure-SSIS IR is csatolhat SSIS-projektekhez, majd végrehajthatja a csomagjait.  Támogatjuk a futó csomagok telepítését a Azure SQL Database-kiszolgáló vagy a felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) a projekt-telepítési modellben. Támogatjuk a futó csomagok telepítését is az Azure SQL felügyelt példánya által üzemeltetett fájlrendszer/Azure Files/SQL Server adatbázisba (MSDB), a csomag üzembe helyezési modelljében. 

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához töltse le és telepítse a legújabb SSDT a SSIS projects bővítménnyel a Visual studióhoz (VS) innen [.](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) Azt is megteheti, hogy [innen](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)letöltheti és telepítheti a legújabb SSDT önálló telepítőként.

## <a name="azure-enable-ssis-projects"></a>Azure – SSIS-projektek engedélyezése

### <a name="creating-new-azure-enabled-ssis-projects"></a>Új Azure-kompatibilis SSIS-projektek létrehozása

A SSDT-on új Azure-kompatibilis SSIS-projekteket hozhat létre az **Integration Services Project (Azure-enabled)** sablon használatával.

   ![Új Azure-kompatibilis SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Az Azure-kompatibilis projekt létrehozása után a rendszer felszólítja, hogy Azure Data Factory-ben kapcsolódjon a SSIS.

   ![Azure-SSIS IR-üzenet összekötése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Ha azonnal szeretne csatlakozni a Azure-SSIS IRhoz, további részletekért lásd: [Csatlakozás a Azure-SSIS IRhoz](#connectssisir) . Később is csatlakozhat, ha a SSDT Megoldáskezelő ablakában a jobb gombbal rákattint a projekt csomópontjára, és megnyit egy menüt. Ezután válassza a **Kapcsolódás a SSIS Azure Data Factory** elemet a **SSIS Azure Data Factory** almenüjében.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure – meglévő SSIS-projektek engedélyezése

A meglévő SSIS-projektek esetében az alábbi lépéseket követve engedélyezheti az Azure-t:

1. A SSDT Megoldáskezelő ablakában kattintson a jobb gombbal a projekt csomópontjára a menü felugró menüjében. Ezután válassza ki az **Azure-t támogató** **SSIS Azure Data Factory** almenüben az **Azure-kompatibilis projekt varázsló** elindításához.

   ![Azure – meglévő SSIS-projekt engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. A **Visual Studio konfigurációjának kiválasztása** lapon válassza ki a meglévő vs konfigurációt, hogy alkalmazza a csomag-végrehajtási beállításokat az Azure-ban. Létrehozhat egy újat is, ha még nem tette meg, tekintse meg [az új vs konfiguráció létrehozása](/visualstudio/ide/how-to-create-and-edit-configurations)című témakört. Javasoljuk, hogy a helyi és a felhőalapú környezetekben legalább két különböző VS konfigurációval rendelkezzen a csomagok végrehajtásához, így az Azure-ban engedélyezheti a projektet a felhő konfigurációjával. Ily módon, ha a projekt vagy a csomagok paramétereit konfigurálta, különböző értékeket rendelhet a projekthez vagy a csomag paramétereinek futtatásához a különböző végrehajtási környezetek alapján (akár a helyi gépen, akár az Azure-ban). Lásd például: [csomagok végrehajtási környezetének váltása](#switchenvironment).

   ![Visual Studio-konfiguráció kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure – a meglévő SSIS-projektek engedélyezése megköveteli a célkiszolgáló verziójának beállítását, hogy az Azure-SSIS IR által támogatott legkésőbbi legyen. Azure-SSIS IR jelenleg **SQL Server 2017**-alapú. Győződjön meg arról, hogy a csomagok nem tartalmaznak olyan további összetevőket, amelyeket nem támogat a SQL Server 2017. Győződjön meg arról is, hogy az összes kompatibilis további összetevő is telepítve van a Azure-SSIS IR egyéni telepítéseken keresztül: [a Azure-SSIS IR testreszabása](./how-to-configure-azure-ssis-ir-custom-setup.md). A folytatáshoz kattintson a **tovább** gombra.

   ![A célkiszolgáló verziójának váltása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. A projekt Azure-SSIS IRhoz való csatlakoztatásának befejezéséhez tekintse meg a [Csatlakozás a Azure-SSIS IRhoz](#connectssisir) című témakört.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Azure-kompatibilis projektek összekapcsolása a Azure Data Factory SSIS

Az Azure-kompatibilis projektek ADF-SSIS való csatlakoztatásával feltöltheti a csomagokat Azure Filesba, és futtathatja azokat Azure-SSIS IR. Ezt a következő lépések végrehajtásával teheti meg:

1. Az **ADF bevezetésének SSIS** lapon tekintse át a bevezetést, és a folytatáshoz kattintson a **tovább** gombra.

   ![SSIS az ADF-ben – bevezetés](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Az **SSIS IR kiválasztása az ADF-ben** lapon válassza ki a meglévő ADF-t, és Azure-SSIS IR a csomagok futtatásához. Újakat is létrehozhat, ha nem rendelkezik ilyen lehetőségekkel.
   - A meglévő Azure-SSIS IR kiválasztásához először válassza ki a megfelelő Azure-előfizetést és az ADF-t.
   - Ha kijelöli a meglévő ADF-t, amelynek nincs Azure-SSIS IRa, válassza a **SSIS létrehozása IR** -t, és hozzon létre egy újat az ADF-portálon. A létrehozás után visszatérhet erre a lapra az új Azure-SSIS IR kiválasztásához.
   - Ha olyan meglévő Azure-előfizetést választ ki, amely nem rendelkezik ADF-vel, akkor a **Integration Runtime létrehozási varázsló** elindításához kattintson a **SSIS létrehozása** elemre. A varázslóban megadhatja a kijelölt helyét és előtagját, hogy automatikusan létrehozzon egy új Azure-erőforráscsoportot, Data Factory és SSIS az Ön nevében, a következő mintában: **YourPrefix-RG/DF/IR-YourCreationTime**. A létrehozás után visszatérhet erre a lapra, és kiválaszthatja az új ADF-t és Azure-SSIS IR.

   ![Válassza ki a SSIS IR-t az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Az **Azure Storage kiválasztása** lapon válassza ki a meglévő Azure Storage-fiókot a csomagok Azure Filesba való feltöltéséhez. Ha nem rendelkezik ilyennel, létrehozhat egy újat is.
   - A meglévő Azure Storage-fiók kiválasztásához először válassza ki a megfelelő Azure-előfizetést.
   - Ha ugyanazt az Azure-előfizetést választja, mint az Azure Storage-fiókkal nem rendelkező Azure-SSIS IR, válassza az **Azure Storage létrehozása** gombot. A rendszer automatikusan létrehoz egy újat az Ön nevében ugyanazon a helyen, mint a Azure-SSIS IR, amelyet az Azure-SSIS IR neve és a létrehozási dátum előtagjának kombinálásával nevezünk el. A létrehozás után visszatérhet erre a lapra az új Azure Storage-fiók kiválasztásához.
   - Ha olyan Azure-előfizetést választ ki, amely nem rendelkezik Azure Storage-fiókkal, akkor az **Azure Storage létrehozása** gombra kattintva hozzon létre egy újat a Azure Portal. A létrehozás után visszatérhet erre a lapra az új Azure Storage-fiók kiválasztásához.

   ![Az Azure Storage kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Kattintson a **Csatlakozás** gombra a projekt Azure-SSIS IRhoz való csatlakoztatásának befejezéséhez. A kiválasztott Azure-SSIS IR és az Azure Storage-fiókot a SSDT Megoldáskezelő ablakának **társított Azure-erőforrások** csomópontja alatt jelenítjük meg. Emellett rendszeresen frissíti és megjeleníti a Azure-SSIS IR állapotát. A Azure-SSIS IR kezeléséhez kattintson a jobb gombbal a csomópontra, és válassza ki azt a **Start\Stop\Manage** -elemet, amely az ADF-portálra kerül.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>SSIS-project\packages kiértékelése az Azure-beli végrehajtásokhoz

### <a name="assessing-single-or-multiple-packages"></a>Egy vagy több csomag értékelése

A csomagok Azure-ban való végrehajtása előtt felhasználhatja az esetleges Felhőbeli kompatibilitási problémák felszínét. Ezek közé tartozik az áttelepítési blokkolók és a további információk, amelyekről tisztában kell lennie. 
-  Lehetősége van arra, hogy a projektben egyszerre egy vagy több csomagot is felmérjen.

   ![Csomag értékelése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Projekt felmérése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  Az **értékelési jelentés** ablakában megtalálhatja a felszínre kerülő összes lehetséges Felhőbeli kompatibilitási problémát, amelyek mindegyike a saját részletes leírásával és JAVASLATával SSDT meg. Az értékelési jelentést egy CSV-fájlba is exportálhatja, amely a problémák enyhítése érdekében bárkivel megosztható. 

   ![Értékelési jelentés](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Értékelési szabályok mellőzése

Ha biztos benne, hogy egyes lehetséges Felhőbeli kompatibilitási problémák nem alkalmazhatók, vagy a csomagjaiban megfelelően lettek elhárítva, akkor a rájuk felszínre vonatkozó értékelési szabályokat is letilthatja. Ez csökkenti a következő értékelési jelentések által okozott zajt.
-  Az értékelési szabály letiltási hivatkozásának **beállítása** az értékelési **jelentés** ablakban a SSDT elemre kattintva megnyithatja az **értékelési szabály letiltási beállításainak** ablakát, ahol kiválaszthatja a letiltani kívánt értékelési szabályokat.

   ![Értékelési szabály letiltási beállításai](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Azt is megteheti, hogy a jobb gombbal rákattint a projekt csomópontjára a SSDT Megoldáskezelő ablakában a menü felugró menüjében. Válassza ki az **Azure-kompatibilis beállítások** elemet a **SSIS Azure Data Factory** almenüben a Project Property lapjait tartalmazó ablak felugró ablakban. Válassza a **letiltott értékelési szabályok azonosítói** tulajdonságát az **Azure-kompatibilis beállítások** szakaszban. Végül a három pont (**..**.) gombra kattintva megnyithatja az **értékelési szabály letiltási beállításainak** ablakát, ahol kiválaszthatja a letiltani kívánt értékelési szabályokat.

   ![Azure-kompatibilis beállítások](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Az értékelési szabály letiltásának beállításai az Azure-kompatibilis beállításokon keresztül](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>SSIS-csomagok végrehajtása az Azure-ban

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Az Azure-kompatibilis beállítások konfigurálása

A csomagok Azure-ban való végrehajtása előtt beállíthatja az Azure-ban engedélyezett beállításokat. Engedélyezheti például a Windows-hitelesítést a Azure-SSIS IR a helyszíni/Felhőbeli adattárak eléréséhez a következő lépések végrehajtásával:

1. A SSDT Megoldáskezelő ablakában kattintson a jobb gombbal a projekt csomópontjára a menü felugró menüjében. Ezután válassza ki az **Azure-kompatibilis beállítások** elemet a **SSIS Azure Data Factory** almenüben a Project Property lapjait tartalmazó ablak felugró ablakban.

   ![Azure-kompatibilis beállítások](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Jelölje be a **Windows-hitelesítés engedélyezése** tulajdonságot az **Azure-kompatibilis beállítások** szakaszban, majd válassza a **true (igaz** ) lehetőséget a legördülő menüben. Ezután válassza ki a **Windows-hitelesítés hitelesítő adatai** tulajdonságot, majd válassza ki a három pont (**...**) gombot a **Windows-hitelesítés hitelesítő adatainak** ablakának megjelenítéséhez.

   ![Windows-hitelesítés engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Adja meg a Windows-hitelesítéshez tartozó hitelesítő adatait. A Azure Fileshoz való hozzáféréshez például megadhatja `Azure` a `YourStorageAccountName` tartományt, `YourStorageAccountKey` a **felhasználónevet** és a **jelszót**, illetve a **tartomány**, a Felhasználónév és a jelszó megadását.

   ![Windows-hitelesítés hitelesítő adatai](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Csomagok végrehajtásának indítása

Miután csatlakoztatta az Azure-t támogató projektjeit az ADF-hez való SSIS, a Felhőbeli kompatibilitás kiértékelése és a lehetséges problémák enyhítése érdekében végrehajthatja vagy tesztelheti a csomagokat Azure-SSIS IRon.
-  Kattintson a **Start** gombra a SSDT eszköztárán a menü legördülő listából. Ezután válassza a **végrehajtás az Azure-ban** elemet.

   ![Végrehajtás az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Azt is megteheti, hogy a jobb gombbal rákattint a csomag csomópontjára a SSDT Megoldáskezelő ablakban a menü felugró menüjében. Ezután válassza ki a **csomag végrehajtása az Azure-** elemben lehetőséget.

   ![Csomag végrehajtása az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A csomagok Azure-ban való végrehajtásához futó Azure-SSIS IR szükséges, így ha a Azure-SSIS IR leáll, megnyílik egy párbeszédpanel, amely elindítja. Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz. Miután futtatta a csomagokat az Azure-ban, leállíthatja a Azure-SSIS IR a futó költségeket úgy, hogy a jobb gombbal a csomópontra kattint a SSDT Megoldáskezelő ablakban, és kijelöli azt a **Start\Stop\Manage** elemet, amely az ADF-portálra viszi.

### <a name="using-execute-package-task"></a>Csomag végrehajtása feladat használata

Ha a csomagok a helyi fájlrendszereken tárolt gyermek csomagokra hivatkozó csomagokat tartalmaznak, hajtsa végre az alábbi további lépéseket:

1. Töltse fel a gyermek csomagokat a projektekhez kapcsolódó Azure Storage-fiókba Azure Filesba, és szerezzen be új univerzális elnevezési konvenció (UNC) elérési útját, például: `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. A csomagok elérési útjának cseréje a Fájlkezelőben az új UNC elérési úttal
   - Ha a SSDT-t futtató helyi gép nem fér hozzá az új UNC elérési úthoz, megadhatja azt a file kapcsolatkezelő tulajdonságok paneljén.
   - Azt is megteheti, hogy egy változót használ a fájl elérési útjához, hogy a megfelelő értéket adja hozzá futásidőben

Ha a csomagok olyan csomagokat hajtanak végre, amelyek ugyanabban a projektben lévő gyermek csomagokra hivatkoznak, nincs szükség további lépésekre.

### <a name="switching-package-protection-level"></a>A csomag védelmi szintjének váltás

A SSIS-csomagok Azure-ban való végrehajtása nem támogatja a **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -védelmi szinteket. Következésképpen, ha a csomagjai a használatára vannak konfigurálva, a rendszer átmenetileg átalakítja azokat a **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** védelmi szintjeinek használatával. A titkosítási jelszavakat véletlenszerűen is létrehozjuk, amikor a csomagokat Azure Filesba töltjük fel a Azure-SSIS IR végrehajtásához.

> [!NOTE]
> Ha a csomagok végrehajtják a **EncryptSensitiveWithUserKey** EncryptAllWithUserKey védelmi szintek használatára konfigurált alcsomagokra hivatkozó csomagokat /  , akkor a csomagok végrehajtása előtt manuálisan kell konfigurálnia ezeket a gyermek csomagokat a **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -védelmi szintjének használatára.

Ha a csomagok már úgy vannak konfigurálva, hogy **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** védelmi szinteket használjanak, változatlanok maradnak. Ha a csomagokat Azure Filesba tölti fel a Azure-SSIS IR-ban, a rendszer továbbra is véletlenszerűen generált titkosítási jelszavakat.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Csomagok végrehajtási környezetének váltása

Ha projekt-/csomagjait parametrizálja a projekt-telepítési modellben, több VS konfigurációt is létrehozhat a csomagok végrehajtási környezetének váltásához. Így környezet-specifikus értékeket is hozzárendelhet a projekt/csomag paramétereinek futtatásához. Javasoljuk, hogy a helyi és a felhőalapú környezetekben legalább két különböző VS konfigurációval rendelkezzen a csomagok végrehajtásához, így az Azure-ban is engedélyezheti projektjeit a felhő konfigurációjával. Íme egy lépésenkénti példa a csomagok végrehajtási környezetének váltására a helyi gép és az Azure között:

1. Tegyük fel, hogy a csomag tartalmaz egy fájlrendszer-feladatot, amely beállítja egy fájl attribútumait. Ha a helyi gépen futtatja, a a helyi fájlrendszerben tárolt fájlok attribútumait állítja be. Ha a Azure-SSIS IR futtatja, akkor azt szeretné, hogy a Azure Filesban tárolt fájl attribútumai legyenek beállítva. Először hozzon létre egy karakterlánc típusú **filepath** , és nevezze el a célfájl elérési útjának értékének tárolására.

   ![Csomag paraméterének létrehozása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Ezután a **fájlrendszer Feladatkezelő** ablakának **általános** lapján parametrizálja az **SourceVariable** tulajdonságot a **forrásoldali kapcsolatok** szakaszban a **filepath** Package paraméterrel. 

   ![Parametrizálja](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Alapértelmezés szerint a csomag végrehajtásához a **fejlesztés** nevű helyi környezetben már létezik egy meglévő vs konfiguráció. Hozzon létre egy új, VS konfigurációt a csomagok végrehajtásához az **Azure** nevű felhőalapú környezetben: [új vs konfiguráció létrehozása](/visualstudio/ide/how-to-create-and-edit-configurations), ha még nem tette volna meg.

4. Ha megtekinti a csomag paramétereit, válassza a **Paraméterek hozzáadása konfigurációkhoz** gombot a csomag paramétereinek **kezelése** ablak megnyitásához. Ezután rendeljen hozzá különböző értékeket a célfájl elérési útjához a **filepath** Package paraméterhez a **fejlesztési** és az **Azure** -konfigurációk alatt.

   ![Paraméterek értékének megadása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – a projekt Felhőbeli konfigurálásának engedélyezése: [Azure – meglévő SSIS-projektek engedélyezése](#azureenableproject), ha még nem tette meg. Ezután konfigurálja az Azure-kompatibilis beállításokat, hogy engedélyezze a Windows-hitelesítést a Azure-SSIS IR számára a Azure Files eléréséhez, lásd: az [Azure-kompatibilis beállítások konfigurálása](#azureenabledsettings), ha még nem tette volna meg.

6. Hajtsa végre a csomagot az Azure-ban. A csomag végrehajtási környezetét visszaállíthatja a helyi gépre a **fejlesztési** konfiguráció kiválasztásával.

   ![A Visual Studio konfigurációjának váltása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Csomag konfigurációs fájljának használata

Ha csomag-konfigurációs fájlokat használ a csomag üzembe helyezési modelljében, akkor az adott környezetre jellemző értékeket is hozzárendelhet a csomag tulajdonságaihoz a Futtatás ideje alatt. Ezeket a fájlokat automatikusan feltöltjük a csomagjaiba Azure Files a Azure-SSIS IR végrehajtásához.

### <a name="checking-package-execution-logs"></a>Csomag-végrehajtási naplók ellenőrzése

A csomag végrehajtásának megkezdése után a naplókat a SSDT **folyamatjelző** ablakában formázhatja és jelenítheti meg. A hosszú ideig futó csomagok esetében a naplókat a percek alatt frissítjük. A csomag végrehajtását azonnal megszakíthatja a SSDT eszköztár **Leállítás** gombjára kattintva. A naplófájlok nyers adatát ideiglenesen is megtalálhatja a következő UNC elérési úton: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , de egy nap elteltével megtisztítjuk.

## <a name="current-limitations"></a>Aktuális korlátozások

-  Az Azure-kompatibilis SSDT csak a kereskedelmi/globális Felhőbeli régiókat támogatja, és jelenleg nem támogatja a kormányzati/nemzeti Felhőbeli régiókat.

## <a name="next-steps"></a>Következő lépések

Ha meggyőződött arról, hogy a csomagokat az Azure-ban futtatja a SSDT-ból, üzembe helyezheti és futtathatja őket az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként, lásd: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtható SSIS csomagként](./how-to-invoke-ssis-package-ssis-activity.md).