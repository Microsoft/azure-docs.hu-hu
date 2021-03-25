---
title: Azure Monitor riasztásokra vonatkozó műveleti szabályok
description: Megtudhatja, hogyan konfigurálhatja és kezelheti a Azure Monitorban szereplő műveleti szabályokat.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: 12e7cf8e72c5423b4a2edd6ea2a0f4537e328b08
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105036781"
---
# <a name="action-rules-preview"></a>Műveleti szabályok (előzetes verzió)

A műveleti szabályok lehetővé teszik a felgyújtott riasztásokban lévő műveleti csoportok hozzáadását vagy letiltását. Egyetlen szabály a cél erőforrásainak különböző hatóköreit fedi le, például az adott erőforrásra vonatkozó riasztásokat (például egy adott virtuális gépet), vagy bármely, az előfizetésben lévő erőforrással kapcsolatos riasztást. Hozzáadhat különböző szűrőket is, amelyekkel szabályozhatja, hogy mely riasztások tartoznak egy szabályhoz, és hogyan határozhat meg egy ütemtervet, például úgy, hogy csak a munkaidőn kívül vagy egy tervezett karbantartási időszakban legyen érvényben.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Miért és mikor érdemes a műveleti szabályokat használni?

### <a name="suppression-of-alerts"></a>Riasztások letiltása

Számos esetben hasznos a riasztások által generált értesítések letiltása. Ezek a forgatókönyvek a tervezett karbantartási időszakokban a munkaidőn kívüli időpontokban való letiltásig terjedhetnek. A  **ContosoVM** felelős csapat például a közelgő hétvégére vonatkozó riasztási értesítéseket szeretné letiltani, mivel a **ContosoVM** tervezett karbantartás alatt áll.

Bár a csapat letilthatja az **ContosoVM** -on manuálisan konfigurált összes riasztási szabályt (és azt a karbantartás után is engedélyezheti), nem egyszerű folyamat. A műveleti szabályok segítenek a riasztások letiltásának megadásában a letiltási idő rugalmas konfigurálásával. Az előző példában a csapat meghatározhat egy műveleti szabályt a **ContosoVM** , amely letiltja a hétvégére vonatkozó összes riasztási értesítést.

### <a name="actions-at-scale"></a>Léptékű műveletek

Bár a riasztási szabályok segítenek meghatározni a riasztás előállításakor kiváltó műveleti csoportot, az ügyfeleknek gyakran közös műveleti csoportjuk van a műveleteik hatókörén belül. Például az erőforráscsoport **ContosoRG** felelős csapat valószínűleg ugyanazt a műveleti csoportot fogja meghatározni a **ContosoRG**-ben definiált összes riasztási szabályhoz.

A műveleti szabályok segítenek leegyszerűsíteni ezt a folyamatot. A skálázási műveletek megadásával a konfigurált hatókörön létrehozott összes riasztás esetében aktiválható egy műveleti csoport. Az előző példában a csapat egyetlen műveleti szabályt is meghatározhat a **ContosoRG** , amely ugyanazt a műveleti csoportot fogja kiváltani a benne létrehozott összes riasztáshoz.

> [!NOTE]
> A műveleti szabályok nem vonatkoznak Azure Service Health riasztásokra.

## <a name="configuring-an-action-rule"></a>Műveleti szabály konfigurálása

### <a name="portal"></a>[Portál](#tab/portal)

A szolgáltatáshoz a Azure Monitor **riasztások** kezdőlapján a **műveletek kezelése** lehetőségre kattintva férhet hozzá. Ezután válassza a **műveleti szabályok (előzetes verzió)** lehetőséget. A szabályokat a riasztások kezdőlapján a **műveleti szabályok (előzetes verzió)** lehetőség kiválasztásával érheti el.

![A Azure Monitor kezdőlapjának műveleti szabályai](media/alerts-action-rules/action-rules-landing-page.png)

Válassza az **+ új műveleti szabály** lehetőséget.

![A képernyőképen a műveletek kezelése oldalon látható az új műveleti szabály gomb kiemelve.](media/alerts-action-rules/action-rules-new-rule.png)

