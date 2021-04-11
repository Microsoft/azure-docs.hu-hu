---
title: Azure Service Fabric-fürtök frissítése
description: További információ az Azure Service Fabric-fürt frissítésének lehetőségeiről
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731112"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Azure Service Fabric-fürtök frissítése és frissítése

Az Azure Service Fabric-fürt egy Ön tulajdonában lévő erőforrás, de részben a Microsoft felügyeli. Ez a cikk azokat a lehetőségeket ismerteti, amelyekkel frissítheti az Azure Service Fabric-fürtöt.

## <a name="automatic-versus-manual-upgrades"></a>Automatikus és manuális frissítések

Fontos, hogy a Service Fabric-fürt mindig egy [támogatott futtatókörnyezet-verziót](service-fabric-versions.md)futtasson. Minden alkalommal, amikor a Microsoft a Service Fabric új verziójának kiadását bejelentette, az előző verzió a *támogatás végére* van megjelölve, legalább 60 nappal az adott dátumtól számítva. Az új kiadások a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)jelennek meg.

A fürt kiadásának lejárta előtt tizennégy nappal egy állapot-esemény jön létre, amely *Figyelmeztetési* állapotba helyezi a fürtöt. A fürt figyelmeztetési állapotban marad, amíg nem frissít egy támogatott futtatókörnyezet-verzióra.

Beállíthatja, hogy a fürt a Microsoft által kiadott automatikus Service Fabric-frissítéseket fogadja, vagy manuálisan is választhat a jelenleg támogatott verziók listájáról. Ezek a beállítások a Service Fabric fürterőforrás **háló verziófrissítések** szakaszában érhetők el.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Válassza az automatikus vagy manuális frissítés lehetőséget a fürterőforrás &quot;Fabric Upgrades&quot; szakaszában a Azure Portal.":::

