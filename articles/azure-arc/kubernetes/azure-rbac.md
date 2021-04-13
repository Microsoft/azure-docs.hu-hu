---
title: Azure RBAC az Azure arc-kompatibilis Kubernetes-fürtökhöz
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Az Azure RBAC használata engedélyezési ellenőrzésekhez az Azure arc-kompatibilis Kubernetes-fürtökön
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304205"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Azure Active Directory integrálása az Azure arc-kompatibilis Kubernetes-fürtökkel

A Kubernetes [ClusterRoleBinding és a RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) típusú objektumtípusok segítségével natív módon definiálhatja az Kubernetes-hitelesítést. Ezzel a szolgáltatással Azure Active Directory és szerepkör-hozzárendeléseket használhat az Azure-ban a fürt engedélyezési ellenőrzésének vezérléséhez. Ez azt jelenti, hogy mostantól használhatja az Azure szerepkör-hozzárendeléseket a Kubernetes-objektumok, például az üzembe helyezés, a pod és a szolgáltatás olvasási, írási és törlési lehetőségének részletes szabályozásához.

A szolgáltatás fogalmi áttekintése elérhető az [Azure RBAC – Azure arc-kompatibilis Kubernetes](conceptual-azure-rbac.md) cikkben.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure CLI telepítése vagy frissítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a következő verzióra: >= 2.16.0

