---
title: Hotpatch for Windows Server Azure Edition (előzetes verzió)
description: A Hotpatch for Windows Server Azure Edition működése és engedélyezése
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830177"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Gyorsjavítás új virtuális gépekhez (előzetes verzió)

A gyorsjavítás egy új módszer a frissítések olyan új Windows Server Azure Edition rendszerű virtuális gépekre való telepítésére, amelyek nem igényelnek újraindítást a telepítés után. Ez a cikk a Windows Server Azure Edition virtuális gépek gyorsjavításával kapcsolatos információkat tartalmazza, amelyek a következő előnyökkel járnak:
* Kisebb munkaterhelés-hatás kevesebb újraindítással
* A frissítések gyorsabb üzembe helyezése, mivel a csomagok kisebbek, gyorsabban telepíthetők, és egyszerűbb a javítások vezénylése az Azure Update Managerrel
* Jobb védelem, mivel a hotpatch frissítési csomagok hatóköre olyan Windows biztonsági frissítésekre terjed ki, amelyek újraindítás nélkül gyorsabban telepíthetők

## <a name="how-hotpatch-works"></a>A hotpatch működése

A hotpatch először úgy működik, hogy alapkonfigurációt Windows Update összesítve. A hotpatches rendszeresen ki van adva (például a hónap második keddén), amelyek erre az alapkonfigurációra épülnek. A gyorsjavítások olyan frissítéseket tartalmaznak, amelyekhez nincs szükség újraindításra. Rendszeres időközönként (három hónappal kezdődően) az alapkonfiguráció frissül egy új legújabb összegző frissítéssel.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Gyorsjavítási minta ütemezése.":::

Az alapkonfigurációknak két típusa van: **Tervezett alaptervek** és **nem tervezett alaptervek.**
*  **A tervezett alapkonfigurációk** rendszeres időközönként szabadulnak fel, a gyorsjavítások kiadása pedig a kettő között van.  A tervezett alapkonfigurációk  az adott hónap összes frissítését tartalmazzák egy hasonló összes összegző frissítésben, és újraindítást igényelnek.
    * A fenti minta-ütemezés négy tervezett alapkonfigurációt ábrázol egy naptári évben (a diagramon ötöt), és nyolc gyorsjavítást tartalmazó kiadást.
* **A nem tervezett alapkonfigurációk** egy fontos frissítés (például egy nullanapos javítás) kiadott frissítéseknél szabadulnak fel, és az adott frissítés nem oldható fel Hotpatch-frissítésként.  Nem tervezett alaptervek kiadásakor a hotpatch kiadás egy nem tervezett alapkonfigurációra lesz lecserélve az adott hónapban.  A nem tervezett alapkonfigurációk az  adott hónap összes frissítését is tartalmazzák egy összehasonlítható legújabb kumulatív frissítésben, és újraindítást igényelnek.
    * A fenti minta-ütemezés két nem tervezett alaptervet szemléltet, amelyek lecserélik a hotpatch kiadásokat az adott hónapokra (a nem tervezett alaptervek tényleges száma egy évben előre nem ismert).

## <a name="regional-availability"></a>Regionális elérhetőség
A hotpatch előzetes verzióban minden globális Azure-régióban elérhető. Azure Government előzetes verzió nem támogatja a régiókat.

## <a name="how-to-get-started"></a>Első lépések

