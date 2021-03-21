---
title: A Kubernetes Azure Policy megismerése
description: Ismerje meg, hogyan használja a Azure Policy a Rego-t és a nyílt házirend-ügynököt az Azure-ban vagy a helyszínen futó Kubernetes futtató fürtök kezelésére.
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: 0aaf610cd5712ee195ed2a4108cf9e5ca9c65183
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577092"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>A Kubernetes-fürtökhöz tartozó Azure Policy ismertetése

A Azure Policy kiterjeszti a [forgalomirányító](https://github.com/open-policy-agent/gatekeeper) v3-t, amely egy, a [nyílt házirendügynök](https://www.openpolicyagent.org/) (OPA) által biztosított _belépésvezérlés-webhook_ , amely a fürtökre központosított, konzisztens módon alkalmazható. Azure Policy lehetővé teszi, hogy a Kubernetes-fürtök megfelelőségi állapotát egyetlen helyről kezelhesse és jelentse. A bővítmény a következő funkciókat hozza létre:

- Ellenőrzi a Azure Policy szolgáltatást a fürtre vonatkozó házirend-hozzárendelésekhez.
- A házirend-definíciókat a fürtben, a [korlátozási sablon](https://github.com/open-policy-agent/gatekeeper#constraint-templates) és az egyéni erőforrások [korlátozásával](https://github.com/open-policy-agent/gatekeeper#constraints) telepíti.
- A naplózási és megfelelőségi adatok visszaállítása a Azure Policy szolgáltatásba.

A Kubernetes Azure Policy a következő fürt-környezeteket támogatja:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc-kompatibilis Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS-motor](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Az KABAi motor és az arc-kompatibilis Kubernetes-bővítmények **előzetes** verzióban érhetők el. A Kubernetes Azure Policy csak a Linux-csomópontok készleteit és a beépített szabályzat-definíciókat támogatja. A beépített szabályzat-definíciók a **Kubernetes** kategóriában találhatók. A korlátozott előzetes verziójú házirend-definíciók a **EnforceOPAConstraint** és a **EnforceRegoPolicy** effektussal, a kapcsolódó **Kubernetes-szolgáltatások** kategóriája pedig _elavult_. Ehelyett használja a hatások _naplózása_ és a _Megtagadás_ erőforrás-szolgáltatói módot `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Áttekintés

A Kubernetes-fürttel való Azure Policy engedélyezéséhez és használatához végezze el a következő műveleteket:

1. Konfigurálja a Kubernetes-fürtöt, és telepítse a bővítményt:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc-kompatibilis Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-motor](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > A telepítéssel kapcsolatos gyakori problémákért lásd: [Hibaelhárítás – Azure Policy bővítmény](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [A Kubernetes Azure Policy nyelvének megismerése](#policy-language)

1. [Beépített definíció társítása a Kubernetes-fürthöz](#assign-a-built-in-policy-definition)

1. [Várakozás az ellenőrzésre](#policy-evaluation)

## <a name="limitations"></a>Korlátozások

A következő általános korlátozások érvényesek a Kubernetes-fürtök Azure Policy-bővítményére:

- A Kubernetes Azure Policy bővítménye a **1,14** -es vagy újabb verziójú Kubernetes támogatott.
- A Kubernetes Azure Policy bővítménye csak Linux-csomópontos készleteken telepíthető
- Csak a beépített szabályzat-definíciók támogatottak
- A szabályzattal nem kompatibilis rekordok maximális száma a fürtön: **500**
- Nem megfelelő rekordok maximális száma (előfizetés: **1 000 000** )
- Az Azure Policy-bővítményen kívüli forgalomirányító telepítése nem támogatott. A Azure Policy bővítmény engedélyezése előtt távolítsa el az előző forgalomirányító-telepítés által telepített összetevőket.
- A [meg nem felelés okai](../how-to/determine-non-compliance.md#compliance-reasons) nem érhetők el az `Microsoft.Kubernetes.Data` 
   [erőforrás-szolgáltató módban](./definition-structure.md#resource-provider-modes). [Összetevő részleteinek](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)használata.
- Az [erőforrás-szolgáltatói módok](./definition-structure.md#resource-provider-modes)nem támogatják a [kivételeket](./exemption-structure.md) .

A következő korlátozások érvényesek az AK-ra vonatkozó Azure Policy-bővítményre:

- Az [AK Pod biztonsági házirend](../../../aks/use-pod-security-policies.md) és az AK Azure Policy bővítménye nem engedélyezhető egyszerre. További információ: [AK Pod biztonsági korlátozás](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- A Azure Policy bővítmény által automatikusan kizárt névterek a következő kiértékeléshez: _Kube-System_, _forgalomirányító-System_ és _AK-periszkóp_.

## <a name="recommendations"></a>Javaslatok

Az alábbi általános javaslatok a Azure Policy bővítmény használatára:

- A Azure Policy bővítmény 3 forgalomirányító-összetevő futtatását igényli: 1 naplózási Pod és 2 webhook Pod-replika. Ezek az összetevők több erőforrást használnak, mint a Kubernetes erőforrások száma és a házirend-hozzárendelések a fürtben, amely naplózási és kényszerítési műveleteket igényelnek.

  - Ha kevesebb mint 500 hüvely van egyetlen fürtben, legfeljebb 20 megkötést biztosítunk: 2 vCPU és 350 MB memória/összetevő.
  - Több mint 500 hüvelyre egyetlen fürtben, legfeljebb 40 korlátozással: 3 vCPU és 600 MB memória/összetevő.

- A Windows-hüvelyek [nem támogatják a biztonsági környezeteket](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Így a Azure Policy-definíciók némelyike, például a legfelső szintű jogosultságok letiltását, a Windows-hüvelyekben nem lehet kibővíteni, és csak a Linux-hüvelyekre vonatkozik.

A következő javaslat csak az AK-ra és a Azure Policy bővítményre vonatkozik:

- A rendszercsomópont-készletet és a `CriticalAddonsOnly` Taint használva ütemezze a forgalomirányító hüvelyeket. További információ: [a rendszercsomópont-készletek használata](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Biztonságos kimenő forgalom az AK-fürtökből. További információ: a [csomópontok kimenő forgalmának szabályozása a fürtcsomópontok esetében](../../../aks/limit-egress-traffic.md).
- Ha a fürt `aad-pod-identity` engedélyezve van, a csomópont által felügyelt identitás (NMI)-hüvelyek módosítja a csomópontok iptables-et az Azure-példány metaadatainak végpontjának lehallgatására. Ez a konfiguráció azt jelenti, hogy a metaadat-végpontra irányuló kéréseket a NMI akkor is elfogja, ha a pod nem használja `aad-pod-identity` . A AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa `aad-pod-identity` , hogy a tőkekövetelmény-ből származó, a CRD-ban definiált címkével rendelkező, a NMI-ben való feldolgozás nélkül proxyra irányuló kérelmeket. A `kubernetes.azure.com/managedby: aks` _Kube-_ rendszernévtérben címkével ellátott rendszerhüvelyeket ki kell ZÁRNI a `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [a HRE-Pod-Identity letiltása egy adott Pod vagy alkalmazáshoz](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Kivétel konfigurálásához telepítse a [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Azure Policy bővítmény telepítése az AK-hoz

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. tárolószolgáltatás** és a **Microsoft. PolicyInsights** erőforrás-szolgáltatót.

1. Szüksége lesz az Azure CLI-verzió 2.12.0 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Regisztrálja az erőforrás-szolgáltatókat és az előzetes verziójú funkciókat.

   - Azure Portal:

     Regisztrálja a **Microsoft. tárolószolgáltatás** és a **Microsoft. PolicyInsights** erőforrás-szolgáltatókat. A lépéseket lásd: [erőforrás-szolgáltatók és típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Ha a korlátozott előzetes verziójú szabályzat-definíciók telepítve lettek, távolítsa el a bővítményt az AK-fürt **Letiltás** gombjával a **házirendek** lapon.

1. Az AK-fürt _1,14_ -es vagy újabb verziójának kell lennie. A következő parancsfájl használatával ellenőrizze az AK-fürt verzióját:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Telepítse az Azure CLI _2.12.0_ vagy újabb verzióját. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A fenti előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy-bővítményt a felügyelni kívánt AK-fürtön.

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **Kubernetes-szolgáltatások** keresésével és kiválasztásával.

  1. Válasszon egy AK-beli fürtöt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek** elemet.

  1. A Főoldalon kattintson a **bővítmény engedélyezése** gombra.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Ha a **bővítmény letiltása** gomb engedélyezve van, és az áttelepítési figyelmeztetés v2 üzenet jelenik meg, a v1 bővítmény telepítve van, és a v2 házirend-definíciók kiosztása előtt el kell távolítani. Az _elavult_ v1 bővítményt a rendszer automatikusan lecseréli a v2. augusztus 24-én kezdődő bővítményre.
     > 2020. Ezután hozzá kell rendelni a házirend-definíciók új v2-verzióit. A frissítéshez kövesse az alábbi lépéseket:
     >
     > 1. Ellenőrizze, hogy az AK-fürtön telepítve van-e a v1-bővítmény, ha felkeresi a **szabályzatok** lapot az AK-fürtön, és "az aktuális fürt használja Azure Policy bővítmény v1..." üzenetet.
     > 1. [Távolítsa el a bővítményt](#remove-the-add-on-from-aks).
     > 1. Kattintson a **bővítmény engedélyezése** gombra a bővítmény v2-verziójának telepítéséhez.
     > 1. [A v1 beépített szabályzat-definíciók v2 verziójának kiosztása](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Végül ellenőrizze, hogy a legújabb bővítmény telepítve van-e az Azure CLI-parancs futtatásával, az `<rg>` erőforráscsoport nevével és `<cluster-name>` az AK-fürt nevével: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Az eredménynek az alábbi kimenethez és konfigurációhoz hasonlóan kell kinéznie **. a verziónak** a következőnek kell lennie `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Policy bővítmény telepítése az Azure arc használatára képes Kubernetes (előzetes verzió)

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

1. Szüksége lesz az Azure CLI-verzió 2.12.0 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az erőforrás- [szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lépéseit, vagy futtassa az Azure CLI vagy a Azure PowerShell parancsot:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. A Kubernetes-fürt _1,14_ -es vagy újabb verziójúnak kell lennie.

1. Telepítse a [Helm 3](https://v3.helm.sh/docs/intro/install/)programot.

1. A Kubernetes-fürt engedélyezve van az Azure arc számára. További információ: [Kubernetes-fürt előkészítése az Azure-](../../../azure-arc/kubernetes/connect-cluster.md)ba.

1. Az Azure arc-kompatibilis Kubernetes-fürt teljes Azure-erőforrás-azonosítója.

1. Nyissa meg a bővítmény portjait. A Azure Policy bővítmény ezeket a tartományokat és portokat használja a szabályzat-definíciók és-hozzárendelések beolvasásához, valamint a fürt megfelelőségének visszahívásához a Azure Policy.

   |Tartomány |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. A házirend-elemzési adatíró (előzetes verzió) szerepkör hozzárendelése az Azure arc-kompatibilis Kubernetes-fürthöz. Cserélje le az értékét az `<subscriptionId>` előfizetés-azonosítóra, `<rg>` Az Azure arc-kompatibilis Kubernetes-fürt erőforráscsoporthoz, valamint az `<clusterName>` Azure arc-kompatibilis Kubernetes-fürt nevével. Tartsa nyomon a _AppID_, a _jelszó_ és a _bérlő_ visszaadott értékeit a telepítési lépésekhez.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Példa a fenti parancsok kimenetére:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Az előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy-bővítményt az Azure arc-kompatibilis Kubernetes-fürtön:

1. Adja hozzá a Azure Policy kiegészítő tárházat a Helmhoz:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Telepítse a Azure Policy bővítményt a Helm diagram használatával:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   További információ arról, hogy a Hogyan telepíti a kiegészítő Helm-diagramot: a [Azure Policy a Helm diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) a githubon.

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Azure Policy-bővítmény telepítése az KABAi motorhoz (előzetes verzió)

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

1. Szüksége lesz az Azure CLI-verzió 2.0.62 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az erőforrás- [szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lépéseit, vagy futtassa az Azure CLI vagy a Azure PowerShell parancsot:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Hozzon létre egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

   - Ha nem ismeri a fürtszolgáltatás egyszerű alkalmazásának AZONOSÍTÓját, keresse meg az alábbi parancsot.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - A "Policy betekintő adatíró (előzetes verzió)" szerepkör-hozzárendelés hozzárendelése a fürtszolgáltatás egyszerű _aadClientID_ (az előző lépésből származó érték) az Azure CLI-vel. A helyére írja be az `<subscriptionId>` előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AK-motor saját felügyeletű Kubernetes-fürtje található.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Az előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy bővítményt. A telepítés lehet egy AK-motor létrehozási vagy frissítési ciklusa, vagy egy meglévő fürtön futó önálló művelet.

- Telepítés a létrehozási vagy frissítési ciklus során

  Ha engedélyezni szeretné a Azure Policy bővítményt egy új, önfelügyelt fürt létrehozásakor vagy egy meglévő fürt frissítéseként, adja meg a [bővítmények](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) tulajdonságának fürtjének definícióját az AK motorhoz.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  A szolgáltatással kapcsolatos további információkért tekintse meg a külső útmutató az [AK-motor-fürt definícióját](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Telepítés meglévő fürtben Helm-diagramokkal

  A következő lépésekkel készítse elő a fürtöt, és telepítse a bővítményt:

  1. Telepítse a [Helm 3](https://v3.helm.sh/docs/intro/install/)programot.

  1. Adja hozzá a Azure Policy-tárházat a Helmhoz.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     További információ: [Helm chart – rövid útmutató](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Telepítse a bővítményt egy Helm diagrammal. A helyére írja be az `<subscriptionId>` előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AK-motor saját felügyeletű Kubernetes-fürtje található.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     További információ arról, hogy a Hogyan telepíti a kiegészítő Helm-diagramot: a [Azure Policy a Helm diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) a githubon.

     > [!NOTE]
     > A Azure Policy-bővítmény és az erőforráscsoport-azonosító közötti kapcsolat miatt Azure Policy minden erőforráscsoport esetében csak egy AK-beli motor-fürtöt támogat.

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Házirend nyelve

A Kubernetes kezelésének Azure Policy nyelvi szerkezete a meglévő szabályzat-definíciókat követi. Az [erőforrás-szolgáltatói móddal](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` a Kubernetes-fürtök kezeléséhez a hatások [naplózása](./effects.md#audit) és [elutasítása](./effects.md#deny) történik. A _naplózási_ és a _megtagadási_ adatoknak meg kell adniuk az [Opa-korlátozási keretrendszer](https://github.com/open-policy-agent/frameworks/tree/master/constraint) és a forgalomirányító v3 használatának **részleteit** .

A _details. constraintTemplate_ és a _details. megkötés_ tulajdonságok részeként a házirend-definícióban Azure Policy átadja ezen [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI-azonosítóját a bővítménynek. A Rego az a nyelv, amelyet az OPA és a forgalomirányító támogat a Kubernetes-fürtre irányuló kérelem érvényesítéséhez. A Kubernetes-kezelés meglévő szabványának támogatásával a Azure Policy lehetővé teszi a meglévő szabályok újbóli használatát, és azok párosítását Azure Policy egy egységes Felhőbeli megfelelőségi jelentéskészítési élmény érdekében. További információ: [What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Beépített szabályzatdefiníció hozzárendelése

Ahhoz, hogy szabályzat-definíciót rendeljen a Kubernetes-fürthöz, hozzá kell rendelnie a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) szabályzat-hozzárendelési műveleteket. Az Azure beépített szerepköreinek **erőforrás-házirend közreműködője** és **tulajdonosa** rendelkezik ezekkel a műveletekkel. További információ: [Azure RBAC-engedélyek Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

A következő lépésekkel megkeresheti a fürt kezelésére szolgáló beépített szabályzat-definíciókat a Azure Portal használatával:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán, majd keresse meg és válassza ki a **házirend** elemet.

1. A Azure Policy lap bal oldali ablaktábláján válassza a **definíciók** lehetőséget.

1. A kategória legördülő listából válassza az **összes kijelölése lehetőséget** a szűrő törléséhez, majd válassza a **Kubernetes** lehetőséget.

1. Válassza ki a házirend-definíciót, majd kattintson a **hozzárendelés** gombra.

1. Állítsa a **hatókört** arra a felügyeleti csoportra, előfizetésre vagy Kubernetes, amelyben a házirend-hozzárendelés érvényes lesz.

   > [!NOTE]
   > A Azure Policy Kubernetes-definícióhoz való hozzárendeléséhez a **hatókörnek** tartalmaznia kell a fürterőforrás-t. Az KABAi motor fürtjében a **hatókörnek** a fürt erőforráscsoport kell lennie.

1. Adja meg a szabályzat hozzárendelésének **nevét** és **leírását** , hogy könnyen azonosítható legyen.

1. Állítsa be a [szabályzat kényszerítését](./assignment-structure.md#enforcement-mode) az alábbi értékek egyikére.

   - **Engedélyezve** – a szabályzat érvénybe léptetése a fürtön. A Kubernetes vonatkozó beléptetési kérelmek megtagadva.

   - **Letiltva** – nem kényszeríti ki a szabályzatot a fürtön. A Kubernetes vonatkozó beléptetési kérelmeket nem tagadja meg a rendszer. A megfelelőség értékelésének eredményei továbbra is elérhetők. Amikor új szabályzat-definíciókat hoz létre a fürtök futtatásához, a _letiltott_ beállítás hasznos lehet a szabályzat-definíció teszteléséhez, mivel a rendszer nem tagadja meg a beléptetési kérelmek megsértését.

1. Kattintson a **Tovább** gombra.

1. **Paraméterek értékének** beállítása

   - Ha ki szeretné zárni a Kubernetes-névtereket a szabályzat kiértékelése alól, a névtér **kizárása** paraméterben határozza meg a névterek listáját. Azt javasoljuk, hogy zárja ki a következőket: _Kube-System_, _forgalomirányító-System_ és _Azure-arc_.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

Másik megoldásként használja a [szabályzat társítása – portál](../assign-policy-portal.md) rövid útmutatót a Kubernetes szabályzat megkereséséhez és hozzárendeléséhez. Keressen egy Kubernetes házirend-definíciót a "naplózási virtuális gépek" minta helyett.

> [!IMPORTANT]
> A beépített szabályzat-definíciók a **Kubernetes** kategóriába tartozó Kubernetes-fürtökhöz érhetők el. A beépített szabályzat-definíciók listáját lásd: Kubernetes- [minták](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Szabályzat kiértékelése

A bővítmény a (z) Azure Policy szolgáltatással ellenőrzi, hogy a házirend-hozzárendelések 15 percenként változnak-e.
A frissítési ciklus során a bővítmény ellenőrzi a módosításokat. Ezek a változások a megkötési sablonok és megkötések létrehozásához, frissítéséhez vagy törléséhez jönnek létre.

Kubernetes-fürtben, ha a névtér a következő címkék valamelyikével rendelkezik, a szabályok megsértésével rendelkező beléptetési kérelmeket nem tagadja meg a rendszer. A megfelelőség értékelésének eredményei továbbra is elérhetők.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Habár a fürt rendszergazdája jogosult lehet megkötési sablonok létrehozására és frissítésére, és a Azure Policy bővítmény által telepített erőforrások megkötésére, ezek a nem támogatott forgatókönyvek, mert a manuális frissítések felül vannak írva. A forgalomirányító továbbra is kiértékeli azokat a házirendeket, amelyek a bővítmény telepítése és a Azure Policy szabályzat-definíciók kiosztása előtt léteztek.

A bővítmény 15 percenként meghívja a fürt teljes vizsgálatát. Miután összegyűjtötte a teljes vizsgálat részleteit és minden valós idejű értékelést a fürtön történt megkísérelt módosításokat, a bővítmény jelentést készít az eredményről Azure Policy a [megfelelőségi adatokba](../how-to/get-compliance-data.md) , például bármely Azure Policy hozzárendelésbe való felvételhez. A naplózási ciklusban csak az aktív házirend-hozzárendelések eredményeit adja vissza a rendszer. A naplózási eredmények a sikertelen korlátozás állapota mezőjében megjelenő [szabálysértésként](https://github.com/open-policy-agent/gatekeeper#audit) is megtekinthetők. A _nem megfelelő_ erőforrásokkal kapcsolatos részletekért tekintse meg [az erőforrás-szolgáltatói módok összetevőinek részleteit](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> A Kubernetes-fürtök Azure Policy összes megfelelőségi jelentése magában foglalja az elmúlt 45 percen belüli összes jogsértést. Az időbélyegző azt jelzi, hogy mikor történt megsértés.

Egyéb megfontolások:

- Ha a fürt előfizetése Azure Security Center van regisztrálva, akkor Azure Security Center Kubernetes szabályzatokat a rendszer automatikusan alkalmazza a fürtön.

- Ha a meglévő Kubernetes-erőforrásokkal rendelkező fürtön megtagadási házirend van érvényben, az új szabályzatnak nem megfelelő összes meglévő erőforrás továbbra is fut. Ha a nem megfelelő erőforrás egy másik csomóponton lesz átütemezett, a forgalomirányító blokkolja az erőforrás-létrehozást.

- Ha egy fürt olyan megtagadási házirenddel rendelkezik, amely érvényesíti az erőforrásokat, a felhasználó nem fog elutasítási üzenetet látni a központi telepítés létrehozásakor. Vegyünk például egy olyan Kubernetes-telepítést, amely replicasets és hüvelyeket tartalmaz. Amikor a felhasználó végrehajtja a műveleteket `kubectl describe deployment $MY_DEPLOYMENT` , nem küld visszautasítási üzenetet az események részeként. `kubectl describe replicasets.apps $MY_DEPLOYMENT`A visszautasításhoz tartozó eseményeket azonban visszaadja.

## <a name="logging"></a>Naplózás

Kubernetes-vezérlőként/tárolóként az _Azure-Policy_ és a _forgalomirányító_ hüvelyek is megőrzik a naplókat a Kubernetes-fürtben. A naplók elérhetők a Kubernetes-fürt elemzések **lapján.**
További információ: [a Kubernetes-fürt teljesítményének figyelése a Azure monitor for containers szolgáltatással](../../../azure-monitor/containers/container-insights-analyze.md).

A bővítmények naplófájljainak megtekintéséhez használja a következőt `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

További információ: a forgalomirányító dokumentációjának [hibakeresése](https://github.com/open-policy-agent/gatekeeper#debugging) .

## <a name="troubleshooting-the-add-on"></a>A bővítmény hibaelhárítása

A Kubernetes-bővítmény hibaelhárításával kapcsolatos további információkért tekintse meg a Azure Policy hibaelhárítási cikk [Kubernetes című szakaszát](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) .

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

### <a name="remove-the-add-on-from-aks"></a>A bővítmény eltávolítása az AK-ból

Ha el szeretné távolítani a Azure Policy-bővítményt az AK-fürtből, használja a Azure Portal vagy az Azure CLI-t:

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **Kubernetes-szolgáltatások** keresésével és kiválasztásával.

  1. Válassza ki az AK-fürtöt, ahol le szeretné tiltani a Azure Policy-bővítményt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek** elemet.

  1. A Főoldalon kattintson a **bővítmény letiltása** gombra.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>A bővítmény eltávolítása az Azure arc-kompatibilis Kubernetes

Ha el szeretné távolítani a Azure Policy-bővítményt és a forgalomirányítót az Azure arc-kompatibilis Kubernetes-fürtről, futtassa a következő Helm-parancsot:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>A bővítmény eltávolítása az AK-motorból

Ha el szeretné távolítani a Azure Policy-bővítményt és a forgalomirányító-t az KABAi motor fürtjéből, használja a bővítmény telepítésének módját a következő módon:

- Ha telepítve van, állítsa be az **addons** tulajdonságot az AK-motorhoz tartozó fürt definíciójában:

  Az _Azure-Policy_ **addons** tulajdonságának hamis értékre való módosítása után telepítse újra a fürt definícióját az AK-motorra:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  További információkért lásd: az [Kabai motor – Azure Policy bővítmény letiltása](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Ha Helm-diagramokkal van telepítve, futtassa a következő Helm-parancsot:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy-bővítménye korlátozott számú fürt diagnosztikai adatokat gyűjt. A diagnosztikai adat a szoftverrel és teljesítménnyel kapcsolatos létfontosságú technikai jellegű adat. A következő módokon használható:

- Azure Policy bővítmény naprakészen tartása
- Azure Policy-bővítmény biztonságos, megbízható, teljesítménybeli megőrzése
- A bővítmény használatának összesített elemzésével Azure Policy kiegészítő bővítmények továbbfejlesztése

A bővítmény által gyűjtött információk nem személyes adatok. A rendszer jelenleg a következő adatokat gyűjti össze:

- Azure Policy bővítmény ügynökének verziója
- Fürt típusa
- Fürt régiója
- Fürterőforrás-csoport
- Fürterőforrás-azonosító
- Fürt-előfizetés azonosítója
- Fürt operációs rendszer (példa: Linux)
- Fürt városa (példa: Seattle)
- Fürt állapota vagy tartománya (példa: Washington)
- Fürt országa vagy régiója (példa: Egyesült Államok)
- Az ügynök telepítése során Azure Policy bővítmény által észlelt kivételek/hibák a szabályzat kiértékelése során
- A Azure Policy bővítmény által nem telepített forgalomirányító házirend-definíciók száma

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).