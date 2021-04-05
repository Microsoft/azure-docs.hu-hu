---
title: A SSIS Integration Runtime felügyeletének megoldása
description: Ez a cikk hibaelhárítási útmutatást nyújt a SSIS Integration Runtime (SSIS IR) felügyeleti problémáival kapcsolatban.
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361842"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>A SSIS Integration Runtime-kezelés hibáinak megoldása Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk hibaelhárítási útmutatást nyújt az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), más néven SSIS IR kezelési problémáira.

## <a name="overview"></a>Áttekintés

Ha bármilyen probléma merül fel a SSIS IR kiépítése vagy megszüntetése során, hibaüzenet jelenik meg a Microsoft Azure Data Factory portálon, vagy egy PowerShell-parancsmag által visszaadott hibát. A hiba mindig egy részletes hibaüzenettel rendelkező hibakód formájában jelenik meg.

Ha a hibakód InternalServerError, a szolgáltatás átmeneti problémákba ütközik, és később újra kell próbálkoznia a művelettel. Ha egy újrapróbálkozás nem segít, lépjen kapcsolatba a Azure Data Factory támogatási csapatával.

Ellenkező esetben a három fő külső függőség hibát okozhat: Azure SQL Database vagy az Azure SQL felügyelt példányát, egy egyéni telepítési parancsfájlt és egy virtuális hálózati konfigurációt.

## <a name="sql-database-or-sql-managed-instance-issues"></a>A felügyelt példányok SQL Database vagy SQL-problémái

SQL Database vagy SQL felügyelt példányra van szükség, ha a SSIS IR-t egy SSIS-katalógus-adatbázissal szeretné kiépíteni. A SSIS IR-nek képesnek kell lennie a felügyelt SQL Database vagy SQL-példányhoz való hozzáférésre. Emellett a SQL Database vagy SQL felügyelt példányhoz tartozó bejelentkezési fióknak engedéllyel kell rendelkeznie a SSIS-katalógus adatbázisának létrehozásához (SSISDB). Ha hiba történik, a Data Factory portálján megjelenik a hibakód és egy, az SQL-kivétellel kapcsolatos részletes üzenet. Az alábbi listában található információkkal értelmezheti a hibakódokat.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ezt a hibát akkor láthatja, ha új SSIS IR-t helyez üzembe, vagy miközben az IR fut. Ha ezt a hibát az IR üzembe helyezése során tapasztalja, akkor az SQL-kivétellel kapcsolatos részletes üzenetet találhat a hibaüzenetben, amely a következő problémák valamelyikét jelzi:

* Hálózati kapcsolati hiba. Győződjön meg arról, hogy a SQL Database vagy SQL felügyelt példány állomásneve elérhető. Azt is ellenőrizze, hogy nem blokkolja-e tűzfal vagy hálózati biztonsági csoport (NSG) az SSIS IR-t a kiszolgáló elérése során.
* A bejelentkezés sikertelen volt az SQL-hitelesítés során. A megadott fiókkal nem lehet bejelentkezni az SQL Server-adatbázisba. Ügyeljen rá, hogy helyes felhasználói fiókot adjon meg.
* A bejelentkezés sikertelen volt a Microsoft Azure Active Directory- (Azure AD-) hitelesítés során (felügyelt identitás). Adja hozzá a gyár felügyelt identitását egy AAD-csoporthoz, és győződjön meg arról, hogy a felügyelt identitás rendelkezik hozzáférési engedélyekkel a katalógus adatbázis-kiszolgálójához.
* Kapcsolati időtúllépés. Ezt a hibát mindig egy biztonsággal kapcsolatos konfiguráció okozza. A következő megoldást javasoljuk:
  1. Hozzon létre egy új virtuális gépet.
  1. Csatlakoztassa a virtuális GÉPET ugyanahhoz a Microsoft Azure Virtual Networkhoz, ha az IR virtuális hálózaton van.
  1. Telepítse a SSMS, és keresse meg a SQL Database vagy az SQL felügyelt példányának állapotát.

Egyéb problémák esetén javítsa ki az SQL-kivétellel kapcsolatos részletes hibaüzenetben látható hibát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a SQL Database vagy az SQL felügyelt példányának támogatási csapatával.

