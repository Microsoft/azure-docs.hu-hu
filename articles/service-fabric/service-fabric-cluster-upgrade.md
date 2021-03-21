---
title: Azure Service Fabric-fürt frissítése
description: Ismerje meg az Azure Service Fabric-fürt verziójának vagy konfigurációjának frissítését – a fürt frissítési módjának beállítását, a tanúsítványok frissítését, az alkalmazások portjainak hozzáadását, az operációsrendszer-javítások elvégzését, valamint a frissítések végrehajtásakor várhatóan szükséges tudnivalókat.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 028c91f85a6e318f7ea686c1bcd50262eb7c6bf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571028"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Azure Service Fabric-fürt frissítése

Bármely modern rendszer esetében a minőségének megtervezése kulcsfontosságú a termék hosszú távú sikerességének megvalósításához. Az Azure Service Fabric-fürt olyan erőforrás, amelyet Ön birtokol, de részben a Microsoft felügyeli. Ez a cikk azt ismerteti, hogy mi felügyelhető automatikusan, és mi konfigurálható.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó háló verziójának szabályozása

Győződjön meg arról, hogy a fürt mindig [támogatott háló-verziót](service-fabric-versions.md)futtat. Minden alkalommal, amikor a Microsoft a Service Fabric új verziójának kiadását bejelentette, az előző verzió a támogatás végére van megjelölve, legalább 60 nappal az adott dátumtól számítva. Az új kiadások a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)jelennek meg.

a fürt kiadásának lejárta előtt 14 nappal egy állapot-esemény jön létre, amely figyelmeztetési állapotba helyezi a fürtöt. A fürt figyelmeztetési állapotban marad, amíg nem frissít egy támogatott Fabric-verzióra.

Beállíthatja, hogy a fürt a Microsoft által kiadott automatikus háló-frissítéseket fogadja, vagy kiválaszthat egy olyan támogatott háló-verziót, amelyet be szeretne állítani a fürtön.  További információért olvassa el [a fürt Service Fabric verziójának frissítése](service-fabric-cluster-upgrade-version-azure.md)című témakört.

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>A háló frissítési viselkedése az automatikus frissítések során

A Microsoft karbantartja az Azure-fürtön futó háló kódját és konfigurációját. A szoftverhez szükség szerint automatikusan figyelt frissítéseket végzünk. Ezek a frissítések kód, konfiguráció vagy mindkettő lehet. Annak biztosítása érdekében, hogy az alkalmazás a frissítések miatt ne okozzon semmilyen hatást vagy minimális hatást, a rendszer a következő fázisokban hajtja végre a frissítéseket:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1. fázis: a frissítés a fürt összes állapotára vonatkozó szabályzatok használatával történik.

Ebben a fázisban a frissítések egyszerre egy frissítési tartományt végeznek, a fürtön futó alkalmazások pedig leállás nélkül is futnak. A fürt állapot-házirendjei (a csomópont állapota és az alkalmazás állapota) a frissítés során be vannak tartva.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést, és egy e-mailt küld az előfizetés tulajdonosának. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy a fürt frissítését vissza kellett állítani.
* Javasolt javító műveletek, ha vannak ilyenek.
* A napok száma (*n*), amíg végre nem Hajtjuk a 2. fázist.

Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. Az e-mailek elküldésének napjától számított *n* nappal azután folytassa a 2. fázissal.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések során fordulhat elő. A sikeres Futtatás e-mail-megerősítése nem történt meg. Ezzel elkerülhető, hogy túl sok e-mailt küldjön; az e-mailek fogadása kivételként normálisnak tekintendő. Az alkalmazás rendelkezésre állásának befolyásolása nélkül elvárjuk, hogy a fürt legtöbb frissítése sikeres legyen.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2. fázis: a frissítés csak az alapértelmezett állapotházirendek használatával végezhető el.

Az ebben a fázisban lévő állapotfigyelő házirendek úgy vannak beállítva, hogy a frissítés elején a kifogástalan állapotú alkalmazások száma változatlan maradjon a frissítési folyamat időtartamára. Ahogy az 1. fázisban, a 2. fázis frissíti egyszerre egy frissítési tartományt, a fürtön futó alkalmazások pedig leállás nélkül is futnak. A fürt állapot-házirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) a frissítés időtartamára vannak betartva.

Ha a fürt állapotára vonatkozó házirendek nem teljesülnek, a rendszer visszaállítja a frissítést. Ezt követően a rendszer elküld egy e-mailt az előfizetés tulajdonosának. Az e-mail a következő információkat tartalmazza:

* Értesítés arról, hogy a fürt frissítését vissza kellett állítani.
* Javasolt javító műveletek, ha vannak ilyenek.
* Azon napok száma (n), amíg a 3. fázist végre nem hajtjuk.

Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. A rendszer elküld egy emlékeztető e-mailt néhány nappal az n nap előtt. Az e-mailek elküldésének napjától számított n nappal azután folytassa a 3. fázissal. A 2. fázisban elküldött e-maileket komolyan kell venni, és a javító műveleteket is el kell végezni.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések során fordulhat elő. A sikeres Futtatás e-mail-megerősítése nem történt meg.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3. fázis: a frissítés az agresszív állapotházirendek használatával történik.

Az ebben a fázisban található állapot-szabályzatok az alkalmazások állapota helyett a frissítés befejezését célozzák. Ebben a fázisban a fürt néhány frissítése megszűnik. Ha a fürt erre a fázisba kerül, jó eséllyel lehet, hogy az alkalmazás nem megfelelő állapotba kerül, és/vagy elveszti a rendelkezésre állást.