> [!NOTE]
> Az előzetes verzió fázisában csak a következő hivatkozással Azure Portal az [első lépésekhez.](https://aka.ms/AzureAutomanageHotPatch)

A Hotpatch új virtuális gépen való használatának első lépései a következők:
1.  Előzetes verziójú hozzáférés engedélyezése
    * Az előzetes verzióhoz való hozzáférés engedélyezésére előfizetésenként van szükség.
    * Az előzetes verziójú hozzáférés az API-n, a PowerShellen vagy a parancssori felületen keresztül engedélyezhető a következő szakaszban leírtak szerint.
1.  Virtuális gép létrehozása a Azure Portal
    * Az előzetes verzióban el kell kezdenie a [hivatkozást.](https://aka.ms/AzureAutomanageHotPatch)
1.  Virtuális gép részleteinek megszabadása
    * Győződjön meg arról, hogy a Rendszerkép legördülő menüben a _Windows Server 2019 Datacenter: Azure Edition_ van kiválasztva.
    * A Felügyelet lap lépésében görgessen le a Vendég operációs rendszer frissítései szakaszhoz. Láthatja, hogy a Hotpatching be van kapcsolva, a Patch telepítés pedig alapértelmezés szerint az Azure által vezényelt javításra van beállítva.
    * A virtuális gépek automatikus automatizálható – ajánlott eljárások alapértelmezés szerint engedélyezve lesznek
1. Az új virtuális gép létrehozása

## <a name="enabling-preview-access"></a>Előzetes verziójú hozzáférés engedélyezése

### <a name="rest-api"></a>REST API

Az alábbi példa bemutatja, hogyan engedélyezheti az előzetes verziójú előfizetését:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Miután regisztrálta a funkciót az előfizetésben, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

A ```Register-AzProviderFeature``` parancsmag használatával engedélyezheti az előzetes verziójú előfizetését.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Miután regisztrálta a funkciót az előfizetésben, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Az ```az feature register``` előzetes verzió előfizetéshez való engedélyezéséhez használja a következőt: .

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Miután regisztrálta a funkciót az előfizetésben, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Javítás telepítése

Az előzetes verzióban az automatikus virtuálisgép-vendégjavítás automatikusan engedélyezve van a _Windows Server 2019 Datacenterrel_ létrehozott összes virtuális gépen: Azure Edition. [](../virtual-machines/automatic-vm-guest-patching.md) Az automatikus virtuálisgép-vendégjavítás engedélyezése esetén:
* A kritikus vagy biztonsági besorolású javítások automatikusan letöltve és alkalmazva vannak a virtuális gépen.
* A javítások a virtuális gép időzónájában, csúcsidőn kívül vannak alkalmazva.
* A patch vezénylést az Azure kezeli, és a javítások a rendelkezésre [állásra vonatkozó alapelvek alapján vannak alkalmazva.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)
* A rendszer a platform állapotjelzői alapján meghatározott virtuális gépek állapotát figyeli a javítási hibák észlelése érdekében.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hogyan működik a virtuális gépek automatikus vendégjavítása?

Ha [a virtuális gépek automatikus](../virtual-machines/automatic-vm-guest-patching.md) vendégjavítása engedélyezve van egy virtuális gépen, a rendszer automatikusan letölti és alkalmazza az elérhető kritikus és biztonsági javításokat. Ez a folyamat minden hónapban automatikusan elindul, amikor új javítások megjelenik. A javítások felmérése és telepítése automatikus, és a folyamat szükség szerint magában foglalja a virtuális gép újraindítását.

Ha a Hotpatch engedélyezve van a _Windows Server 2019 Datacenteren: Azure Edition_ virtuális gépek, a legtöbb havi biztonsági frissítés olyan gyorsjavításként lesz kézbesítve, amely nem igényel újraindítást. A tervezett vagy nem tervezett alap hónapokra küldött legújabb összegző frissítésekhez a virtuális gép újraindítása szükséges. Rendszeres időközönként további kritikus vagy biztonsági javítások is elérhetők lehetnek, amelyekhez szükség lehet a virtuális gépek újraindítására.

A rendszer néhány naponta és többször is automatikusan értékeli a virtuális gépet egy 30 napos időszakon belül a virtuális gép megfelelő javításának meghatározásához. Ez az automatikus értékelés biztosítja, hogy a hiányzó javítások a lehető leghamarabb felderítve legyenek.

A javítások telepítése a havi javítások kiadását követő 30 napon belül történik, a rendelkezésre [állásra vonatkozó alapelvek alapján.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching) A javítások telepítése csak a virtuális gép csúcsidőszakán kívül, a virtuális gép időzónája alapján kerül telepítésre. A javítások automatikus telepítésének a csúcsidőn kívüli időszakban kell futnia. Ha egy virtuális gép rendszeres értékelés során ki van kapcsolva, a rendszer értékeli a virtuális gépet, és a megfelelő javítások automatikusan telepítve lesznek a következő rendszeres értékelés során, amikor a virtuális gép be van kapcsolva. A következő rendszeres értékelés általában néhány napon belül megtörténik.

A definíciófrissítések és a nem kritikus vagy biztonsági besorolású egyéb javítások nem lesznek telepítve a virtuális gépek vendégének automatikus javításával.

## <a name="understanding-the-patch-status-for-your-vm"></a>A virtuális gép javítási állapotának ismertetése

A virtuális gép javítási állapotának megtekintéséhez lépjen a virtuális gép Vendég **és** gazdagép frissítései szakaszra a Azure Portal. A Vendég **operációs rendszer frissítései** szakaszban kattintson a "Gyorsjavítás (előzetes verzió)" elemre a virtuális gép legfrissebb javítási állapotának megtekintéséhez.

Ezen a képernyőn a virtuális gép Hotpatch állapota látható. Azt is áttekintheti, hogy vannak-e olyan javítások a virtuális géphez, amelyek még nincsenek telepítve. A fenti "Patch installation" (Javítás telepítése) című szakaszban leírtaknak megfelelően [](../virtual-machines/automatic-vm-guest-patching.md) a rendszer automatikusan telepíti az összes biztonsági és kritikus frissítést a virtuális gépre a virtuális gépek vendégjavításának automatikus javításával, és nincs szükség további műveletekre. A rendszer nem telepíti automatikusan a más frissítési besorolásokkal együtt telepített javításokat. Ehelyett az elérhető javítások listájában, a "Megfelelőség frissítése" lapon érhetők el. A virtuális gépen a frissítéstelepítések előzményeit a frissítési előzmények között is megtekintheti. Megjelennek az elmúlt 30 nap frissítési előzményei, valamint a javítástelepítés részletei.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch Management.":::

Az automatikus virtuálisgép-vendégjavításokkal a rendszer rendszeres időközönként ellenőrzi a virtuális gép frissítéseit, és automatikusan ellenőrzi az elérhető frissítéseket. Ezek az időszakos értékelések biztosítják, hogy a rendszer észlelni fogja a rendelkezésre álló javításokat. Az értékelés eredményeit a fenti Frissítések képernyőn, az utolsó értékelés idejét is beleértve megtekintheti. Az "Értékelés most" lehetőséggel bármikor aktiválhat egy igény szerinti javításfelmérést a virtuális géphez, és az értékelés befejezése után áttekintheti az eredményeket.

Az igény szerinti felméréshez hasonlóan igény szerint is telepíthet javításokat a virtuális géphez a "Frissítések telepítése most" lehetőséggel. Itt telepítheti az összes frissítést adott javításbesorolások alatt. Az egyes tudásbáziscikkek listájának megadásával a tartalmazni vagy kizárni kívánt frissítéseket is megadhatja. Az igény szerint telepített javítások nem a rendelkezésre állásra vonatkozó alapelvek alapján vannak telepítve, és a frissítés telepítéséhez további újraindításra és virtuális gépek állásidejére lehet szükség.

## <a name="supported-updates"></a>Támogatott frissítések

A Hotpatch Windows biztonság frissítéseket, és a rendszeres (nem hotpatch) Windows-frissítési csatornán kiadott biztonsági frissítések tartalmával való paritást tart fenn.

A Hotpatch funkcióval rendelkező Windows Server Azure kiadású virtuális gépek futtatása során figyelembe kell venni néhány fontos szempontot. A Hotpatch program által nem tartalmazott frissítések telepítéséhez újraindítás szükséges. Az új alapkonfiguráció telepítése után rendszeres időközönként újraindításra is szükség van. Ezek az újraindítások szinkronban tartják a virtuális gépet a legújabb összegző frissítésben szereplő nem biztonsági javításokkal.
* A Hotpatch program által jelenleg nem tartalmazott javítások közé tartoznak a Windowshoz kiadott nem biztonsági frissítések és a nem Windows-frissítések (például .NET-javítások).  Az ilyen típusú javításokat az alapkonfiguráció hónapja során kell telepíteni, és újraindításra van szükség.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-hotpatching"></a>Mi az a gyorsjavítás?

* A gyorsjavítás egy új módszer a frissítések Windows Server 2019 Datacenterre való telepítésére: Azure Edition virtuális gép az Azure-ban, amely nem igényel újraindítást a telepítés után. Úgy működik, hogy a folyamat újraindítása nélkül javítja a futó folyamatok memóriában futó kódját.

### <a name="how-does-hotpatching-work"></a>Hogyan működik a gyorsjavítás?

* A gyorsjavítás úgy működik, hogy alapkonfigurációt hoz létre egy Windows Update legújabb összegző frissítéssel, majd erre az alapkonfigurációra épít olyan frissítésekkel, amelyek nem igényelnek újraindítást a életbe lépésük indításakor.  Az alapkonfiguráció rendszeres időközönként frissül egy új összegző frissítéssel. Az összegző frissítés minden biztonsági és minőségi frissítést tartalmaz, és újraindítást igényel.

### <a name="why-should-i-use-hotpatch"></a>Miért használjam a Hotpatchot?

* Ha a Windows Server 2019 Datacenter: Azure Edition rendszeren használja a Hotpatchet, a virtuális gép magasabb rendelkezésre állású (kevesebb újraindítással) és gyorsabb frissítésekkel (a folyamatok újraindítása nélkül gyorsabban telepített kisebb csomagokkal) fog futni. Ez a folyamat egy mindig naprakész és biztonságos virtuális gépet hoz létre.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Milyen típusú frissítésekre vonatkozik a Hotpatch?

* A hotpatch jelenleg a Windows biztonsági frissítéseit tartalmazza.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Mikor kapom meg az első hotpatch frissítést?

* A gyorsjavítások frissítéseit általában minden hónap második keddén adták ki. További információért lásd alább.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hogyan fog kinézni a Hotpatch ütemezése?

* A gyorsjavítás úgy működik, hogy alapkonfigurációt Windows Update összesítve, majd erre az alapkonfigurációra épít a havonta kiadott Hotpatch-frissítésekkel.  Az előzetes verzióban az alapkonfigurációk három hónaponként megjelenik. Az alábbi képen egy éves három hónapos ütemezésre láthat példát (a nullanapos javítások által nem tervezett alapkonfigurációkat is beleértve).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Gyorsjavítási minta ütemezése.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>A Hotpatchben regisztrált virtuális gépek újraindítása továbbra is szükséges?

* A Hotpatch program által nem tartalmazott frissítések telepítéséhez újraindításra van szükség, és az alapkonfiguráció (Windows Update legújabb kumulatív frissítés) telepítését követően rendszeres időközönként szükség van rá. Ez az újraindítás szinkronban tartja a virtuális gépet az összegző frissítésben szereplő összes javítással. Az alapkonfigurációk (amelyek újraindítást igényelnek) három hónapos ütemben indulnak el, és idővel növekednek.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Érinti a rendszer az alkalmazásokat a Hotpatch-frissítések telepítésekor?

* Mivel a Hotpatch anélkül javítja a futó folyamatok memóriában való kódját, hogy újra kellene indítania a folyamatot, a javítási folyamat nem érinti az alkalmazásokat. Vegye figyelembe, hogy ez elkülönül a javítás esetleges teljesítményre és funkciókra gyakorolt hatástól.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Ki tudom kapcsolni a Hotpatchot a virtuális gépen?

* A hotpatch a virtuális gépen a következő Azure Portal.  A Hotpatch kikapcsolásával a virtuális gép a Hotpatch szolgáltatásból való leszűkül, ami visszaállítja a virtuális gépet a Windows Server szokásos frissítési viselkedésére.  Ha egy virtuális gépen már nem regisztrált a Hotpatchre, a következő Hotpatch-alapkonfiguráció kiíratáskor újra regisztrálhatja a virtuális gépet.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Frissíthető a meglévő Windows Server operációs rendszerről?

* A Windows Server meglévő verzióiról (azaz a Windows Server 2016-os vagy 2019-es nem Azure-kiadásokról) való frissítés jelenleg nem támogatott. A Windows Server Azure Edition későbbi kiadásaira való frissítés támogatott lesz.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Használhatom a Hotpatchot éles számítási feladatokhoz az előzetes verzióban?

* Az előzetes verziók csak tesztelési célokra szolgálnak, éles környezetben nem használhatók.

### <a name="will-i-be-charged-during-the-preview"></a>Az előzetes verzióban díjat számítunk fel?

* A Windows Server Azure Edition licence az előzetes verzióban ingyenes. A virtuális géphez beállított mögöttes infrastruktúra (tárolás, számítás, hálózat stb.) költsége azonban továbbra is az előfizetésre lesz terhelve.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hogyan kaphatok hibaelhárítási támogatást a Hotpatchinghez?

* Műszaki támogatási jegyet is [be lehet nyújtatni.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) A Szolgáltatás lehetőség alatt keresse meg és válassza a **Windows rendszerű** virtuális gép lehetőséget a Számítás alatt. Válassza **az Azure-szolgáltatások** lehetőséget a probléma típusaként, és az Automatikus virtuális gép vendégjavítása lehetőséget a probléma altípusaként. 

## <a name="next-steps"></a>Következő lépések

* Az Azure Update Management [itt.](../automation/update-management/overview.md)
* Az automatikus virtuálisgép-vendégjavításról itt olvashat [bővebben](../virtual-machines/automatic-vm-guest-patching.md)
