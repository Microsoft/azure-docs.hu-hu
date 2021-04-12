---
title: Azure SSIS integrációs modul létrehozása Azure Data Factory
description: Ismerje meg, hogyan hozhat létre Azure-SSIS integrációs modult Azure Data Factory, így SSIS-csomagokat helyezhet üzembe és futtathat az Azure-ban.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/09/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: e4f60539e2eb524e95c76814f7527377630349e2
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285241"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure SSIS integrációs modul létrehozása Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) Azure Data Factory (ADF)-ben való üzembe helyezésének lépéseit ismerteti. Az Azure-SSIS IR a következőket támogatja:

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök már Azure-kompatibilisek, és tartalmaznak SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) és parancssori segédeszközöket, például a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

A [kiépítési Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) oktatóanyag azt mutatja be, hogyan hozható létre egy Azure-SSIS IR a Azure Portal vagy a Data Factory alkalmazás használatával. Az oktatóanyag azt is bemutatja, hogyan használhat egy Azure SQL Database-kiszolgálót vagy felügyelt példányt a SSISDB üzemeltetéséhez. Ez a cikk az oktatóanyagon alapul, és leírja, hogyan végezheti el ezeket a választható feladatokat:

- Használjon olyan Azure SQL Database-kiszolgálót, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy a SSISDB üzemeltetéséhez privát végponttal rendelkező felügyelt példánnyal rendelkezik. Előfeltételként a virtuális hálózatokhoz való csatlakozáshoz konfigurálnia kell a virtuális hálózati engedélyeket és a Azure-SSIS IR beállításait.

- Azure Active Directory-(Azure AD-) hitelesítést használhat az adatgyár felügyelt identitásával egy Azure SQL Database-kiszolgálóhoz vagy felügyelt példányhoz való kapcsolódáshoz. Előfeltételként hozzá kell adnia az adatok előállítójának felügyelt identitását olyan adatbázis-felhasználóként, aki létrehozhat egy SSISDB-példányt.