- Telepítse az `connectedk8s` >= 1.1.0 verzióhoz tartozó Azure CLI-bővítményt:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Ha a `connectedk8s` bővítmény már telepítve van, a következő parancs használatával frissítheti a legújabb verzióra: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürt.
    - Ha még nem csatlakoztatta a fürtöt, használja a gyors üzembe helyezési [útmutatót.](quickstart-connect-cluster.md)
    - [Frissítse az ügynököket](agent-upgrade.md#manually-upgrade-agents) a >= 1.1.0 verzióra.

> [!NOTE]
> Ez a funkció nem állítható be olyan felhőalapú szolgáltatók felügyelt Kubernetes, mint a rugalmas Kubernetes szolgáltatás vagy a Google Kubernetes motor, ahol a felhasználónak nincs hozzáférése `apiserver` a fürthöz. Az Azure Kubernetes Service-(ak-) fürtök esetében ez a [funkció natív módon elérhető](../../aks/manage-azure-rbac.md) , és nem igényli, hogy az AK-fürt csatlakozni lehessen az Azure arc-hoz.

## <a name="set-up-azure-ad-applications"></a>Azure AD-alkalmazások beállítása

### <a name="create-server-application"></a>Kiszolgálóalkalmazás létrehozása

1. Hozzon létre egy új Azure AD-alkalmazást, és szerezze `appId` be az értékét, amelyet a későbbi lépésekben használ a következő módon `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Az alkalmazáscsoport tagsági jogcímeinek frissítése:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Hozzon létre egy egyszerű szolgáltatásnevet, és szerezze `password` be a mező értékét, amelyet később a `serverApplicationSecret` szolgáltatás fürtön való engedélyezésekor kell megadni:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Adja meg az alkalmazás API-engedélyeit:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Ezt a lépést egy Azure bérlői rendszergazdának kell végrehajtania.
    > * A szolgáltatás éles környezetben való használata esetén ajánlott egy másik kiszolgálóalkalmazás létrehozása minden fürthöz.

### <a name="create-client-application"></a>Ügyfélalkalmazás létrehozása

1. Hozzon létre egy új Azure AD-alkalmazást, és szerezze be az "appId" értéket, amelyet a későbbi lépésekben használ a következő módon `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Egyszerű szolgáltatásnév létrehozása az ügyfélalkalmazás számára:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. A kiszolgálói alkalmazás beszerzése `oAuthPermissionId` :

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Adja meg a szükséges engedélyeket az ügyfélalkalmazás számára:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Szerepkör-hozzárendelés létrehozása a kiszolgálóalkalmazás számára

A kiszolgálói alkalmazásnak szüksége van az `Microsoft.Authorization/*/read` engedélyekre, hogy ellenőrizze, hogy a kérést kérő felhasználó jogosult-e a kérelem részét képező Kubernetes-objektumokra.

1. Hozzon létre egy accessCheck.jsnevű fájlt a következő tartalommal:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Cserélje le a- `<subscription-id>` t a tényleges előfizetés-azonosítóra.

2. Az új egyéni szerepkör létrehozásához hajtsa végre a következő parancsot:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. A fenti parancs kimenetében tárolja a `id` mező értékét, amelyet a későbbi lépésekben használ `roleId` .

4. Hozzon létre egy szerepkör-hozzárendelést a kiszolgálói alkalmazásban a fent létrehozott szerepkör használatával:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Azure-RBAC engedélyezése a fürtön

1. A következő parancs futtatásával engedélyezheti az Azure RBAC használatát az ív-kompatibilis Kubernetes-fürtön:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. A fenti parancs futtatása előtt győződjön meg arról, hogy a számítógépen a fájl arra a `kubeconfig` fürtre mutat, amelyen engedélyezni kívánja az Azure RBAC funkciót.
    > 2. A `--skip-azure-rbac-list` fenti paranccsal a felhasználónevek/e-mailek/OID-azonosítók vesszővel tagolt listáját használhatja az Kubernetes natív ClusterRoleBinding és RoleBinding objektumok használatával az Azure RBAC helyett.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Olyan általános fürthöz, ahol nincs apiserver-specifikáción futó egyeztető:

1. SSH-t a fürt összes főcsomópontján, és hajtsa végre a következő lépéseket:

    1. `apiserver`A jegyzékfájl megnyitása szerkesztési módban:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Adja hozzá a következő specifikációt a alatt `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Adja hozzá a következő specifikációt a alatt `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Adja hozzá a következő `apiserver` argumentumokat:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Ha a Kubernetes-fürt verziója >= 1.19.0, akkor a következőket `apiserver argument` is be kell állítani:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Mentse és zárja be a szerkesztőt a `apiserver` Pod frissítéséhez.


### <a name="for-a-cluster-created-using-cluster-api"></a>A cluster API használatával létrehozott fürtök esetében

1. Másolja a hitelesítés és engedélyezés webhook konfigurációs fájljait tartalmazó titkos kulcsot a `from the workload cluster` gépre:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Módosítsa a `namespace` fájl mezőjét a `azure-arc-guard-manifests.yaml` felügyeleti fürtben található névtérre, ahol az egyéni erőforrásokat alkalmazza a munkaterhelés-fürtök létrehozásához.

1. A jegyzékfájl alkalmazása:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Szerkessze az `KubeadmControlPlane` objektumot a végrehajtásával `kubectl edit kcp <clustername>-control-plane` :
    
    1. Adja hozzá a következő kódrészletet a alatt `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Adja hozzá a következő kódrészletet a alatt `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Adja hozzá a következő kódrészletet a alatt `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Mentse és lépjen ki az objektum frissítéséhez `KubeadmControlPlane` . Várjon, amíg ezek a módosítások megvalósulnak a munkaterhelés-fürtön.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Szerepkör-hozzárendelések létrehozása a felhasználók számára a fürt eléréséhez

Az Azure arc-kompatibilis Kubernetes-erőforrás tulajdonosai használhatnak beépített szerepköröket vagy egyéni szerepköröket, így más felhasználóknak is hozzáférést biztosíthatnak a Kubernetes-fürthöz.

### <a name="built-in-roles"></a>Beépített szerepkörök

| Szerepkör | Leírás |
|---|---|
| [Azure arc Kubernetes megjelenítője](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Ez a szerepkör nem teszi lehetővé a titkok megtekintését. Ennek az az oka, hogy a `read` titkos kulcsok engedélyei lehetővé teszik `ServiceAccount` a névtérben lévő hitelesítő adatokhoz való hozzáférést, ami viszont lehetővé teszi az API-hozzáférés használatát `ServiceAccount` (a jogosultságok kiterjesztését). |
| [Azure arc Kubernetes-író](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a titkok elérését és a hüvelyek futtatását `ServiceAccount` a névtérben, így a névtér bármelyikének API-hozzáférési szintjeinek megszerzésére használható `ServiceAccount` . |
| [Az Azure arc Kubernetes rendszergazdája](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Lehetővé teszi a rendszergazdai hozzáférést. Egy RoleBinding használó névtéren belül kell megadni. Ha RoleBinding használja, az olvasási/írási hozzáférést biztosít a névtérben található legtöbb erőforráshoz, beleértve a szerepkörök és a szerepkör-kötések létrehozását a névtéren belül. Ez a szerepkör nem engedélyez írási hozzáférést az erőforrás-kvótához vagy magához a névtérhez. |
| [Azure arc Kubernetes-fürt rendszergazdája](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Lehetővé teszi a felügyelők számára, hogy bármilyen műveletet végrehajtson bármilyen erőforráson. A ClusterRoleBinding használatakor teljes hozzáférést biztosít a fürt összes erőforrásához és az összes névtérhez. A RoleBinding használatakor teljes hozzáférést biztosít a szerepkör-kötés névterében lévő összes erőforráshoz, beleértve a névteret is.|

A Azure Portal-on lévő fürterőforrás paneljén létrehozhat olyan szerepkör-hozzárendeléseket, amelyek az arc-kompatibilis Kubernetes-fürthöz vannak hozzárendelve `Access Control (IAM)` . Az Azure CLI-parancsokat is használhatja az alább látható módon:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

hol `AZURE-AD-ENTITY-ID` lehet a Felhasználónév (például testuser@mytenant.onmicrosoft.com ), vagy akár `appId` egy egyszerű szolgáltatásnév is.

Íme egy másik példa arra, hogyan hozható létre egy szerepkör-hozzárendelés egy adott névtérhez a fürtön belül –

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Míg a fürtre hatókörrel rendelkező szerepkör-hozzárendelések a Azure Portal vagy a parancssori felület használatával hozhatók létre, a névterek hatókörében lévő szerepkör-hozzárendelések csak a CLI használatával hozhatók létre.

### <a name="custom-roles"></a>Egyéni szerepkörök

Dönthet úgy, hogy saját szerepkör-definíciót hoz létre a szerepkör-hozzárendelésekben való használathoz.

Tekintse át a szerepkör-definíció alábbi példáját, amely lehetővé teszi, hogy a felhasználó csak az üzembe helyezéseket olvassa. További információkért tekintse [meg a szerepkör-definíció létrehozásához használható adatműveletek teljes listáját](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Másolja az alábbi JSON-objektumot egy custom-role.jsnevű fájlba. Cserélje le a `<subscription-id>` helyőrzőt a tényleges előfizetés-azonosítóra. Az alábbi egyéni szerepkör az egyik adatműveletet használja, és lehetővé teszi az összes központi telepítés megtekintését a hatókörben (fürt/névtér), ahol a szerepkör-hozzárendelés létrejött.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Hozza létre a szerepkör-definíciót az alábbi parancs futtatásával a mentett mappából `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Szerepkör-hozzárendelés létrehozása az egyéni szerepkör-definíció használatával:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Kubectl konfigurálása felhasználói hitelesítő adatokkal

`kubeconfig`A fürt eléréséhez kétféleképpen lehet beolvasni a fájlt:
1. Használja [](cluster-connect.md) `az connectedk8s proxy` Az Azure arc-kompatibilis Kubernetes-fürt fürt-csatlakozási funkcióját ().
1. A fürt rendszergazdája `kubeconfig` minden más felhasználóval megosztja a fájlt.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Ha a fürtöt a fürthöz való csatlakozási funkcióval fér hozzá

Futtassa a következő parancsot a proxy folyamat elindításához:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

A proxy folyamatának futtatása után megnyithatja a konzol egy másik lapját, hogy [megkezdje a kérések fürtre küldését](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Ha a fürt rendszergazdája megosztotta `kubeconfig` Önnel a fájlt 

1. A felhasználó hitelesítő adatainak beállításához hajtsa végre a következő parancsot:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Nyissa meg a `kubeconfig` korábban létrehozott fájlt. A alatt `contexts` ellenőrizze, hogy a fürthöz társított környezet az előző lépésben létrehozott felhasználói hitelesítő adatokkal van-e társítva.

1. Adja hozzá a **config-Mode** beállítást a felhasználói konfiguráció területen:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Kérelmek küldése a fürtnek

1. Futtasson bármilyen `kubectl` parancsot. Például:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Ha a rendszer egy böngészőalapú hitelesítést kér, másolja az eszköz bejelentkezési URL-címét, `https://microsoft.com/devicelogin` és nyissa meg a webböngészőjében.

1. Adja meg a konzolon kinyomtatott kódot, másolja és illessze be a kódot a terminálba az eszköz-hitelesítési bemeneti üzenetbe.

1. Adja meg a felhasználónevet ( testuser@mytenant.onmicrosoft.com ) és a hozzá tartozó jelszót.

1. Ha a következőhöz hasonló hibaüzenet jelenik meg, az azt jelenti, hogy a kért erőforráshoz való hozzáférés nem engedélyezett:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    A rendszergazdának létre kell hoznia egy új szerepkör-hozzárendelést, amely engedélyezi, hogy a felhasználó hozzáférjen az erőforráshoz.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Biztonságos kapcsolódás a fürthöz a [fürt csatlakoztatása](cluster-connect.md) használatával