Ha a hiba akkor jelenik meg, ha az IR fut, a hálózati biztonsági csoport vagy a tűzfal módosításai valószínűleg meggátolják, hogy a SSIS IR feldolgozó csomópont hozzáférjen SQL Database vagy SQL felügyelt példányához. Oldja fel a SSIS IR Worker csomópontot, hogy az hozzáférhessen SQL Database vagy SQL felügyelt példányhoz.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

A következő hibaüzenet jelenhet meg: "a (z) SSISDB adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, indexeli az indexeket, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. 

A lehetséges megoldások a következők:
* Növelje a SSISDB kvótájának méretét.
* Módosítsa az SSISDB konfigurációját a méret csökkentéséhez a következőképpen:
   * A megőrzési időtartam és a projekt-verziók számának csökkentése.
   * A napló megőrzési idejének csökkentése.
   * A napló alapértelmezett szintjének módosítása.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Ez a hiba azt jelenti, SQL Database vagy SQL felügyelt példány már rendelkezik egy SSISDB, és azt egy másik IR használja. Meg kell adnia egy másik SQL Database vagy SQL felügyelt példányt, vagy törölnie kell a meglévő SSISDB, és újra kell indítania az új IR-t.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* Az SSIS IR-hez konfigurált felhasználói fiók nem rendelkezik engedéllyel az adatbázis létrehozásához. Engedélyt adhat a felhasználónak az adatbázis létrehozásához.
* Időtúllépés (például végrehajtási időtúllépés vagy adatbázis-műveleti időtúllépés) történik az adatbázis létrehozása során. Próbálja meg később újra végrehajtani a műveletet. Ha az Újrapróbálkozás nem sikerül, lépjen kapcsolatba a SQL Database vagy az SQL felügyelt példányának támogatási csapatával.

Egyéb problémák esetén tekintse meg az SQL-kivétel hibaüzenetét, és javítsa ki a hiba részleteiben szereplő problémát. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba a SQL Database vagy az SQL felügyelt példányának támogatási csapatával.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Az ilyen típusú hibaüzenet így néz ki: "érvénytelen objektumnév" catalog.catalog_properties "." Ebben az esetben vagy már rendelkezik egy SSISDB nevű adatbázissal, de nem a SSIS IR hozta létre, vagy az adatbázis érvénytelen állapotban van, amelyet az utolsó SSIS IR-kiépítés során hibák okoztak. A meglévő adatbázist elhúzhatja a SSISDB néven, vagy beállíthat egy új SQL Database vagy SQL felügyelt példányt az IR-hez.

## <a name="custom-setup-issues"></a>Egyéni telepítési problémák

Az egyéni telepítés során használható felülten hozzáadhatja saját telepítési lépései az SSIS IR üzembe helyezése vagy újrakonfigurálása során. További információ: [Az Azure SSIS IR telepítésének testreszabása](./how-to-configure-azure-ssis-ir-custom-setup.md).

Győződjön meg arról, hogy a tároló csak a szükséges egyéni telepítőfájlokat tartalmazza; a tároló összes fájlja le lesz töltve az SSIS IR feldolgozó csomópontjára. Azt javasoljuk, hogy a szkript az SSIS IR-ban való futtatása előtt tesztelje az egyéni telepítési szkriptet egy helyi gépen a szkriptvégrehajtási hibák javítása érdekében.

Az egyéni telepítési szkripttárolót a rendszer az integrációs modul futása közben ellenőrzi, mivel az SSIS IR rendszeresen frissül. Ennek a frissítésnek hozzá kell férnie a tárolóhoz az egyéni telepítési szkript letöltéséhez és újratelepítéséhez. A folyamat azt is ellenőrzi, hogy a tároló elérhető-e, illetve hogy létezik-e a main.cmd fájl.

Az egyéni telepítéssel kapcsolatos bármilyen hiba esetén egy CustomSetupScriptFailure-hibakód jelenik meg, például a CustomSetupScriptBlobContainerInaccessible vagy a CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Ez a hiba azt jelenti, hogy az SSIS IR nem fér hozzá az Azure Blob-tárolóhoz az egyéni telepítés céljából. Győződjön meg róla, hogy a tároló SAS URI-ja elérhető és érvényes.

