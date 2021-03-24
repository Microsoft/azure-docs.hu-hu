---
title: HotPatching melletti telepítéséről for Windows Server Azure Edition (előzetes verzió)
description: Ismerje meg, hogyan működik a Windows Server Azure Edition HotPatching melletti telepítéséről, és hogyan engedélyezhető
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 92b8bf240dfd73cc9191675db07f20816b7156a8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953391"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>HotPatching melletti telepítéséről az új virtuális gépekhez (előzetes verzió)

A javítás új módszer a frissítések telepítéséhez olyan új Windows Server Azure Edition rendszerű virtuális gépeken (VM), amelyeken a telepítés után nem szükséges újraindítás. Ez a cikk a Windows Server Azure Edition rendszerű virtuális gépek HotPatching melletti telepítéséről kapcsolatos információkat ismerteti, amelyek a következő előnyöket nyújtják:
* Alacsonyabb munkaterhelés-hatás kevesebb újraindítással
* A frissítések gyorsabb üzembe helyezése, mivel a csomagok kisebbek, gyorsabbak, és egyszerűbben javítható az Azure Update Manager javítása
* Jobb védelem, mivel a HotPatching melletti telepítéséről frissítési csomagjai a Windows biztonsági frissítéseire vannak korlátozva, amelyek az újraindítás nélkül gyorsabban települnek

## <a name="how-hotpatch-works"></a>A HotPatching melletti telepítéséről működése

A HotPatching melletti telepítéséről úgy működik, hogy először létrehoz egy alapkonfigurációt Windows Update legújabb összesítő frissítéssel. A Hotpatches rendszeres időközönként (például a hónap második keddjén) jelennek meg, amelyek az adott alaptervre épülnek. A Hotpatches olyan frissítéseket fog tartalmazni, amelyek nem igényelnek újraindítást. Időnként (három havonta kezdődően) az alapkonfiguráció egy új, legújabb összesítő frissítéssel frissül.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="HotPatching melletti telepítéséről minta-ütemterv.":::

Kétféle alapkonfiguráció létezik: **tervezett** alapkonfigurációk és nem **tervezett alaptervek**.
*  A **tervezett** alapkonfigurációk egy normál ütemben jelennek meg, a HotPatching melletti telepítéséről-kiadásokkal együtt.  A tervezett alapkonfigurációk közé tartozik az adott hónaphoz hasonló, _legújabb összesítő frissítéssel_ rendelkező összes frissítés, és újraindítás szükséges.
    * A fenti ütemezés a naptári év négy tervezett alapkiadását mutatja be (a diagram öt teljes összegét), valamint nyolc HotPatching melletti telepítéséről kiadást.
* A nem **tervezett alaptervek** akkor jelennek meg, ha egy fontos frissítés (például egy nulla napos javítás) megjelent, és az adott frissítés nem szabadítható fel HotPatching melletti telepítéséről.  A nem tervezett alapkonfigurációk felszabadításakor a rendszer az adott hónapban egy nem tervezett alapkonfigurációval helyettesíti a HotPatching melletti telepítéséről.  A nem tervezett alapkonfigurációk tartalmazzák az adott hónap hasonló, _legújabb összesített frissítésében_ található összes frissítést is, és újraindítást igényelnek.
    * A fenti minta ütemezés két nem tervezett alapkonfigurációt mutat be, amelyek lecserélik az adott hónap HotPatching melletti telepítéséről kiadásait (az év nem tervezett alapkonfigurációinak tényleges száma nem ismert előre).

## <a name="regional-availability"></a>Regionális elérhetőség
A HotPatching melletti telepítéséről az összes globális Azure-régióban elérhető előzetes verzióban. Az előzetes verzióban a Azure Government régiók nem támogatottak.

## <a name="how-to-get-started"></a>Első lépések

