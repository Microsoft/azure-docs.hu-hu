---
title: A riasztások Azure Monitor szabályai
description: A szabályzatok Azure Monitor és konfigurálásuk és kezelésük ismertetése.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4f54ee7d21d52386bd18921aec33cabe02046852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772558"
---
# <a name="action-rules-preview"></a>Műveletszabályok (előzetes verzió)

A műveletszabályokkal hozzáadhatja vagy letilthatja az ön által kibocsátott riasztások műveletcsoportját. Egyetlen szabály különböző célerőforrások hatókörére vonatkozhat, például egy adott erőforrásra (például egy adott virtuális gépre) vonatkozó riasztásra, vagy egy előfizetés bármely erőforrására vonatkozó riasztásra. Szükség esetén különböző szűrőket adhat hozzá, amelyek szabály által lefedett riasztásokat szabályoznak, és meghatározhat egy ütemezést, például hogy az csak munkaidőn kívül vagy egy tervezett karbantartási időszakban legyen érvényben.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Miért és mikor érdemes a műveletszabályokat használni?

### <a name="suppression-of-alerts"></a>Riasztások mellőzése

Számos esetben hasznos lehet letiltani a riasztások által generált értesítéseket. Ezek a forgatókönyvek a tervezett karbantartási időszakban való mellőzéstől a munkaidőn kívüli időszakban való mellőzésig tartnak. A  **ContosoVM-hez** felelős csapat például szeretné letiltani a riasztási értesítéseket a következő hétvégére, mert a **ContosoVM** tervezett karbantartás alatt áll.

Bár a csapat letilthatja a **ContosoVM-re** manuálisan konfigurált riasztási szabályokat (és a karbantartás után újra engedélyezheti őket), ez nem egyszerű folyamat. A műveletszabályok segítségével nagy léptékben határozhatja meg a riasztások mellőzését, és rugalmasan konfigurálhatja a mellőzés időszakát. Az előző példában a csapat meghatározhat egy olyan műveletszabályt a **ContosoVM-hez,** amely letiltja a hétvégére vonatkozó összes riasztási értesítést.

### <a name="actions-at-scale"></a>Nagy léptékű műveletek

Bár a riasztási szabályok segítenek meghatározni azt a műveletcsoportot, amely a riasztás generálásakor aktiválódik, az ügyfelek gyakran közös műveletcsoporttal is tagjaik vannak a műveletek hatókörében. Például a ContosoRG erőforráscsoportért felelős csapat valószínűleg ugyanazt a műveletcsoportot fogja definiálni a **ContosoRG** erőforráscsoportban meghatározott összes **riasztási szabályhoz.**

A műveletszabályok segítenek leegyszerűsíteni ezt a folyamatot. Ha nagy léptékű műveleteket definiál, a műveletcsoport a konfigurált hatókörben létrehozott összes riasztáshoz aktiválható. Az előző példában a csapat meghatározhat  egy műveletszabályt a ContosoRG-hez, amely ugyanazt a műveletcsoportot aktiválja a benne generált összes riasztáshoz.

> [!NOTE]
> A műveletszabályok nem vonatkoznak a Azure Service Health riasztásra.

## <a name="configuring-an-action-rule"></a>Műveleti szabály konfigurálása

### <a name="portal"></a>[Portál](#tab/portal)

A szolgáltatás eléréséhez válassza a Műveletek  **kezelése** lehetőséget a riasztások kezdőlapján a Azure Monitor. Ezután válassza a **Műveletszabályok (előzetes verzió) lehetőséget.** A szabályokhoz úgy férhet hozzá, hogy a riasztások kezdőlapjának irányítópultján a Műveletszabályok **(előzetes verzió)** gombra választ.

![A kezdőlapon Azure Monitor szabályok](media/alerts-action-rules/action-rules-landing-page.png)

Válassza **az + Új műveletszabály lehetőséget.**

![Képernyőkép a Műveletek kezelése lapról, kiemelt Új műveletszabály gombbal.](media/alerts-action-rules/action-rules-new-rule.png)

Másik lehetőségként létrehozhat egy műveletszabályt a riasztási szabály konfigurálása közben.

![Képernyőkép a Szabály létrehozása lapról, kiemelt Műveletszabály létrehozása gombbal.](media/alerts-action-rules/action-rules-alert-rule.png)