Azt is megteheti, hogy a riasztási szabály konfigurálása közben létrehoz egy műveleti szabályt.

![Képernyőfelvétel: a szabály létrehozása lap, a műveleti szabály létrehozása gomb kiemelve.](media/alerts-action-rules/action-rules-alert-rule.png)

Ekkor megjelenik a folyamat lap a műveleti szabályok létrehozásához. Konfigurálja a következő elemeket:

![Új műveleti szabály létrehozási folyamata](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Hatókör

Először válassza ki a hatókört (Azure-előfizetés, erőforráscsoport vagy cél-erőforrás). Több hatókört is kijelölhet egy adott előfizetésen belül.

![Műveleti szabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Szűrési feltételek

Megadhat szűrőket is, hogy a szabály a riasztások egy adott részhalmazára vonatkozzon, vagy az egyes riasztások adott eseményeire (például csak a "fired" vagy csak "feloldott").

A rendelkezésre álló szűrők a következők:

* **Súlyosság**  
Ez a szabály csak a kijelölt megszakításokkal rendelkező riasztásokra vonatkozik.  
Például a **Súlyosság = Sev1** azt jelenti, hogy a szabály csak a Sev1 súlyosságú riasztásokra vonatkozik.
* **Szolgáltatás figyelése**  
Ez a szabály csak a kiválasztott figyelési szolgáltatásokból érkező riasztásokra vonatkozik.  
A (z) **"Azure Backup" figyelő** például azt jelenti, hogy a szabály csak a biztonsági mentési riasztásokra vonatkozik (Azure Backup).
* **Erőforrás típusa**  
Ez a szabály csak a kiválasztott erőforrástípusok riasztására vonatkozik.  
A (z) **"Virtual Machines" erőforrástípus** például azt jelenti, hogy a szabály csak a virtuális gépeken lévő riasztásokra lesz érvényes.
* **Riasztási szabály azonosítója**  
Ez a szabály csak az adott riasztási szabálytól érkező riasztásokra vonatkozik. Az értéknek a riasztási szabály Resource Manager-AZONOSÍTÓjának kell lennie.  
Például a **riasztási szabály azonosítója = "/Subscriptions/SubId1/resourceGroups/RG1/Providers/Microsoft.Insights/metricalerts/API-latency"** azt jelenti, hogy ez a szabály csak az "API-késleltetés" metrikai szabályból érkező riasztásokra vonatkozik.  
_Megjegyzés – a riasztási szabályok megfelelő AZONOSÍTÓjának beszerzéséhez adja meg a riasztási szabályokat a parancssori felületről, vagy nyisson meg egy adott riasztási szabályt a portálon, majd kattintson a Tulajdonságok elemre, és másolja az "erőforrás-azonosító" értéket._
* **Figyelési feltétel**  
Ez a szabály csak a megadott figyelési feltétellel rendelkező riasztási eseményekre vonatkozik – vagy **kilőtt** vagy **megoldott**.
* **Leírás**  
Ez a szabály csak olyan riasztásokra vonatkozik, amelyek egy adott karakterláncot tartalmaznak a riasztás leírása mezőben. Ez a mező tartalmazza a riasztási szabály leírását.  
Például a **Leírás tartalmazza a "Prod"** kifejezést, amely azt jelenti, hogy a szabály csak a "Prod" karakterláncot tartalmazó riasztásokat fogja használni a leírásában.
* **Riasztási környezet (hasznos adat)**  
Ez a szabály csak olyan riasztásokra vonatkozik, amelyek egy vagy több meghatározott értéket tartalmaznak a riasztás környezeti mezőiben.  
Például a **riasztási környezet (hasznos adat) tartalmazza a "Computer-01"** karakterláncot, amely azt jelenti, hogy a szabály csak azokra a riasztásokra vonatkozik, amelyek hasznos adatai tartalmazzák a "Computer-01" karakterláncot.

Ha egy szabályban több szűrőt is beállít, mindegyiket alkalmazza. Ha például a **"= Virtual Machines** és **Súlyosság" = Sev0** értéket állítja be, akkor a szabály csak a virtuális gépeken futó Sev0-riasztásokra lesz érvényes.

![Műveleti szabály szűrői](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Mellőzés vagy műveleti csoport konfigurációja

Ezután konfigurálja a műveleti szabályt a riasztások letiltására vagy a műveleti csoport támogatására. Mindkettőt nem választhatja ki. A konfiguráció minden olyan riasztási példányon működik, amely megfelel a korábban definiált hatókörnek és szűrőknek.

#### <a name="suppression"></a>Mellőzés

Ha a **Mellőzés** lehetőséget választja, a műveletek és értesítések letiltásának időtartamát adja meg. Válasszon egyet az alábbi lehetőségek közül:
* **Mostantól (mindig)**: az összes értesítést határozatlan ideig letiltja.
* **Ütemezett időpontban**: az értesítések letiltását egy kötött időtartamon belül.
* **Ismétlődés** esetén: az értesítések napi, heti vagy havi rendszerességű ütemezéseit letiltja.

![Műveleti szabály letiltása](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveletcsoport

Ha a váltásban a **műveleti csoport** lehetőséget választja, akkor adjon hozzá egy meglévő műveleti csoportot, vagy hozzon létre újat.

> [!NOTE]
> Egy műveleti szabállyal csak egy műveleti csoportot lehet hozzárendelni.

![Új műveleti szabály hozzáadása vagy létrehozása a műveleti csoport kiválasztásával](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Műveleti szabály részletei

Utolsó lépésként adja meg a következő adatokat a műveleti szabályhoz:
* Name
* Az erőforráscsoport, amelyben mentve van
* Leírás

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A műveleti szabályokat az az [monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) paranccsal hozhatja létre az Azure CLI használatával.  A `az monitor action-rule` hivatkozás csak a [Azure monitor számos Azure CLI-hivatkozásának](/cli/azure/azure-cli-reference-for-monitor)egyike.

### <a name="prepare-your-environment"></a>A környezet előkészítése

1. [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)

   Ha szeretné, a Azure Cloud Shell használatával is elvégezheti a jelen cikkben ismertetett lépéseket.  A Azure Cloud Shell egy interaktív rendszerhéj-környezet, amelyet a böngészőben használhat.  Cloud Shell elindítása a következő módszerek egyikével:

   - Nyissa meg Cloud Shell [https://shell.azure.com](https://shell.azure.com)

   - A [Azure Portal](https://portal.azure.com) jobb felső sarkában lévő menüsorban kattintson a **Cloud Shell** gombra

1. Jelentkezzen be.

   Ha a parancssori felület helyi telepítését használja, jelentkezzen be az az [login](/cli/azure/reference-index#az-login) parancs használatával.  A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

    ```azurecli
    az login
    ```

1. A `alertsmanagement` bővítmény telepítése

   A `az monitor action-rule` parancs az alapszintű Azure CLI kísérleti bővítménye. További információ a bővítmények [használatáról az Azure CLI használatával](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   A következő figyelmeztetés várható.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Műveleti szabályok létrehozása az Azure CLI-vel

A szükséges és választható paraméterek megismeréséhez tekintse meg az Azure CLI hivatkozási tartalmát az [az monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) paranccsal.

Hozzon létre egy műveleti szabályt, amely letiltja az értesítéseket egy erőforráscsoporthoz.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Hozzon létre egy műveleti szabályt, amely minden hétvégén letiltja az összes Sev4-riasztás értesítéseit az előfizetésben lévő összes virtuális gépen.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>1. forgatókönyv: riasztások letiltása súlyosság alapján

A contoso minden hétvégén szeretné letiltani az összes Sev4-riasztás értesítéseit az előfizetési **ContosoSub** lévő összes virtuális gépen.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoSub**
* Szűrők
    * Súlyosság = **Sev4**
    * Erőforrás típusa = **Virtual Machines**
* Törlés az ismétlődési beállítással hetente, a **szombat** és a **vasárnap** bejelölve

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2. forgatókönyv: riasztások letiltása a riasztási környezet (hasznos adat) alapján

A contoso szeretné letiltani az értesítéseket minden, a **ContosoSub** -hez a **Computer-01** -hez létrehozott összes naplózási riasztáshoz, mert a karbantartási folyamatban van.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoSub**
* Szűrők
    * Szolgáltatás figyelése = **log Analytics**
    * A riasztási környezet (hasznos adat) tartalmazza a **számítógépet – 01**
* Letiltási beállítás **mostantól (mindig)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3. forgatókönyv: erőforráscsoport által definiált műveleti csoport

A contoso [metrikus riasztást adott meg egy előfizetési szinten](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). De meg szeretné határozni azokat a műveleteket, amelyek kifejezetten az erőforráscsoport **ContosoRG** létrehozott riasztásokhoz kapcsolódnak.

**Megoldás:** Műveleti szabály létrehozása a alábbiakkal:
* Hatókör = **ContosoRG**
* Nincsenek szűrők
* **ContosoActionGroup** beállított műveleti csoport

> [!NOTE]
> *A műveleti szabályokban és a riasztási szabályokban definiált műveleti csoportok egymástól függetlenül működnek, és nincs ismétlődés.* Ha a fentebb ismertetett forgatókönyvben egy műveleti csoport van megadva a riasztási szabályhoz, a művelet a műveleti szabályban definiált műveleti csoporttal együtt aktiválódik.

## <a name="managing-your-action-rules"></a>A műveleti szabályok kezelése

### <a name="portal"></a>[Portál](#tab/portal)

A következő lista nézetből megtekintheti és kezelheti a műveleti szabályokat:

![Műveleti szabályok listája nézet](media/alerts-action-rules/action-rules-list-view.png)

Itt engedélyezheti, letilthatja vagy törölheti az egyes műveletek szabályait a mellette található jelölőnégyzet bejelölésével. A műveleti szabály kiválasztásakor megnyílik a konfigurációs lapja. Az oldalon a műveleti szabály definíciójának frissítése és engedélyezése vagy letiltása segít.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-ből megtekintheti és kezelheti a művelet szabályait az az [monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) parancs használatával.

A műveleti szabályok Azure CLI-vel való kezelése előtt készítse elő a környezetet a [műveleti szabály konfigurálása](#configuring-an-action-rule)című részben ismertetett utasítások alapján.

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Ajánlott eljárások

Az [eredmények száma](./alerts-unified-log.md) lehetőséggel létrehozott riasztások a teljes keresési eredmény használatával hoznak létre egyetlen riasztási példányt (amely több számítógép között is terjedhet). Ebben a forgatókönyvben, ha egy műveleti szabály a **riasztási környezet (hasznos adattartalom)** szűrőt használja, akkor az a riasztási példányon működik, amíg van egyezés. A korábban ismertetett 2. forgatókönyvben, ha a generált naplózási riasztás keresési eredményei mind a **Computer-01** , mind a **Computer-02** karakterláncot tartalmazzák, a teljes értesítés le lesz tiltva. Nincs értesítés létrehozva a **Computer-02-** hez.

![A diagramon a műveleti szabályok és a naplózási riasztások láthatók, amelyek egyetlen riasztási példánnyal vannak kiemelve.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Ha a riasztásokat a műveleti szabályokkal szeretné legjobban használni, hozzon létre naplózási riasztásokat a [metrika mértékének](./alerts-unified-log.md) beállításával. Ezzel a beállítással külön riasztási példányok jönnek létre a definiált csoport mező alapján. Ezt követően a 2. forgatókönyvben különálló riasztási példányok jönnek létre a **Computer-01** és a **Computer-02** esetében. A forgatókönyvben ismertetett műveleti szabály miatt a rendszer csak a **számítógép-01** értesítését letiltja. A **számítógép-02-** es értesítés a szokásos módon folytatódik.

![Műveleti szabályok és naplók riasztásai (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>A műveleti szabály konfigurálása közben szeretném megtekinteni az összes lehetséges átfedésben lévő művelet szabályát, így elkerülhetők az ismétlődő értesítések. Ez lehetséges?

Miután meghatározta a hatókört a műveleti szabály konfigurálása során, megtekintheti az ugyanazon hatókörön (ha van) átfedésben lévő műveleti szabályok listáját. Az átfedés az alábbi lehetőségek egyike lehet:

* Pontos egyezés: például a definiált műveleti szabály és az átfedésben lévő műveleti szabály ugyanahhoz az előfizetéshez van kapcsolva.
* Egy részhalmaz: például a definiált műveleti szabály egy előfizetésen belül van, és az átfedésben lévő műveleti szabály az előfizetésben található erőforráscsoporthoz van.
* Felülbíráló: például a definiált műveleti szabály egy erőforráscsoport, és az átfedésben lévő műveleti szabály az erőforráscsoportot tartalmazó előfizetésen található.
* Metszet: például a definiált műveleti szabály a **VM1** és a **VM2**, és az átfedésben lévő műveleti szabály a **VM2** és a **VM3**.

![A képernyőképen az új műveleti szabály lap jelenik meg, amelyen az átfedésben lévő műveleti szabályok láthatók az azonos hatókörű ablakban megadott műveleti szabályokban.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>A riasztási szabályok konfigurálása során tudnia kell, hogy vannak-e már definiált műveleti szabályok, amelyek az általam definiált riasztási szabály esetében működhetnek?

Miután meghatározta a riasztási szabály céljának erőforrását, megtekintheti az ugyanazon hatókörön (ha van) lévő műveleti szabályok listáját, ha a **műveletek** szakaszban a **konfigurált műveletek megtekintése** lehetőségre kattint. A lista a hatókörre vonatkozó következő forgatókönyvek alapján töltődik fel:

* Pontos egyezés: például a definiált riasztási szabály, és a műveleti szabály ugyanahhoz az előfizetéshez tartoznak.
* Egy részhalmaz: például a definiált riasztási szabály egy előfizetésen belül van, és a műveleti szabály az előfizetésben található erőforráscsoporthoz van.
* Felülbíráló: például a definiált riasztási szabály egy erőforráscsoport, és a műveleti szabály az erőforráscsoportot tartalmazó előfizetésen található.
* Metszet: például a definiált riasztási szabály a **VM1** és a **VM2**, a műveleti szabály pedig a **VM2** és a **VM3**.

![Átfedésben lévő műveleti szabályok](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Megtekinthetem a műveleti szabály által letiltott riasztásokat?

A [riasztások listája oldalon](./alerts-managing-alert-instances.md)kiválaszthatja az **elnyomási állapot** nevű további oszlopot is. Ha a riasztási példány értesítését a rendszer letiltotta, akkor az az állapot jelenik meg a listában.

![Letiltott riasztási példányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Ha van műveleti szabály egy műveleti csoporttal, és egy másik a letiltással aktív ugyanazon a hatókörön, mi történik?

A Mellőzés mindig elsőbbséget élvez ugyanazon a hatókörön.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha olyan erőforrással rendelkezem, amelyre két műveleti szabály vonatkozik? Kapok egy vagy két értesítést? A **VM2** például a következő esetben:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

A VM1 és a VM3 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. A **VM2** összes riasztása esetében a műveleti csoport AG1 kétszer aktiválódik, mert a műveleti szabályok nem ismétlik le a műveleteket.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha egy erőforrást két külön műveleti szabályban figyeltem meg, és egy másikat a művelet közben letiltanak? A **VM2** például a következő esetben:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

A VM1 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. A VM2 és a VM3 összes riasztására vonatkozó műveleteket és értesítéseket a rendszer letiltja.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Mi történik, ha a riasztási szabály és az ugyanahhoz az erőforráshoz definiált műveleti szabály különböző műveleti csoportokat hív meg? A **VM1** például a következő esetben:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

A VM1 összes riasztása esetén a Action Group AG1 egyszer aktiválódik. Ha a "rule1" riasztási szabályt aktiválták, akkor a AG2 is aktiválódik. A műveleti szabályokban és a riasztási szabályokban definiált műveleti csoportok egymástól függetlenül működnek, és nincs ismétlődés.

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure-beli riasztásokról](./alerts-overview.md)
