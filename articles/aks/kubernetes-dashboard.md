---
title: Azure Kubernetes-szolgáltatásfürt kezelése a webes irányítópulton
description: Ismerje meg, hogyan használhatja a beépített Kubernetes webes felhasználói felület irányítópultját egy Azure Kubernetes-szolgáltatás (AKS) fürt jének kezeléséhez
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595348"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>A Kubernetes webes irányítópultjának elérése az Azure Kubernetes szolgáltatásban (AKS)

A Kubernetes tartalmaz egy webes irányítópultot, amely az alapvető felügyeleti műveletekhez használható. Ez az irányítópult lehetővé teszi az alkalmazások alapvető állapotának és metrikáinak megtekintését, a szolgáltatások létrehozását és üzembe helyezését, valamint a meglévő alkalmazások szerkesztését. Ez a cikk bemutatja, hogyan érheti el a Kubernetes irányítópultját az Azure CLI használatával, majd végigvezeti néhány alapvető irányítópult-műveleten.

A Kubernetes irányítópultjáról további információt a [Kubernetes webfelhasználói felületirányítópultja talál.][kubernetes-dashboard]

## <a name="before-you-begin"></a>Előkészületek

Az ebben a dokumentumban ismertetett lépések feltételezik, hogy `kubectl` létrehozott egy AKS-fürtöt, és kapcsolatot létesített a fürttel. Ha AKS-fürtöt kell létrehoznia, olvassa el az [AKS rövid útmutatóját.][aks-quickstart]

Emellett az Azure CLI 2.0.46-os vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt:  `az --version` . Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="start-the-kubernetes-dashboard"></a>A Kubernetes irányítópult jának indítása

A Kubernetes irányítópult elindításához használja az [aks tallózás][az-aks-browse] parancsot. A következő példa megnyitja a myResourceGroup nevű erőforráscsoportban a *myAKSCluster* nevű fürt *irányítópultját:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Ez a parancs létrehoz egy proxyt a fejlesztői rendszer és a Kubernetes API között, és megnyit egy webböngészőt a Kubernetes irányítópultjára. Ha egy webböngésző nem nyílik meg a Kubernetes irányítópultjára, másolja és illessze `http://127.0.0.1:8001`be az Azure CLI-ben megjegyzett URL-címet, általában.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Ha az AKS-fürt RBAC-ot használ, az irányítópult megfelelő elérése előtt létre kell hozni egy *ClusterRoleBinding-t.* Alapértelmezés szerint a Kubernetes-irányítópult minimális olvasási hozzáféréssel van telepítve, és rbac hozzáférési hibákat jelenít meg. A Kubernetes-irányítópult jelenleg nem támogatja a felhasználó által megadott hitelesítő adatokat a hozzáférés szintjének meghatározásához, hanem a szolgáltatásfióknak biztosított szerepköröket használja. A fürt rendszergazdája dönthet úgy, hogy további hozzáférést biztosít a *kubernetes-irányítópult* szolgáltatásfiókhoz, azonban ez a jogosultságok eszkalációjának vektora lehet. Az Azure Active Directory-hitelesítést is integrálhatja, hogy részletesebb hozzáférési szintet biztosítson.
> 
> Kötés létrehozásához használja a [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] parancsot. A következő példa bemutatja, hogyan hozhat létre mintakötést, azonban ez a mintakötés nem alkalmaz további hitelesítési összetevőket, és nem biztonságos használathoz vezethet. A Kubernetes irányítópultja bárki számára elérhető, aki hozzáfér az URL-címhez. Ne tegye közzé nyilvánosan a Kubernetes irányítópultot.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> A különböző hitelesítési módszerek használatáról a [Hozzáférés-vezérlésről][dashboard-authentication]szóló Kubernetes irányítópult-wikiben talál további információt.

![A Kubernetes webes irányítópultjának áttekintő lapja](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha meg szeretné tekinteni, hogy a Kubernetes-irányítópult hogyan csökkentheti a felügyeleti feladatok összetettségét, hozzon létre egy alkalmazást. A Kubernetes irányítópultjáról szövegbevitel, YAML-fájl vagy grafikus varázsló segítségével hozhat létre alkalmazást.

Alkalmazás létrehozásához hajtsa végre az alábbi lépéseket:

1. A jobb felső ablakban válassza a **Létrehozás** gombot.
1. A grafikus varázsló használatához válassza **az Alkalmazás létrehozása**lehetőséget.
1. Adja meg a telepítés nevét, például *nginx*
1. Adja meg a használni használandó tárolórendszerkép nevét, például *nginx:1.15.5*
1. A 80-as port webes forgalomhoz való elérhetővé tenni, hozzon létre egy Kubernetes-szolgáltatást. A **Szolgáltatás**csoportban válassza **a Külső**lehetőséget, majd írja be a **80** értéket mind a portra, mind a célportra.
1. Ha készen áll, válassza **a Telepítés** lehetőséget az alkalmazás létrehozásához.

![Alkalmazás üzembe helyezése a Kubernetes webes irányítópultján](./media/kubernetes-dashboard/create-app.png)

Egy-két percet vesz igénybe egy nyilvános külső IP-cím hozzárendelése a Kubernetes szolgáltatáshoz. A bal oldali méretnél a **Felderítés és a terheléselosztás** területen válassza a **Szolgáltatások**lehetőséget. Az alkalmazás szolgáltatása szerepel a *listában,* beleértve a külső végpontokat is, ahogy az a következő példában látható:

![Szolgáltatások és végpontok listájának megtekintése](./media/kubernetes-dashboard/view-services.png)

A végpont címének kiválasztásával nyisson meg egy böngészőablakot az alapértelmezett NGINX-lapra:

![Az üzembe helyezett alkalmazás alapértelmezett NGINX-lapjának megtekintése](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-információk megtekintése

A Kubernetes irányítópult alapvető figyelési metrikákat és hibaelhárítási információkat, például naplókat biztosít.

Ha további információt szeretne látni az alkalmazáspodokról, válassza a Bal oldali menü **Pods parancsát.** Megjelenik a rendelkezésre álló podok listája. Válassza ki a *nginx* pod információk megtekintéséhez, például az erőforrás-felhasználás:

![Pod-információk megtekintése](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Az alkalmazások létrehozása és megtekintése mellett a Kubernetes irányítópultja is használható az alkalmazások központi telepítésének szerkesztésére és frissítésére. További redundancia biztosítása érdekében az alkalmazás, növeljük az NGINX replikák számát.

Központi telepítés szerkesztése:

1. Válassza **a központi telepítések lehetőséget** a bal oldali menüben, majd válassza ki a *nginx* központi telepítését.
1. A jobb felső navigációs sávon válassza a **Szerkesztés** lehetőséget.
1. Keresse `spec.replica` meg az értéket a 20- as vonal körül. Az alkalmazás replikáiszámának növeléséhez módosítsa ezt az értéket *1-ről* *3-ra.*
1. Válassza a **Frissítés** lehetőséget, ha készen áll.

![A központi telepítés szerkesztése a replikák számának frissítéséhez](./media/kubernetes-dashboard/edit-deployment.png)

Néhány percet vesz igénybe, amíg az új podok létre kell hozni egy replikakészletben. A bal oldali menüben válassza a **Replikakészletek**parancsot, majd válassza ki a *nginx* replikakészletet. A podok listája most tükrözi a frissített replika számát, ahogy az a következő példa kimenetben látható:

![A kópiakészlet adatainak megtekintése](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>További lépések

A Kubernetes irányítópultjáról további információt a [Kubernetes webes felhasználói felületének irányítópultján talál.][kubernetes-dashboard]

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