Meg kell jelenni a műveletszabályok létrehozására vonatkozó folyamatlapnak. Konfigurálja a következő elemeket:

![Új műveletszabály-létrehozási folyamat](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Hatókör

Először válassza ki a hatókört (Azure-előfizetés, erőforráscsoport vagy célerőforrás). Egy előfizetésen belül több hatókör kombinációját is kiválaszthatja.

![Műveletszabály hatóköre](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria&quot;></a>Szűrési feltételek

Szűrőket is definiálhat, hogy a szabály a riasztások egy adott részkészletére vagy az egyes riasztások adott eseményeire vonatkozzon (például csak &quot;Ki van bocsátva&quot; vagy csak &quot;Megoldva").

A rendelkezésre álló szűrők a következők:

* **Súlyosság**  
Ez a szabály csak a kiválasztott súlyosságú riasztások esetén érvényes.  
Például a **severity = "Sev1"** azt jelenti, hogy a szabály csak az Sev1 súlyosságú riasztások esetén lesz érvényes.
* **Szolgáltatás figyelése**  
Ez a szabály csak a kiválasztott monitorozási szolgáltatásoktól érkező riasztások esetén érvényes.  
Például a **monitor service = "Azure Backup"** azt jelenti, hogy a szabály csak a biztonsági mentési riasztásra vonatkozik (amely a Azure Backup).
* **Erőforrás típusa**  
Ez a szabály csak a kiválasztott erőforrástípusok riasztásaira vonatkozik.  
Például az **erőforrástípus = "Virtual Machines"** azt jelenti, hogy a szabály csak a virtuális gépek riasztásaira lesz érvényes.
* **Riasztási szabály azonosítója**  
Ez a szabály csak az adott riasztási szabályból érkező riasztások esetén érvényes. Az értéknek a Resource Manager szabály azonosítójának kell lennie.  
Például a riasztási szabály **azonosítója = "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** azt jelenti, hogy ez a szabály csak az "API-késés" metrika riasztási szabályból érkező riasztásokra lesz érvényes.  
_MEGJEGYZÉS – a megfelelő riasztási szabály azonosítóját úgy kaphatja meg, ha listálja a riasztási szabályokat a CLI-ről, vagy megnyit egy adott riasztási szabályt a portálon, a "Tulajdonságok" gombra kattint, és kimásolód az "Erőforrás-azonosító" érték._
* **Feltétel figyelése**  
Ez a szabály csak olyan riasztási eseményekre vonatkozik, amelyek a megadott figyeléses feltétellel (például **"Fired"** vagy **"Resolved" (Megoldva) vannak.**
* **Leírás**  
Ez a szabály csak azokra a riasztásra vonatkozik, amelyek egy adott sztringet tartalmaznak a riasztás leírása mezőben. Ez a mező tartalmazza a riasztási szabály leírását.  
A leírás például **"prod"** (termék) értékeket tartalmaz, ami azt jelenti, hogy a szabály csak azokat a riasztásokat fogja megfeleltetni, amelyek leírásában a "prod" sztring található.
* **Riasztási környezet (hasznos tartalom)**  
Ez a szabály csak azokra a riasztásokra vonatkozik, amelyek egy vagy több konkrét értéket tartalmaznak a riasztási környezet mezőiben.  
A riasztási környezet (hasznos tartalom) például a **"Computer-01"** karakterláncot tartalmazza, ami azt jelenti, hogy a szabály csak azokra a riasztásra fog vonatkozni, amelyek hasznos tartalma tartalmazza a "Computer-01" sztringet.

> [!NOTE]
> Minden szűrő legfeljebb öt értéket tartalmazhat.  
> A figyelőszolgáltatás szűrője például legfeljebb öt figyelt szolgáltatásnevet tartalmazhat.




Ha több szűrőt is beállít egy szabályban, mindegyik érvényes lesz. Ha például az erőforrástípus **= "Virtual Machines"** és **a severity = "Sev0"** értékeket adja meg, akkor a szabály csak a virtuális gépeken lévő Sev0-riasztások esetén érvényes.

![Műveletszabály szűrői](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Mellőzés vagy műveletcsoport konfigurálása

Ezután konfigurálja a műveletszabályt a riasztások mellőzése vagy a műveletcsoport támogatása érdekében. Nem választhatja mindkettőt. A konfiguráció minden olyan riasztáspéldányon működik, amely megfelel a korábban meghatározott hatókörnek és szűrőknek.

#### <a name="suppression"></a>Mellőzés

Ha a **mellőzés lehetőséget** választja, konfigurálja a műveletek és értesítések mellőzését időtartamot. Válasszon egyet az alábbi lehetőségek közül:
* **Mostantól (Always)**: Határozatlan ideig letiltja az összes értesítést.
* **Ütemezett időpontban:** Letiltja az értesítéseket egy adott időtartamon belül.
* **Ismétlődéssel:** Letiltja az értesítéseket egy ismétlődő napi, heti vagy havi ütemezés szerint.

![Műveletszabály mellőzése](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Műveletcsoport

Ha a **váltógombon a Műveletcsoport** lehetőséget választja, adjon hozzá egy meglévő műveletcsoportot, vagy hozzon létre egy újat.

> [!NOTE]
> Egy műveletszabályhoz csak egy műveletcsoport társíthat.

![Új műveletszabály hozzáadása vagy létrehozása a Műveletcsoport kiválasztásával](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>A műveletszabály részletei

Végül konfigurálja a következő adatokat a műveletszabályhoz:
* Name
* Az erőforráscsoport, amelybe mentve van
* Description

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az [monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) paranccsal hozhat létre műveletszabályokat az Azure CLI-val.  A `az monitor action-rule` hivatkozás csak egy a számos Azure [CLI-referenciából a Azure Monitor.](/cli/azure/azure-cli-reference-for-monitor)

### <a name="prepare-your-environment"></a>A környezet előkészítése

1. [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)

   Ha szeretné, a Azure Cloud Shell is használhatja a cikkben található lépések befejezéséhez.  Azure Cloud Shell egy interaktív felületi környezet, amely a böngészőben használható.  Indítsa Cloud Shell a következő módszerek egyikével:

   - Nyissa Cloud Shell a következőt: [https://shell.azure.com](https://shell.azure.com)

   - Kattintson **Cloud Shell** jobb felső sarkában található menüsáv Azure Portal [](https://portal.azure.com)

1. Jelentkezzen be.

   Ha a parancssori felület helyi telepítését használja, jelentkezzen be az [az login paranccsal.](/cli/azure/reference-index#az_login)  A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

    ```azurecli
    az login
    ```

1. A bővítmény `alertsmanagement` telepítése

   A `az monitor action-rule` parancs az alapvető Azure CLI kísérleti bővítménye. További információ a bővítményreferenciákról: [Bővítmény használata az Azure CLI-val.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name alertsmanagement
   ```

   A következő figyelmeztetés várható.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Műveletszabályok létrehozása az Azure CLI használatával

A kötelező és választható paraméterekről az [Az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) Azure CLI-referenciatartalma tartalmaz további információt.

Hozzon létre egy olyan műveletszabályt, amely letiltja az értesítéseket egy erőforráscsoportban.

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

Hozzon létre egy olyan műveletszabályt, amely minden hétvégente letiltja az összes Sev4-riasztás értesítését az előfizetésen belüli összes virtuális gépre.

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

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>1. forgatókönyv: Riasztások mellőzése súlyosság alapján

A Contoso minden hétvégen szeretné letiltani az összes Sev4-riasztás értesítését az előfizetésen belüli összes **virtuális** gépről.

**Megoldás:** Hozzon létre egy műveletszabályt a következővel:
* Hatókör = **ContosoSub**
* Szűrők
    * Súlyosság = **Sev4**
    * Erőforrástípus = **Virtual Machines**
* Mellőzés heti ismétlődéssel, **valamint szombat és** vasárnap **bejelölve**

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>2. forgatókönyv: Riasztások mellőzése riasztási környezet alapján (hasznos adatok)

A Contoso szeretné letiltani az összes, a **ContosoSubban a ContosoSubban,** a karbantartás során a **Computer-01** számítógéphez generált naplóriasztás értesítését.

**Megoldás:** Hozzon létre egy műveletszabályt a következővel:
* Hatókör = **ContosoSub**
* Szűrők
    * Monitor Service = **Log Analytics**
    * A riasztási környezet (hasznos adatokat) **tartalmazza: Computer-01**
* A Mellőzés **beállítása Mostantól (Mindig)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>3. forgatókönyv: Erőforráscsoportban definiált műveletcsoport

A Contoso egy [metrikákra vonatkozó riasztást adott meg az előfizetés szintjén.](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) De meg szeretné határozni azokat a műveleteket, amelyek kifejezetten a ContosoRG erőforráscsoportból létrehozott riasztások **kiváltása érdekében aktiválódnak.**

**Megoldás:** Hozzon létre egy műveletszabályt a következővel:
* Hatókör = **ContosoRG**
* Nincsenek szűrők
* A műveletcsoport beállítása **ContosoActionGroup**

> [!NOTE]
> *A műveletszabályokban és riasztási szabályokban meghatározott műveletcsoportok egymástól függetlenül, deduplikáció nélkül működnek.* A korábban ismertetett forgatókönyvben, ha a riasztási szabályhoz definiálva van egy műveletcsoport, az a műveletszabályban meghatározott műveletcsoporttal együtt aktiválódik.

## <a name="managing-your-action-rules"></a>A műveletszabályok kezelése

### <a name="portal"></a>[Portál](#tab/portal)

A listanézetben megtekintheti és kezelheti a műveletszabályokat:

![Műveletszabályok listanézete](media/alerts-action-rules/action-rules-list-view.png)

Itt nagy méretekben engedélyezheti, letilthatja vagy törölheti a műveletszabályokat a mellettük található jelölőnégyzet be jelölőnégyzetének be választásával. Amikor kiválaszt egy műveletszabályt, megnyílik annak konfigurációs lapja. A lapon frissítheti a műveletszabály definícióját, és engedélyezheti vagy letilthatja azt.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A műveletszabályokat az Azure CLI [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) parancsával megtekintheti és kezelheti.

Mielőtt a műveletszabályokat az Azure CLI-val felügyeli, készítse elő a környezetet a Következő [lépésekkel: Műveletszabály konfigurálása.](#configuring-an-action-rule)

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

Az eredmények számával létrehozott [](./alerts-unified-log.md) naplóriasztás egyetlen riasztáspéldányt hoz létre a teljes keresési eredmény használatával (amely több számítógépre is kihathat). Ebben a forgatókönyvben, ha egy műveletszabály a Riasztási környezet **(hasznos adat)** szűrőt használja, akkor a riasztási példányon működik, ha van egyezés. Ha a korábban ismertetett 2. forgatókönyvben a generált naplóriasztás keresési eredményei tartalmazzák a **Computer-01** és **a Computer-02** riasztást is, a teljes értesítés le van tiltva. A **Computer-02-hez** egyáltalán nem jön létre értesítés.

![A diagram a műveletszabályokat és a naplóriasztásokat jeleníti meg egyetlen riasztáspéldány kiemelése után.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Ahhoz, hogy a naplóriasztásokat a lehető legjobban használva használjuk a műveletszabályokkal, hozzon létre naplóriasztásokat a [metrikamérési beállítással.](./alerts-unified-log.md) Ez a beállítás külön riasztáspéldányokat hoz létre a meghatározott csoportmező alapján. Ezután a 2. forgatókönyvben külön riasztáspéldányok jönnek létre a **Computer-01** és **Computer-02 számítógépekhez.** A forgatókönyvben leírt műveletszabály miatt csak a **Computer-01** értesítése lesz letiltva. A **Computer-02 értesítése** a szokásos módon továbbra is megjelenik.

![Műveletszabályok és naplóriasztás (eredmények száma)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>GYIK

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Amíg egy műveletszabályt konfigurálok, szeretném látni az összes lehetséges átfedésben lévő műveletszabályt, hogy elkerüljem az ismétlődő értesítéseket. Lehetséges ezt megtenni?

Miután meghatározta a hatókört a műveletszabály konfigurálásakor, láthatja az azonos hatókörre vonatkozó műveletszabályok listáját (ha vannak). Ez az átfedés a következő lehetőségek egyike lehet:

* Pontos egyezés: Például a meghatározott műveletszabály és az átfedésben lévő műveletszabály ugyanazon az előfizetésen van.
* Részkészlet: Például a definiálni tett műveletszabály egy előfizetéshez tartozik, az átfedésben lévő műveletszabály pedig az előfizetésen belüli erőforráscsoporthoz.
* Egy szuperkészlet: Például a definiálni kívánt műveletszabály egy erőforráscsoporton van, az átfedésben lévő műveletszabály pedig az erőforráscsoportot tartalmazó előfizetésen.
* Metszet: A definiálni szükséges műveletszabály például a **VM1** és a **VM2** virtuális gépen, az átfedésben lévő műveletszabály pedig a **VM2** és a **VM3 virtuális** gépen található.

![Képernyőkép az Új műveletszabály lapról, amely az ugyanazon hatókörablakban definiált műveletszabályok között átfedésben lévő műveletszabályokat jeleníti meg.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Amíg konfigurálok egy riasztási szabályt, lehetséges-e tudni, hogy vannak-e már meghatározott műveletszabályok, amelyek a meghatározott riasztási szabályra is vonatkoznak?

Miután meghatározta a riasztási szabály célerőforrását, a Műveletek szakasz Konfigurált műveletek megtekintése  lehetőségének kiválasztásával megtekintheti az azonos hatókörre vonatkozó műveletszabályok **listáját** (ha vannak). A lista a hatókör következő forgatókönyvei alapján van kitöltve:

* Pontos egyezés: Például a riasztási szabály, amit definiál, és a műveletszabály ugyanazon az előfizetésen van.
* Részkészlet: A riasztási szabály például egy előfizetésen, a műveletszabály pedig az előfizetésen belüli erőforráscsoportra vonatkozik.
* Egy szuperkészlet: A definiálni kívánt riasztási szabály például egy erőforráscsoporthoz tartozik, a műveletszabály pedig az erőforráscsoportot tartalmazó előfizetéshez.
* Metszet: A riasztási szabály például a **VM1** és a **VM2** virtuális gépen, a műveletszabály pedig a **VM2-en** és a **VM3-on található.**

![Átfedésben lévő műveletszabályok](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Látom a műveletszabály által mellőzt riasztásokat?

A [riasztások listáját tartalmazó lapon](./alerts-managing-alert-instances.md)kiválaszthat egy további oszlopot, a **Mellőzés állapota oszlopot.** Ha egy riasztási példány értesítése le lett tiltva, akkor ez az állapot jelenik meg a listában.

![Letiltott riasztáspéldányok](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Mi történik, ha van olyan műveletszabály, amely egy műveletcsoporthoz tartozik, egy másik pedig aktív lefoktatásos hatókörrel?

A mellőzés mindig elsőbbséget élvez ugyanabban a hatókörben.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha olyan erőforrásom van, amelyet két műveletszabály fed le? Kapok egy vagy két értesítést? Például: **VM2** a következő forgatókönyvben:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

A VM1 és VM3 virtuális gépeken minden riasztásnál egyszer aktiválódik az AG1 műveletcsoport. A **VM2 minden riasztása** után az AG1 műveletcsoport kétszer aktiválódik, mert a műveletszabályok nem deduplikálják a műveleteket.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Mi történik, ha két külön műveletszabályban figyelek egy erőforrást, és az egyik beavatkozást, míg egy másikat mellőzni kell? Például: **VM2** a következő forgatókönyvben:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

A VM1 virtuális gépen minden riasztásnál egyszer aktiválódik az AG1 műveletcsoport. A VM2 és VM3 minden riasztásának műveletei és értesítései le lesznek tiltva.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Mi történik, ha egy riasztási szabály és egy műveletszabály van meghatározva ugyananhoz az erőforráshoz, amely különböző műveletcsoportokat hív meg? Például: **VM1** a következő forgatókönyvben:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

A VM1 virtuális gépen minden riasztásnál egyszer aktiválódik az AG1 műveletcsoport. Ha a "rule1" riasztási szabály aktiválódik, az AG2-t is aktiválja. A műveletszabályokban és riasztási szabályokban meghatározott műveletcsoportok egymástól függetlenül, deduplikáció nélkül működnek.

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure-beli riasztásokkal kapcsolatban](./alerts-overview.md)
