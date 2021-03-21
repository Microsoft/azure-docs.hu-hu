---
title: Szabályzatok megfelelőségi állapotának beolvasása
description: Azure Policy értékelések és hatások határozzák meg a megfelelőséget. Ismerje meg, hogyan kérheti le Azure-erőforrásai megfelelőségi adatait.
ms.date: 03/16/2021
ms.topic: how-to
ms.openlocfilehash: cdd23d685750fb8a5d3803f4b6030e7e67bbddce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598541"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure-erőforrások megfelelőségi információk beolvasása

Azure Policy egyik legnagyobb előnye az, hogy az előfizetések és a vezérlőelemek az előfizetések vagy az előfizetések [felügyeleti csoportjai](../../management-groups/overview.md) számára biztosítanak forrásokat. Ez a vezérlő számos különböző módon gyakorolható, például megakadályozhatja, hogy az erőforrások nem a megfelelő helyen jöjjenek létre, melyek a közös és konzisztens címkék használatát kényszerítik, vagy a meglévő erőforrásokat naplózzák a megfelelő konfigurációkhoz és beállításokhoz. A Azure Policy az összes esetben létrehozta az adatait, így megismerheti a környezet megfelelőségi állapotát.

A szabályzat és a kezdeményezési hozzárendelések által létrehozott megfelelőségi adatokhoz többféleképpen férhet hozzá:

