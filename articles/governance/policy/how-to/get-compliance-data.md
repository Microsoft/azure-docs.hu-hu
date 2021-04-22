---
title: Szabályzat-megfelelőségi adatok lekérte
description: Azure Policy értékelés és hatás határozza meg a megfelelőséget. Megtudhatja, hogyan olvashatja be az Azure-erőforrások megfelelőségi adatait.
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: e1a9a7fcbbcbd7f490b2f665b40c7ed922ec61ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864594"
---
# <a name="get-compliance-data-of-azure-resources"></a>Az Azure-erőforrások megfelelőségi adatainak lekért adatai

Az előfizetések egyik Azure Policy előnye az előfizetések vagy előfizetések felügyeleti [](../../management-groups/overview.md) csoportjának erőforrásaihoz nyújtott elemzés és vezérlés. Ez a vezérlő sokféleképpen használhatja, például megakadályozhatja, hogy az erőforrások nem a megfelelő helyen legyenek létrehozva, közös és konzisztens címkehasználatot kényszerítsen ki, vagy naplózhatja a meglévő erőforrásokat a megfelelő konfigurációk és beállítások érdekében. Az adatokat minden esetben a Azure Policy, hogy jobban megértse a környezet megfelelőségi állapotát.

A szabályzat- és kezdeményezés-hozzárendelések által létrehozott megfelelőségi információkhoz többféle módon is hozzáférhet:

