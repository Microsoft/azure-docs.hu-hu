---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a házirend-definíciók, a különböző SDK-k és a Kubernetes-bővítmények létrehozásával kapcsolatos problémákat.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6f31f6e6f8d24f83f44dc14112f1bdc90c8af859
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897071"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>A Azure Policy használatával kapcsolatos hibák elhárítása

Amikor szabályzat-definíciókat hoz létre, együttműködik az SDK-val, vagy beállítja a Kubernetes-bővítményhez [tartozó Azure Policy](../concepts/policy-for-kubernetes.md) , hibákba ütközhet. Ez a cikk az esetlegesen előforduló különféle általános hibákat ismerteti, és javaslatokat tesz a megoldására.

## <a name="find-error-details"></a>Hiba részleteinek keresése

A hiba részleteinek helye attól függ, hogy Azure Policy milyen aspektusa van a munkájának.

- Ha egyéni szabályzattal dolgozik, lépjen a Azure Portalra, és tekintse át a sémával kapcsolatos visszajelzéseket, vagy tekintse át az eredményül kapott [megfelelőségi információkat](../how-to/get-compliance-data.md) , hogy láthassa az erőforrások kiértékelésének módját.
- Ha a különböző SDK-k bármelyikével dolgozik, az SDK részletesen ismerteti, hogy a függvény miért nem sikerült.
- Ha együttműködik a Kubernetes-bővítménysel, kezdje a fürt [naplózásával](../concepts/policy-for-kubernetes.md#logging) .

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-alias-not-found"></a>Forgatókönyv: az alias nem található

#### <a name="issue"></a>Probléma

Egy házirend-definícióban helytelen vagy nem létező alias van használatban. A Azure Policy [aliasokat](../concepts/definition-structure.md#aliases) használ a Azure Resource Manager tulajdonságok leképezéséhez.

#### <a name="cause"></a>Ok

Egy házirend-definícióban helytelen vagy nem létező alias van használatban.

#### <a name="resolution"></a>Feloldás

Először ellenőrizze, hogy a Resource Manager-tulajdonságnak van-e aliasa. Az elérhető aliasok megkereséséhez nyissa meg a [Visual Studio Code Azure Policy bővítményét](../how-to/extension-for-vscode.md) vagy az SDK-t. Ha egy Resource Manager-tulajdonság aliasa nem létezik, hozzon létre egy támogatási jegyet.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Forgatókönyv: a kiértékelés részletei nem naprakészek

#### <a name="issue"></a>Probléma

Egy erőforrás nincs *elindítva* állapotban, vagy a megfelelőségi adatok nem aktuálisak.

#### <a name="cause"></a>Ok

Egy új szabályzat vagy kezdeményezési hozzárendelés körülbelül 30 percet vesz igénybe. Egy meglévő hozzárendelés hatókörén belüli új vagy frissített erőforrások körülbelül 15 perc alatt elérhetővé válnak. A standard megfelelőségi vizsgálat 24 óránként történik. További információ: [kiértékelési eseményindítók](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Feloldás

Először is várjon egy megfelelő időtartamot a kiértékelés befejezésére, és a megfelelőségi eredmények elérhetővé válnak a Azure Portalban vagy az SDK-ban. Ha Azure PowerShell vagy a REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.

### <a name="scenario-compliance-isnt-as-expected"></a>Forgatókönyv: a megfelelőség nem a várt módon

#### <a name="issue"></a>Probléma

Egy erőforrás nem az erőforráshoz várt _megfelelő_ vagy _nem megfelelő_ értékelési állapotban van.

#### <a name="cause"></a>Ok

Az erőforrás nincs a szabályzat-hozzárendelés megfelelő hatókörében, vagy a házirend-definíció nem a kívánt módon működik.

#### <a name="resolution"></a>Feloldás

A házirend-definícióval kapcsolatos hibák megoldásához tegye a következőket:

1. Először is várjon, amíg a próbaverzió befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. 

1. Ha Azure PowerShell vagy a REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva.
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - A üzemmódnak minden erőforrástípus kell lennie `all` .
   - A mód akkor kell `indexed` , ha a házirend-definíció ellenőrzi a címkéket vagy a helyet.
1. Ügyeljen arra, hogy az erőforrás hatóköre ne legyen [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy ne legyen [kivétel](../concepts/exemption-structure.md).
1. Ha a szabályzat-hozzárendelés megfelelősége `0/0` erőforrásokat mutat, a hozzárendelési hatókörön belül nem határoztak meg erőforrásokat. A házirend-definíciót és a hozzárendelési hatókört egyaránt ellenőriznie kell.
1. A megfelelőnek tartott nem megfelelő erőforrásokért lásd: [a nem megfelelőség okának megállapítása](../how-to/determine-non-compliance.md). A definíció és a kiértékelt tulajdonság értékének összehasonlítása azt jelzi, hogy az erőforrás miért nem megfelelő.
   - Ha a **célként megadott érték** helytelen, módosítsa a házirend-definíciót.
   - Ha az **aktuális érték** helytelen, ellenőrizze az erőforrás adattartalmát a használatával `resources.azure.com` .
1. Más gyakori problémák és megoldások esetében lásd [: Hibaelhárítás: a végrehajtás nem a várt módon](#scenario-enforcement-not-as-expected).

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-enforcement-not-as-expected"></a>Forgatókönyv: a végrehajtás nem a várt módon történik

#### <a name="issue"></a>Probléma

Olyan erőforrásra van szükség, amelyről Azure Policy, hogy a művelet nem zajlik le, és nincs bejegyzés az [Azure-beli tevékenység naplójában](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Ok

A házirend-hozzárendelés _le_ lett állítva egy [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) -beállításhoz. Amíg a **enforcementMode** le van tiltva, a házirend hatálya nem kényszerített, és nincs bejegyzés a tevékenység naplójában.

#### <a name="resolution"></a>Feloldás

A szabályzat-hozzárendelés kényszerítésének hibakeresése a következő módon végezhető el:

1. Először is várjon, amíg a próbaverzió befejeződik, és a megfelelőségi eredmények elérhetővé válnak a Azure Portalban vagy az SDK-ban. 

1. Ha Azure PowerShell vagy a REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva, és hogy a **EnforcementMode** _engedélyezve_ van.
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - A üzemmódnak az `all` összes erőforrás-típushoz kell tartoznia.
   - A mód akkor kell `indexed` , ha a házirend-definíció ellenőrzi a címkéket vagy a helyet.
1. Ügyeljen arra, hogy az erőforrás hatóköre ne legyen [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy ne legyen [kivétel](../concepts/exemption-structure.md).
1. Ellenőrizze, hogy az erőforrás-tartalom megfelel-e a szabályzat logikájának. Ezt [egy http Archive (har) nyomkövetés rögzítésével](../../../azure-portal/capture-browser-trace.md) vagy a Azure Resource Manager sablon (ARM-sablon) tulajdonságainak áttekintésével teheti meg.
1. Más gyakori problémák és megoldások esetében lásd [: Hibaelhárítás: a megfelelőség nem a várt módon](#scenario-compliance-isnt-as-expected).

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-denied-by-azure-policy"></a>Forgatókönyv: Azure Policy megtagadva

#### <a name="issue"></a>Probléma

Egy erőforrás létrehozása vagy frissítése megtagadva.

#### <a name="cause"></a>Ok

Az új vagy frissített erőforrás hatóköréhez tartozó szabályzat-hozzárendelés megfelel egy [megtagadási](../concepts/effects.md#deny) hatású házirend-definíció feltételeinek. A rendszer nem tudja létrehozni vagy frissíteni a definícióknak megfelelő erőforrásokat.

#### <a name="resolution"></a>Feloldás

Egy megtagadási szabályzat-hozzárendelés hibaüzenete tartalmazza a szabályzat-definíciót és a szabályzat-hozzárendelési azonosítókat. Ha az üzenetben szereplő hibaüzenetek nem maradnak meg, akkor a [tevékenység naplójában](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)is elérhető. Ezekkel az információkkal további részleteket tudhat meg az erőforrás-korlátozásokról, és módosíthatja a kérésben szereplő erőforrás-tulajdonságokat az engedélyezett értékek egyeztetéséhez.

## <a name="template-errors"></a>Sablon hibái

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Forgatókönyv: a szabályzat által támogatott függvények sablon szerint dolgozva

#### <a name="issue"></a>Probléma

Azure Policy számos ARM-sablon függvényt és funkciót támogat, amelyek csak egy házirend-definícióban érhetők el. A Resource Manager ezeket a függvényeket egy központi telepítés részeként dolgozza fel a házirend-definíció részeként.

#### <a name="cause"></a>Ok

A támogatott függvények, például a vagy a használata esetén a `parameter()` `resourceGroup()` függvény feldolgozott eredménye az üzembe helyezéskor történik, és nem engedélyezi a függvényt a házirend-definícióhoz, és Azure Policy a motort a feldolgozáshoz.

#### <a name="resolution"></a>Feloldás

Ha egy függvényt egy házirend-definíció részeként szeretne átadni, akkor a teljes karakterláncot olyan módon kell megadnia, `[` hogy a tulajdonság hasonlítson `[[resourceGroup().tags.myTag]` . Az escape-karakter hatására a Resource Manager az értéket karakterláncként kezeli a sablon feldolgozásakor. Azure Policy ezután a függvényt a házirend-definícióba helyezi, amely lehetővé teszi, hogy az a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Bővítmény a Kubernetes telepítési hibáihoz

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Forgatókönyv: a telepítés nem sikerült, mert hiba történt egy Helm-diagram használatával.

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs végrehajtása sikertelen, és a következő hibák valamelyikét adja vissza:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Ok

A generált jelszó tartalmaz egy vesszőt ( `,` ), amely a Helm diagramra van felosztva.

#### <a name="resolution"></a>Feloldás

Ha futtatja a parancsot, a jelszó mezőben adja meg a `helm install azure-policy-addon` vessző ( `,` ) karakterláncot egy fordított perjel () értékkel `\` .

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Forgatókönyv: a telepítés a Helm-diagram használatával meghiúsul, mert a név már létezik

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs végrehajtása sikertelen, és a következő hibaüzenetet adja vissza:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Ok

A (z) nevű Helm `azure-policy-addon` -diagram már telepítve van vagy részben telepítve van.

#### <a name="resolution"></a>Feloldás

Az utasításokat követve [távolítsa el a Kubernetes-bővítmény Azure Policy](../concepts/policy-for-kubernetes.md#remove-the-add-on), majd futtassa újra a `helm install azure-policy-addon` parancsot.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Forgatókönyv: az Azure-beli virtuális gépek felhasználó által hozzárendelt identitásai a rendszer által hozzárendelt felügyelt identitások helyére kerülnek.

#### <a name="issue"></a>Probléma

Miután hozzárendelte a vendég-konfigurációs házirend-kezdeményezéseket a számítógépeken lévő beállítások naplózásához, a számítógéphez hozzárendelt felhasználóhoz rendelt felügyelt identitások már nincsenek hozzárendelve. Csak rendszerhez rendelt felügyelt identitás van hozzárendelve.

#### <a name="cause"></a>Ok

A korábban a vendég konfiguráció DeployIfNotExists-definíciókban használt szabályzat-definíciók biztosítják, hogy a rendszerhez hozzárendelt identitás hozzá legyen rendelve a géphez, de a felhasználó által hozzárendelt identitás-hozzárendelések is el lettek távolítva.

#### <a name="resolution"></a>Feloldás

A hibát okozó definíciók *[elavult]* néven jelennek meg, és azokat a házirend-definíciók váltja fel, amelyek az előfeltételeket a felhasználó által hozzárendelt felügyelt identitások eltávolítása nélkül kezelik. Manuális lépésre van szükség. Törölje a (z) *[elavult]* jelölésű meglévő szabályzat-hozzárendeléseket, és cserélje le azokat a frissített előfeltétel-házirend kezdeményezésre, valamint az eredetivel megegyező nevű szabályzat-definícióra.

Részletes tájékoztatásért tekintse meg a [vendég konfigurációs naplózási szabályzatok című blogbejegyzésben megjelent fontos módosítást](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Bővítmény a Kubernetes általános hibáihoz

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Forgatókönyv: a bővítmény nem tudja elérni a Azure Policy szolgáltatási végpontot a kimenő forgalomra vonatkozó korlátozások miatt

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatás végpontját, és a következő hibák valamelyikét adja vissza:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy fürt leszállása zárolva van.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a következő cikkekben említett tartományok és portok nyitva vannak:

- [Szükséges kimenő hálózati szabályok és teljes tartománynevek (FQDN) az AK-fürtökhöz](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Az Azure arc-kompatibilis Kubernetes Azure Policy-bővítményének telepítése (előzetes verzió)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Forgatókönyv: a bővítmény nem tudja elérni a Azure Policy szolgáltatási végpontot a HRE-Pod-Identity konfiguráció miatt

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatás végpontját, és a következő hibák valamelyikét adja vissza:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a _Add-Pod-Identity_ telepítve van a fürtön, és a _Kube_ hüvelyek nem zárhatók ki a _HRE-Pod-Identity_ szolgáltatásban.

A _HRE-Pod-Identity_ összetevő csomópont felügyelt identitás (NMI) hüvelye módosítja a csomópontok iptables-et az Azure-példány metaadatainak végpontjának lehallgatására. Ez a beállítás azt jelenti, hogy a metaadat-végpontra irányuló kérelmeket a NMI észleli, még akkor is, ha a pod nem használja a _HRE-Pod-Identity-_ t.
A *AzurePodIdentityException* -CUSTOMRESOURCEDEFINITION (CRD) úgy konfigurálható, hogy tájékoztassa a _HRE-Pod-Identity-_ t, hogy a CRD által meghatározott címkékből származó összes metaadat-végpontra irányuló kérések a NMI-ben végzett feldolgozás nélkül legyenek proxyn.

#### <a name="resolution"></a>Feloldás

A `kubernetes.azure.com/managedby: aks` *AzurePodIdentityException* CRD konfigurálásával zárja ki a _Kube-System_ névtérben található címkével ellátott rendszerhüvelyeket a _HRE-Pod-Identity_ objektumban.

További információ: [a Azure Active Directory (Azure ad) Pod-identitás letiltása egy adott Pod/alkalmazáshoz](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

A kivételek konfigurálásához kövesse az alábbi példát:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Forgatókönyv: az erőforrás-szolgáltató nincs regisztrálva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatás végpontját, de a kiegészítő naplók a következő hibák valamelyikét jelenítik meg:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Ok

A "Microsoft. PolicyInsights" erőforrás-szolgáltató nincs regisztrálva. A bővítménynek regisztrálnia kell a szabályzat-definíciók beolvasásához és a megfelelőségi részletek visszaadásához.

#### <a name="resolution"></a>Feloldás

Regisztrálja a Microsoft. PolicyInsights erőforrás-szolgáltatót a fürt előfizetésében. Útmutatásért tekintse meg az [erőforrás-szolgáltató regisztrálása](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)című témakört.

### <a name="scenario-the-subscription-is-disabled"></a>Forgatókönyv: az előfizetés le van tiltva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatás végpontját, de a következő hiba jelenik meg:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Ok

Ez a hiba azt jelzi, hogy az előfizetés problémás volt, és a szolgáltatás jelzője `Microsoft.PolicyInsights/DataPlaneBlocked` hozzá lett adva az előfizetés blokkolásához.

#### <a name="resolution"></a>Feloldás

A probléma kivizsgálásához és megoldásához [forduljon a szolgáltatás csapatához](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Következő lépések

Ha a probléma nem szerepel a cikkben, vagy nem oldható fel, kérjen támogatást az alábbi csatornák egyikének meglátogatásával:

- Választ kaphat a szakértőktől a [Microsoft Q&A](/answers/topics/azure-policy.html)használatával.
- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) . A Twitteren ez a hivatalos Microsoft Azure-erőforrás segít a felhasználói élmény javításában azáltal, hogy a megfelelő válaszokat, támogatást és szakértőket csatlakoztatja az Azure-közösséghez.
- Ha továbbra is segítségre van szüksége, nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatási kérelem küldése** lehetőséget.