- A [Azure Portal](#portal) használata
- [Parancssori](#command-line) parancsfájlok használatával

Mielőtt megvizsgáljuk a megfelelőségi jelentés módszereit, nézzük meg, hogy mikor frissülnek a megfelelőségi információk, és milyen gyakorisággal és eseményekkel indítják el a kiértékelési ciklust.

> [!WARNING]
> Ha a megfelelőségi állapotot **nem regisztráltként** jelenti, ellenőrizze, hogy a **Microsoft. PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyekkel az [Azure RBAC engedélyei](../overview.md#azure-rbac-permissions-in-azure-policy)című cikkben leírtak szerint Azure Policy.

## <a name="evaluation-triggers"></a>Próbaverziós eseményindítók

A befejezett kiértékelési ciklusok eredményei az erőforrás- `Microsoft.PolicyInsights` szolgáltatón keresztül `PolicyStates` és a `PolicyEvents` műveletekben érhetők el. Az Azure Policy-információk REST API működésével kapcsolatos további információkért tekintse meg az [Azure Policy](/rest/api/policy/)elemzése című témakört.

A hozzárendelt szabályzatok és kezdeményezések értékelése a különböző események eredményeképpen történik:

- Egy házirend vagy kezdeményezés újonnan van hozzárendelve egy hatókörhöz. A hozzárendelés a meghatározott hatókörre való alkalmazása körülbelül 30 percet vesz igénybe. Az alkalmazása után a kiértékelési ciklus az adott hatókörön belüli erőforrásokra az újonnan hozzárendelt házirenddel vagy kezdeményezéssel kezdődik, és a házirend vagy kezdeményezés által használt hatástól függően az erőforrások megfelelőnek, nem megfelelőnek vagy adómentesnek vannak megjelölve. Nagy mennyiségű erőforrásra kiértékelt nagyméretű házirend vagy kezdeményezés időt vehet igénybe. Ezért nincs előre definiált várakozás, ha a kiértékelési ciklus befejeződik. A befejezést követően a frissített megfelelőségi eredmények elérhetők a Portálon és az SDK-ban.

- Egy hatókörhöz már hozzárendelt házirend vagy kezdeményezés frissül. A kiértékelési ciklus és a forgatókönyv időzítése megegyezik egy hatókör új hozzárendelésével.

- Egy erőforrás üzembe helyezése vagy frissítése Azure Resource Manageron, REST APIon vagy egy támogatott SDK-n keresztüli hozzárendelési hatókörön belül történik. Ebben az esetben az egyes erőforrásokra vonatkozó hatás esemény (Hozzáfűzés, naplózás, megtagadás, üzembe helyezés) és a megfelelő állapotadatok elérhetővé válnak a Portálon és az SDK-k körülbelül 15 perccel később. Ez az esemény nem okoz más erőforrások kiértékelését.

- A [házirend-kivétel](../concepts/exemption-structure.md) létrehozása, frissítése vagy törlése megtörtént. Ebben az esetben a rendszer kiértékeli a megfelelő hozzárendelést a megadott kivételi hatókörhöz.

- Szabványos megfelelőség kiértékelési ciklusa. 24 óránként egyszer automatikusan újraértékeli a hozzárendeléseket. Számos erőforrás nagyméretű házirendje vagy kezdeményezése időt vehet igénybe, így a próbaverzió befejezését követően nincs előre definiált várakozási idő. A befejezést követően a frissített megfelelőségi eredmények elérhetők a Portálon és az SDK-ban.

- A [vendég-konfiguráció](../concepts/guest-configuration.md) erőforrás-szolgáltatóját egy felügyelt erőforrás megfelelőségi részleteivel frissíti.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti értékelési vizsgálat

Az előfizetések vagy az erőforráscsoportok kiértékelése az Azure CLI-vel, a Azure PowerShellával, a REST API vagy a [Azure Policy megfelelőségi vizsgálat GitHub-művelettel](https://github.com/marketplace/actions/azure-policy-compliance-scan)indítható el.
Ez a vizsgálat egy aszinkron folyamat.

#### <a name="on-demand-evaluation-scan---github-action"></a>Igény szerinti értékelés vizsgálata – GitHub-művelet

A [Azure Policy megfelelőség-ellenőrzési művelettel](https://github.com/marketplace/actions/azure-policy-compliance-scan) elindíthat egy igény szerinti értékelési vizsgálatot a [GitHub-munkafolyamatból](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) egy vagy több erőforráson, erőforráscsoporton vagy előfizetésen, és a munkafolyamatot az erőforrások megfelelőségi állapota alapján. A munkafolyamatot úgy is beállíthatja, hogy az ütemezett időpontban fusson, hogy a legújabb megfelelőségi állapotot a megfelelő időpontban kapja meg. Opcionálisan ez a GitHub-művelet jelentést készíthet a beolvasott erőforrások megfelelőségi állapotáról további elemzéshez vagy archiváláshoz.

Az alábbi példa egy előfizetés megfelelőségi vizsgálatát futtatja. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

További információ és munkafolyamat-példák: [Azure Policy megfelelőségi vizsgálathoz használható GitHub-művelet](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Igény szerinti értékelés vizsgálata – Azure CLI

A megfelelőségi vizsgálat az az [Policy State trigger-Scan](/cli/azure/policy/state#az_policy_state_trigger_scan) paranccsal indítható el.

Alapértelmezés szerint `az policy state trigger-scan` a elindítja az aktuális előfizetésben lévő összes erőforrás értékelését. Egy adott erőforráscsoport értékelésének elindításához használja az **Erőforrás-csoport** paramétert. Az alábbi példa a _MyRG_ erőforráscsoport aktuális előfizetésében elindítja a megfelelőségi vizsgálatot:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Dönthet úgy is, hogy nem várja meg az aszinkron folyamat befejeződését, mielőtt folytatná a **No-WAIT** paramétert.

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Igény szerinti értékelés vizsgálata – Azure PowerShell

A megfelelőségi vizsgálat a [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) parancsmaggal indul el.

Alapértelmezés szerint `Start-AzPolicyComplianceScan` a elindítja az aktuális előfizetésben lévő összes erőforrás értékelését. Egy adott erőforráscsoport értékelésének elindításához használja a **ResourceGroupName** paramétert. Az alábbi példa a _MyRG_ erőforráscsoport aktuális előfizetésében elindítja a megfelelőségi vizsgálatot:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Az eredmények kimenetének megadását, illetve a háttérben futtatott [feladatok](/powershell/module/microsoft.powershell.core/about/about_jobs)futtatása előtt PowerShell-várakozást is kérhet az aszinkron hívás befejezéséhez. Ha a háttérben a megfelelőségi vizsgálat futtatásához PowerShell-feladatot szeretne használni, használja az **AsJob** paramétert, és állítsa az értéket egy objektumra, például `$job` a következő példában:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

A feladatok állapotát az objektum ellenőrzésével ellenőrizheti `$job` . A feladattípus típusa a következő: `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . `Get-Member`Az objektum használatával `$job` megtekintheti a rendelkezésre álló tulajdonságokat és metódusokat.

Amíg a megfelelőségi vizsgálat fut, az `$job` objektum kimenete az alábbi eredményeket ellenőrzi:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Ha a megfelelőségi vizsgálat befejeződik, az **állapot** tulajdonság a _befejezett_ értékre változik.

#### <a name="on-demand-evaluation-scan---rest"></a>Igény szerinti értékelés vizsgálata – REST

Aszinkron folyamatként a vizsgálat elindításához szükséges REST-végpont nem várja meg, amíg a vizsgálat nem fejeződik be a válaszadáshoz. Ehelyett egy URI-t biztosít a kért értékelés állapotának lekérdezéséhez.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}` – A helyére írja be az erőforráscsoport nevét.
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat támogatja az előfizetésben vagy egy erőforráscsoporthoz lévő erőforrások értékelését. A következő URI-struktúrák használatával indítson el egy REST API **post** paranccsal vizsgálatot a hatókörben:

- Előfizetés

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Erőforráscsoport

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

A hívás **202 elfogadott** állapotot ad vissza. A válasz fejlécében szereplő **hely** tulajdonsága a következő formátumú:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` statikusan jön létre a kért hatókörhöz. Ha egy hatókör már igény szerinti vizsgálatot futtat, az új vizsgálat nem indul el. Ehelyett az új kérelem ugyanazt a `{ResourceContainerGUID}` **Location** URI-t kapja meg az állapothoz. Egy REST API **Get** parancs a **Location** URI-hoz egy **202** -as értéket ad vissza, miközben a kiértékelés folyamatban van. Az értékelési vizsgálat befejezését követően **200 OK** állapotot ad vissza. A befejezett vizsgálat törzse egy JSON-válasz a (z) állapottal:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Igény szerinti értékelés vizsgálata – Visual Studio Code

A Visual Studio Code-hoz készült Azure Policy-bővítmény képes egy adott erőforrásra vonatkozó értékelési vizsgálat futtatására. Ez a vizsgálat egy szinkron folyamat, ellentétben a Azure PowerShell és a REST metódusokkal.
A részleteket és a lépéseket lásd: [igény szerinti értékelés a vs Code bővítménnyel](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>A megfelelőség működése

Egy hozzárendelésben az erőforrás **nem megfelelő** , ha nem követi a házirend-vagy kezdeményezési szabályokat, és nem _mentesül_. A következő táblázat bemutatja, hogyan működnek a különböző szabályzatok az eredményül kapott megfelelőségi állapotra vonatkozó feltételek kiértékelésével:

| Erőforrás-állapot | Hatás | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Új vagy frissített | Naplózás, módosítás, AuditIfNotExist | Igaz | Nem megfelelő |
| Új vagy frissített | Naplózás, módosítás, AuditIfNotExist | Hamis | Megfelelő |
| Létezik | Megtagadás, naplózás, Hozzáfűzés, módosítás, DeployIfNotExist, AuditIfNotExist | Igaz | Nem megfelelő |
| Létezik | Megtagadás, naplózás, Hozzáfűzés, módosítás, DeployIfNotExist, AuditIfNotExist | Hamis | Megfelelő |

> [!NOTE]
> A DeployIfNotExist-és AuditIfNotExist-effektusok esetében az IF utasításnak IGAZnak kell lennie, és a létezési feltétel nem megfelelőnek kell lennie. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Tegyük fel például, hogy van egy erőforráscsoport – ContsoRG, és néhány Storage-fiók (piros színnel), amelyek nyilvános hálózatokon vannak kitéve.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="A contoso R G erőforráscsoporthoz tartozó nyilvános hálózatok számára elérhető Storage-fiókok diagramja." border="false":::
   Ábra, amely a contoso R G erőforráscsoporthoz tartozó öt Storage-fiókhoz tartozó képeket mutatja.  A Storage-fiókok egy és három kék színűek, míg a Storage-fiókok két, négy és öt piros színnel jelennek meg.
:::image-end:::

Ebben a példában óvatosnak kell lennie a biztonsági kockázatokkal szemben. Most, hogy létrehozott egy szabályzat-hozzárendelést, a rendszer kiértékeli a ContosoRG erőforráscsoport összes belefoglalt és nem mentesített tárolási fiókját. A három nem megfelelő tárolási fiókot naplózza, így az állapotukat **nem megfelelőre** változtatja.

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="A Storage-fiók megfelelőségének diagramja a contoso R G-erőforráscsoporthoz." border="false":::
   Ábra, amely a contoso R G erőforráscsoporthoz tartozó öt Storage-fiókhoz tartozó képeket mutatja. A Storage-fiókok közül az egyik és a három már rendelkezik zöld pipa jellel, míg a Storage-fiókok két, négy és öt múlva piros figyelmeztető jelekkel rendelkeznek.
:::image-end:::

A **megfelelő** és **nem megfelelő** szabályzatok és erőforrások mellett négy másik állam is van:

- **Kivétel**: az erőforrás egy hozzárendelés hatókörében van, de [meghatározott kivételt](../concepts/exemption-structure.md)tartalmaz.
- **Ütköző**: két vagy több, ütköző szabályokkal rendelkező házirend-definíció létezik. Például két definíció fűzi hozzá ugyanazt a címkét különböző értékekkel.
- **Nem indult el**: a kiértékelési ciklus nem indult el a házirendhez vagy az erőforráshoz.
- **Nincs regisztrálva**: a Azure Policy erőforrás-szolgáltató nincs regisztrálva, vagy a bejelentkezett fióknak nincs engedélye a megfelelőségi információk olvasásához.

Azure Policy a definíció **típus**, **név** vagy **típus** mezőjét használja annak megállapításához, hogy az erőforrás egyezik-e. Ha az erőforrás megfelel, a rendszer megfelelőnek tekinti, és állapota **megfelelő**, **nem megfelelő** vagy **adómentes**. Ha a definíció egyetlen tulajdonsága a **Type**, a **Name** vagy a **Kind** , akkor a rendszer az összes befoglalt és a nem mentesített erőforrást is figyelembe veszi, és kiértékeli őket.

A megfelelőség százalékos arányát úgy határozzák meg, hogy a **megfelelő** és a **mentesített** erőforrásokat az _összes erőforrás_ alapján osztja el. Az _összes erőforrás_ a **megfelelő**, **nem megfelelő**, **mentesített** és **ütköző** erőforrások összegeként van meghatározva. Az összesített megfelelőségi számok a **megfelelő** vagy a **kivétel** alá eső különálló erőforrások összege, amely az összes különálló erőforrás összegével egyenlő. Az alábbi képen 20 különálló erőforrás áll rendelkezésre, és csak az egyik **nem megfelelő**.
A teljes erőforrás-megfelelőség 95% (19 – 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Képernyőkép a szabályzatok megfelelőségi részleteiről a megfelelőségi lapról." border="false":::

> [!NOTE]
> A Azure Policy előzetes verziójának megfelelőségi funkciója. Az SDK és a portál oldalain lévő megfelelőségi tulajdonságok eltérnek az engedélyezett kezdeményezésekhez. További információ: a [szabályozások megfelelősége](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portál

A Azure Portal grafikus élményt nyújt a környezet megfelelőségi állapotának megjelenítéséhez és megismeréséhez. **A szabályzat lapon az** **áttekintő** lehetőség a házirendek és kezdeményezések megfelelőségére vonatkozó rendelkezésre álló hatókörök részletes adatait tartalmazza. A megfelelőségi állapottal és a hozzárendelések számával együtt az elmúlt hét nap megfelelőségét bemutató diagramot tartalmaz. A **megfelelőségi** oldal ezen információk nagy részét tartalmazza (kivéve a diagramot), de további szűrési és rendezési lehetőségeket is biztosít.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Képernyőkép a megfelelőség lapról, szűrési beállítások és részletek." border="false":::

Mivel egy házirend vagy kezdeményezés különböző hatókörökhöz rendelhető, a tábla tartalmazza az egyes hozzárendelések hatókörét és a hozzárendelt definíció típusát. A nem megfelelő erőforrások száma és az egyes hozzárendelésekhez nem megfelelő szabályzatok is rendelkezésre állnak. A táblázatban szereplő házirend vagy kezdeményezés kiválasztásával mélyebben megtekintheti az adott hozzárendelés megfelelőségét.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Képernyőkép a megfelelőség részleteiről oldalról, beleértve a Counts és az erőforrás-megfelelőségi adatokat." border="false":::

Az **erőforrás-megfelelőség** lapon található erőforrások listája az aktuális hozzárendelés meglévő erőforrásainak kiértékelési állapotát jeleníti meg. A lap alapértelmezett értéke **nem megfelelő**, de szűrhető.
Az erőforrás-létrehozási kérelem által aktivált események (Hozzáfűzés, naplózás, megtagadás, üzembe helyezés, módosítás) az **események** lapon jelennek meg.

> [!NOTE]
> Az AK-motor házirendjének esetében a megjelenő erőforrás az erőforráscsoport.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Képernyőkép a megfelelőségi részletek oldal események lapján." border="false":::

<a name="component-compliance"></a>[Erőforrás-szolgáltatói mód](../concepts/definition-structure.md#resource-provider-modes) erőforrásai esetében az **erőforrás-megfelelőség** lapon válassza ki az erőforrást, vagy kattintson a jobb gombbal a sorra, és válassza a **megfelelőség megtekintése részletei** lehetőséget. Ekkor megnyílik az összetevő megfelelőségi adatai. Ez a lap lapokat is kínál az ehhez az erőforráshoz, eseményekhez, összetevő-eseményekhez és változási előzményekhez rendelt házirendek megtekintéséhez.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Képernyőkép az összetevő-megfelelőség lapról, valamint az erőforrás-szolgáltatói mód hozzárendelésének megfelelőségi részleteiről." border="false":::

Az erőforrás-megfelelőség lapon kattintson a jobb gombbal az esemény azon sorára, amelyről további részleteket szeretne gyűjteni, majd válassza a **tevékenységi naplók megjelenítése** lehetőséget. Megnyílik a műveletnapló lap, és a rendszer előre szűri a hozzárendelés részleteit és az eseményeket. A műveletnapló további kontextust és információkat nyújt ezekről az eseményekről.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Képernyőkép a Azure Policy tevékenységekről és értékelésekről." border="false":::

### <a name="understand-non-compliance"></a>A nem megfelelőség megismerése

Ha egy erőforrás úgy van meghatározva, hogy **nem megfelelőnek** minősül, számos lehetséges oka lehet. Annak megállapításához, hogy az erőforrás **nem megfelelő-** e, vagy hogy megkeresse a felelős változást, tekintse [meg a nem megfelelőség megállapítása](./determine-non-compliance.md)című témakört.

## <a name="command-line"></a>Parancssor

A portálon megjelenő információk a REST API (beleértve a [ARMClient](https://github.com/projectkudu/ARMClient)-t), az Azure PowerShell és az Azure CLI-vel is kérhetők le. A REST API részletes ismertetését lásd: [Azure Policy](/rest/api/policy/) -hivatkozás. Az REST API-hivatkozási lapokon minden művelethez zöld "kipróbálás" gomb tartozik, amely lehetővé teszi, hogy közvetlenül a böngészőben próbálja ki.

Használjon ARMClient vagy hasonló eszközt az Azure-ba való hitelesítés kezeléséhez az REST API példákkal.

### <a name="summarize-results"></a>Eredmények összegzése

A REST API az összegzést tároló, definíció vagy hozzárendelés alapján végezheti el. Íme egy példa az előfizetés szintjén az Azure Policy Insight összefoglalása az [előfizetéshez](/rest/api/policy/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

A kimenet összefoglalja az előfizetést. Az alábbi példában szereplő kimenetben az összegzett megfelelőség az **Value. Results. nonCompliantResources** és **Value. Results. nonCompliantPolicies** alatt található. Ez a kérelem további részleteket tartalmaz, beleértve az egyes hozzárendeléseket, amelyek a nem megfelelő számokat és az egyes hozzárendelések definíciós információit tartalmazzák. A hierarchiában lévő minden egyes házirend-objektum egy **queryResultsUri** tartalmaz, amely az adott szinten további részletek beszerzésére használható.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Erőforrások lekérdezése

A fenti példában az **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** egy minta URI-t biztosít egy adott házirend-definícióhoz tartozó összes nem megfelelő erőforráshoz. Ha a **$Filter** értéket keresi, a ComplianceState egyenlő (EQ) a "nem megfelelő" értékre, a PolicyAssignmentId meg van adva a házirend-definícióhoz, majd maga a PolicyDefinitionId. A szűrőben lévő PolicyAssignmentId belefoglalásának oka, hogy a PolicyDefinitionId különböző hatókörű házirend-vagy kezdeményezési hozzárendelésekben létezhetnek. A PolicyAssignmentId és a PolicyDefinitionId megadásával explicit módon megadhatjuk a keresett eredményeket. Korábban a **legújabb** PolicyStates használtuk, amely az elmúlt 24 órában automatikusan beállítja a **from** és **az** időpontot.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példában szereplő válasz egyetlen nem megfelelő erőforrásra van kimetszve a rövidség kedvéért. A részletes válasz több adatot tartalmaz az erőforrásról, a házirendről vagy a kezdeményezésről, valamint a hozzárendelésről. Figyelje meg, hogy azt is láthatja, hogy milyen hozzárendelési paramétereket adtak át a szabályzat-definíciónak.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Események megtekintése

Egy erőforrás létrehozásakor vagy frissítésekor a rendszer létrehoz egy szabályzat-kiértékelési eredményt. Az eredményeket _házirend-eseményeknek_ nevezzük. A következő URI használatával megtekintheti az előfizetéshez társított legutóbbi házirend-eseményeket.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

A házirend-események lekérdezésével kapcsolatos további információkért tekintse meg a [Azure Policy Events](/rest/api/policy/policyevents) Reference című cikket.

### <a name="azure-cli"></a>Azure CLI

A Azure Policyhez készült [Azure CLI](/cli/azure/what-is-azure-cli) -parancs a REST-ben vagy Azure PowerShellban elérhető legtöbb műveletet magában foglalja. Az elérhető parancsok teljes listájáért lásd: az [Azure CLI – Azure Policy áttekintése](/cli/azure/policy).

Példa: az állapot összegzésének beolvasása a legfelső szintű hozzárendelt szabályzathoz a legmagasabb számú nem megfelelő erőforrással.

```azurecli-interactive
az policy state summarize --top 1
```

A válasz felső része a következő példához hasonlít:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Példa: az állapot rekordjának beolvasása a legutóbb kiértékelt erőforráshoz (alapértelmezés szerint az időbélyegző csökkenő sorrendben történik).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Példa: az összes nem megfelelő virtuális hálózati erőforrás adatainak beolvasása.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Példa: olyan nem megfelelő virtuális hálózati erőforrásokhoz kapcsolódó események beszerzése, amelyek egy adott dátum után történtek.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

A Azure Policy Azure PowerShell modulja a PowerShell-galéria az az [. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)néven érhető el. A PowerShellGet használatával telepítheti a modult a használatával (ellenőrizze, `Install-Module -Name Az.PolicyInsights` hogy a legújabb [Azure PowerShell](/powershell/azure/install-az-ps) van-e telepítve):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

A modul a következő parancsmagokkal rendelkezik:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Példa: az állapot összegzésének beolvasása a legfelső szintű hozzárendelt szabályzathoz a legmagasabb számú nem megfelelő erőforrással.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: az állapot rekordjának beolvasása a legutóbb kiértékelt erőforráshoz (alapértelmezés szerint az időbélyegző csökkenő sorrendben történik).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: az összes nem megfelelő virtuális hálózati erőforrás adatainak beolvasása.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: olyan nem megfelelő virtuális hálózati erőforrásokhoz kapcsolódó események beszerzése, amelyek egy adott dátum után lettek átalakítva, és egy CSV-objektumra lettek konvertálva, és fájlba exportálhatók.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Az objektum kimenete a `$policyEvents` következő kimenethez hasonlít:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

A **PrincipalOid** mező használatával egy adott felhasználó beolvasható a Azure PowerShell parancsmaggal `Get-AzADUser` . Cserélje le a **{principalOid}** helyére az előző példából kapott választ.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Ha [](../../../azure-monitor/logs/log-query-overview.md) `AzureActivity` az előfizetéshez kötött [Activity Log Analytics megoldással](../../../azure-monitor/essentials/activity-log.md) rendelkező log Analytics munkaterülettel rendelkezik, az új és frissített erőforrások kiértékelésével is megtekintheti az egyszerű Kusto-lekérdezések és a `AzureActivity` tábla segítségével. Azure Monitor naplók részleteivel a riasztások úgy konfigurálhatók, hogy megfigyeljék a nem megfelelőséget.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="A AzureActivity tábla Azure Policy műveleteit bemutató Azure Monitor naplók képernyőképe." border="false":::

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