- A [Azure Portal](#portal)
- Parancssori [szkriptek](#command-line) használatával

Mielőtt megnézi a megfelelőségről szóló jelentés módszereit, nézzük meg, mikor frissülnek a megfelelőségi adatok, és hogy milyen gyakran és milyen események aktiválnak egy kiértékelési ciklust.

> [!WARNING]
> Ha a megfelelőségi állapot jelentése Nincs regisztrálva, ellenőrizze, hogy a **Microsoft.PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure [RBAC)](../overview.md#azure-rbac-permissions-in-azure-policy)engedélyekkel a Azure Policy.

## <a name="evaluation-triggers"></a>Kiértékelési eseményindítók

A befejezett kiértékelési ciklusok eredményei a és műveleteken keresztül érhetők `Microsoft.PolicyInsights` el az `PolicyStates` erőforrás-szolgáltatóban. `PolicyEvents` További információ a Azure Policy Insights REST API elemzések [Azure Policy kapcsolatban.](/rest/api/policy/)

A hozzárendelt szabályzatok és kezdeményezések értékelése különböző események eredményeként történik:

- A szabályzat vagy kezdeményezés újonnan hozzá van rendelve egy hatókörhöz. A hozzárendelés a megadott hatókörre való alkalmazása körülbelül 30 percet vesz igénybe. Az alkalmazás után a kiértékelési ciklus az ebben a hatókörben található erőforrások esetében az újonnan hozzárendelt szabályzathoz vagy kezdeményezéshez tartozik, és a szabályzat vagy kezdeményezés hatásaitól függően az erőforrások megfelelőként, nem megfelelőként vagy kivételként vannak megjelölve. A nagy mennyiségű erőforrásra kiértékelt nagy szabályzatok vagy kezdeményezések sok időt venek majd el. Így nem várható előre, hogy a kiértékelési ciklus mikor fejeződik be. Ha befejeződött, a frissített megfelelőségi eredmények elérhetők a portálon és az SDK-kban.

- Frissül egy hatókörhöz már hozzárendelt szabályzat vagy kezdeményezés. A forgatókönyv kiértékelési ciklusa és időzítése megegyezik a hatókörhöz való új hozzárendeléssel.

- Az erőforrások egy hatókörben helyezhetők üzembe vagy frissíthetők egy Azure Resource Manager, REST API vagy egy támogatott SDK-n keresztüli hozzárendeléssel. Ebben a forgatókönyvben az egyes erőforrások hatáseseménye (hozzáfűzés, naplózás, megtagadás, üzembe helyezés) és a megfelelő állapotinformációk körülbelül 15 perccel később válnak elérhetővé a portálon és az SDK-kban. Ez az esemény nem ad ki más erőforrásokat.

- Az előfizetés (erőforrástípus) egy felügyeleticsoport-hierarchián belül jön létre vagy lesz áthelyezve az előfizetés erőforrástípusára vonatkozó hozzárendelt `Microsoft.Resource/subscriptions` szabályzatdefinícióval. [](../../management-groups/overview.md) Az előfizetés által támogatott hatások (audit, auditIfNotExist, deployIfNotExists, modify), a naplózás és az esetleges szervizelési műveletek kiértékelése körülbelül 30 percet vesz igénybe.

- A [rendszer létrehoz,](../concepts/exemption-structure.md) frissít vagy töröl egy szabályzati kivételt. Ebben a forgatókönyvben a rendszer kiértékeli a megfelelő hozzárendelést a meghatározott kivételi hatókörre.

- Standard megfelelőség kiértékelési ciklusa. A hozzárendelések 24 óránként automatikusan újra kiértékelése történik. Egy nagy méretű szabályzat vagy számos erőforrás kezdeményezése időt vehet igénybe, ezért nem várható előre, hogy a kiértékelési ciklus mikor fejeződik be. Ha befejeződött, a frissített megfelelőségi eredmények elérhetők a portálon és az SDK-kban.

- A [Vendégkonfiguráció](../concepts/guest-configuration.md) erőforrás-szolgáltató egy felügyelt erőforrás megfelelőségi adataival frissül.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti értékelési vizsgálat

Az előfizetések vagy erőforráscsoportok kiértékelési vizsgálatának első lépése az Azure CLI, a Azure PowerShell, az REST API hívása vagy a [Azure Policy Compliance Scan GitHub Action](https://github.com/marketplace/actions/azure-policy-compliance-scan)művelet használata.
Ez a vizsgálat aszinkron folyamat.

#### <a name="on-demand-evaluation-scan---github-action"></a>Igény szerinti értékelési vizsgálat – GitHub Action

Az [Azure Policy Compliance Scan](https://github.com/marketplace/actions/azure-policy-compliance-scan) művelet használatával elindíthat egy igény szerinti kiértékelési vizsgálatot a [GitHub-munkafolyamatból](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) egy vagy több erőforráson, erőforráscsoporton vagy előfizetésen, és az erőforrások megfelelőségi állapota alapján kaput ad a munkafolyamatnak. Azt is beállíthatja, hogy a munkafolyamat ütemezett időpontban fusson, hogy a legfrissebb megfelelőségi állapotot a megfelelő időpontban tudja lefutni. Ez a GitHub-művelet létrehozhat egy jelentést a beolvasott erőforrások megfelelőségi állapotáról további elemzés vagy archiválás céljából.

Az alábbi példában egy előfizetés megfelelőségi vizsgálatot futtatunk. 

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

További információ és munkafolyamat-minták: [GitHub Action for Azure Policy Compliance Scan adattár](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Igény szerinti értékelési vizsgálat – Azure CLI

A megfelelőségi vizsgálat az [az policy state trigger-scan](/cli/azure/policy/state#az_policy_state_trigger_scan) paranccsal indítható el.

Alapértelmezés szerint a elindítja az aktuális `az policy state trigger-scan` előfizetésben lévő összes erőforrás értékelését. Egy adott erőforráscsoport kiértékelése az **erőforráscsoport paraméterrel indítja** el a kiértékelést. Az alábbi példa elindít egy megfelelőségi vizsgálatot a _MyRG_ erőforráscsoport aktuális előfizetésében:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Dönthet úgy, hogy nem vár az aszinkron folyamat befejezésére, mielőtt folytatná a **"no-wait" paraméterrel.**

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Igény szerinti értékelési vizsgálat – Azure PowerShell

A megfelelőségi vizsgálat a [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) parancsmaggal indítható el.

Alapértelmezés szerint a elindítja az aktuális `Start-AzPolicyComplianceScan` előfizetésben lévő összes erőforrás értékelését. Egy adott erőforráscsoport kiértékelése a **ResourceGroupName** paraméterrel indítja el a kiértékelést. Az alábbi példa elindít egy megfelelőségi vizsgálatot a _MyRG_ erőforráscsoport aktuális előfizetésében:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

A PowerShell megvárhatja, amíg az aszinkron hívás befejeződik, mielőtt megadja az eredmények kimenetét, vagy futtathatja a háttérben [feladatként.](/powershell/module/microsoft.powershell.core/about/about_jobs) Ha PowerShell-feladat használatával futtatja a megfelelőségi vizsgálatot a háttérben, használja az **AsJob** paramétert, és állítsa az értéket egy objektumra, például `$job` a következő példában:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

A feladat állapotát az objektum ellenőrzésával `$job` ellenőrizheti. A feladat típusa `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Az `Get-Member` objektumon `$job` az elérhető tulajdonságok és metódusok érhetők el.

Amíg a megfelelőségi vizsgálat fut, az `$job` objektum ellenőrzése az alábbi eredményekhez hasonló eredményeket ad vissza:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Amikor a megfelelőségi vizsgálat befejeződik, az **Állapot** tulajdonság Befejezve _állapotra változik._

#### <a name="on-demand-evaluation-scan---rest"></a>Igény szerinti kiértékelési vizsgálat – REST

Aszinkron folyamatként a vizsgálat indítani szükséges REST-végpont nem várja meg, amíg a vizsgálat befejeződik a válaszhoz. Ehelyett egy URI-t biztosít a kért kiértékelés állapotának lekérdezéséhez.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}` – Cserélje le a helyére az erőforráscsoport nevét
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat támogatja az előfizetésben vagy erőforráscsoportban elérhető erőforrások kiértékelét. Indítsa el a hatókör alapján való vizsgálatot egy REST API **POST** paranccsal a következő URI-struktúrák használatával:

- Előfizetés

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Erőforráscsoport

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

A hívás **202 Accepted (Elfogadva) állapotot ad** vissza. A válaszfejléc tartalmaz egy **Location** tulajdonságot, amely a következő formátumot tartalmazza:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` A statikusan jön létre a kért hatókör számára. Ha egy hatókör már futtat igény szerinti vizsgálatot, nem indul el új vizsgálat. Ehelyett az új kérés az állapothoz ugyanazt a hely `{ResourceContainerGUID}`  URI-ját biztosítja. A REST API **GET parancsa** a **Location** URI-hoz egy **202 Accepted (Elfogadva)** értéket ad vissza, amíg a kiértékelés folyamatban van. Ha a kiértékelés befejeződött, **200 OK** állapotot ad vissza. A befejezett vizsgálat törzse egy JSON-válasz, amely a következő állapotot jelzi:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Igény szerinti kiértékelési vizsgálat – Visual Studio Code

A Azure Policy kódhoz Visual Studio bővítmény képes egy adott erőforrás kiértékelési vizsgálatának futtatására. Ez a vizsgálat szinkron folyamat, ellentétben a Azure PowerShell REST-metódusokkal.
Részletekért és lépésekért lásd: [Igény szerinti kiértékelés a VS Code bővítménysel.](./extension-for-vscode.md#on-demand-evaluation-scan)

## <a name="how-compliance-works"></a>A megfelelőség működése

Egy hozzárendelésben az  erőforrás nem megfelelő, ha nem követi a szabályzat- vagy kezdeményezési szabályokat, és nem mentesül a _alól._ Az alábbi táblázat bemutatja, hogyan működnek a szabályzatok különböző hatásai az eredményül kapott megfelelőségi állapot feltételértékelésével:

| Erőforrás-állapot | Hatás | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Új vagy frissített | Audit, Modify, AuditIfNotExist | Igaz | Nem megfelelő |
| Új vagy frissített | Audit, Modify, AuditIfNotExist | Hamis | Megfelelő |
| Létezik | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Igaz | Nem megfelelő |
| Létezik | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Hamis | Megfelelő |

> [!NOTE]
> A DeployIfNotExist és az AuditIfNotExist hatás megköveteli, hogy az IF utasítás IGAZ legyen, és a feltétel megléte FALSE legyen, hogy nem megfelelő. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Tegyük fel például, hogy van egy (ContsoRG) erőforráscsoportja, amely nyilvános hálózatok számára elérhető tárfiókokkal (piros színnel ki van emelve).

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="A Contoso R G erőforráscsoportban nyilvános hálózatok számára elérhető tárfiókok ábrája." border="false":::
   A Contoso R G erőforráscsoportban található öt tárfiók rendszerképét bemutató ábra.  Az első és a harmadik tárfiók kék, a második, a négy és az öt tárfiók piros.
:::image-end:::

Ebben a példában óvatosnak kell lennie a biztonsági kockázatokkal. Most, hogy létrehozott egy szabályzat-hozzárendelést, a rendszer kiértékeli a ContosoRG erőforráscsoportban található összes, nem kivételt képező tárfiókot. Naplót végez a három nem megfelelő tárfiókon, ezért nem megfelelőre módosítja az **államukat.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="A Contoso R G erőforráscsoportban található tárfiók-megfelelőség diagramja." border="false":::
   A Contoso R G erőforráscsoportban található öt tárfiók rendszerképét bemutató ábra. Az 1. és a 3. tárfiók alatt zöld pipa látható, a tárfiókok alatt pedig a második, a négy és az öt piros figyelmeztető jel látható.
:::image-end:::

A Megfelelő **és** a **Nem** megfelelő mellett a szabályzatok és erőforrások négy másik államba vannak bevetve:

- **Kivétel:** Az erőforrás egy hozzárendelés hatókörében van, de van egy [meghatározott kivétele.](../concepts/exemption-structure.md)
- **Ütköző:** Két vagy több szabályzatdefiníció létezik ütköző szabályokkal. Például két definíció ugyanazt a címkét fűzi hozzá különböző értékekkel.
- **Nincs elindítva:** A kiértékelési ciklus nem indult el a szabályzathoz vagy az erőforráshoz.
- **Nincs regisztrálva:** Azure Policy erőforrás-szolgáltató nincs regisztrálva, vagy a bejelentkezett fióknak nincs engedélye a megfelelőségi adatok olvasására.

Azure Policy típus **,** **név** vagy  típus mezőket használ a definícióban annak megállapításához, hogy egy erőforrás egyezés-e. Az erőforrás egyezése esetén alkalmazhatónak minősül, és **Megfelelő,** Nem megfelelő **vagy** **Kivétel** állapotú. Ha a **definíció** **egyetlen** tulajdonsága a típus , név vagy típus, akkor a rendszer az összes érintett és nem mentesített erőforrást alkalmazhatónak és kiértékeli. 

A megfelelőségi százalékos arányt  a Megfelelő  és a Mentesített erőforrások összes erőforrás alapján _való osztása határozza meg._ _Az összes_ erőforrás a Megfelelő, Nem megfelelő, Kivétel és Ütköző erőforrások  **összegeként van meghatározva.**  Az összesített megfelelőségi számok a Megfelelő  vagy a  Mentesített különálló erőforrások összegei, az összes különálló erőforrás összegének osztva. Az alábbi képen 20 különböző erőforrás alkalmazható, és csak egy nem **megfelelő.**
Az erőforrások teljes megfelelősége 95% (20-asból 19).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Képernyőkép a Megfelelőség lap szabályzat-megfelelőségi részleteiről." border="false":::

> [!NOTE]
> A megfelelőségi szabályzatok Azure Policy előzetes verziójú funkció. Az SDK megfelelőségi tulajdonságai és a portál oldalai eltérőek az engedélyezett kezdeményezések esetében. További információ: Jogszabályi [megfelelőség](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portál

A Azure Portal grafikus felületet nyújt a környezet megfelelőségi állapotának megjelenítéséhez és megértéséhez. A Szabályzat **lapon** az **Áttekintés** lehetőség a szabályzatok és kezdeményezések megfelelőségére vonatkozó elérhető hatókörök részleteit tartalmazza. A megfelelőségi állapot és a hozzárendelésenkénti darabszám mellett egy diagramot is tartalmaz, amely az elmúlt hét nap megfelelőségét mutatja. A **Megfelelőség** lap (a diagram kivételével) sok információt tartalmaz, de további szűrési és rendezési beállításokat is biztosít.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Képernyőkép a Megfelelőség lapról, a szűrési beállításokról és a részletekről." border="false":::

Mivel egy szabályzat vagy kezdeményezés különböző hatókörökhöz rendelhető, a tábla tartalmazza az egyes hozzárendelések hatókörét és a hozzárendelt definíció típusát. Az egyes hozzárendelések nem megfelelő erőforrásainak száma és nem megfelelő szabályzatok is meg vannak biztosítanak. Ha kiválaszt egy szabályzatot vagy kezdeményezést a táblázatban, mélyebben is megnézzük az adott hozzárendelés megfelelőségét.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Képernyőkép a Megfelelőség részletei lapról, beleértve a darabszámokat és az erőforrásoknak megfelelő adatokat." border="false":::

Az Erőforrás-megfelelőség  lapon található erőforrások listája az aktuális hozzárendelés meglévő erőforrásainak kiértékelési állapotát jeleníti meg. A lap alapértelmezett beállítása **Nem megfelelő,** de szűrhető.
Az erőforrások létrehozására vonatkozó kérés által kiváltott események (hozzáfűzés, naplózás, megtagadás, üzembe helyezés, módosítás) az **Események lapon jelennek** meg.

> [!NOTE]
> AKS Engine-szabályzatok számára a megjelenő erőforrás az erőforráscsoport.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="A Megfelelőség részletei lap Események lapjának képernyőképe." border="false":::

<a name="component-compliance"></a>Az [Erőforrás-szolgáltató módú](../concepts/definition-structure.md#resource-provider-modes) erőforrások esetén az Erőforrás-megfelelőség lapon válassza ki az erőforrást, vagy kattintson a jobb gombbal a sorra, majd válassza a **Megfelelőségi** adatok megtekintése lehetőséget, és megnyitja az összetevő megfelelőségi adatait.  Ezen az oldalon lapokat is talál az erőforráshoz rendelt szabályzatok, események, összetevőesemények és módosítási előzmények megtekintése érdekében.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Az Erőforrás-szolgáltató mód hozzárendelésének Összetevő-megfelelőség lapját és megfelelőségi részleteit tartalmazó képernyőkép." border="false":::

Az erőforrás-megfelelőségi lapra visszatérve kattintson a jobb gombbal annak az eseménynek a sorára, amelyről további adatokat szeretne gyűjteni, majd válassza a **Tevékenységnaplók megjelenítése lehetőséget.** Megnyílik a tevékenységnapló oldala, és előre szűrve van a keresésre, amely a hozzárendelés és az események részleteit mutatja. A tevékenységnapló további kontextust és információkat biztosít az eseményekről.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Képernyőkép a tevékenységnaplóról a Azure Policy és kiértékeléseiről." border="false":::

### <a name="understand-non-compliance"></a>A meg nem felelés

Ha egy erőforrás nem megfelelőnek **minősül,** annak számos oka lehet. Annak megállapításához, hogy  egy erőforrás miért nem megfelelő, vagy ha meg szeretné találni a felelőst, tekintse meg a Meg nem felelés [meghatározása(](./determine-non-compliance.md)).

## <a name="command-line"></a>Parancssor

A portálon elérhető információk a REST API (beleértve az [ARMClient-](https://github.com/projectkudu/ARMClient)), a Azure PowerShell és az Azure CLI-t. Az útmutató részletes REST API lásd a [](/rest/api/policy/) Azure Policy referenciáját. A REST API oldalon minden művelethez egy zöld "Próbálja ki" gomb található, amely lehetővé teszi, hogy közvetlenül a böngészőben próbálja ki.

Az ARMClient vagy egy hasonló eszköz használatával az Azure-ba való hitelesítést a REST API példákhoz.

### <a name="summarize-results"></a>Eredmények összegzése

A REST API az összegzés tároló, definíció vagy hozzárendelés alapján hajtható végre. Itt látható egy példa az előfizetés szintjén az Azure Policy Insight Summarize for Subscription (Előfizetés [összegzése) szakaszának használatával:](/rest/api/policy/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

A kimenet összegzi az előfizetést. Az alábbi példakimenetben az összesített megfelelőség a **value.results.nonCompliantResources** és **a value.results.nonCompliantPolicies alatt található.** Ez a kérés további részleteket tartalmaz, beleértve a nem megfelelő számokat tartalmazó hozzárendeléseket és az egyes hozzárendelések definíciós információit. A hierarchia minden szabályzatobjektuma biztosít egy **queryResultsUri** adatokat, amelyek további részleteket kaphatnak ezen a szinten.

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

A fenti példában **a value.policyAssignments.policyDefinitions.results.queryResultsUri** egy minta URI-t biztosít minden nem megfelelő erőforráshoz egy adott szabályzatdefinícióhoz. A $filter  a ComplianceState értéke egyenlő (eq) és "Nem megfelelő", a PolicyAssignmentId meg van adva a szabályzatdefinícióhoz, majd maga a PolicyDefinitionId. A PolicyAssignmentId azért van a szűrőben, mert a PolicyDefinitionId több szabályzat- vagy kezdeményezés-hozzárendelésben is létezhet, különböző hatókörökben. A PolicyAssignmentId és a PolicyDefinitionId megadásával explicitek lehetünk a keresett eredmények között. Korábban a PolicyStates beállításhoz a **legújabbat**  használtunk, amely automatikusan beállítja **az** elmúlt 24 óra és időpont között időszakát.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példaválasz egyetlen nem megfelelő erőforrásra lett levágva az egyszerűség kedvéért. A részletes válasz több adatot tartalmaz az erőforrásról, a szabályzatról vagy kezdeményezésről, valamint a hozzárendelésről. Figyelje meg azt is, hogy milyen hozzárendelési paramétereket adott át a rendszer a szabályzatdefiníciónak.

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

Egy erőforrás létrehozásakor vagy frissítésekor a rendszer létrehoz egy szabályzatértékelési eredményt. Az eredményeket _szabályzateseménynek nevezzük._ Az előfizetéshez társított legutóbbi szabályzatesemények megtekintéséhez használja a következő URI-t.

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

A szabályzatesemények lekérdezésére vonatkozó további információkért tekintse meg a Azure Policy [eseményekre vonatkozó](/rest/api/policy/policyevents) referenciát.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/what-is-azure-cli) parancssori felülethez Azure Policy REST vagy Azure PowerShell. Az elérhető parancsok teljes listáját lásd: [Azure CLI – Azure Policy áttekintés.](/cli/azure/policy)

Példa: A legmagasabb számú nem megfelelő erőforrással rendelkező legfelső szintű hozzárendelt szabályzat állapotának összegzése.

```azurecli-interactive
az policy state summarize --top 1
```

A válasz felső része az alábbi példához hasonló:

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

Példa: A legutóbb kiértékelt erőforrás állapotrekordja (az alapértelmezett érték az időbélyegző csökkenő sorrendben).

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

Példa: Az összes nem megfelelő virtuális hálózati erőforrás részleteinek lekért adatai.

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

Példa: A nem megfelelő virtuális hálózati erőforrásokkal kapcsolatos események beszerzése, amelyek egy adott dátum után történtek.

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

A Azure PowerShell modul az Azure Policy az [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)PowerShell-galéria érhető el a PowerShell-galéria alatt. A PowerShellGet használatával telepítheti a modult (győződjön meg arról, hogy a legújabb Azure PowerShell `Install-Module -Name Az.PolicyInsights` telepítve): [](/powershell/azure/install-az-ps)

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

A modul a következő parancsmagokat tartalmazza:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Példa: A legmagasabb számú nem megfelelő erőforrással rendelkező legfelső szintű hozzárendelt szabályzat állapotának összegzése.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: A legutóbb kiértékelt erőforrás állapotrekordja (az alapértelmezett érték az időbélyegző csökkenő sorrendben).

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

Példa: Az összes nem megfelelő virtuális hálózati erőforrás részleteinek lekért adatai.

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

Példa: Egy adott dátum után bekövetkezett, nem megfelelő virtuális hálózati erőforrásokkal kapcsolatos események beszerzése, CSV-objektumká konvertálás és fájlba exportálás.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Az objektum `$policyEvents` kimenete a következő kimenethez hasonló:

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

A **PrincipalOid** mezővel lekért egy adott felhasználót a Azure PowerShell parancsmaggal. `Get-AzADUser` Cserélje **le a {principalOid}** helyére az előző példából származó választ.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Ha az előfizetéséhez a Activity Log Analytics-megoldásból származó [Log Analytics-munkaterülettel](../../../azure-monitor/logs/log-query-overview.md) rendelkezik, az új és frissített erőforrások kiértékelése során a meg nem felelési eredményeket egyszerű `AzureActivity` Kusto-lekérdezésekkel és a táblával is [](../../../azure-monitor/essentials/activity-log.md) `AzureActivity` megtekintheti. A riasztási naplókban Azure Monitor riasztások konfigurálhatóak úgy, hogy figyeljék a nem megfelelőséget.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Az AzureActivity Azure Monitor műveleteket Azure Policy naplók képernyőképe." border="false":::

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [a Azure Policy példák között.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- A házirendek [programozott módon történő létrehozása.](programmatically-create.md)
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](remediate-resources.md)
- Tekintse át a felügyeleti csoportokat az Erőforrások rendszerezése [Azure-beli felügyeleti csoportokkal.](../../management-groups/overview.md)