A másik két fázishoz hasonlóan a 3. fázis frissítése egyszerre csak egy frissítési tartományt hajt végre.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést. Megpróbáljuk végrehajtani ugyanezt a frissítést néhány alkalommal, ha az infrastruktúra miatt nem sikerült frissíteni a frissítéseket. Ezt követően a fürt rögzítve lett, így többé nem fog támogatást és/vagy frissítéseket kapni.

A rendszer e-mailt küld az előfizetés tulajdonosának a javító műveletekkel együtt. Nem várjuk, hogy egy fürt olyan állapotba kerüljön, amelyben a 3. fázis sikertelen volt.

Ha a fürt állapot-házirendjei teljesülnek, a frissítés sikeresnek minősül, és készen van megjelölve. Ez a kezdeti frissítés során vagy az ebben a fázisban lévő frissítési ismétlések során fordulhat elő. A sikeres Futtatás e-mail-megerősítése nem történt meg.

## <a name="manage-certificates"></a>Tanúsítványok kezelése

A Service Fabric [X. 509 kiszolgálói tanúsítványokat](service-fabric-cluster-security.md) használ, amelyeket akkor ad meg, amikor fürt létrehozásakor a fürtcsomópontok közötti kommunikációt és az ügyfelek hitelesítését végzi. A fürthöz és az ügyfélhez tanúsítványokat adhat hozzá, frissíthet vagy törölhet a [Azure Portal](https://portal.azure.com) vagy a PowerShell vagy az Azure CLI használatával.  További információ a [tanúsítványok hozzáadása és eltávolítása](service-fabric-cluster-security-update-certs-azure.md) című témakörben olvasható.

## <a name="open-application-ports"></a>Alkalmazás portjainak megnyitása

Az alkalmazás portjait a csomópont típusához társított Load Balancer erőforrás-tulajdonságok módosításával módosíthatja. Használhatja a Azure Portal, vagy használhatja a PowerShellt vagy az Azure CLI-t is. További információért olvassa el az [alkalmazás-portok megnyitása fürthöz](create-load-balancer-rule.md)című témakört.

## <a name="define-node-properties"></a>Csomópont tulajdonságainak megadása

Előfordulhat, hogy bizonyos számítási feladatok csak bizonyos típusú csomópontokon futnak a fürtben. Előfordulhat például, hogy egyes számítási feladatok GPU vagy SSD-ket igényelnek, míg mások esetleg nem. A fürt minden egyes csomópont-típusához hozzáadhat egyéni csomópont-tulajdonságokat a fürtcsomópontok számára. Az elhelyezési megkötések az egyes szolgáltatásokhoz csatolt utasítások, amelyek egy vagy több csomópont-tulajdonságot választanak ki. Az elhelyezési megkötések határozzák meg a szolgáltatások futtatásának helyét.

Az elhelyezési megkötések, a csomópont-tulajdonságok és a definiált beállítások használatával kapcsolatos részletekért olvassa el a [csomópont-tulajdonságok és az elhelyezési korlátozások](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)című témakört.

## <a name="add-capacity-metrics"></a>Kapacitási mérőszámok hozzáadása

Az egyes csomópont-típusoknál hozzáadhat egyéni kapacitási mérőszámokat, amelyeket az alkalmazásokban használni szeretne a betöltés jelentéséhez. A kapacitási mérőszámok betöltésének jelentésével kapcsolatos részletekért tekintse meg a fürt és a [metrikák, valamint a terhelések](service-fabric-cluster-resource-manager-metrics.md) [leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) Service Fabric fürterőforrás-kezelő dokumentumait.

## <a name="set-health-policies-for-automatic-upgrades"></a>Állapotházirendek beállítása az automatikus frissítésekhez

Megadhat egyéni állapot-házirendeket a háló frissítéséhez. Ha úgy állította be a fürtöt, hogy az automatikus háló frissítése megtörténjen, akkor ezek a szabályzatok az automatikus háló frissítéseinek 1. fázisára vonatkoznak.
Ha a fürtöt manuális hálós frissítésre állította be, akkor a rendszer minden alkalommal alkalmazza ezeket a házirendeket, amikor kiválaszt egy új verziót, amely elindítja a rendszerindítást a fürtben. Ha nem bírálja felül a szabályzatokat, a rendszer az alapértelmezett értékeket használja.

A speciális frissítési beállítások kiválasztásával megadhatja az egyéni állapotfigyelő házirendeket, vagy áttekintheti az aktuális beállításokat a "háló frissítése" panelen. Tekintse át a következő képet a című témakörben.

![Egyéni állapotfigyelő házirendek kezelése][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>A fürthöz tartozó háló beállításainak testreszabása

Számos különböző konfigurációs beállítás testreszabható a fürtön, például a fürt és a csomópont tulajdonságainak megbízhatósági szintje. További információkért olvassa el [Service Fabric a fürt hálójának beállításait](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Az operációs rendszer javítása a fürtcsomópontok között

A javítási előkészítési alkalmazás (POA) egy Service Fabric alkalmazás, amely az operációs rendszer javítását automatizálja egy Service Fabric-fürtön állásidő nélkül. A [Windowshoz készült patch](service-fabric-patch-orchestration-application.md) -előkészítési alkalmazás üzembe helyezhető a fürtön úgy, hogy a javításokat koordinált módon telepítse, miközben a szolgáltatások rendelkezésre állását is megőrizheti.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan szabhatja testre a [Service Fabric-fürtök néhány beállítását](service-fabric-cluster-fabric-settings.md)
* Ismerje meg, hogyan [méretezheti a fürtöt és ki](service-fabric-cluster-scale-in-out.md)
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md) megismerése

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