Ha az integrációs modul fut, állítsa le, konfigurálja újra az új egyéni telepítési tároló SAS URI-jával, majd indítsa újra.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Ez a hiba azt jelenti, hogy az SSIS IR nem talál egyéni telepítési szkriptet (main.cmd) a blobtárolóban. Győződjön meg arról, hogy a main.cmd létezik a tárolóban, amely belépési pontként szolgál az egyéni telepítéshez.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Ez a hiba azt jelenti, hogy az egyéni telepítési szkript (main.cmd) végrehajtása sikertelen volt. Először próbálja ki a helyi gépén a szkriptet, vagy ellenőrizze az egyéni telepítés végrehajtási naplóit a blobtárolóban.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Ez a hiba az egyéni telepítési szkript végrehajtásának időtúllépését jelzi. Győződjön meg arról, hogy a szkript végrehajtható csendes módban, felhasználói beavatkozás nélkül, és hogy a blobtároló csak a szükséges egyéni telepítési fájlokat tartalmazza. Javasoljuk, hogy a szkriptet először tesztelje a helyi számítógépen. Az egyéni telepítés végrehajtási naplóit is ellenőriznie kell a blobtárolóban. Az egyéni telepítés maximális időtartama 45 perc, mielőtt időtúllépés történik, amely magában foglalja az összes fájl a tárolóból való letöltéséhez, majd az SSIS IR-ban való telepítéséhez szükséges időt is. Ha hosszabb időre van szüksége, hozzon létre egy támogatási jegyet.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Ez a hiba azt jelenti, hogy az egyéni telepítés végrehajtási naplóinak a blobtárolóba való feltöltésére tett kísérlet sikertelen volt. Ez a probléma akkor fordul elő, ha a SSIS IR nem rendelkezik írási engedéllyel a blobtárolóhoz, vagy ha tárolási vagy hálózati problémák állnak fenn. Ha az egyéni telepítés sikeres, a hiba nem befolyásolja a SSIS működését, de a naplók hiányozni fognak. Ha az egyéni telepítés egy másik hiba miatt meghiúsul, és a napló nincs feltöltve, ezt a hibát jelentjük először, hogy a naplót fel lehessen tölteni az elemzéshez. Emellett a probléma megoldása után minden további konkrét problémát jelenteni fogunk. Ha ez a probléma nem oldódik meg az újrapróbálkozás után, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Amikor az SSIS integrációs modult az Azure Virtual Networkhöz csatlakoztatja, a SSIS IR a felhasználói előfizetéshez tartozó virtuális hálózatot használja. További információért tekintse meg [az Azure-SSIS IR virtuális hálózathoz történő csatlakoztatásával](./join-azure-ssis-integration-runtime-virtual-network.md) foglalkozó cikket.
Miután a SSIS IR sikeresen elindult, ha hálózati kapcsolati problémák léptek fel, megpróbálhatja a probléma diagnosztizálására használni a [kapcsolat diagnosztizálása eszközt](ssis-integration-runtime-diagnose-connectivity-faq.md) .
Ha virtuális hálózattal kapcsolatos probléma jelentkezik, az alábbi hibák egyike jelenik meg.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Ennek a hibának több különböző okból jelentkezhet. A hiba elhárításához tekintse meg a [Forbidden](#forbidden), az [InvalidPropertyValue](#invalidpropertyvalue) és a [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) szakaszt.

### <a name="forbidden"></a>Forbidden

Ez a hiba a következőhöz hasonló lehet: "a denetid nincs engedélyezve az aktuális fiókhoz. Microsoft.BatCH erőforrás-szolgáltató nincs regisztrálva a VNet azonos előfizetésében. "

Ezek a részletek azt jelentik, hogy az Azure Batch nem fér hozzá a virtuális hálózathoz. Regisztrálja a Microsoft.Batch erőforrás-szolgáltatót ugyanabban az előfizetésében, amelybe a virtuális hálózat is tartozik.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Az ilyen típusú hiba az alábbiakhoz lehet hasonló: 

- "A megadott VNet nem létezik, vagy a Batch szolgáltatásnak nincs hozzáférése."
- "A megadott alhálózat (XXX) nem létezik."

Ezek a hibák azt jelentik, hogy a virtuális hálózat nem létezik, az Azure Batch szolgáltatás nem fér hozzá, vagy a megadott alhálózat nem létezik. Győződjön meg arról, hogy a virtuális hálózat és az alhálózat létezik, illetve hogy az Azure Batch hozzájuk tud férni.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

A következő hibaüzenet így néz ki: "nem sikerült kiépíteni a Integration Runtime a VNet. Ha a DNS-kiszolgáló vagy a NSG beállításai konfigurálva vannak, ellenőrizze, hogy a DNS-kiszolgáló elérhető-e, és hogy a NSG megfelelően van-e konfigurálva. "

Ebben az esetben valószínűleg egyéni konfigurációt alkalmazott a DNS-kiszolgáló vagy a hálózati biztonsági csoport beállításait illetően, amely megakadályozza az SSIS IR által igényelt Azure-kiszolgálónév feloldását vagy elérését. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](./join-azure-ssis-integration-runtime-virtual-network.md). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Az SSIS IR rendszeres időközönként automatikusan frissül. A frissítés során létrejön egy új Azure Batch-készlet, a régi Azure Batch-készlet pedig törlődik. A régi készlet virtuális hálózathoz kapcsolódó erőforrásait is törli a rendszer, az új virtuális hálózathoz kapcsolódó erőforrások pedig az előfizetése alatt jönnek létre. Ez a hiba azt jelenti, hogy az előfizetés vagy az erőforráscsoport szintjén érvényben lévő törlési zárolás miatt nem sikerült törölni a régi készlet virtuális hálózathoz kapcsolódó erőforrásait. Mivel az ügyfél szabályozza és állítja be a törlési zárolást, ilyen esetben neki kell eltávolítania a törlési zárolást.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Ha az SSIS IR üzembe helyezése meghiúsul, a rendszer az összes létrehozott erőforrást törli. Ha viszont van egy erőforrástörlési zárolás az előfizetés vagy az erőforráscsoport szintjén, a virtuális hálózat erőforrásai nem törlődnek a várt módon. A hiba kijavításához oldja fel a törlési zárolást, majd indítsa újra az integrációs modult.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

