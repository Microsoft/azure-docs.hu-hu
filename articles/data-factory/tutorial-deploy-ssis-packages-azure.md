---
title: Az Azure SSIS Integration Runtime kiépítése
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7c439d71806d2deba508ce35131f21ebfbd7a3ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740409"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS integrációs modul kiépítése Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) Azure Data Factory (ADF) való kiépítésének lépéseit ismerteti a Azure Portal használatával. Az Azure-SSIS IR a következőket támogatja:

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök már Azure-kompatibilisek, és tartalmaznak SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) és parancssori segédeszközöket, például a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

A jelen oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

- **Azure SQL Database kiszolgáló (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [SQL Database és az SQL felügyelt példányának összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatcsatorna konfigurálása nélkül szeretné elérni a helyszíni adatait, csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](./create-azure-ssis-integration-runtime.md).

  - Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez. További információ: [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.

  - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy olyan IP-címtartományt, amely tartalmazza az ügyfélszámítógép IP-címét az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listára. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../azure-sql/database/firewall-configure.md).

  - A kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítéssel, vagy az adat-előállító felügyelt identitásával az Azure AD-hitelesítés használatával kapcsolódhat az adatbázis-kiszolgálóhoz. Az utóbbi esetében hozzá kell adnia az adatok előállítójának felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](./create-azure-ssis-integration-runtime.md).

  - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB-példánnyal. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB-példányok használatát.

