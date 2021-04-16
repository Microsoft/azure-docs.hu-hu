---
title: További Azure Policy Kuberneteshez
description: Megtudhatja, Azure Policy a Rego és az Open Policy Agent használatával hogyan felügyeli a Kubernetes-t futtató fürtök az Azure-ban vagy a helyszínen.
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ca33c3a937b0a155928f20469830388a95a08e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506023"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>A Kubernetes-fürtökhöz tartozó Azure Policy ismertetése

Azure Policy a [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3-at, az [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) belépésvezérlési _webhookját,_ amely központosított, konzisztens módon alkalmazza a nagy léptékű kényszerítéseket és védelmet a fürtökön. Azure Policy lehetővé teszi a Kubernetes-fürtök megfelelőségi állapotának egyetlen helyről való kezelését és jelentését. A bővítmény a következő függvényeket adja vissza:

- Ellenőrzi a Azure Policy szolgáltatásban, hogy vannak-e szabályzat-hozzárendelések a fürthöz.
- Szabályzatdefiníciókat helyez üzembe a fürtön [megkötési sablonként](https://github.com/open-policy-agent/gatekeeper#constraint-templates) és egyéni erőforrások megkötéseként. [](https://github.com/open-policy-agent/gatekeeper#constraints)
- A naplózási és megfelelőségi adatokat a Azure Policy jelenti.

Azure Policy Kuberneteshez készült alkalmazás a következő fürtkörnyezeteket támogatja:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc-kompatibilis Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS-motor](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Az AKS Engine és arc-kompatibilis Kubernetes bővítményei előzetes verzióban **érhetőek el.** Azure Policy Kuberneteshez készült alkalmazások csak a Linux-csomópontkészleteket és a beépített szabályzatdefiníciókat támogatják. A beépített szabályzatdefiníciók a **Kubernetes kategóriába tartoznak.** A Korlátozott előzetes verziójú szabályzatdefiníciók **EnforceOPAConstraint** és **EnforceRegoPolicy** effektussal, valamint a kapcsolódó **Kubernetes** Service-kategóriával _elavultak._ Ehelyett használja a hatásvizsgálat _és a_ _megtagadás erőforrás-szolgáltató_ módban való `Microsoft.Kubernetes.Data` használatát.

## <a name="overview"></a>Áttekintés

A Kubernetes-Azure Policy engedélyezéséhez és a fürthöz való használathoz tegye a következőket:

1. Konfigurálja a Kubernetes-fürtöt, és telepítse a bővítményt:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc-kompatibilis Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-motor](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > A telepítéssel kapcsolatos gyakori problémákért lásd: [Hibaelhárítás – Azure Policy bővítmény.](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors)

1. [A Kuberneteshez Azure Policy nyelv](#policy-language)

1. [Beépített definíció hozzárendelése a Kubernetes-fürthöz](#assign-a-built-in-policy-definition)

1. [Várakozás az érvényesítésre](#policy-evaluation)

## <a name="limitations"></a>Korlátozások

A Kubernetes-fürtökhöz Azure Policy bővítményre a következő általános korlátozások vonatkoznak:

- Azure Policy Kubernetes-bővítmény a Kubernetes **1.14-es** vagy újabb verzióiban támogatott.
- Azure Policy Kuberneteshez használható bővítmény csak Linux-csomópontkészletekbe telepíthető
- Csak a beépített szabályzatdefiníciók támogatottak
- Szabályzatonkénti nem megfelelő rekordok maximális száma fürtönként: **500**
- Nem megfelelő rekordok maximális száma előfizetésenként: **1 millió**
- A Gatekeeper telepítése a Azure Policy bővítményen kívül nem támogatott. Távolítsa el az előző Gatekeeper-telepítés által telepített összes összetevőt, mielőtt engedélyez Azure Policy-bővítményt.
- [A meg nem felelés okai](../how-to/determine-non-compliance.md#compliance-reasons) nem érhetők el az `Microsoft.Kubernetes.Data` 
   [erőforrás-szolgáltató módban.](./definition-structure.md#resource-provider-modes) Összetevő [részleteinek használata:](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [A kivételek](./exemption-structure.md) nem támogatottak az [erőforrás-szolgáltatói módok esetében.](./definition-structure.md#resource-provider-modes)

Az alábbi korlátozások csak az AKS Azure Policy bővítményre vonatkoznak:

- [Az AKS-podok](../../../aks/use-pod-security-policies.md) biztonsági szabályzata és Azure Policy AKS-hez elérhető bővítmény nem engedélyezhető. További információ: [Az AKS-podok biztonsági korlátozásai.](../../../aks/use-azure-policy.md)
- A névtereket a Azure Policy bővítménye automatikusan kizárja a kiértékeléshez: _kube-system,_ _gatekeeper-system_, _és aks-periscope_.

## <a name="recommendations"></a>Javaslatok

A következő általános javaslatok ajánlásokat tartalmaznak a Azure Policy bővítmény használatával kapcsolatban:

- A Azure Policy bővítmény három Gatekeeper-összetevőt igényel a futtatáshoz: 1 audit pod és 2 webhook podreplikát. Ezek az összetevők több erőforrást használnak, ahogy a Kubernetes-erőforrások és a szabályzat-hozzárendelések száma növekszik a fürtben, amihez naplózási és kényszerítési műveletekre van szükség.

  - Kevesebb mint 500 podhoz egyetlen fürtben, legfeljebb 20 korlátozással: összetevőnként 2 virtuálispu-val és 350 MB memóriával.
  - Több mint 500 podhoz egyetlen fürtben, legfeljebb 40 korlátozással: összetevőnként 3 virtuálispu-val és 600 MB memóriával.

- A Windows-podok [nem támogatják a biztonsági környezeteket.](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)
  Így a Azure Policy egyes definíciói, például a gyökér szintű jogosultságok eszkalálása nem eszkalálható Windows-podok esetén, és csak Linux-podok esetén alkalmazható.

A következő javaslat csak az AKS-re és a Azure Policy bővítményre vonatkozik:

- Forgalomirányító podok ütemezéséhez használjon fertőzött `CriticalAddonsOnly` rendszercsomópont-készletet. További információ: [Rendszer-csomópontkészletek használata.](../../../aks/use-system-pools.md#system-and-user-node-pools)
- Biztonságos kimenő forgalom az AKS-fürtökről. További információ: [A fürtcsomópontok bejövő forgalmának szabályozása.](../../../aks/limit-egress-traffic.md)
- Ha a fürt engedélyezve van, Node Managed Identity (NMI) podok úgy módosítják a csomópontok iptable-it, hogy elfogják az `aad-pod-identity` Azure Instance Metadata végpontra hívásokat. Ez a konfiguráció azt jelenti, hogy az NMI elfogja a metaadat-végpontra vonatkozó kéréseket, még akkor is, ha a pod nem használja a `aad-pod-identity` et. Az AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa arról, hogy a CRD-ben meghatározott címkéknek megfelelő podból származó metaadat-végpontra vonatkozó kéréseket az NMI-ben történő feldolgozás nélkül kell proxyra `aad-pod-identity` állítani. A kube-system névtérben címkével jelölt rendszerpodokat ki kell zárni a fájlból az `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [Disable aad-pod-identity for a specific pod or application (Aad-pod-identitás letiltása adott podhoz vagy alkalmazáshoz).](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Kivétel konfiguráláshoz telepítse a [mic-exception YAML-t.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="install-azure-policy-add-on-for-aks"></a>Az Azure Policy bővítmény telepítése az AKS-hez

Az Azure Policy bővítmény telepítése vagy bármely szolgáltatásszolgáltatás engedélyezése előtt az előfizetésének engedélyeznie kell a **Microsoft.PolicyInsights** erőforrás-szolgáltatót.

1. Az Azure CLI 2.12.0-s vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Regisztrálja az erőforrás-szolgáltatókat és az előzetes verziójú funkciókat.

   - Azure Portal:

     Regisztrálja **a Microsoft.PolicyInsights** erőforrás-szolgáltatót. További lépések: [Erőforrás-szolgáltatók és -típusok.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Ha korlátozott előzetes verziójú szabályzatdefiníciókat telepített, távolítsa el a bővítményt az AKS-fürt Szabályzatok lapján található Letiltás **gombbal.** 

1. Az AKS-fürtnek _1.14-es_ vagy újabb verziójúnak kell lennie. Az AKS-fürt verziójának ellenőrzéséhez használja a következő szkriptet:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Telepítse az Azure CLI _2.12.0-s_ vagy újabb verzióját. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

A fenti előfeltételként felsorolt lépések befejezése után telepítse a Azure Policy bővítményt a kezelni kívánt AKS-fürtön.

- Azure Portal

  1. Indítsa el az AKS-szolgáltatást a Azure Portal a **Minden** szolgáltatás lehetőség kiválasztásával, majd a **Kubernetes-szolgáltatások** keresését és kiválasztását.

  1. Válassza ki az egyik AKS-fürtöt.

  1. Válassza **a** Szabályzatok lehetőséget a Kubernetes-szolgáltatás oldalának bal oldalán.

  1. A főoldalon válassza a Bővítmény **engedélyezése gombot.**

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Ha a **Bővítmény letiltása** gomb engedélyezve van, és megjelenik az áttelepítésre figyelmeztető v2 üzenet, a rendszer telepíti a v1 bővítményt, és el kell távolítania a v2-szabályzatdefiníciók hozzárendelése előtt. Az _elavult_ v1 bővítményt a rendszer automatikusan lecseréli a v2 bővítményre augusztus 24-től kezdve.
     > 2020. Ezután hozzá kell rendelni a szabályzatdefiníciók új v2-verzióit. A frissítéshez kövesse az alábbi lépéseket:
     >
     > 1. Ellenőrizze, hogy az AKS-fürtön telepítve  van-e a v1 bővítmény. Ehhez látogasson el az AKS-fürt Szabályzatok oldalára, és adja meg a következőt: "Az aktuális fürt Azure Policy bővítményt használ... Üzenetet.
     > 1. [Távolítsa el a bővítményt.](#remove-the-add-on-from-aks)
     > 1. Válassza a **Bővítmény engedélyezése gombot** a bővítmény v2 verziójának telepítéséhez.
     > 1. [V1 beépített szabályzatdefiníciók v2-verzióinak hozzárendelése](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

A bővítmény sikeres telepítésének, valamint az _azure-policy_ és a _gatekeeper_ podok futásának ellenőrzéséhez futtassa a következő parancsot:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Végül ellenőrizze, hogy telepítve van-e a legújabb bővítmény. Ehhez futtasa le ezt az Azure CLI-parancsot, a et cserélje le az erőforráscsoport nevére, a et pedig az `<rg>` `<cluster-name>` AKS-fürt nevére: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Az eredménynek az alábbi kimenethez hasonlóan kell kinéznie, a **config.version fájlnak** pedig a következőnek kell `v2` lennie:

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Az Azure Policy Kuberneteshez Azure Arc bővítmény telepítése (előzetes verzió)

A Azure Policy bővítmény telepítése vagy bármely szolgáltatásszolgáltatás engedélyezése előtt az előfizetésének engedélyeznie kell a **Microsoft.PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürt szolgáltatásnévhez.

1. Az Azure CLI 2.12.0-s vagy újabb verziójának telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse [](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) az erőforrás-szolgáltatók és -típusok lépéseit, vagy futtassa az Azure CLI-t vagy Azure PowerShell parancsot:

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

1. A Kubernetes-fürtnek _1.14-es_ vagy újabb verziójúnak kell lennie.

1. Telepítse a [Helm 3-asát.](https://v3.helm.sh/docs/intro/install/)

1. A Kubernetes-fürtön engedélyezve van a Azure Arc. További információ: [Kubernetes-fürt](../../../azure-arc/kubernetes/quickstart-connect-cluster.md)Azure Arc.

1. Az engedélyezett Kubernetes-fürthöz Azure Arc Azure-erőforrás-azonosítót.

1. Nyissa meg a bővítmény portját. A Azure Policy bővítmény ezeket a tartományokat és portokat használja a szabályzatdefiníciók és -hozzárendelések lekéréséhez, valamint a fürt megfelelőségét a Azure Policy.

   |Tartomány |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Rendelje hozzá a "Policy Insights adatíró (előzetes verzió)" szerepkör-hozzárendelést a Azure Arc Kubernetes-fürthöz. Cserélje le a helyére az előfizetés-azonosítóját, a Azure Arc kubernetes-fürt erőforráscsoportjára, a helyére pedig a Azure Arc `<subscriptionId>` `<rg>` engedélyezett `<clusterName>` Kubernetes-fürt nevét. A telepítési lépésekhez kövesse nyomon az _appId,_ _a password_ és a _tenant_ visszaadott értékeit.

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

A fenti előfeltételként felsorolt lépések befejezése után telepítse a Azure Policy bővítményt a Azure Arc Kubernetes-fürtön:

1. Adja hozzá Azure Policy bővítmény-adattúdát a Helmhez:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Telepítse a Azure Policy bővítményt a Helm-diagram használatával:

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

   A bővítmény Helm-diagram telepítésének további információiért lásd a Azure Policy [Helm-diagram](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) definícióját a GitHubon.

A bővítmény sikeres telepítésének, valamint az _azure-policy_ és a _gatekeeper_ podok futásának ellenőrzéséhez futtassa a következő parancsot:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Az Azure Policy AKS-motorhoz (előzetes verzió) elérhető bővítmény telepítése

Az Azure Policy bővítmény telepítése vagy bármely szolgáltatásszolgáltatás engedélyezése előtt az előfizetésének engedélyeznie kell a **Microsoft.PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürt szolgáltatásnévhez.

1. Az Azure CLI 2.0.62-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse [](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) az erőforrás-szolgáltatók és -típusok lépéseit, vagy futtassa az Azure CLI-t vagy a Azure PowerShell parancsot:

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

1. Hozzon létre egy szerepkör-hozzárendelést a fürt szolgáltatásnévhez.

   - Ha nem ismeri a fürt egyszerű szolgáltatásának alkalmazásazonosítóját, keresse meg a következő paranccsal.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Rendelje hozzá a "Policy Insights-adatíró (előzetes verzió)" szerepkör-hozzárendelést a fürt szolgáltatásnév-alkalmazásazonosítójának (az előző lépésből származó _aadClientID_ érték) az Azure CLI-val. Cserélje le a helyére az előfizetés azonosítóját, az helyére pedig azt az erőforráscsoportot, amelybe az AKS-motor saját maga által felügyelt `<subscriptionId>` `<aks engine cluster resource group>` Kubernetes-fürtje tartozik.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

A fenti előfeltételként felsorolt lépések befejezése után telepítse a Azure Policy bővítményt. A telepítés lehet egy AKS-motor létrehozási vagy frissítési ciklusa során, vagy egy meglévő fürtön független műveletként.

- Telepítés létrehozási vagy frissítési ciklus során

  Ha engedélyezni szeretné a Azure Policy bővítményt egy új, ön által felügyelt fürt létrehozása során vagy egy meglévő fürt frissítése során, adja meg az [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) tulajdonságfürt definícióját az AKS-motorhoz.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  További információkért lásd az [AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)Engine-fürtdefiníció külső útmutatóját.

- Telepítés meglévő fürtben Helm-diagramokkal

  A fürt előkészítéséhez és a bővítmény telepítéséhez kövesse az alábbi lépéseket:

  1. Telepítse a [Helm 3-asát.](https://v3.helm.sh/docs/intro/install/)

  1. Adja hozzá a Azure Policy a Helmhez.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     További információ: [Helm-diagram – rövid útmutató.](https://helm.sh/docs/using_helm/#quickstart-guide)

  1. Telepítse a bővítményt egy Helm-diagrammal. Cserélje le a helyére az előfizetés azonosítóját, az helyére pedig azt az erőforráscsoportot, amelybe az AKS-motor saját maga által felügyelt `<subscriptionId>` `<aks engine cluster resource group>` Kubernetes-fürtje tartozik.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     A bővítmény Helm-diagram telepítésének további információiért lásd a Azure Policy [Helm-diagram](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) definícióját a GitHubon.

     > [!NOTE]
     > A bővítmény Azure Policy és az erőforráscsoport-azonosító közötti kapcsolat miatt a Azure Policy minden erőforráscsoporthoz csak egy AKS-motorfürtöt támogat.

A bővítmény sikeres telepítésének, valamint az _azure-policy_ és a _gatekeeper_ podok futásának ellenőrzéséhez futtassa a következő parancsot:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Szabályzat nyelve

A Azure Policy Kubernetes kezeléséhez használt nyelvstruktúra a meglévő szabályzatdefiníciókét követi. Az [erőforrás-szolgáltató módban](./definition-structure.md#resource-provider-modes) a hatásvizsgálat és a megtagadás a `Microsoft.Kubernetes.Data` Kubernetes-fürtök kezelésére használható. [](./effects.md#audit) [](./effects.md#deny) _A_ _naplózásnak és_ a megtagadásnak **meg** kell adnia a forgalomirányítóval és [a forgalomirányító v3 OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) való munkavégzésre vonatkozó részleteket.

A szabályzatdefiníció _details.constraintTemplate_ és _details.constraint_ tulajdonságainak részeként a Azure Policy átadja ezen [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI-jeit a bővítménynek. A Rego az a nyelv, amely támogatja az OPA és a Gatekeeper számára a Kubernetes-fürthöz való kérések érvényesítését. Egy meglévő, Kubernetes-felügyeletre vonatkozó szabvány támogatásával a Azure Policy lehetővé teszi a meglévő szabályok újrafelhasználásának és az Azure Policy-val való párosítását az egységes felhőbeli megfelelőségi jelentéskészítés érdekében. További információ: Mi az a [Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Beépített szabályzatdefiníció hozzárendelése

Ha szabályzatdefiníciót szeretne hozzárendelni a Kubernetes-fürthöz, hozzá kell rendelnie a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) szabályzat-hozzárendelési műveleteket. Ezek a műveletek az Azure beépített **Erőforrás-szabályzat közreműködője** és **Tulajdonosa** szerepkörei. További információ: [Azure RBAC-engedélyek a Azure Policy.](../overview.md#azure-rbac-permissions-in-azure-policy)

Keresse meg a fürt felügyeletéhez szükséges beépített szabályzatdefiníciókat a Azure Portal alábbi lépésekkel:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal. A **bal oldali panelen** válassza a Minden szolgáltatás lehetőséget, majd keresse meg és válassza a Szabályzat **lehetőséget.**

1. A bal oldali panelen válassza Azure Policy **Definíciók lehetőséget.**

1. A Kategória legördülő listából törölje  a szűrőt az Összes kijelölése gombra, majd válassza a **Kubernetes lehetőséget.**

1. Válassza ki a szabályzatdefiníciót, majd kattintson a Hozzárendelés **gombra.**

1. A Hatókör **beállításnál** adja meg annak a Kubernetes-fürtnek a felügyeleti csoportját, előfizetését vagy erőforráscsoportját, ahol a szabályzat-hozzárendelés érvényes lesz.

   > [!NOTE]
   > A Kubernetes-Azure Policy hozzárendelésekor a  hatókörnek tartalmaznia kell a fürterőforrást. AKS-motorfürt esetén a **hatókörnek** a fürt erőforráscsoportján kell lennie.

1. Adjon a szabályzat-hozzárendelésnek **egy Nevet** és **Leírást,** amely alapján könnyen azonosítható.

1. Állítsa a [Házirend kényszerítési](./assignment-structure.md#enforcement-mode) értékét az alábbi értékek egyikére.

   - **Engedélyezve** – Kényszeríti a házirendet a fürtön. A szabálysértésekkel kapcsolatos Kubernetes-belépési kérelmeket a rendszer megtagadja.

   - **Letiltva** – Ne kényszerítse ki a szabályzatot a fürtön. A szabálysértésekkel kapcsolatos Kubernetes-belépési kérelmeket a rendszer nem utasítja el. A megfelelőségi felmérés eredményei továbbra is elérhetők. Amikor új szabályzatdefiníciókat vezet be a fürtök futtatására, a _Disabled_ (Letiltva) beállítás hasznos lehet a szabályzatdefiníció teszteléséhez, mivel a szabálysértésekkel kapcsolatos belépési kérelmeket a rendszer nem tiltja le.

1. Kattintson a **Tovább** gombra.

1. **Paraméterértékek beállítása**

   - Ha ki szeretne zárni Kubernetes-névtereket a szabályzatértékelésből, adja meg a névterek listáját a **Namespace exclusions paraméterben.** Javasoljuk a _kube-system,_ _a gatekeeper-system_ és az _azure-arc kizárását._

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

Másik esetben a Szabályzat hozzárendelése [– Portál](../assign-policy-portal.md) gyorsútmutatóval is megkereshet és hozzárendelhet egy Kubernetes-szabályzatot. A "virtuális gépek naplózása" minta helyett keressen egy Kubernetes-szabályzatdefiníciót.

> [!IMPORTANT]
> Beépített szabályzatdefiníciók érhetők el a Kubernetes-fürtökhöz a **Kubernetes kategóriában.** A beépített szabályzatdefiníciók listájáért tekintse meg a [Kubernetes-mintákat.](../samples/built-in-policies.md#kubernetes)

## <a name="policy-evaluation"></a>Szabályzatok kiértékelése

A bővítmény 15 percenként Azure Policy a szabályzat-hozzárendelések változásait a szolgáltatásban.
Ebben a frissítési ciklusban a bővítmény ellenőrzi a módosításokat. Ezek a módosítások aktiválják a korlátozások sablonjainak és megkötéseinek a létrehozása, frissítése vagy törléseit.

Ha egy Kubernetes-fürtben a névtér az alábbi címkék valamelyikével rendelkezik, a szabálysértésekkel kapcsolatos belépési kérelmeket a rendszer nem utasítja el. A megfelelőségi értékelés eredményei továbbra is elérhetők.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Bár a fürt rendszergazdája engedélyt adhat a megkötési sablonok és korlátozások létrehozására és frissítésére az Azure Policy-bővítmény által telepítve, ezek a forgatókönyvek nem támogatottak, mivel a manuális frissítések felül vannak írva. A forgalomirányító továbbra is kiértékeli a bővítmény telepítése és a szabályzatdefiníciók Azure Policy előtt meglévő szabályzatokat.

A bővítmény 15 percenként hívja meg a fürt teljes vizsgálatot. Miután a Gatekeeper részletesen kiértékelte a teljes vizsgálatot és a fürt módosításainak valós idejű kiértékelését, a bővítmény [](../how-to/get-compliance-data.md) visszaküldi az eredményeket az Azure Policy-nak, hogy felveze őket a megfelelőségi részletekbe, például bármely Azure Policy hozzárendelésbe. A rendszer csak az aktív szabályzat-hozzárendelések eredményeit ad vissza a naplózási ciklus során. A naplózási eredmények [](https://github.com/open-policy-agent/gatekeeper#audit) a meghiúsult korlátozás állapot mezőjében felsorolt szabálysértésekként is láthatók. A nem megfelelő erőforrásokkal kapcsolatos részletekért lásd: Az _erőforrás-szolgáltatói_ [módok összetevő-részletei.](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)

> [!NOTE]
> A Kubernetes-Azure Policy összes megfelelőségi jelentése tartalmazza az elmúlt 45 percben történt összes szabálysértéseket. Az időbélyeg azt jelzi, hogy mikor történt szabálysértés.

Néhány további szempont:

- Ha a fürt-előfizetés regisztrálva van az Azure Security Center, Azure Security Center kubernetes-szabályzatok automatikusan alkalmazva vannak a fürtön.

- Ha egy megtagadási szabályzatot alkalmaznak a meglévő Kubernetes-erőforrásokkal futó fürtön, az új szabályzatnak nem megfelelő, már létező erőforrások továbbra is futnak. Ha a nem megfelelő erőforrást egy másik csomóponton ütemezik át, a Forgalomirányító blokkolja az erőforrás létrehozását.

- Ha egy fürt megtagadási szabályzata érvényesíti az erőforrásokat, a felhasználó nem kap elutasítási üzenetet az üzembe helyezés létrehozásakor. Vehet például egy Kubernetes-telepítést, amely replikákat és podokat tartalmaz. A végrehajtásakor a felhasználó nem `kubectl describe deployment $MY_DEPLOYMENT` ad vissza elutasítási üzenetet az események részeként. A azonban `kubectl describe replicasets.apps $MY_DEPLOYMENT` az elutasításhoz kapcsolódó eseményeket adja vissza.

## <a name="logging"></a>Naplózás

Kubernetes-vezérlőként/tárolóként az _azure-policy_ és a _forgalomirányító_ podok is a Kubernetes-fürtben tartják a naplókat. A naplókat a Kubernetes-fürt Insights oldalán lehet elérhetővé téve.  További információ: [A Kubernetes-fürt](../../../azure-monitor/containers/container-insights-analyze.md)teljesítményének figyelése Azure Monitor tárolókhoz című oldalon.

A bővítménynaplók megtekintéséhez használja a `kubectl` következőt:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

További információkért lásd [a Forgalomirányító hibakeresése a Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) dokumentációjában.

## <a name="troubleshooting-the-add-on"></a>A bővítmény hibaelhárítása

A Kubernetes-bővítmény hibaelhárításáról további információt a [Kubernetes hibaelhárítási cikkÉnek Kubernetes Azure Policy](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) szakaszában talál.

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

### <a name="remove-the-add-on-from-aks"></a>A bővítmény eltávolítása az AKS-ről

A bővítmény Azure Policy AKS-fürtből való eltávolításához használja a Azure Portal vagy az Azure CLI-t:

- Azure Portal

  1. Indítsa el az AKS-szolgáltatást a Azure Portal a **Minden** szolgáltatás lehetőség kiválasztásával, majd a **Kubernetes-szolgáltatások** keresését és kiválasztását.

  1. Válassza ki azt az AKS-fürtöt, ahol le szeretné tiltani Azure Policy bővítményt.

  1. Válassza **a** Szabályzatok lehetőséget a Kubernetes-szolgáltatás oldalának bal oldalán.

  1. A főoldalon válassza a **Bővítmény letiltása gombot.**

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>A bővítmény eltávolítása Azure Arc Kubernetesből

Ha el szeretné távolítani a Azure Policy bővítményt és a forgalomirányítót a Azure Arc Kubernetes-fürtből, futtassa a következő Helm-parancsot:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>A bővítmény eltávolítása az AKS-motorból

Ha el szeretné távolítani Azure Policy bővítményt és forgalomirányítót az AKS-motorfürtből, használja azt a metódust, amely igazodik a bővítmény telepítésének módjaihoz:

- Ha az AKS-motor fürtdefiníciójában az **addons** tulajdonság beállításával van telepítve:

  Az _azure-policy_ **addons** tulajdonságának false (hamis) értékre módosítása után a fürtdefiníciót az AKS-motorban újra üzembe kell adni:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  További információ: [AKS Engine - Disable Azure Policy add-on](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Ha Helm-diagramokkal van telepítve, futtassa a következő Helm-parancsot:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>A bővítmény által Azure Policy diagnosztikai adatok

A Azure Policy Kubernetes-bővítmény korlátozott fürtdiagnosztikai adatokat gyűjt. Ezek a diagnosztikai adatok létfontosságú technikai adatok a szoftverekhez és a teljesítményhez. A következő módokon használható:

- A Azure Policy naprakészen tarthatja a bővítményt
- A Azure Policy, megbízható és nagy teljesítmény biztosítása
- A Azure Policy javítása – a bővítmény használatának összesített elemzésével

A bővítmény által gyűjtött információk nem személyes adatok. Jelenleg a következő adatokat gyűjtjük be:

- Azure Policy bővítmény ügynökverziója
- Fürt típusa
- Fürt régiója
- Fürt erőforráscsoportja
- Fürterőforrás-azonosító
- Fürt előfizetés-azonosítója
- Fürt operációs rendszer (példa: Linux)
- Fürt városa (példa: Budapest)
- Fürt állapota vagy tartománya (példa: Washington)
- Fürt országa vagy régiója (például: Egyesült Államok)
- A bővítmény által a szabályzatértékeléskor Azure Policy ügynök telepítése során észlelt kivételek/hibák
- A gatekeeper-szabályzatdefiníciók száma, amelyet Azure Policy bővítmény nem telepített

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [a következő Azure Policy:](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- A szabályzatok [programozott módon történő létrehozása.](../how-to/programmatically-create.md)
- Megtudhatja, hogyan [olvashatja be a megfelelőségi adatokat.](../how-to/get-compliance-data.md)
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
- Tekintse át a felügyeleti csoportokat az [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal való használatával.](../../management-groups/overview.md)