> [!NOTE]
> Az előzetes verzió ideje alatt csak a Azure Portal használatával kezdheti meg a [hivatkozást](https://aka.ms/AzureAutomanageHotPatch).

A HotPatching melletti telepítéséről új virtuális gépen való használatának megkezdéséhez kövesse az alábbi lépéseket:
1.  Előnézeti hozzáférés engedélyezése
    * Előfizetés esetén egyszeri előzetes hozzáférési jogosultság szükséges.
    * Az előnézeti hozzáférés az API, a PowerShell vagy a CLI használatával engedélyezhető a következő szakaszban leírtak szerint.
1.  Virtuális gép létrehozása a Azure Portalból
    * Az előzetes verzió ideje alatt meg kell kezdenie ezt a [hivatkozást](https://aka.ms/AzureAutomanageHotPatch).
1.  Adja meg a virtuális gép részleteit
    * Győződjön meg arról, hogy a _Windows Server 2019 Datacenter: Azure Edition_ van kiválasztva a rendszerkép legördülő menüben
    * A kezelés lapon lépjen a "vendég operációs rendszer frissítései" szakaszra. Látni fogja, hogy a javítás be van kapcsolva, és a javítások telepítése az Azure-ban előkészített javításra van beállítva.
    * A virtuális gépek autokezelése ajánlott eljárásai alapértelmezés szerint engedélyezve lesznek
1. Új virtuális gép létrehozása

## <a name="enabling-preview-access"></a>Az előnézeti hozzáférés engedélyezése

### <a name="rest-api"></a>REST API

Az alábbi példa azt ismerteti, hogyan engedélyezhető az előfizetés előnézete:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

A ```Register-AzProviderFeature``` parancsmag használatával engedélyezze az előfizetéshez tartozó előnézetet.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

A használatával ```az feature register``` engedélyezheti az előfizetéshez tartozó előnézetet.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Javítás telepítése

Az előzetes verzió során a rendszer automatikusan engedélyezi a [virtuális gépek automatikus javítását](../virtual-machines/automatic-vm-guest-patching.md) a _Windows Server 2019 Datacenter: Azure Edition_ rendszerrel létrehozott összes virtuális gép számára. Ha engedélyezve van az automatikus virtuális gép vendégének javítása:
* A kritikus vagy biztonsági besorolású javítások automatikusan letöltődnek és alkalmazhatók a virtuális gépen.
* A javítások a virtuális gép időzónáján kívüli órákon belül lesznek alkalmazva.
* A javítás-előkészítést az Azure felügyeli, és a javítások a [rendelkezésre állási első alapelveket](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)követik.
* A platform állapotára vonatkozó jelek alapján meghatározott virtuálisgép-állapotot a rendszer figyeli a hibajavítások észlelése érdekében.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hogyan működik az automatikus VM-vendég-javítás?

Ha egy virtuális gépen engedélyezve van az automatikus virtuálisgép- [javítás](../virtual-machines/automatic-vm-guest-patching.md) , a rendszer automatikusan letölti és alkalmazza a rendelkezésre álló kritikus és biztonsági javításokat. Ez a folyamat minden hónapban automatikusan indul el, amikor új javításokat bocsátanak ki. A javítások értékelése és telepítése automatikusan történik, és a folyamat a virtuális gép szükség szerinti újraindítását is magában foglalja.

Ha a HotPatching melletti telepítéséről engedélyezve van a _Windows Server 2019 Datacenter rendszerben: az Azure Edition_ rendszerű virtuális gépeken a legtöbb havi biztonsági frissítés olyan hotpatches, amely nem igényel újraindítást. A tervezett vagy nem tervezett alaphónapokban elküldett legújabb összesítő frissítések esetén a virtuális gépek újraindítása szükséges. A további kritikus vagy biztonsági javítások is rendszeres időközönként elérhetővé válhatnak, ami a virtuális gépek újraindítását igényli.

A virtuális gépet az adott virtuális gép megfelelő javításának megállapítása érdekében néhány naponként és többször, a 30 napos időszakon belül automatikusan értékelni kell. Ez az automatikus értékelés biztosítja, hogy a hiányzó javítások a lehető legkorábbi módon legyenek felderítve.

A javítások a havi javításokat követő 30 napon belül települnek, a [rendelkezésre állási első alapelvek](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)alapján. A javítások a virtuális gép időzónájától függően csak a virtuális gép időzónája szerint lesznek telepítve. Ahhoz, hogy a javítások automatikusan telepítve legyenek, a virtuális gépnek le kell futnia a leállási idő alatt. Ha egy virtuális gép ki van kapcsolva egy időszakos értékelés során, a rendszer a virtuális gépet értékeli, és a megfelelő javítások automatikusan települnek a következő rendszeres értékelés során, amikor a virtuális gép be van kapcsolva. A következő időszakos értékelés általában néhány napon belül megtörténik.

A definíciós frissítések és a nem kritikus vagy biztonsági besorolású javítások nem települnek automatikusan a virtuális gép vendégének javításával.

## <a name="understanding-the-patch-status-for-your-vm"></a>A virtuális gép javítási állapotának ismertetése

A virtuális gép javítási állapotának megtekintéséhez navigáljon a **vendég + gazdagép frissítései** szakaszhoz a virtuális gépen a Azure Portal. A **vendég operációs rendszer frissítései** szakaszban kattintson a "Ugrás a HotPatching melletti telepítéséről (előzetes verzió)" lehetőségre a virtuális gép legújabb javítási állapotának megtekintéséhez.

Ezen a képernyőn a virtuális gép HotPatching melletti telepítéséről állapota látható. Azt is megtekintheti, hogy van-e elérhető javítás a virtuális géphez, amely még nincs telepítve. A fenti "javítás telepítése" című szakaszban leírtak szerint a rendszer az összes biztonsági és kritikus frissítést automatikusan telepíti a virtuális gépre a [virtuális gép vendégének automatikus javításával](../virtual-machines/automatic-vm-guest-patching.md) , és nincs szükség további műveletekre. A más frissítési besorolású javítások nem települnek automatikusan. Ehelyett az elérhető javítások listájában láthatók a "frissítés megfelelősége" lapon. A virtuális gépen található frissítési központi telepítések előzményeit a "frissítési előzmények" segítségével is megtekintheti. Az elmúlt 30 napban frissültek az előzmények, a javítások telepítésének részleteivel együtt.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="HotPatching melletti telepítéséről-kezelés.":::

A virtuális gépek automatikus javításával a virtuális gép rendszeres időközönként és automatikusan kiértékelésre kerül az elérhető frissítésekhez. Ezek az időszakos értékelések biztosítják a rendelkezésre álló javítások észlelését. Az értékelés eredményeit a fenti frissítések képernyőn tekintheti meg, beleértve a legutóbbi értékelés időpontját is. Azt is megteheti, hogy az igény szerinti javítás értékelését bármikor elindítja a virtuális gépen a "felmérés most" lehetőség használatával, és az értékelés befejezése után áttekinti az eredményeket.

Az igény szerinti értékeléshez hasonlóan a virtuális gép igény szerinti javításait is telepítheti a "frissítések telepítése most" lehetőség használatával. Itt dönthet úgy, hogy az adott javítás besorolása alatt telepíti az összes frissítést. Megadhatja a belefoglalni vagy kizárni kívánt frissítéseket is, ha megadja az egyes tudásbáziscikkek listáját. Az igény szerint telepített javítások nincsenek telepítve a rendelkezésre állással – az első alapelveket, és a frissítés telepítéséhez több újraindításra és virtuális gépek leállására lehet szükség.

## <a name="supported-updates"></a>Támogatott frissítések

A HotPatching melletti telepítéséről kiterjed a Windows biztonsági frissítéseire, és fenntartja a paritást a rendszeres (nem HotPatching melletti telepítéséről) Windows Update Channel-ben kiadott biztonsági frissítések tartalmával.

Fontos szempont, hogy a Windows Server Azure Edition rendszerű virtuális gépeket a HotPatching melletti telepítéséről-t engedélyező számítógépen futtassa. Az újraindítások továbbra is szükségesek a HotPatching melletti telepítéséről programban nem szereplő frissítések telepítéséhez. Az újraindítások az új alapterv telepítése után is rendszeres időközönként szükségesek. Ezek az újraindítások megőrzik a virtuális gépet szinkronban a legújabb összesítő frissítésben található nem biztonsági javításokkal.
* A HotPatching melletti telepítéséről programban jelenleg nem szereplő javítások közé tartoznak a Windows és a nem Windows rendszerű frissítések (például a .NET-javítások) által kiadott nem biztonsági frissítések.  Ezeket a típusú javításokat az alapkonfiguráció hónapjában kell telepíteni, és újraindítást kell végezni.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-hotpatching"></a>Mi az a működés közbeni javítás?

* A javítás új módszer a frissítések telepítésére egy Windows Server 2019 Datacenter rendszeren: Azure Edition virtuális gép az Azure-ban, amely nem igényel újraindítást a telepítés után. Úgy működik, hogy a folyamat újraindítása nélkül javítja a futó folyamatok memóriában lévő kódját.

### <a name="how-does-hotpatching-work"></a>Hogyan működik a működés közbeni javítás?

* A javítás úgy működik, hogy létrehoz egy alapkonfigurációt egy Windows Update legújabb összesítő frissítéssel, majd az alaptervre épít olyan frissítéseket, amelyek nem igényelnek újraindítást.  Az alapterv rendszeres időközönként frissül egy új összesítő frissítéssel. Az összesítő frissítés tartalmazza az összes biztonsági és minőségi frissítést, és újraindítást igényel.

### <a name="why-should-i-use-hotpatch"></a>Miért érdemes használni a HotPatching melletti telepítéséről?

* Ha a HotPatching melletti telepítéséről-t használja a Windows Server 2019 Datacenter: Azure Edition rendszeren, a virtuális gép magasabb rendelkezésre állású lesz (kevesebb újraindítással) és gyorsabb frissítésekkel (a kisebb csomagok, amelyek a folyamatok újraindítása nélkül lettek telepítve). Ez a folyamat olyan virtuális gépet eredményez, amely mindig naprakész és biztonságos.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Milyen típusú frissítéseket érint a HotPatching melletti telepítéséről?

* A HotPatching melletti telepítéséről jelenleg a Windows biztonsági frissítéseit fedi le.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Mikor jelenik meg az első HotPatching melletti telepítéséről-frissítés?

* A HotPatching melletti telepítéséről-frissítések általában minden hónap második keddjén jelennek meg. További információ: alább.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Milyen módon fog kinézni a HotPatching melletti telepítéséről-ütemterv?

* A javítás úgy működik, hogy létrehoz egy alapkonfigurációt egy Windows Update legújabb összesítő frissítéssel, majd az alaptervre épít, és a HotPatching melletti telepítéséről-frissítések havonta jelennek meg.  Az előzetes verzió ideje alatt a rendszer három havonta elindítja az alapkonfigurációkat. Tekintse meg az alábbi ábrát egy, az éves három hónapos időszakra vonatkozó példára (például a nem tervezett alapkonfigurációkat a nulla napi javítások miatt).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="HotPatching melletti telepítéséről minta-ütemterv.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>A HotPatching melletti telepítéséről-ben regisztrált virtuális gépek esetében is szükség van újraindításra?

* Az újraindítások továbbra is szükségesek a HotPatching melletti telepítéséről programban nem szereplő frissítések telepítéséhez, és az alapkonfiguráció (Windows Update legújabb összesítő frissítés) után rendszeres időközönként szükségesek. Az újraindítás után a virtuális gép szinkronizálva marad a kumulatív frissítésben található összes javítással. Az alapkonfigurációk (amelyek újraindítást igényelnek) a három hónapos ütemben kezdődnek, és idővel növekednek.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Vannak érintett alkalmazásaim egy HotPatching melletti telepítéséről-frissítés telepítésekor?

* Mivel a HotPatching melletti telepítéséről megjavítja a futó folyamatok memóriában lévő kódját anélkül, hogy újra kellene indítania a folyamatot, az alkalmazásokat a javítási folyamat nem érinti. Vegye figyelembe, hogy ez elkülönül a javítás esetleges teljesítményével és funkcióinak következményeivel.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Ki tudom kapcsolni a HotPatching melletti telepítéséről a virtuális gépen?

* A HotPatching melletti telepítéséről kikapcsolhatja a virtuális gépen a Azure Portal használatával.  A HotPatching melletti telepítéséről kikapcsolásával törli a virtuális gép regisztrációját a HotPatching melletti telepítéséről-ből, amely visszaállítja a virtuális gépet a Windows Server jellemző frissítési viselkedésére.  Miután törli a virtuális gép HotPatching melletti telepítéséről való regisztrációt, újból regisztrálhatja a virtuális gépet a következő HotPatching melletti telepítéséről-alapterv megjelenésekor.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Frissíthetem a meglévő Windows Server operációs rendszerről?

* A Windows Server meglévő verzióiról (azaz a Windows Server 2016 vagy a 2019 nem Azure-kiadásokról) való frissítés jelenleg nem támogatott. A Windows Server Azure Edition jövőbeli kiadásaira való frissítés támogatott lesz.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Használhatom a HotPatching melletti telepítéséről az éles számítási feladatokhoz az előzetes verzióban?

* Az előzetes verziók csak tesztelési célokat szolgálnak, és nem éles környezetben való használatra.

### <a name="will-i-be-charged-during-the-preview"></a>Az előzetes verzióban kell fizetni?

* A Windows Server Azure Edition licence ingyenes az előzetes verzió alatt. A virtuális géphez (tárolás, számítás, hálózatkezelés stb.) beállított mögöttes infrastruktúra költsége azonban továbbra is az előfizetésre lesz terhelve.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hogyan Kérhetek hibaelhárítási támogatást a működés közbeni javításhoz?

* A [technikai támogatási esetekről szóló jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)is megadhat. A szolgáltatás beállításnál keresse meg és válassza ki a **Windows rendszert futtató virtuális gépet** a számítás területen. Válassza ki az **Azure-szolgáltatásokat** a probléma típusa és az **automatikus VM-vendég javításához** a probléma altípusa esetében.

## <a name="next-steps"></a>Következő lépések

* Tudjon meg többet az [Azure Update Managementról](../automation/update-management/overview.md).
* További információ az automatikus VM vendég javításáról [](../virtual-machines/automatic-vm-guest-patching.md)