Ha leállítja a SSIS IR-t, a rendszer a virtuális hálózathoz kapcsolódó összes erőforrást törli. A törlés azonban meghiúsulhat, ha előfizetés vagy az erőforráscsoport szintjén erőforrástörlési zárolás van életben. Itt is az ügyfél szabályozza és állítja be a törlési zárolást. Ezért el kell távolítaniuk a törlési zárolást, majd újra le kell állítaniuk a SSIS IR-t.

### <a name="nodeunavailable"></a>NodeUnavailable

Ez a hiba akkor fordul elő, ha az integrációs modul fut, és azt jelenti, hogy az integrációs modul állapota nem megfelelő. Ezt a hibát mindig a DNS-kiszolgáló vagy a hálózati biztonsági csoport konfigurációjának változása okozza, amely blokkolja a SSIS IR-t a szükséges szolgáltatáshoz való csatlakozáskor. Mivel a DNS-kiszolgáló és a hálózati biztonsági csoport konfigurációját az ügyfél szabályozza, az ügyfélnek kell kijavítania a blokkolást okozó problémákat a saját oldaláról. További információ: [Az SSIS IR virtuális hálózatának konfigurálása](./join-azure-ssis-integration-runtime-virtual-network.md). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az Azure Data Factory támogatási csapatával.

## <a name="static-public-ip-addresses-configuration"></a>Statikus nyilvános IP-címek konfigurálása

Ha a Azure-SSIS IRt az Azure Virtual Networkhoz csatlakoztatja, akkor a saját statikus nyilvános IP-címei is megadhatók az IR-hez, hogy az IR hozzáférhessen az adott IP-címekhez való hozzáférést korlátozó adatforrásokhoz. További információért tekintse meg [az Azure-SSIS IR virtuális hálózathoz történő csatlakoztatásával](./join-azure-ssis-integration-runtime-virtual-network.md) foglalkozó cikket.

