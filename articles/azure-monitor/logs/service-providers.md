---
title: A szolgáltatók naplófájljainak Azure Monitor | Microsoft Docs
description: Azure Monitor naplók segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára a kiszolgálók kezelésében és figyelésében az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 09b5a6bd11db1a8faa29e7b3871156f9c28d1140
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717383"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Szolgáltatók Azure Monitor naplófájljai

Log Analytics munkaterületek Azure Monitor segíthetnek a felügyelt szolgáltatók (MSP-k), a nagyvállalatok, a független szoftvergyártók (ISV-k) és az üzemeltetési szolgáltatók számára, hogy az ügyfél helyszíni vagy Felhőbeli infrastruktúrájában kezeljék és felügyeljék a kiszolgálókat.

A nagyvállalatok számos hasonlóságot biztosítanak a szolgáltatók számára, különösen akkor, ha van egy központosított informatikai csapat, amely számos különböző üzleti egységért felelős. Az egyszerűség kedvéért ez a dokumentum a *szolgáltató* kifejezést használja, de ugyanez a funkció a vállalatok és más ügyfelek számára is elérhető.

A [Cloud Solution Provider (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) program részét képező partnerek és szolgáltatók esetében a log Analytics Azure monitor az Azure CSP-előfizetésekben elérhető Azure-szolgáltatások egyike.

A Azure Monitor Log Analyticst egy, az Azure-beli Azure-beli felügyelt erőforrás-kezelési képességgel rendelkező szolgáltató is használhatja az [Azure Lighthouse](../../lighthouse/overview.md)-ban.

## <a name="architectures-for-service-providers"></a>Szolgáltatók architektúrái

Log Analytics munkaterületek lehetővé teszik a rendszergazdák számára a [naplózási](../logs/data-platform-logs.md) folyamatok áramlását és elkülönítését, valamint az adott üzleti igényeknek megfelelő architektúra létrehozását. [Ez a cikk](../logs/design-logs-deployment.md) a munkaterület tervezési, üzembe helyezési és áttelepítési szempontjait ismerteti, és a [hozzáférés kezelése](../logs/manage-access.md) című cikk ismerteti, hogyan kell alkalmazni és kezelni a naplózási engedélyeket. A szolgáltatók további szempontokat is figyelembe kell venniük.

A szolgáltatók három lehetséges architektúrával rendelkeznek Log Analytics munkaterületekre vonatkozóan:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. az elosztott naplók az ügyfél bérlője területén található munkaterületeken tárolódnak

Ebben az architektúrában egy munkaterületet helyezünk üzembe az ügyfél bérlője, amely az adott ügyfél összes naplójában használatos.

A szolgáltatói rendszergazdák kétféleképpen férhetnek hozzá egy Log Analytics munkaterülethez az ügyfél bérlője számára:

- Az ügyfél [Azure Active Directory vendég felhasználóként (B2B)](../../active-directory/external-identities/what-is-b2b.md)veheti fel az egyes felhasználókat a szolgáltatótól. A szolgáltató rendszergazdáinak be kell jelentkezniük az egyes ügyfelek könyvtárába a Azure Portal, hogy el tudják érni ezeket a munkaterületeket. Ehhez az is szükséges, hogy az ügyfelek az egyes szolgáltatók rendszergazdáinak egyéni hozzáférését kezeljék.
- A jobb méretezhetőség és rugalmasság érdekében a szolgáltatók az [Azure világítótorony](../../lighthouse/overview.md) Azure-beli [delegált erőforrás-kezelési](../../lighthouse/concepts/azure-delegated-resource-management.md) funkciójának használatával érhetik el az ügyfél bérlőjét. Ezzel a módszerrel a szolgáltatói rendszergazdák egy Azure AD felhasználói csoportba tartoznak a szolgáltató bérlője számára, és ez a csoport a bevezetési folyamat során kap hozzáférést minden ügyfél számára. Ezek a rendszergazdák ezután hozzáférhetnek az egyes ügyfelek munkaterületeihez a saját szolgáltatói bérlőn belül, és nem kell egyenként bejelentkezniük az egyes ügyfelek bérlői számára. Az ügyfelek Log Analytics munkaterületek erőforrásainak ily módon való elérése csökkenti az ügyfél oldalán szükséges munkát, és megkönnyíti az adatok összegyűjtését és elemzését ugyanazon szolgáltató által felügyelt eszközökön keresztül, például [Azure monitor munkafüzetek](../visualize/workbooks-overview.md)használatával. További információ: az [ügyfelek erőforrásainak figyelése nagy méretekben](../../lighthouse/how-to/monitor-at-scale.md).

Az elosztott architektúra előnyei a következők:

* Az ügyfél megerősítheti bizonyos szintű engedélyeket az [Azure-beli delegált erőforrás-kezelésen](../../lighthouse/concepts/azure-delegated-resource-management.md)keresztül, vagy a saját [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)használatával kezelheti a naplóhoz való hozzáférést.
* A naplók bármilyen típusú erőforrásból gyűjthetők, nem csak az ügynök-alapú virtuálisgép-adatok. Például az Azure naplók.
* Minden ügyfél különböző beállításokkal rendelkezhet a munkaterülethez, például a megőrzéshez és az adatkorláthoz.
* Az ügyfelek közötti elkülönítés szabályozási és kompatibilitás.
* Az egyes munkaterületek díját az ügyfél előfizetése fogja bevezetni.

Az elosztott architektúra hátrányai a következők:

* Az olyan eszközök, mint például a Azure Monitor munkafüzetek, központilag jeleníthető meg és elemezhető az [ügyfél-bérlők között](cross-workspace-query.md) , ami lassabb élményt eredményezhet, különösen az adatok több mint 50 + munkaterületen való elemzésekor.
* Ha az ügyfeleket nem az Azure-beli delegált erőforrás-kezeléshez rendeli, a szolgáltatói rendszergazdákat az ügyfél címtárában kell kiépíteni, és a szolgáltató számára nehezebb a nagyszámú ügyfél-bérlő kezelése egyszerre.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. a központi naplók tárolása a szolgáltatói bérlőben található munkaterületen történik

Ebben az architektúrában a rendszer nem tárolja a naplókat az ügyfél bérlői között, de csak a szolgáltató előfizetésében lévő egyik központi helyen. Az ügyfél virtuális gépei számára telepített ügynökök úgy vannak konfigurálva, hogy a munkaterület-azonosító és a titkos kulcs használatával küldjék el a naplókat erre a munkaterületre.

A központosított architektúra előnyei a következők:

* Könnyen kezelheti a nagy mennyiségű ügyfelet, és integrálhatja őket a különböző háttérrendszer-rendszerekbe.
* A szolgáltató teljes körű tulajdonjogot biztosít a naplók és a különböző összetevők, például a függvények és a mentett lekérdezések felett.
* A szolgáltató az összes ügyfelén elvégezheti az elemzést.

A központosított architektúra hátrányai a következők:

* Ez az architektúra csak az ügynök-alapú virtuálisgép-adatforrásokra vonatkozik, nem fedi le a Pásti, az SaaS és az Azure Fabric adatforrásait.
* Előfordulhat, hogy a felhasználók közötti adategységeket nem lehet külön munkaterületre egyesíteni. Az egyetlen jó módszer, ha a számítógép teljes tartománynevét (FQDN) vagy az Azure-előfizetés AZONOSÍTÓját használja.
* Az összes ügyféltől származó összes adatok ugyanabban a régióban lesznek tárolva, mint egyetlen számla és egy adatmegőrzési és konfigurációs beállítás.
* Az Azure Fabric és a Pásti-szolgáltatások, mint például a Azure Diagnostics és az Azure-naplók esetében a munkaterület ugyanabban a bérlőben kell lennie, mint az erőforrásnak, így a naplók nem küldhetők el a központi munkaterületre.
* Az összes ügyféltől származó összes virtuálisgép-ügynök hitelesítése a központi munkaterületre ugyanazzal a munkaterület-AZONOSÍTÓval és kulccsal történik. Egy adott ügyfél naplóit nem lehet blokkolni más ügyfelek megszakítása nélkül.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. a hibrid naplók az ügyfél bérlője területén található munkaterületen tárolódnak, és némelyikük központi helyre van húzva.

A harmadik architektúra a két lehetőség között van. Ez az első olyan elosztott architektúrán alapul, ahol a naplók helyiek az egyes ügyfelek számára, de a naplók központi tárházának létrehozásához valamilyen mechanizmust használunk. A naplók egy része a jelentéskészítés és az elemzés központi helyére kerül. Ez a rész lehet kis számú adattípus vagy a tevékenység összegzése, például a napi statisztika.

A naplók központi helyen történő megvalósításának két lehetősége van:

1. Központi munkaterület: a szolgáltató létrehozhat egy munkaterületet a bérlőben, és olyan parancsfájlt használhat, amely a [lekérdezési API](https://dev.loganalytics.io/) -t használja az [ADATgyűjtési API](../logs/data-collector-api.md) -val, hogy az adatok a különböző munkaterületekről a központi helyre kerüljenek. Egy másik lehetőség, amely nem parancsfájl, a [Azure Logic apps](../../logic-apps/logic-apps-overview.md)használatára szolgál.

2. Power BI központi helyként: Power BI a központi helyként működhet, ha a különböző munkaterületek az Log Analytics munkaterület és a [Power bi](../visualize/powerbi.md)közötti integráció használatával exportálják az adataikat.

## <a name="next-steps"></a>Következő lépések

* Munkaterületek létrehozásának és konfigurálásának automatizálása [Resource Manager-sablonok](../logs/resource-manager-workspace.md) használatával

* Munkaterületek létrehozásának automatizálása a [PowerShell](../logs/powershell-workspace-configuration.md) használatával

* [Riasztások](../alerts/alerts-overview.md) használata a meglévő rendszerekkel való integrációhoz

* Összegző jelentések előállítása [Power bi](../visualize/powerbi.md) használatával

* Ügyfelek bevonása az Azure-beli [delegált erőforrás-felügyeletbe](../../lighthouse/concepts/azure-delegated-resource-management.md).