- Csatlakoztassa a Azure-SSIS IRt egy virtuális hálózathoz, vagy konfiguráljon egy saját üzemeltetésű IR-t proxyként a Azure-SSIS IR számára a helyszíni adateléréshez.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure-SSIS IR a Azure Portal, a Azure PowerShell és egy Azure Resource Manager sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbaverziós](https://azure.microsoft.com/pricing/free-trial/) fiókot is.

- **Azure SQL Database kiszolgáló vagy SQL felügyelt példánya (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval vagy felügyelt példánnyal, hozzon létre egyet a Azure Portal az első lépések megkezdése előtt. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Azt javasoljuk, hogy hozza létre az adatbázis-kiszolgálót vagy a felügyelt példányt ugyanabban az Azure-régióban, mint az Integration Runtime. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. A SQL Database és az SQL felügyelt példányának a SSISDB gazdagépre való kiválasztásával kapcsolatos útmutatásért tekintse meg a jelen cikk [SQL Database és SQL felügyelt példányának összehasonlítása](#comparison-of-sql-database-and-sql-managed-instance) című szakaszát. 
  
    Ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező SQL-alapú felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi hálózathoz szeretne hozzáférni a saját üzemeltetésű integrációs modul konfigurálása nélkül, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy, a SSISDB gazdagéphez privát végponttal rendelkező SQL felügyelt példánnyal rendelkezik. További információ: [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.

  - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy olyan IP-címtartományt, amely tartalmazza az ügyfélszámítógép IP-címét az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listára. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../azure-sql/database/firewall-configure.md).

  - A kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítéssel, vagy az adat-előállító felügyelt identitásával az Azure AD-hitelesítés használatával kapcsolódhat az adatbázis-kiszolgálóhoz. Az utóbbi esetében hozzá kell adnia az adatok előállítójának felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](./enable-aad-authentication-azure-ssis-ir.md).

  - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB-példánnyal. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB-példányok használatát.

- **Azure Resource Manager virtuális hálózat (nem kötelező)**. Ha a következő feltételek legalább egyike teljesül, rendelkeznie kell egy Azure Resource Manager virtuális hálózattal:

  - A SSISDB egy olyan Azure SQL Database-kiszolgálón üzemelteti, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy privát végponttal rendelkező felügyelt példánnyal rendelkezik.

  - Helyi adattárakhoz szeretne csatlakozni a Azure-SSIS IR futó SSIS-csomagokból a saját üzemeltetésű integrációs modul konfigurálása nélkül.

- **Azure PowerShell (nem kötelező)**. Kövesse a [Azure PowerShell telepítésének és konfigurálásának](/powershell/azure/install-az-ps)lépéseit, ha PowerShell-parancsfájlt szeretne futtatni a Azure-SSIS IR kiépítéséhez.

### <a name="regional-support"></a>Regionális támogatás

Azon Azure-régiók listájáért, amelyekben Data Factory és Azure-SSIS IR elérhetők, tekintse meg a [Data Factory és a SSIS az IR elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>SQL Database és az SQL felügyelt példányának összehasonlítása

Az alábbi táblázat összehasonlítja egy Azure SQL Database-kiszolgáló és az SQL felügyelt példány bizonyos funkcióit, amelyek az Azure-SSIR IR-hez kapcsolódnak:

| Szolgáltatás | SQL Database| SQL felügyelt példány |
|---------|--------------|------------------|
| **Ütemezés** | A SQL Server Agent nem érhető el.<br/><br/>Lásd: [csomagok végrehajtásának ütemezett Data Factory folyamata](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| A felügyelt példány ügynöke elérhető. |
| **Hitelesítés** | Létrehozhat egy SSISDB-példányt egy olyan tárolt adatbázis-felhasználóval, aki az adat-előállító felügyelt identitásával rendelkező Azure AD-csoportot az **db_owner** szerepkör tagjaként.<br/><br/>Lásd: az [Azure ad-hitelesítés engedélyezése SSISDB létrehozásához Azure SQL Database kiszolgálón](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Létrehozhat egy SSISDB-példányt egy olyan tárolt adatbázis-felhasználóval, aki az adatelőállító felügyelt identitását képviseli. <br/><br/>Lásd: az [Azure ad-hitelesítés engedélyezése SSISDB létrehozásához az Azure SQL felügyelt példányában](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Szolgáltatási szint** | Amikor létrehoz egy Azure-SSIS IR a Azure SQL Database-kiszolgálóval, kiválaszthatja a SSISDB szolgáltatási szintjét. Több szolgáltatási szint is rendelkezésre áll. | Ha felügyelt példánnyal hoz létre Azure-SSIS IR, nem választhatja ki a SSISDB szolgáltatási szintjét. A felügyelt példány összes adatbázisa ugyanazt az erőforrást használja, mint a példány. |
| **Virtuális hálózat** | Ha olyan Azure SQL Database kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal rendelkezik, az Azure-SSIS IR csatlakozhat egy Azure Resource Manager virtuális hálózathoz. | Ha privát végponttal felügyelt példányt használ, az Azure-SSIS IR csatlakozhat egy Azure Resource Manager virtuális hálózathoz. A virtuális hálózatra akkor van szükség, ha nem engedélyez nyilvános végpontot a felügyelt példány számára.<br/><br/>Ha a Azure-SSIS IR a felügyelt példányhoz tartozó virtuális hálózathoz csatlakoztatja, győződjön meg arról, hogy a Azure-SSIS IR a felügyelt példánytól eltérő alhálózaton található. Ha a Azure-SSIS IR egy másik virtuális hálózathoz csatlakoztatja a felügyelt példányból, a virtuális hálózat vagy a hálózat – hálózat közötti kapcsolatot javasoljuk. Lásd: [az alkalmazás összekötése egy Azure SQL Database felügyelt példányhoz](../azure-sql/managed-instance/connect-application-instance.md). |
| **Elosztott tranzakciók** | Ez a funkció rugalmas tranzakciókkal támogatott. A Microsoft Distributed Transaction Coordinator (MSDTC) tranzakciói nem támogatottak. Ha a SSIS-csomagok az MSDTC használatával koordinálják az elosztott tranzakciókat, érdemes lehet áttelepíteni a Azure SQL Database rugalmas tranzakcióit. További információ: [Elosztott tranzakciók felhőalapú adatbázisok között](../azure-sql/database/elastic-transactions-overview.md). | Nem támogatott. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Integrációs modul létrehozása a Azure Portal használatával

Ebben a szakaszban a Azure-SSIS IR létrehozásához a Azure Portal, konkrétan a Data Factory felhasználói felületét (UI) vagy alkalmazást kell használnia.

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ha az adatgyárat a Azure Portal segítségével szeretné létrehozni, kövesse az [adatfeldolgozó létrehozása a felhasználói felületen](./quickstart-create-data-factory-portal.md#create-a-data-factory)című témakör részletes utasításait. Ha ezt megteszi, válassza a **rögzítés az irányítópulton** lehetőséget, hogy a létrehozása után engedélyezze a gyors hozzáférést. 

Az adatelőállító létrehozása után nyissa meg a Azure Portal áttekintés lapját. Válassza a **szerző & monitor** csempét, hogy megnyissa az első **lépések** oldalt egy külön lapon. Itt továbbra is létrehozhatja a Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

Az **első lépések** lapon válassza a **SSIS-integráció konfigurálása** csempét az **Integration Runtime beállítása** panel megnyitásához.

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Az **Integration Runtime beállítása** ablaktáblán három oldal található, ahol egymás után az általános, a központi telepítés és a speciális beállítások konfigurálhatók.

#### <a name="general-settings-page"></a>Általános beállítások lap

Az **Integration Runtime telepítési** paneljének **általános beállítások** oldalán végezze el a következő lépéseket.

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. A **Név** mezőben adja meg az integrációs modul nevét.

   2. A **Leírás** mezőben adja meg az integrációs modul leírását.

   3. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon.

   4. A **csomópont mérete** beállításnál válassza ki a csomópont méretét az Integration Runtime-fürtben. Csak a támogatott csomópontméretek jelennek meg. Válasszon nagy méretű csomópontot (vertikális felskálázás), ha sok nagy számítási igényű vagy memória-igényű csomagot szeretne futtatni.
   > [!NOTE]
   > Ha [számítási elkülönítésre](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)van szüksége, válassza ki a **Standard_E64i_v3** csomópont méretét. Ez a csomóponti méret a teljes fizikai gazdagépet használó elkülönített virtuális gépeket jelöli, és biztosítja a szükséges elkülönítési szintet bizonyos munkaterhelések, például az Egyesült Államok védelmi Minisztériuma 5 (IL5) számítási feladataihoz.
   
   5. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha egyszerre több csomagot szeretne futtatni, válasszon ki egy nagy méretű fürtöt sok csomóponttal.

   6. A **kiadás/licenc** lapon válassza ki az integrációs modul SQL Server kiadását: standard vagy Enterprise. Válassza a vállalat lehetőséget, ha az integrációs modul speciális funkcióit szeretné használni.

   7. A **pénz megtakarítása** lehetőségnél válassza a Azure Hybrid Benefit lehetőséget az integrációs futtatókörnyezethez: **Igen** vagy **nem**. Válassza az **Igen** lehetőséget, ha a frissítési garanciával rendelkező saját SQL Server licencét szeretné kihasználni a hibrid használattal járó költségmegtakarítással.

   8. Válassza a **Folytatás** lehetőséget.

#### <a name="deployment-settings-page"></a>Központi telepítési beállítások lap

Az **Integration Runtime** telepítési panel **központi telepítési beállítások** LAPJÁN lehetősége van a SSISDB létrehozására és a Azure-SSIS IR csomagok tárolására.

##### <a name="creating-ssisdb"></a>SSISDB létrehozása

Ha a csomagokat a SSISDB (projekt-telepítési modell) környezetbe szeretné telepíteni, akkor az **integrációs** modul telepítése ablaktábla **központi telepítési beállítások** lapján válassza ki a **Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett, a projektek/csomagok/környezetek/végrehajtási naplók tárolásához használt SSIS-katalógust (SSISDB)** . Ha a csomagokat olyan fájlrendszerbe, Azure Files vagy SQL Server adatbázisba (MSDB) szeretné telepíteni, amelyet az Azure SQL felügyelt példánya (csomag telepítési modellje) üzemeltet, nem kell létrehoznia SSISDB, és nem kell bejelölnie a jelölőnégyzetet.

Az üzembe helyezési modelltől függetlenül, ha azt szeretné, hogy az Azure SQL felügyelt példánya által üzemeltetett SQL Server Agent a csomagok végrehajtásának előkészítéséhez/bevezetéséhez használja, akkor a SSISDB engedélyezi a jelölőnégyzet bejelölését. További információ: SSIS- [csomagok végrehajtásának ütemezett végrehajtása az Azure SQL felügyelt példány-ügynök használatával](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Ha bejelöli a jelölőnégyzetet, a következő lépésekkel hozhatja létre a saját adatbázis-kiszolgálóját, hogy az Ön nevében létrehozható és kezelhető SSISDB működtessen.

   ![A SSISDB üzembe helyezési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   1. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

   1. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
   
      A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [SQL Database és az SQL felügyelt példányának összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Ha olyan Azure SQL Database-kiszolgálót választ ki, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatközpont konfigurálása nélkül szeretné elérni a helyszíni adatkezelést, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md).

   1. Jelölje be az **Azure ad-hitelesítés használata a felügyelt identitással az ADF** -be jelölőnégyzetet, hogy kiválassza az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez. Választhatja az SQL-hitelesítést vagy az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással.

      Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](./enable-aad-authentication-azure-ssis-ir.md).
   
   1. A **rendszergazdai Felhasználónév** mezőben adja meg az adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét a SSISDB üzemeltetéséhez. 

   1. **Rendszergazdai jelszó** esetén adja meg az adatbázis-kiszolgáló SQL-hitelesítési jelszavát a SSISDB üzemeltetéséhez. 

   1. Jelölje be a kettős készenléti **Azure-SSIS Integration Runtime pár SSISDB feladatátvételsel** jelölőnégyzetet egy olyan kettős készenléti Azure SSIS IR-pár konfigurálásához, amely szinkronban működik Azure SQL Database/felügyelt példány feladatátvételi csoporttal az üzletmenet folytonossága és a vész-helyreállítás (BCDR) számára.
   
      Ha bejelöli a jelölőnégyzetet, adjon meg egy nevet az elsődleges és másodlagos Azure-SSIS IRs-nek a **kettős készenléti pair neve** szövegmezőben való azonosításához. Az elsődleges és másodlagos Azure-SSIS IRs létrehozásakor ugyanazt a párokat kell megadnia.

      További információ: [a Azure-SSIS IR konfigurálása a BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. A **katalógus-adatbázis szolgáltatási szintjéhez** válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez. Válassza ki az alapszintű, a standard vagy a prémium szintet, vagy válasszon egy rugalmas készlet nevét.

Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha ez sikeres, válassza a **Folytatás** lehetőséget.

> [!NOTE]
   > Ha Azure SQL Database-kiszolgálót használ a SSISDB üzemeltetéséhez, a rendszer alapértelmezés szerint az adatokat a Geo-redundáns tárolóban tárolja a biztonsági mentésekhez. Ha nem szeretné, hogy az adatai más régiókban legyenek replikálva, kövesse az utasításokat a [biztonsági mentési tár redundancia konfigurálásához a PowerShell használatával](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR Package Stores létrehozása

Ha az **integrációs modul telepítése** ablaktábla **központi telepítési beállítások** LAPJÁN szeretné kezelni a MSDB, fájlrendszerbe vagy Azure Filesba (csomag központi telepítési modellbe Azure-SSIS IR) telepített csomagjait, akkor válassza a csomagok létrehozása lehetőséget az **Azure SQL felügyelt példánya által üzemeltetett fájlrendszer/Azure Files/SQL Server adatbázisba (MSDB) telepített csomagjainak kezeléséhez** jelölőnégyzetet.
   
A Azure-SSIS IR Package Store lehetővé teszi a csomagok importálását/exportálását, törlését és futtatását, valamint a csomagok futtatását a [korábbi SSIS](/sql/integration-services/service/package-management-ssis-service)-SSMS hasonló módon. További információ: SSIS- [csomagok kezelése Azure-SSIS IR Package Stores szolgáltatással](./azure-ssis-integration-runtime-package-store.md).
   
Ha bejelöli ezt a jelölőnégyzetet, az **új** lehetőség kiválasztásával több csomagot is hozzáadhat a Azure-SSIS IRhoz. Ezzel szemben az egyik csomagkezelő több Azure-SSIS IRs számára is megosztható.

![A MSDB/File System/Azure Files telepítési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

A **csomag-áruház hozzáadása** panelen hajtsa végre az alábbi lépéseket.
   
   1. A **csomag tárolójának neve** mezőbe írja be a csomag tárolójának nevét. 

   1. A **Package Store társított szolgáltatás** esetében válassza ki a meglévő társított szolgáltatást, amely tárolja a fájlrendszer/Azure Files/Azure SQL felügyelt példányhoz tartozó hozzáférési adatokat, ahol a csomagok telepítve vannak, vagy hozzon létre egy újat az **új** lehetőség kiválasztásával. Az **új társított szolgáltatás** ablaktáblán hajtsa végre a következő lépéseket.
   
      > [!NOTE]
      > Az Azure Files eléréséhez használhatja az **Azure file Storage** vagy a **fájlrendszerhez** társított szolgáltatásokat. Ha az **Azure file Storage** társított szolgáltatást használja, Azure-SSIS IR a Package Store jelenleg csak az **alapszintű** (nem a **fiók kulcsát** és az **sas URI**-t) hitelesítési módszert támogatja.  

      ![A társított szolgáltatások telepítési beállításai](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. A **név** mezőben adja meg a társított szolgáltatás nevét. 
         
      1. A **Leírás** mezőben adja meg a társított szolgáltatás leírását. 
         
      1. A **Típus mezőben** válassza az **Azure file Storage**, az **Azure SQL felügyelt példány** vagy a **fájlrendszer** lehetőséget.

      1. Az integrációs modulon keresztül figyelmen kívül hagyhatja a **csatlakozást**, mivel mindig a Azure-SSIS IR használjuk a csomagok tárolóinak hozzáférési információinak beolvasására.

      1. Ha az **Azure file Storage** lehetőséget választja, a **hitelesítési módszerhez** válassza az **alapszintű** lehetőséget, majd hajtsa végre a következő lépéseket. 

         1. A **fiók kiválasztása módszer** esetében válassza **Az Azure-előfizetés** lehetőséget, vagy **adja meg manuálisan**.
         
         1. Ha **Az Azure-előfizetésből** lehetőséget választja, válassza ki a megfelelő **Azure-előfizetést**, a **Storage-fiók nevét** és a **fájlmegosztást**.
            
         1. Ha a **manuális bevitel** lehetőséget választja, adja meg `\\<storage account name>.file.core.windows.net\<file share name>` a **gazdagépet**, `Azure\<storage account name>` a **felhasználónevet** és `<storage account key>` a **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Ha az **Azure SQL felügyelt példányát** választja, hajtsa végre az alábbi lépéseket. 

         1. Válassza ki a **kapcsolatok karakterláncát** , vagy a **Azure Key Vault** , ahol titkosként van tárolva.

         1. Ha a **kapcsolatok karakterláncát** választja, hajtsa végre az alábbi lépéseket. 
             1. Ha az **Azure-előfizetés** lehetőséget választja, válassza ki a megfelelő **Azure-előfizetést**, a **kiszolgáló nevét**, a **végpont típusát** **és az** **adatbázis nevét**. Ha a **manuális bevitelt** választja, hajtsa végre az alábbi lépéseket. 
                1.  A **teljes tartománynév** mezőben adja meg az `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` Azure SQL felügyelt példányának magán-vagy nyilvános végpontját. Ha megadja a privát végpontot, a **kapcsolat tesztelése** nem alkalmazható, mert az ADF felhasználói felülete nem tudja elérni.

                1. Az **adatbázis neve** mezőbe írja be a következőt: `msdb` .

            1. A **Hitelesítés típusa** beállításnál válassza az **SQL-hitelesítés**, a **felügyelt identitás** vagy az **egyszerű szolgáltatásnév** lehetőséget.

                - Ha az **SQL-hitelesítés** lehetőséget választja, adja meg a megfelelő **felhasználónevet** és **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

                -  Ha a **felügyelt identitás** lehetőséget választja, adja meg az ADF felügyelt identitás hozzáférését az Azure SQL felügyelt példányához.

                - Ha az **egyszerű szolgáltatásnév** lehetőséget választja, adja meg a megfelelő **egyszerű szolgáltatásnév** és **egyszerű szolgáltatásnév kulcsát** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Ha a **fájlrendszer** lehetőséget választja, adja meg annak a mappának az UNC elérési útját, ahol a csomagok telepítve vannak a **gazdagéphez**, valamint a megfelelő **felhasználónevet** és **jelszót** , vagy válassza ki a **Azure Key Vault** , ahol titkosként van tárolva.

      1. Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha sikeres, válassza a **Létrehozás** lehetőséget.

   1. A hozzáadott csomagok tárolói a **központi telepítési beállítások** lapon jelennek meg. Ha el szeretné távolítani őket, jelölje be a jelölőnégyzeteket, majd válassza a **Törlés** lehetőséget.

Jelölje be a **Kapcsolódás tesztelése** , ha alkalmazható, és ha ez sikeres, válassza a **Folytatás** lehetőséget.

#### <a name="advanced-settings-page"></a>Speciális beállítások lap

Az **Integration Runtime telepítési** paneljének **Speciális beállítások** oldalán végezze el a következő lépéseket.

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **Csomópontok maximális párhuzamos végrehajtásához** válassza ki az Integration Runtime-fürt csomópontjain egyidejűleg futtatandó csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több mag használatával szeretné futtatni a számítási vagy a memória-igényű egyetlen nagyméretű csomagot, válasszon ki egy kis számot. Válassza ki a magas számot, ha egy vagy több kis csomagot szeretne futtatni egyetlen mag használatával.

   1. Jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet, és válassza ki, hogy szeretné-e hozzáadni a szabványos/expressz egyéni beállításokat a Azure-SSIS IR. További információ: [Azure-SSIS IR egyéni beállítása](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Speciális beállítások egyéni telepítésekkel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Az **egyéni telepítési tároló sas URI azonosítójának** megadásával adja meg a tároló sas URI-ját, ahol a parancsfájlok és a társított fájlok szabványos egyéni telepítésekhez vannak tárolva.

      1. Az **expressz egyéni telepítéshez** válassza az **új** lehetőséget az **expressz egyéni telepítés hozzáadása** panel megnyitásához, majd válassza ki az **expressz egyéni telepítés típusa** legördülő menü bármelyik típusát, például futtassa a **cmdkey parancsot**, **adja hozzá a környezeti változót**, **telepítse a licencelt összetevőt** stb.

         Ha bejelöli a **licencelt összetevő** típusának telepítése lehetőséget, akkor az **összetevő neve** legördülő menüben kiválaszthatja az ISV-partnereinktől származó összes integrált összetevőt, és ha szükséges, megadhatja a termék licencének kulcsát, és feltöltheti a tőlük vásárolt terméket a **licenckulcs** / **licenc fájl** mezőjébe.
  
         A hozzáadott expressz egyéni telepítések a **Speciális beállítások** lapon jelennek meg. Az eltávolításhoz jelölje be a jelölőnégyzeteket, majd kattintson a **Törlés** lehetőségre.

   1. Válassza ki a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz való csatlakozáshoz, az ADF engedélyezése bizonyos hálózati erőforrások létrehozásához, és ha kívánja, a saját statikus nyilvános IP-címei** jelölőnégyzet bejelölésével adja meg, hogy szeretne-e csatlakozni az integrációs modulhoz egy virtuális hálózathoz. 

      Jelölje be, ha olyan Azure SQL Database kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyszíni adatforrásokhoz (azaz a SSIS-csomagokban lévő helyszíni adatforrásokhoz vagy célhelyekhez) szeretne hozzáférni a saját üzemeltetésű integrációs modul konfigurálása nélkül. További információ: [csatlakozás Azure-SSIS IR egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Az **előfizetés** mezőben válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

      1. A **hely** esetében az integrációs modul ugyanazon helye van kiválasztva.

      1. A **Típus mezőben** válassza ki a virtuális hálózat típusát: klasszikus vagy Azure Resource Manager. Javasoljuk, hogy válasszon ki egy Azure Resource Manager virtuális hálózatot, mert a klasszikus virtuális hálózatok hamarosan elavulttá válnak.

      1. A **VNet neve** mezőben válassza ki a virtuális hálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal vagy a felügyelt példányokkal együtt, a SSISDB üzemeltetéséhez használt Azure SQL Database-kiszolgáló esetében azonosnak kell lennie. Vagy a helyszíni hálózathoz csatlakoztatva kell lennie. Ellenkező esetben bármely virtuális hálózat lehet a saját statikus nyilvános IP-címeinek használata Azure-SSIS IR számára.

      1. Az **alhálózat neve** mezőben válassza ki a virtuális hálózatához tartozó alhálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal a SSISDB üzemeltetéséhez használt Azure SQL Database-kiszolgáló esetében azonosnak kell lennie. Vagy más alhálózatnak kell lennie, amelyet a felügyelt példányhoz használ a SSISDB üzemeltetésére szolgáló privát végponttal. Ellenkező esetben bármilyen alhálózat lehet a saját statikus nyilvános IP-címeinek Azure-SSIS IR számára.

      1. Jelölje be a **statikus nyilvános IP-címek** bekapcsolása a Azure-SSIS Integration Runtime jelölőnégyzetet, és válassza ki, hogy a saját statikus nyilvános IP-címeit szeretné-e használni a Azure-SSIS IRhoz, így engedélyezheti azokat a tűzfalon az adatforrások számára.

         Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

         1. Az **első statikus nyilvános IP-cím** mezőben válassza ki az első statikus nyilvános IP-címet, amely megfelel a Azure-SSIS IR követelményeinek. Ha nincs, kattintson az új hivatkozás **létrehozása** lehetőségre statikus nyilvános IP-címek létrehozásához Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.
      
         1. A **második statikus nyilvános IP-cím** mezőben válassza ki a második statikus nyilvános IP-címet, amely megfelel a Azure-SSIS IR követelményeinek. Ha nincs, kattintson az új hivatkozás **létrehozása** lehetőségre statikus nyilvános IP-címek létrehozásához Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.

   1. Jelölje be a **Self-Hosted Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet annak kiválasztásához, hogy az Azure-SSIS IR saját üzemeltetésű integrációs modult szeretne-e konfigurálni. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként](./self-hosted-integration-runtime-proxy-ssis.md). 

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Saját üzemeltetésű **Integration Runtime** esetén válassza ki a meglévő, saját üzemeltetésű IR-t Azure-SSIS IR-proxyként.

      1. Az **átmeneti tároláshoz társított szolgáltatás** esetében válassza ki a meglévő Azure Blob Storage-beli társított szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

      1. Az **előkészítési útvonal** mezőben adja meg a BLOB-tárolót a kiválasztott Azure Blob Storage-fiókban, vagy hagyja üresen, hogy az alapértelmezett beállítást használja az átmeneti tároláshoz.

   1. Válassza a **VNet érvényesítése**  >  **tovább** lehetőséget. 

Az **Összefoglalás** szakaszban tekintse át az összes kiépítési beállítást, könyvjelzőt az ajánlott dokumentációs hivatkozásokkal, és válassza a **Befejezés** lehetőséget az integrációs modul létrehozásának megkezdéséhez.

   > [!NOTE]
   > Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
   >
   > Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
   > 
   > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). További információ a telepíthető további összetevőkről: [a Azure-SSIS IR egyéni beállításai](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Kapcsolatok ablaktábla

A **Manage** hub **kapcsolatok** ablaktábláján váltson az **Integration Runtimes (integrációs** modulok) lapra, és válassza a **frissítés** lehetőséget. 

   ![Kapcsolatok ablaktábla](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   A Azure-SSIS IR szerkesztéséhez/újrakonfigurálásához válassza ki a nevét. Kiválaszthatja a megfelelő gombokat a Azure-SSIS IR figyelésére/indítására/leállítására/törlésére, automatikusan létrehoz egy ADF-folyamatot a SSIS-Azure-SSIS IR csomag végrehajtásával, és megtekintheti a Azure-SSIS IR JSON-kódját/hasznos adatait.  A Azure-SSIS IR szerkesztése/törlése csak a leállítása után hajtható végre.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. A Azure Data Factory felhasználói felületen váltson a **kezelés** lapra, és váltson a **kapcsolatok** ablaktábla **Integration Runtimes (integrációs** modulok) lapjára, ahol megtekintheti a meglévő integrációs modulokat az adatgyárban.

   ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Új Azure-SSIS IR létrehozásához válassza az **új** lehetőséget, majd nyissa meg az **Integration Runtime beállítása** ablaktáblát.

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **integrációs modul telepítése** ablaktáblán válassza ki a **meglévő SSIS-csomagok az Azure csempén való végrehajtásához** , majd válassza a **Folytatás** lehetőséget.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS IR beállításának hátralévő lépéseiért tekintse meg az [Azure SSIS Integration Runtime kiépítése](#provision-an-azure-ssis-integration-runtime) című szakaszt.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Integrációs modul létrehozása Azure PowerShell használatával

Ebben a szakaszban a Azure PowerShell használatával hoz létre egy Azure-SSIS IR.

### <a name="create-variables"></a>Változók létrehozása

Másolja és illessze be az alábbi szkriptet. Határozza meg a változók értékeit. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Jelentkezzen be, és válasszon egy előfizetést

Adja hozzá a következő szkriptet a bejelentkezéshez, és válassza ki az Azure-előfizetését.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Adatbázis-kiszolgálóval létesített kapcsolódás ellenőrzése

Adja hozzá a következő szkriptet a Azure SQL Database-kiszolgáló vagy a felügyelt példány ellenőrzéséhez.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

Adja hozzá a következő szkriptet a virtuális hálózati engedélyek és beállítások automatikus konfigurálásához az Azure-SSIS Integration runtimehez való csatlakozáshoz.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása

Futtassa az alábbi parancsokat egy olyan Azure SSIS integrációs modul létrehozásához, amely SSIS-csomagokat futtat az Azure-ban.

Ha nem használja a SSISDB, kihagyhatja a `CatalogServerEndpoint` , a `CatalogPricingTier` és a `CatalogAdminCredential` paramétereket.

Ha nem használ olyan Azure SQL Database-kiszolgálót, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy a helyszíni adatokhoz való hozzáférést igényel, kihagyhatja a `VNetId` és a `Subnet` paramétereket, vagy üres értékeket adhat hozzájuk. Akkor is kihagyhatja őket, ha saját üzemeltetésű integrációs modult konfigurál a Azure-SSIS IR számára a helyszíni adateléréshez. Ellenkező esetben nem hagyhatja ki őket, és a virtuális hálózat konfigurációjától érvényes értékeket kell átadnia. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md).

Ha felügyelt példányt használ a SSISDB üzemeltetéséhez, kihagyhatja a `CatalogPricingTier` paramétert, vagy üres értéket adhat hozzá. Ellenkező esetben nem hagyhatja ki, és érvényes értéket kell átadnia a Azure SQL Database támogatott díjszabási szintjeinek listájából. További információ: [SQL Database erőforrás-korlátok](../azure-sql/database/resource-limits-logical-server.md).

Ha az Azure AD-hitelesítést használja az adatgyár felügyelt identitásával az adatbázis-kiszolgálóhoz való kapcsolódáshoz, kihagyhatja a `CatalogAdminCredential` paramétert. Az adatok előállítójának felügyelt identitását azonban hozzá kell adnia egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](./enable-aad-authentication-azure-ssis-ir.md). Ellenkező esetben nem hagyhatja ki, és meg kell adnia egy érvényes objektumot, amely az SQL-hitelesítéshez tartozó kiszolgáló-rendszergazdai Felhasználónév és jelszó alapján lett létrehozva.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Az Integration Runtime elindítása

Futtassa az alábbi parancsokat az Azure-SSIS Integration Runtime elindításához.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
>
> Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). További információ a telepíthető további összetevőkről: [a Azure-SSIS IR egyéni beállításai](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Teljes szkript

Itt látható a teljes parancsfájl, amely létrehoz egy Azure-SSIS integrációs modult.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Integrációs modul létrehozása Azure Resource Manager sablonnal

Ebben a szakaszban egy Azure Resource Manager sablonnal hozza létre az Azure-SSIS integrációs modult. Íme egy példa a bemutatóra:

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager sablonnal. A szögletes zárójelben (helyőrzők) lévő értékeket cserélje le a saját értékeire.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. A Azure Resource Manager sablon üzembe helyezéséhez futtassa a `New-AzResourceGroupDeployment` parancsot az alábbi példában látható módon. A példában az `ADFTutorialResourceGroup` erőforráscsoport neve. `ADFTutorialARM.json` az a fájl, amely tartalmazza az adatelőállító JSON-definícióját és a Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza az adatelőállítót, és Azure-SSIS IR, de nem indítja el az IR-t.

3. A Azure-SSIS IR elindításához futtassa a következő `Start-AzDataFactoryV2IntegrationRuntime` parancsot:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
>
> Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). További információ a telepíthető további összetevőkről: [a Azure-SSIS IR egyéni beállításai](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és futtathatja azokat a Azure-SSIS IR az Azure-kompatibilis SSDT vagy SSMS eszközök használatával. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma a következő: `<server name>.database.windows.net` .
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.<dns prefix>.database.windows.net` .
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma a következő: `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Ha nem használja a SSISDB-t, a csomagokat telepítheti fájlrendszerbe, Azure Filesba vagy MSDB, amelyet az Azure SQL felügyelt példánya futtat, és a [dtutil](/sql/integration-services/dtutil-utility) és a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) parancssori segédeszközök használatával futtathatja azokat a Azure-SSIS IR. 

További információ: [SSIS-projektek/csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Mindkét esetben a telepített csomagokat Azure-SSIS IR is futtathatja a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Következő lépések

További Azure-SSIS IR témakörök a jelen dokumentációban:

- [Azure-SSIS integrációs](concepts-integration-runtime.md#azure-ssis-integration-runtime)modul. Ez a cikk általában az integrációs modulokról tartalmaz információkat, beleértve a Azure-SSIS IRt is.
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ebből a cikkből megtudhatja, hogyan kérheti le és értelmezheti a Azure-SSIS IR kapcsolatos információkat.
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy törölni a Azure-SSIS IR. Azt is bemutatja, hogyan bővítheti Azure-SSIS IR több csomópont hozzáadásával.
- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)