> [!NOTE]
> Azon Azure-régiók listájáért, amelyekben a Data Factory és a Azure-SSIS IR jelenleg elérhetők, tekintse meg a [Data Factory és a SSIS az IR elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ha az adatgyárat a Azure Portal segítségével szeretné létrehozni, kövesse az [adatfeldolgozó létrehozása a felhasználói felületen](./quickstart-create-data-factory-portal.md#create-a-data-factory)című témakör részletes utasításait. Ha ezt megteszi, válassza a **rögzítés az irányítópulton** lehetőséget, hogy a létrehozása után engedélyezze a gyors hozzáférést. 

Az adatelőállító létrehozása után nyissa meg a Azure Portal áttekintés lapját. Válassza a **szerző & figyelés** csempét, és nyissa meg az **első lépések** lapot egy külön lapon. Itt továbbra is létrehozhatja a Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

### <a name="from-the-data-factory-overview"></a>A Data Factory áttekintése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>A szerzői felhasználói felületen

1. A Azure Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, és válassza a **kapcsolatok** lehetőséget. Ezután váltson az **Integration Runtimes (integrációs** modulok) lapra, ahol megtekintheti a meglévő integrációs modulokat az adatgyárban. 

   ![Meglévő integrációs modulok megtekintésére szolgáló kiválasztások](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Válassza az **új** lehetőséget Azure-SSIS IR létrehozásához és az **Integration Runtime beállítása** panel megnyitásához. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **integrációs modul telepítése** ablaktáblán válassza ki a **meglévő SSIS-csomagok az Azure csempén való végrehajtásához** , majd kattintson a **tovább** gombra.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

Az **Integration Runtime beállítása** ablaktáblán három oldal található, ahol egymás után az általános, a központi telepítés és a speciális beállítások konfigurálhatók.

### <a name="general-settings-page"></a>Általános beállítások lap

Az **Integration Runtime telepítési** paneljének **általános beállítások** oldalán végezze el a következő lépéseket. 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. A **Név** mezőben adja meg az integrációs modul nevét. 

   1. A **Leírás** mezőben adja meg az integrációs modul leírását. 

   1. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon. 

   1. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Válasszon nagy méretű csomópontot (vertikális felskálázás), ha sok nagy számítási igényű vagy memória-igényű csomagot szeretne futtatni. 

   1. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha egyszerre több csomagot szeretne futtatni, válasszon ki egy nagy méretű fürtöt sok csomóponttal. 

   1. A **kiadás/licenc** lapon válassza ki az integrációs modul SQL Server kiadását: standard vagy Enterprise. Válassza a vállalat lehetőséget, ha az integrációs modul speciális funkcióit szeretné használni. 

   1. A **pénz megtakarítása** lehetőségnél válassza a Azure Hybrid Benefit lehetőséget az integrációs futtatókörnyezethez: **Igen** vagy **nem**. Válassza az **Igen** lehetőséget, ha a frissítési garanciával rendelkező saját SQL Server licencét szeretné kihasználni a hibrid használattal járó költségmegtakarítással. 

   1. Kattintson a **Tovább** gombra. 

### <a name="deployment-settings-page"></a>Központi telepítési beállítások lap

Az **Integration Runtime** telepítési panel **központi telepítési beállítások** LAPJÁN lehetősége van a SSISDB létrehozására és a Azure-SSIS IR csomagok tárolására.

#### <a name="creating-ssisdb"></a>SSISDB létrehozása

Ha a csomagokat a SSISDB (projekt-telepítési modell) környezetbe szeretné telepíteni, akkor az **integrációs** modul telepítése ablaktábla **központi telepítési beállítások** lapján válassza ki a **Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett, a projektek/csomagok/környezetek/végrehajtási naplók tárolásához használt SSIS-katalógust (SSISDB)** . Ha a csomagokat olyan fájlrendszerbe, Azure Files vagy SQL Server adatbázisba (MSDB) szeretné telepíteni, amelyet az Azure SQL felügyelt példánya (csomag telepítési modellje) üzemeltet, nem kell létrehoznia SSISDB, és nem kell bejelölnie a jelölőnégyzetet.

Az üzembe helyezési modelltől függetlenül, ha azt szeretné, hogy az Azure SQL felügyelt példánya által üzemeltetett SQL Server Agent a csomagok végrehajtásának előkészítéséhez/bevezetéséhez használja, akkor a SSISDB engedélyezi a jelölőnégyzet bejelölését. További információ: SSIS- [csomagok végrehajtásának ütemezett végrehajtása az Azure SQL felügyelt példány-ügynök használatával](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Ha bejelöli a jelölőnégyzetet, a következő lépésekkel hozhatja létre a saját adatbázis-kiszolgálóját, hogy az Ön nevében létrehozható és kezelhető SSISDB működtessen.

   ![A SSISDB üzembe helyezési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   1. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

   1. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
   
      A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [SQL Database és az SQL felügyelt példányának összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Ha olyan Azure SQL Database-kiszolgálót választ ki, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatközpont konfigurálása nélkül szeretné elérni a helyszíni adatkezelést, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](./create-azure-ssis-integration-runtime.md).

   1. Jelölje be az **Azure ad-hitelesítés használata a felügyelt identitással az ADF** -be jelölőnégyzetet, hogy kiválassza az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez. Választhatja az SQL-hitelesítést vagy az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással.

      Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](./create-azure-ssis-integration-runtime.md).
   
   1. A **rendszergazdai Felhasználónév** mezőben adja meg az adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét a SSISDB üzemeltetéséhez. 

   1. **Rendszergazdai jelszó** esetén adja meg az adatbázis-kiszolgáló SQL-hitelesítési jelszavát a SSISDB üzemeltetéséhez. 

   1. Jelölje be a kettős készenléti **Azure-SSIS Integration Runtime pár SSISDB feladatátvételsel** jelölőnégyzetet egy olyan kettős készenléti Azure SSIS IR-pár konfigurálásához, amely szinkronban működik Azure SQL Database/felügyelt példány feladatátvételi csoporttal az üzletmenet folytonossága és a vész-helyreállítás (BCDR) számára.
   
      Ha bejelöli a jelölőnégyzetet, adjon meg egy nevet az elsődleges és másodlagos Azure-SSIS IRs-nek a **kettős készenléti pair neve** szövegmezőben való azonosításához. Az elsődleges és másodlagos Azure-SSIS IRs létrehozásakor ugyanazt a párokat kell megadnia.

      További információ: [a Azure-SSIS IR konfigurálása a BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. A **katalógus-adatbázis szolgáltatási szintjéhez** válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez. Válassza ki az alapszintű, a standard vagy a prémium szintet, vagy válasszon egy rugalmas készlet nevét.

Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha sikeres, kattintson a **Tovább gombra**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR Package Stores létrehozása

Ha az **integrációs modul telepítése** ablaktábla **központi telepítési beállítások** LAPJÁN szeretné kezelni a MSDB, fájlrendszerbe vagy Azure Filesba (csomag központi telepítési modellbe Azure-SSIS IR) telepített csomagjait, akkor válassza a csomagok létrehozása lehetőséget az **Azure SQL felügyelt példánya által üzemeltetett fájlrendszer/Azure Files/SQL Server adatbázisba (MSDB) telepített csomagjainak kezeléséhez** jelölőnégyzetet.
   
A Azure-SSIS IR Package Store lehetővé teszi a csomagok importálását/exportálását, törlését és futtatását, valamint a csomagok futtatását a [korábbi SSIS](/sql/integration-services/service/package-management-ssis-service)-SSMS hasonló módon. További információ: SSIS- [csomagok kezelése Azure-SSIS IR Package Stores szolgáltatással](./azure-ssis-integration-runtime-package-store.md).
   
Ha bejelöli ezt a jelölőnégyzetet, az **új** lehetőség kiválasztásával több csomagot is hozzáadhat a Azure-SSIS IRhoz. Ezzel szemben az egyik csomagkezelő több Azure-SSIS IRs számára is megosztható.

![A MSDB/File System/Azure Files telepítési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

A **csomag-áruház hozzáadása** panelen hajtsa végre az alábbi lépéseket.
   
   1. A **csomag tárolójának neve** mezőbe írja be a csomag tárolójának nevét. 

   1. A **Package Store társított szolgáltatás** esetében válassza ki a meglévő társított szolgáltatást, amely tárolja a fájlrendszer/Azure Files/Azure SQL felügyelt példányhoz tartozó hozzáférési adatokat, ahol a csomagok telepítve vannak, vagy hozzon létre egy újat az **új** lehetőség kiválasztásával. Az **új társított szolgáltatás** ablaktáblán hajtsa végre a következő lépéseket. 

      > [!NOTE]
      > Az Azure Files eléréséhez használhatja az **Azure file Storage** vagy a **fájlrendszerhez** társított szolgáltatásokat. Ha az **Azure file Storage** társított szolgáltatást használja, Azure-SSIS IR a Package Store jelenleg csak az **alapszintű** (nem a **fiók kulcsát** és az **sas URI**-t) hitelesítési módszert támogatja. Az **Azure file Storage** társított szolgáltatás **alapszintű** hitelesítésének használatához hozzáfűzheti `?feature.upgradeAzureFileStorage=false` Az ADF portál URL-címét a böngészőben. Azt is megteheti, hogy a **fájlrendszerhez** társított szolgáltatást használja a Azure Files eléréséhez. 

      ![A társított szolgáltatások telepítési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. A **név** mezőben adja meg a társított szolgáltatás nevét. 
         
      1. A **Leírás** mezőben adja meg a társított szolgáltatás leírását. 
         
      1. A **Típus mezőben** válassza az **Azure file Storage**, az **Azure SQL felügyelt példány** vagy a **fájlrendszer** lehetőséget.

      1. Az integrációs modulon keresztül figyelmen kívül hagyhatja a **csatlakozást**, mivel mindig a Azure-SSIS IR használjuk a csomagok tárolóinak hozzáférési információinak beolvasására.

      1. Ha az **Azure file Storage** lehetőséget választja, hajtsa végre az alábbi lépéseket. 

         1. A **fiók kiválasztása módszer** esetében válassza **Az Azure-előfizetés** lehetőséget, vagy **adja meg manuálisan**.
         
         1. Ha **Az Azure-előfizetésből** lehetőséget választja, válassza ki a megfelelő **Azure-előfizetést**, a **Storage-fiók nevét** és a **fájlmegosztást**.
            
         1. Ha a **manuális bevitel** lehetőséget választja, adja meg `\\<storage account name>.file.core.windows.net\<file share name>` a **gazdagépet**, `Azure\<storage account name>` a **felhasználónevet** és `<storage account key>` a **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Ha az **Azure SQL felügyelt példányát** választja, hajtsa végre az alábbi lépéseket. 

         1. Válassza ki a **kapcsolódási sztringet** manuálisan, vagy a **Azure Key Vault** , ahol titkosként van tárolva.
         
         1. Ha a **kapcsolatok karakterláncát** választja, hajtsa végre az alábbi lépéseket. 

            1. A **teljes tartománynév** mezőben adja meg az `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` Azure SQL felügyelt példányának magán-vagy nyilvános végpontját. Ha megadja a privát végpontot, a **kapcsolat tesztelése** nem alkalmazható, mert az ADF felhasználói felülete nem tudja elérni.

            1. Az **adatbázis neve** mezőbe írja be a következőt: `msdb` .
               
            1. A **Hitelesítés típusa** beállításnál válassza az **SQL-hitelesítés**, a **felügyelt identitás** vagy az **egyszerű szolgáltatásnév** lehetőséget.

            1. Ha az **SQL-hitelesítés** lehetőséget választja, adja meg a megfelelő **felhasználónevet** és **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

            1. Ha a **felügyelt identitás** lehetőséget választja, adja meg az ADF felügyelt identitás hozzáférését az Azure SQL felügyelt példányához.

            1. Ha az **egyszerű szolgáltatásnév** lehetőséget választja, adja meg a megfelelő **egyszerű szolgáltatásnév** és **egyszerű szolgáltatásnév kulcsát** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Ha a **fájlrendszer** lehetőséget választja, adja meg annak a mappának az UNC elérési útját, ahol a csomagok telepítve vannak a **gazdagéphez**, valamint a megfelelő **felhasználónevet** és **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha sikeres, válassza a **Létrehozás** lehetőséget.

   1. A hozzáadott csomagok tárolói a **központi telepítési beállítások** lapon jelennek meg. Ha el szeretné távolítani őket, jelölje be a jelölőnégyzeteket, majd válassza a **Törlés** lehetőséget.

Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha sikeres, kattintson a **Tovább gombra**.

### <a name="advanced-settings-page"></a>Speciális beállítások lap

Az **Integration Runtime telepítési** paneljének **Speciális beállítások** oldalán végezze el a következő lépéseket. 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **Csomópontok maximális párhuzamos végrehajtásához** válassza ki az Integration Runtime-fürt csomópontjain egyidejűleg futtatandó csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több mag használatával szeretné futtatni a számítási vagy a memória-igényű egyetlen nagyméretű csomagot, válasszon ki egy kis számot. Válassza ki a magas számot, ha egy vagy több kis csomagot szeretne futtatni egyetlen mag használatával. 

   1. Jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet, és válassza ki, hogy szeretné-e hozzáadni a szabványos/expressz egyéni beállításokat a Azure-SSIS IR. További információ: [Azure-SSIS IR egyéni beállítása](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Válassza ki a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz, hogy az ADF bizonyos hálózati erőforrásokat hozzon létre, és opcionálisan saját statikus nyilvános IP-címeit** is megadhatja, ha azt szeretné kiválasztani, hogy csatlakoztatni kívánja-e a Azure-SSIS IRt egy virtuális hálózathoz.

      Válassza ezt a lehetőséget, ha olyan Azure SQL Database kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi hálózathoz szeretne hozzáférni saját üzemeltetésű integrációs modul konfigurálása nélkül. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](./create-azure-ssis-integration-runtime.md). 
   
   1. Jelölje be a **Self-Hosted Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet annak kiválasztásához, hogy az Azure-SSIS IR saját üzemeltetésű integrációs modult szeretne-e konfigurálni. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Válassza a **Folytatás** lehetőséget. 

Az **Integration Runtime telepítési** paneljének **Összefoglalás** lapján tekintse át az összes kiépítési beállítást, könyvjelzővel az ajánlott dokumentációs hivatkozásokat, és válassza a **Befejezés** lehetőséget az integrációs modul létrehozásának megkezdéséhez. 

   > [!NOTE]
   > Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie.
   >
   > Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. 
   > 
   > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). További információ a telepíthető további összetevőkről: [a Azure-SSIS IR egyéni beállításai](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Kapcsolatok ablaktábla

A **Manage** hub **kapcsolatok** ablaktábláján váltson az **Integration Runtimes (integrációs** modulok) lapra, és válassza a **frissítés** lehetőséget. 

   ![Kapcsolatok ablaktábla](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   A Azure-SSIS IR szerkesztéséhez/újrakonfigurálásához válassza ki a nevét. Kiválaszthatja a megfelelő gombokat a Azure-SSIS IR figyelésére/indítására/leállítására/törlésére, automatikusan létrehoz egy ADF-folyamatot a SSIS-Azure-SSIS IR csomag végrehajtásával, és megtekintheti a Azure-SSIS IR JSON-kódját/hasznos adatait.  A Azure-SSIS IR szerkesztése/törlése csak a leállítása után hajtható végre.

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és futtathatja azokat a Azure-SSIS IR az Azure-kompatibilis SSDT vagy SSMS eszközök használatával. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma a következő: `<server name>.database.windows.net` .
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.<dns prefix>.database.windows.net` .
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Ha nem használja a SSISDB-t, a csomagokat telepítheti fájlrendszerbe, Azure Filesba vagy MSDB, amelyet az Azure SQL felügyelt példánya futtat, és a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) parancssori segédeszközök használatával futtathatja azokat a Azure-SSIS IR. 

További információ: [SSIS-projektek/csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Mindkét esetben a telepített csomagokat Azure-SSIS IR is futtathatja a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).

Lásd még a következő SSIS-dokumentációt: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR testreszabása](./how-to-configure-azure-ssis-ir-custom-setup.md)