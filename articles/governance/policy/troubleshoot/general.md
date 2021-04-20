---
title: Gyakori hibák elhárítása
description: Megtudhatja, hogyan háríthatja el a szabályzatdefiníciók, a különböző SDK-k és a Kubernetes bővítményének létrehozásával kapcsolatos problémákat.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725061"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Hibák elhárítása a Azure Policy

Szabályzatdefiníciók létrehozásakor, az SDK-k futtatásakor vagy a [Azure Policy for Kubernetes](../concepts/policy-for-kubernetes.md) bővítmény beállításakor hibákba belefuthat. Ez a cikk különböző általános hibákat ismertet, amelyek előfordulhatnak, és megoldási módokat javasol.

## <a name="find-error-details"></a>Hiba részleteinek megkeresve

A hiba részleteinek helye attól függ, hogy Azure Policy milyen aspektusával dolgozik.

- Ha egyéni szabályzatokkal dolgozik, a Azure Portal-ben linting visszajelzést kaphat a sémáról, [](../how-to/get-compliance-data.md) vagy áttekinti az eredményül kapott megfelelőségi adatokat az erőforrások kiértékelésének ellenőrzéséhez.
- Ha a különböző SDK-k bármelyikét használja, az SDK részleteket nyújt a függvény sikertelen működésének okával kapcsolatban.
- Ha a Kubernetes bővítményét dolgozik, kezdje a fürt [naplózásával.](../concepts/policy-for-kubernetes.md#logging)

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-alias-not-found"></a>Forgatókönyv: Az alias nem található

#### <a name="issue"></a>Probléma

A szabályzatdefiníciók helytelen vagy nem létező aliasokat használnak. Azure Policy [aliasokat használ](../concepts/definition-structure.md#aliases) a tulajdonságok Azure Resource Manager leképezésére.

#### <a name="cause"></a>Ok

A szabályzatdefiníciók helytelen vagy nem létező aliasokat használnak.

#### <a name="resolution"></a>Feloldás

Először ellenőrizze, hogy a Resource Manager tulajdonság rendelkezik-e aliasokkal. Az elérhető aliasok ki keresse meg a Azure Policy [Code vagy](../how-to/extension-for-vscode.md) az SDK Visual Studio bővítményét.
Ha egy Resource Manager tulajdonság aliasa nem létezik, hozzon létre egy támogatási jegyet.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Forgatókönyv: A kiértékelési adatok nem naprakészek

#### <a name="issue"></a>Probléma

Egy erőforrás Nincs _elindítva_ állapotban van, vagy a megfelelőségi adatok nem aktuálisak.

#### <a name="cause"></a>Ok

Egy új szabályzat- vagy kezdeményezés-hozzárendelés alkalmazása körülbelül 30 percet vesz igénybe. Egy meglévő hozzárendelés hatókörében lévő új vagy frissített erőforrások körülbelül 15 perc múlva válnak elérhetővé. A szabványos megfelelőségi vizsgálat 24 óránként történik. További információ: [Kiértékelési eseményindítók.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Feloldás

Először várjon egy megfelelő ideig, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak a Azure Portal SDK-ban. Ha új kiértékelési vizsgálatot Azure PowerShell vagy a REST API, tekintse meg az [igény szerinti értékelési vizsgálatot.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)

### <a name="scenario-compliance-isnt-as-expected"></a>Forgatókönyv: A megfelelőség nem a várt módon működik

#### <a name="issue"></a>Probléma

Az erőforrás nem az  erőforráshoz  várt megfelelő vagy nem megfelelő kiértékelési állapotban van.

#### <a name="cause"></a>Ok

Az erőforrás nem a megfelelő hatókörben van a szabályzat-hozzárendeléshez, vagy a szabályzatdefiníció nem a kívántnak megfelelően működik.

#### <a name="resolution"></a>Feloldás

A szabályzatdefiníció hibaelhárítása érdekében tegye a következőket:

1. Először várjon a megfelelő ideig, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak a Azure Portal SDK-ban. 

1. Az új kiértékelési vizsgálat Azure PowerShell vagy a REST API igény szerinti kiértékelési [vizsgálatról.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör megfelelően van beállítva.
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - A módnak minden `all` erőforrástípushoz lennie kell.
   - A módnak akkor `indexed` kell lennie, ha a szabályzatdefiníció címkéket vagy helyet keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva vagy](../concepts/assignment-structure.md#excluded-scopes) [kivételként.](../concepts/exemption-structure.md)
1. Ha egy szabályzat-hozzárendelés megfelelősége erőforrásokat mutat, akkor a hozzárendelési hatókörben egyetlen erőforrás sem `0/0` alkalmazható. Ellenőrizze a szabályzatdefiníciót és a hozzárendelési hatókört.
1. A nem megfelelő erőforrásokkal, amelyektől elvárták a megfelelőség megállapítását, tekintse meg a meg nem felelőség [okait.](../how-to/determine-non-compliance.md) A definíció és a kiértékelt tulajdonságérték összehasonlítása azt jelzi, hogy miért nem megfelelő egy erőforrás.
   - Ha a **célérték** helytelen, módosítsa a szabályzatdefiníciót.
   - Ha az **aktuális érték** helytelen, ellenőrizze az erőforrás hasznos adatát a következőn `resources.azure.com` keresztül: .
1. További gyakori problémák és megoldások: [Hibaelhárítás: A kényszerítési nem a várt módon.](#scenario-enforcement-not-as-expected)

Ha továbbra is probléma van a duplikált és testreszabott beépített szabályzatdefinícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a Szabályzat létrehozása alatt **a** probléma megfelelő útválasztásához.

### <a name="scenario-enforcement-not-as-expected"></a>Forgatókönyv: A kényszerítési eljárás nem a várt módon működik

#### <a name="issue"></a>Probléma

A rendszer nem Azure Policy erőforrást, amely alapján el Azure Policy, és nincs bejegyzés az [Azure-tevékenységnaplóban.](../../../azure-monitor/essentials/platform-logs-overview.md)

#### <a name="cause"></a>Ok

A szabályzat-hozzárendelés a Disabled (Letiltva) [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) beállításhoz _lett konfigurálva._
Bár **a enforcementMode** le van tiltva, a szabályzat hatása nem lesz kényszerítve, és nincs bejegyzés a tevékenységnaplóban.

#### <a name="resolution"></a>Feloldás

A szabályzat-hozzárendelés kényszerítési hibáinak elhárításához a következőket kell tenni:

1. Először várjon a megfelelő ideig, amíg egy értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak a Azure Portal sdk-ban. 

1. Ha új kiértékelési vizsgálatot Azure PowerShell vagy a REST API, tekintse meg az Igény szerinti [értékelési vizsgálatot.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Ellenőrizze, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör megfelelően van-e beállítva, és hogy **a enforcementMode** engedélyezve _van-e._
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - A módnak minden `all` erőforrástípushoz meg kell lennie.
   - A módnak akkor kell `indexed` lennie, ha a szabályzatdefiníció címkéket vagy helyeket keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva vagy](../concepts/assignment-structure.md#excluded-scopes) [kivételként ki van zárva.](../concepts/exemption-structure.md)
1. Ellenőrizze, hogy az erőforrás hasznos adatok megfelel-e a szabályzat logikájának. Ez a [HTTP-archívum (HAR)](../../../azure-portal/capture-browser-trace.md) nyomkövetésének rögzítésvel vagy a Azure Resource Manager (ARM-sablon) tulajdonságainak áttekintését is elérhető.
1. További gyakori problémák és megoldások: [Troubleshoot: Compliance not as expected](#scenario-compliance-isnt-as-expected)(A megfelelőség nem a várt módon működik).

Ha továbbra is probléma van a duplikált és testreszabott beépített szabályzatdefinícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a Szabályzat létrehozása alatt **a** probléma megfelelő útválasztásához.

### <a name="scenario-denied-by-azure-policy"></a>Forgatókönyv: A rendszer Azure Policy

#### <a name="issue"></a>Probléma

Az erőforrás létrehozása vagy frissítése megtagadva.

#### <a name="cause"></a>Ok

Az új vagy frissített erőforrás hatókörének szabályzat-hozzárendelése megtagadás hatású szabályzatdefiníciók [feltételeinek felel](../concepts/effects.md#deny) meg. Az ezeknek a definícióknak megfelelő erőforrások létrehozása vagy frissítése nem ússzanak meg.

#### <a name="resolution"></a>Feloldás

A megtagadási szabályzat-hozzárendelésből származó hibaüzenet tartalmazza a szabályzatdefiníciókat és a szabályzat-hozzárendelések osztályát. Ha az üzenetben található hibainformációk kimaradnak, akkor a tevékenységnaplóban [is elérhetők.](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log) Az itt található információk segítségével további részleteket olvashat az erőforrás-korlátozásokról, és módosíthatja a kérésben található erőforrás-tulajdonságokat az engedélyezett értékeknek megfelelően.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Forgatókönyv: A definíció több erőforrástípust célozza meg

#### <a name="issue"></a>Probléma

A több erőforrástípust tartalmazó szabályzatdefiníciók létrehozása vagy frissítése során meghiúsul az ellenőrzés, és a következő hibaüzenet jelenik meg:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Ok

A szabályzatdefiníciós szabály egy vagy több olyan feltételt is rendelkezik, amelyet a célerőforrás-típusok nem értékelnek ki.

#### <a name="resolution"></a>Feloldás

Ha aliast használ, győződjön meg arról, hogy az alias csak arra az erőforrástípusra van kiértékelve, amelyhez tartozik. Ehhez adjon hozzá egy típusra vonatkozó feltételt. Alternatív megoldásként több definícióra is felosztja a szabályzatdefiníciót, hogy elkerülje több erőforrástípus megcélzását.

## <a name="template-errors"></a>Sablonhibák

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Forgatókönyv: Sablon által feldolgozott, szabályzat által támogatott függvények

#### <a name="issue"></a>Probléma

Azure Policy ARM-sablonfunkciókat és -függvényeket támogat, amelyek csak szabályzatdefiníciókban érhetők el. Resource Manager az üzembe helyezés részeként dolgozza fel ezeket a függvényeket, nem pedig egy szabályzatdefiníció részeként.

#### <a name="cause"></a>Ok

A támogatott függvények ( például vagy ) használata az üzembe helyezéskor a függvény feldolgozott eredményét ad eredményül ahelyett, hogy engedélyezné a függvény számára a szabályzatdefiníciót `parameter()` `resourceGroup()` és Azure Policy motor feldolgozását.

#### <a name="resolution"></a>Feloldás

Ha egy függvényt egy szabályzatdefiníció részeként szeretne átadni, a teljes sztringet úgy kell átadnia, hogy a `[` tulajdonság így néz `[[resourceGroup().tags.myTag]` ki: . A escape-karakter Resource Manager hatására a rendszer sztringként kezeli az értéket a sablon feldolgozza. Azure Policy ezután elhelyezi a függvényt a szabályzatdefinícióban, ami lehetővé teszi, hogy a várt módon dinamikus legyen. További információ: Szintaxis és kifejezések a [Azure Resource Manager sablonokban.](../../../azure-resource-manager/templates/template-expressions.md)

## <a name="add-on-for-kubernetes-installation-errors"></a>Bővítmény a Kubernetes telepítési hibáihoz

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Forgatókönyv: A Helm-diagram használatával való telepítés jelszóhiba miatt meghiúsul

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs meghiúsul, és az alábbi hibák egyikét adja vissza:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Ok

A létrehozott jelszó tartalmaz egy vesszőt ( ), amelyre a `,` Helm-diagram felosztást használ.

#### <a name="resolution"></a>Feloldás

A futtatásakor a jelszó értékének vesszőjét ( ) perjellel `helm install azure-policy-addon` `,` () kell `\` megadni.

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Forgatókönyv: A Helm-diagram használatával való telepítés meghiúsul, mert a név már létezik

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs sikertelen, és a következő hibát adja vissza:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Ok

A Helm-diagram már telepítve van, vagy részlegesen `azure-policy-addon` telepítve van.

#### <a name="resolution"></a>Feloldás

Kövesse az utasításokat [a kubernetes Azure Policy](../concepts/policy-for-kubernetes.md#remove-the-add-on)eltávolításához, majd futtassa újra a `helm install azure-policy-addon` parancsot.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Forgatókönyv: Az Azure-beli virtuális gép felhasználó által hozzárendelt identitását a rendszer által hozzárendelt felügyelt identitások váltják fel

#### <a name="issue"></a>Probléma

Miután hozzárendelt vendégkonfigurációs szabályzati kezdeményezéseket a gép beállításainak naplózása érdekében, a felhasználó által hozzárendelt felügyelt identitások hozzárendelése a továbbiakban nem lesz hozzárendelve a géphez. Csak a rendszer által hozzárendelt felügyelt identitás van hozzárendelve.

#### <a name="cause"></a>Ok

A Vendégkonfiguráció DeployIfNotExists definícióiban korábban használt szabályzatdefiníciók gondoskodtak arról, hogy a rendszer által hozzárendelt identitás hozzá legyen rendelve a géphez, de a felhasználó által hozzárendelt identitás-hozzárendeléseket is eltávolították.

#### <a name="resolution"></a>Feloldás

A korábban _\[ \]_ a problémát okozó definíciók elavultként jelennek meg, és olyan szabályzatdefiníciók váltják fel őket, amelyek a felhasználó által hozzárendelt felügyelt identitások eltávolítása nélkül kezelik az előfeltételeket. Manuális lépés szükséges. Törölje az elavultként _\[ \]_ megjelölt meglévő szabályzat-hozzárendeléseket, és cserélje le őket a frissített előfeltételként szükséges szabályzat kezdeményezésére és a szabályzatdefiníciókra, amelyek neve megegyezik az eredetivel.

Részletes leírást a Fontos változás jelent meg a vendégkonfiguráció naplózási házirendek számára [blogbejegyzésben talál.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Bővítmény a Kuberneteshez – általános hibák

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Forgatókönyv: A bővítmény a Azure Policy korlátozásai miatt nem tudja elérni a szolgáltatásvégpontot

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatásvégpontot, és a következő hibák egyikét adja vissza:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha egy fürt ki- és le van zárva.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a következő cikkekben említett tartományok és portok nyitva vannak:

- [Szükséges kimenő hálózati szabályok és teljes tartománynevek (FQDN-ek) az AKS-fürtökhöz](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [A Azure Policy Kuberneteshez (előzetes verzió) Azure Arc bővítmény telepítése](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Forgatókönyv: A bővítmény az aad-pod-identity konfigurációja miatt Azure Policy a szolgáltatásvégpontot

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatásvégpontot, és a következő hibák egyikét adja vissza:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha _az add-pod-identity_ telepítve van a fürtön, és a _kube-system_ podok nincsenek kizárva _az aad-pod-identity identitásból._

Az _aad-pod-identity_ összetevő Node Managed Identity (NMI) podjai úgy módosítják a csomópontok iptable-jét, hogy elfogják az Azure-példány metaadat-végpontjára hívásokat. Ez a beállítás azt jelenti, hogy az NMI elfogja a metaadat-végpontra vonatkozó kéréseket, még akkor is, ha a pod nem _aad-pod-identityt használ._
Az _AzurePodIdentityException_ CustomResourceDefinition (CRD) konfigurálható úgy, hogy tájékoztassa az _aad-pod-identityt_ arról, hogy a CRD-ben meghatározott címkékkel egyező podból származó metaadat-végpontokra vonatkozó kéréseket az NMI-ben történő feldolgozás nélkül kell proxyra állítani.

#### <a name="resolution"></a>Feloldás

Az `kubernetes.azure.com/managedby: aks` _AzurePodIdentityException_ CRD konfigurálásával zárja ki azokat a rendszerpodokat, amelyek a _kube-system_ névtérben találhatóak az _aad-pod-identityben._

További információ: Disable the Azure Active Directory pod identity for a specific pod/application (Az Azure AD-podidentitás letiltása adott [podhoz/alkalmazáshoz).](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)

Kivétel konfiguráláshoz kövesse az alábbi példát:

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

### <a name="scenario-the-resource-provider-isnt-registered"></a>Forgatókönyv: Az erőforrás-szolgáltató nincs regisztrálva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatásvégpontot, de a bővítménynaplók az alábbi hibák egyikét jelenítik meg:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Ok

A "Microsoft.PolicyInsights" erőforrás-szolgáltató nincs regisztrálva. A bővítménynek regisztrálva kell lennie a szabályzatdefiníciók lekért és a megfelelőségi adatok visszaadása érdekében.

#### <a name="resolution"></a>Feloldás

Regisztrálja a "Microsoft.PolicyInsights" erőforrás-szolgáltatót a fürt-előfizetésben. Útmutatásért lásd: [Erőforrás-szolgáltató regisztrálása.](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Forgatókönyv: Az előfizetés le van tiltva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatásvégpontot, de a következő hibaüzenet jelenik meg:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Ok

Ez a hiba azt jelenti, hogy az előfizetés problémásnak lett adva, és a funkciójelzővel blokkoltuk `Microsoft.PolicyInsights/DataPlaneBlocked` az előfizetést.

#### <a name="resolution"></a>Feloldás

A probléma kivizsgálásához és megoldásához lépjen [kapcsolatba a funkció csapatával.](mailto:azuredg@microsoft.com)

## <a name="next-steps"></a>Következő lépések

Ha a probléma nem szerepel a cikkben, vagy nem tudja megoldani, a következő csatornák egyikének felkeresése után kaphat támogatást:

- Szakértőktől kaphat választ a [Microsoft Q&A segítségével.](/answers/topics/azure-policy.html)
- Csatlakozzon a [@AzureSupport](https://twitter.com/azuresupport) hez. Ez a Microsoft Azure Twitter-forrás segít a felhasználói élmény javításában azáltal, hogy az Azure-közösséget a megfelelő válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
- Ha továbbra is segítségre van szüksége, Azure-támogatás [webhelyére,](https://azure.microsoft.com/support/options/) és válassza **a Támogatási kérés elküldése lehetőséget.**