Beállíthatja a fürt frissítési módját is, és kiválaszthatja a futásidejű verziót [egy Resource Manager-sablon használatával](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Az automatikus frissítések az ajánlott frissítési mód, mivel ez a beállítás biztosítja, hogy a fürt egy támogatott állapotban maradjon, és a legújabb javítások és szolgáltatások előnyeit kihasználva is lehetővé teszi, hogy a frissítések olyan módon legyenek leképezve, amely a számítási feladatokhoz a [hullámok üzembe helyezési](#wave-deployment-for-automatic-upgrades) stratégiájának használatával legkevésbé zavarja a feladatokat.

## <a name="wave-deployment-for-automatic-upgrades"></a>A Wave üzembe helyezése automatikus frissítésekhez

A Wave üzembe helyezésével a számítási feladatoktől függően csökkentheti a fürtre való frissítés megszakadását. Beállíthat például egy *tesztelési*  ->  *fázis*  ->  *éles* környezetben történő üzembe helyezési folyamatát a különböző Service Fabric-fürtökhöz, hogy tesztelni lehessen a futásidejű frissítés kompatibilitását, mielőtt alkalmazza azt az éles számítási feladatokra.

A Wave-telepítés elvégzéséhez adja meg a következő hullám-értékek egyikét a fürt számára (a telepítési sablonban):

* **0. hullám**: a fürtök frissítése azonnal megtörténik, amikor új Service Fabric buildet szabadítanak fel. Tesztelési/fejlesztési fürtökhöz.
* **1. hullám**: a fürtök egy héttel (hét nap) frissültek egy új Build felszabadítása után. Előzetes előkészítési/előkészítési fürtökhöz készült.
* **2. hullám**: a fürtök két hetet (14 nap) frissítenek egy új Build felszabadítása után. Éles fürtökhöz készült.

Az e-mailes értesítések regisztrálásával további segítségre lehet szüksége, ha a fürt frissítése sikertelen. Az első lépésekhez tekintse meg [az automatikus frissítésekhez készült Wave Deployment](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) című témakört.

## <a name="phases-of-automatic-upgrade"></a>Az automatikus frissítés fázisai

A Microsoft fenntartja az Azure-fürtön futó Service Fabric futásidejű kódot és konfigurációt. A szoftverre vonatkozó automatikus figyelést a szükséges módon végezzük el. Ezek a frissítések kód, konfiguráció vagy mindkettő lehet. A frissítések az alkalmazásokra gyakorolt hatásának minimalizálásához a következő fázisokban történik:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: a frissítés a fürt összes állapotára vonatkozó szabályzatok használatával történik.

Ebben a fázisban a frissítések egyszerre egy frissítési tartományt végeznek, a fürtön futó alkalmazások pedig leállás nélkül is futnak. A fürt állapot-házirendjei (a csomópont állapota és az alkalmazás állapota) a frissítés során be vannak tartva.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést, és egy e-mailt küld az előfizetés tulajdonosának. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy a fürt frissítését vissza kellett állítani.
* Javasolt javító műveletek, ha vannak ilyenek.
* A napok száma (*n*), amíg végre nem Hajtjuk a 2. fázist.

Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. Az e-mailek elküldésének időpontjától számított *n* nap után a 2. fázisban maradunk.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a helyzet akkor fordulhat elő, ha a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések bármelyikét megtörtént. A nagy mennyiségű e-mailek küldésének elkerülése érdekében a rendszer nem küld e-mailt a sikeres futtatásról. Az e-mail fogadása kivételt jelez a normál működésre. Az alkalmazás rendelkezésre állásának befolyásolása nélkül elvárjuk, hogy a fürt legtöbb frissítése sikeres legyen.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: a frissítés csak az alapértelmezett állapotházirendek használatával végezhető el.

Az ebben a fázisban lévő állapotfigyelő házirendek úgy vannak beállítva, hogy a frissítés elején a kifogástalan állapotú alkalmazások száma változatlan maradjon a frissítési folyamat során. Ahogy az 1. fázisban, a 2. fázis frissíti egyszerre egy frissítési tartományt, a fürtön futó alkalmazások pedig leállás nélkül is futnak. A fürt állapot-házirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) be vannak tartva a frissítés során.

Ha a fürt állapotára vonatkozó házirendek nem teljesülnek, a rendszer visszaállítja a frissítést. Ezt követően a rendszer elküld egy e-mailt az előfizetés tulajdonosának. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy a fürt frissítését vissza kellett állítani.
* Javasolt javító műveletek, ha vannak ilyenek.
* Azon napok száma (n), amíg a 3. fázist végre nem hajtjuk.

Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. A rendszer elküld egy emlékeztető e-mailt néhány nappal az n nap előtt. Az e-mailek elküldésének napjától számított n nappal azután folytassa a 3. fázissal. A 2. fázisban elküldött e-maileket komolyan kell venni, és a javító műveleteket is el kell végezni.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések során fordulhat elő. A sikeres Futtatás e-mail-megerősítése nem történt meg.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: a frissítés az agresszív állapotházirendek használatával történik.

Az ebben a fázisban található állapot-szabályzatok az alkalmazások állapota helyett a frissítés befejezését célozzák. Ebben a fázisban a fürt néhány frissítése megszűnik. Ha a fürt ezt a fázist kapja, jó eséllyel lesz az alkalmazása, és/vagy elveszti a rendelkezésre állást.

A másik két fázishoz hasonlóan a 3. fázis frissítése egyszerre csak egy frissítési tartományt hajt végre.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést. Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. Ezt követően a fürt rögzítve lett, így többé nem fog támogatást és/vagy frissítéseket kapni.

A rendszer e-mailt küld az előfizetés tulajdonosának a javító műveletekkel együtt. Nem várjuk, hogy egy fürt olyan állapotba kerüljön, amelyben a 3. fázis sikertelen volt.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések során fordulhat elő. A sikeres Futtatás e-mail-megerősítése nem történt meg.

## <a name="custom-policies-for-manual-upgrades"></a>Egyéni szabályzatok manuális frissítésekhez

Egyéni házirendeket is megadhat a fürt manuális frissítéseihez. Ezeket a házirendeket a rendszer minden alkalommal alkalmazza, amikor kiválaszt egy új futtatókörnyezet-verziót, amely elindítja a számítógépet a fürt frissítésének elindításához. Ha nem bírálja felül a szabályzatokat, a rendszer az alapértelmezett értékeket használja. További információ: [Egyéni házirendek beállítása kézi frissítésekhez](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>További fürtök frissítése

A futtatókörnyezet frissítésén kívül számos más műveletet is végre kell hajtania a fürt naprakészen tartásához, beleértve a következőket:

### <a name="managing-certificates"></a>Tanúsítványok kezelése

A Service Fabric [X. 509 kiszolgálói tanúsítványokat](service-fabric-cluster-security.md) használ, amelyeket akkor ad meg, amikor fürt létrehozásakor a fürtcsomópontok közötti kommunikációt és az ügyfelek hitelesítését végzi. A fürthöz és az ügyfélhez tanúsítványokat adhat hozzá, frissíthet vagy törölhet a [Azure Portal](https://portal.azure.com) vagy a PowerShell vagy az Azure CLI használatával.  További információ a [tanúsítványok hozzáadása és eltávolítása](service-fabric-cluster-security-update-certs-azure.md) című témakörben olvasható.

### <a name="opening-application-ports"></a>Alkalmazás portjainak megnyitása

Az alkalmazás portjait a csomópont típusához társított Load Balancer erőforrás-tulajdonságok módosításával módosíthatja. Használhatja a Azure Portal, vagy használhatja a PowerShellt vagy az Azure CLI-t is. További információért olvassa el az [alkalmazás-portok megnyitása fürthöz](create-load-balancer-rule.md)című témakört.

### <a name="defining-node-properties"></a>Csomópont-tulajdonságok definiálása

Előfordulhat, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontokon futnak a fürtben. Előfordulhat például, hogy egyes számítási feladatok GPU vagy SSD-ket igényelnek, míg mások esetleg nem. A fürt minden egyes csomópont-típusához hozzáadhat egyéni csomópont-tulajdonságokat a fürtcsomópontok számára. Az elhelyezési megkötések az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomópont-tulajdonságot választanak ki. Az elhelyezési megkötések határozzák meg a szolgáltatások futtatásának helyét.

Az elhelyezési megkötések, a csomópont-tulajdonságok és a definiált beállítások használatával kapcsolatos részletekért olvassa el a [csomópont-tulajdonságok és az elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)című témakört.

### <a name="adding-capacity-metrics"></a>Kapacitási mérőszámok hozzáadása

Az egyes csomópont-típusoknál hozzáadhat egyéni kapacitási mérőszámokat, amelyeket az alkalmazásokban használni szeretne a betöltés jelentéséhez. A kapacitási mérőszámok betöltésének jelentésével kapcsolatos részletekért tekintse meg a fürt és a [metrikák, valamint a terhelések](service-fabric-cluster-resource-manager-metrics.md) [leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) Service Fabric fürterőforrás-kezelő dokumentumait.

### <a name="customizing-settings-for-your-cluster"></a>A fürt beállításainak testreszabása

Számos különböző konfigurációs beállítás testreszabható a fürtön, például a fürt és a csomópont tulajdonságainak megbízhatósági szintje. További információkért olvassa el [Service Fabric a fürt hálójának beállításait](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>A fürtcsomópontok operációsrendszer-lemezképének frissítése

Az operációs rendszer rendszerképének automatikus frissítésének engedélyezése a Service Fabric fürtcsomópontok esetében ajánlott eljárás. Ennek érdekében több fürtre vonatkozó követelményt és lépést kell végrehajtania. Egy másik lehetőség a patch-előkészítési alkalmazás (POA, a Service Fabric alkalmazás, amely az operációs rendszer javítását automatizálja egy Service Fabric-fürtön állásidő nélkül. Ha többet szeretne megtudni ezekről a lehetőségekről, tekintse meg [a Windows operációs rendszer javítása a Service Fabric-fürtben](service-fabric-patch-orchestration-application.md)című témakört.

## <a name="next-steps"></a>Következő lépések

* [Service Fabric-frissítések kezelése](service-fabric-cluster-upgrade-version-azure.md)
* A [Service Fabric-fürt beállításainak](service-fabric-cluster-fabric-settings.md) testreszabása
* [A fürt méretezése és kicsinyítése](service-fabric-cluster-scale-in-out.md)
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md) megismerése