A fenti virtuális hálózati problémák mellett előfordulhat, hogy a statikus nyilvános IP-címekkel kapcsolatos problémát is kielégíti. További segítségért tekintse meg az alábbi hibákat.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Ez a hiba többféle okból is előfordulhat a Azure-SSIS IR indításakor:

| Hibaüzenet | Megoldás|
|:--- |:--- |
| A megadott statikus nyilvános IP-cím már használatban van, adja meg két használaton kívüli Azure-SSIS Integration Runtime. | Válassza ki a két nem használt statikus nyilvános IP-címet, vagy távolítsa el az aktuális referenciákat a megadott nyilvános IP-címre, majd indítsa újra a Azure-SSIS IR. |
| A megadott statikus nyilvános IP-címnek nincs DNS-neve, adjon meg kettőt a Azure-SSIS Integration Runtime DNS-nevével. | A nyilvános IP-cím DNS-nevét Azure Portalban is beállíthatja, ahogy az alábbi képen látható. A konkrét lépések a következők: (1) nyissa meg Azure Portal és goto a nyilvános IP-cím erőforrás lapját. (2) válassza ki a **konfiguráció** szakaszt, és állítsa be a DNS-nevet, majd kattintson a **Save (Mentés** ) gombra; (3) indítsa újra a Azure-SSIS IR. |
| A Azure-SSIS Integration Runtime megadott VNet és statikus nyilvános IP-címeinek ugyanazon a helyen kell lenniük. | Az Azure-hálózat követelményeinek megfelelően a statikus nyilvános IP-címnek és a virtuális hálózatnak ugyanabban a helyen és előfizetésben kell lennie. Adjon meg két érvényes statikus nyilvános IP-címet, és indítsa újra a Azure-SSIS IR. |
| A megadott statikus nyilvános IP-cím egy alapszintű, a Azure-SSIS Integration Runtime számára két standardot adjon meg. | További segítségért tekintse meg a [nyilvános IP-cím SKU-](../virtual-network/public-ip-addresses.md#sku) t. |

![Azure-SSIS integrációs modul](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Ha Azure-SSIS IR kiépítés meghiúsul, a rendszer az összes létrehozott erőforrást törli. Ha azonban van erőforrás-törlési zárolás az előfizetésben vagy az erőforráscsoport (amely a statikus nyilvános IP-cím) szintjét tartalmazza, a hálózati erőforrások nem törlődnek a várt módon. A hiba elhárításához távolítsa el a törlési zárolást, és indítsa újra az IR-t.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

A Azure-SSIS IR leállításakor a nyilvános IP-címet tartalmazó erőforráscsoporthoz létrehozott összes hálózati erőforrás törlődni fog. A törlés azonban sikertelen lehet, ha az előfizetés vagy az erőforráscsoport (amely a statikus nyilvános IP-címet tartalmazza) erőforrás-törlési zárolást tartalmaz. Távolítsa el a törlési zárolást, és indítsa újra az IR-t.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

A Azure-SSIS IR rendszeres időközönként automatikusan frissül. Új IR-csomópontok jönnek létre a frissítés során, és a régi csomópontok törlődnek. A létrehozott hálózati erőforrások (például a terheléselosztó és a hálózati biztonsági csoport) is törlődnek a régi csomópontokhoz, és az előfizetése alatt létrejön az új hálózati erőforrások. Ez a hiba azt jelenti, hogy a régi csomópontok hálózati erőforrásainak törlése nem sikerült, mert az előfizetés vagy az erőforráscsoport (amely a statikus nyilvános IP-címet tartalmazza) esetében törlési zárolást eredményezett. Távolítsa el a törlési zárolást, hogy a régi csomópontok kitakarítása és a régi csomópontok statikus nyilvános IP-címének felszabadítása is megtörténjen. Ellenkező esetben a statikus nyilvános IP-cím nem szabadítható fel, és a rendszer nem fogja tudni frissíteni az IR-t.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Ha saját statikus nyilvános IP-címeit szeretné használni, két nyilvános IP-címet kell megadni. Az egyiket az IR-csomópontok azonnali létrehozására fogjuk használni, és egy másikat a rendszer az IR frissítése során használ. Ez a hiba akkor fordulhat elő, ha a másik nyilvános IP-cím nem használható a frissítés során. A lehetséges okokért tekintse meg a  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) .