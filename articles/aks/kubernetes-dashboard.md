---
title: Fürt Azure Kubernetes Service kezelése a webes irányítópult használatával
description: Megtudhatja, hogyan használhatja a beépített Kubernetes webes felhasználói felületi irányítópultját egy Azure Kubernetes Service -fürt kezelésére
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 0d872a60c4aea89e621fe25ade45697244a74fa8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779722"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>A Kubernetes webes irányítópultjának elérése Azure Kubernetes Service (AKS)

A Kubernetes tartalmaz egy webes irányítópultot, amely alapszintű felügyeleti műveletekhez használható. Ezen az irányítópulton megtekintheti az alkalmazások alapvető állapot- és metrikaállapotát, szolgáltatásokat hozhat létre és telepíthet, valamint szerkesztheti a meglévő alkalmazásokat. Ez a cikk bemutatja, hogyan férhet hozzá a Kubernetes-irányítópulthoz az Azure CLI használatával, majd végigvezeti néhány alapvető irányítópult-műveleten.

További információ a Kubernetes-irányítópultról: [Kubernetes webes felhasználói felületi irányítópult.][kubernetes-dashboard] Az AKS a nyílt forráskódú irányítópult 2.0-s és újabb verzióját használja.

> [!WARNING]
> **Az AKS-irányítópult bővítménye elajátításra van beállítva. Használja inkább a [Kubernetes erőforrásnézetet a Azure Portal (előzetes verzió)][kubernetes-portal] nézetben.** 
> * A Kubernetes-irányítópult alapértelmezés szerint engedélyezve van az 1.18-asnál régebbi Kubernetes-verziót futtató fürtök számára.
> * Az irányítópult-bővítmény alapértelmezés szerint le lesz tiltva a Kubernetes 1.18-as vagy újabb rendszerében létrehozott összes új fürtön. 
 > * Az előzetes verzióban elérhető Kubernetes 1.19-es verziótól kezdve az AKS már nem támogatja a felügyelt kube-dashboard bővítmény telepítését. 
 > * A bővítményt engedélyező meglévő fürtökre ez nem lesz hatással. A felhasználók továbbra is manuálisan telepítheti a nyílt forráskódú irányítópultot felhasználó által telepített szoftverként.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban részletezett lépések feltételezik, hogy létrehozott egy AKS-fürtöt, és létrehozott egy kapcsolatot a `kubectl` fürthöz. Ha létre kell hoznia egy AKS-fürtöt, tekintse meg a rövid útmutató: Azure Kubernetes Service-fürt üzembe helyezése [az Azure CLI használatával.][aks-quickstart]

Emellett az Azure CLI 2.6.0-s vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>A Kubernetes-irányítópult letiltása

A kube-dashboard bővítmény alapértelmezés szerint engedélyezve van a **K8s 1.18-asnál régebbi fürtökön.** A bővítmény az alábbi parancs futtatásával tiltható le.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Indítsa el a Kubernetes-irányítópultot

> [!WARNING]
> Az AKS-irányítópult bővítménye az 1.19-es és újabb verziók esetén elavult. Használja inkább a [Kubernetes erőforrásnézetet a Azure Portal (előzetes verzió)][kubernetes-portal] nézetben. 
> * A következő parancs ekkor megnyitja az Azure Portal erőforrásnézetét a kubernetes-irányítópult helyett az 1.19-es és újabb verziókhoz.

A Kubernetes-irányítópult fürtön való indításához használja az [az aks browse][az-aks-browse] parancsot. Ehhez a parancshoz telepíteni kell a kube-dashboard bővítményt a fürtön, amely alapértelmezés szerint megtalálható a Kubernetes 1.18-as verziójánál régebbi verziót futtató fürtökön.

Az alábbi példa megnyitja a *myResourceGroup* nevű erőforráscsoportban lévő *myAKSCluster* nevű fürt irányítópultját:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Ez a parancs létrehoz egy proxyt a fejlesztői rendszer és a Kubernetes API között, és megnyit egy webböngészőt a Kubernetes-irányítópulton. Ha egy webböngésző nem nyílik meg a Kubernetes-irányítópulton, másolja és illessze be az Azure CLI-ben feljegyzett URL-címet( általában `http://127.0.0.1:8001` ).

> [!NOTE]
> Ha nem látja az irányítópultot a következő címekre manuálisan `http://127.0.0.1:8001` is. Az 1.16-os vagy annál nagyobb fürtök a https protokollt használják, és külön végpontot igényelnek.
> * K8s 1.16 vagy nagyobb: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 és az alábbi: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Bejelentkezés az irányítópultra (kubernetes 1.16+)

> [!IMPORTANT]
> A [Kubernetes-irányítópult vagy a kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) v1.16+ 1.10-es vagy újabb verziókban a "kubernetes-dashboard" szolgáltatásfiók már nem használható erőforrások lekérésekor a kiadásban található biztonsági javítás [miatt.](https://github.com/kubernetes/dashboard/pull/3400) Ennek eredményeképpen a hitelesítési adatok nélküli kérelmek [401-es jogosulatlan hibát eredményeznek.](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998) A szolgáltatásfiókból lekért bearer token továbbra is használható, mint ebben a [Kubernetes-irányítópult-példában,](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)de ez hatással van az irányítópult bővítményének bejelentkezési folyamatára a régebbi verziókhoz képest.
>
>Ha az 1.16-osnál korábbi verziót futtat, továbbra is adhat engedélyeket a "kubernetes-dashboard" szolgáltatásfiókhoz, de ez **nem ajánlott:**
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

A megjelenő kezdeti képernyőn kubeconfig vagy token szükséges. Mindkét lehetőséghez erőforrás-engedélyre van szükség az erőforrások irányítópulton való megjelenítéséhez.

![bejelentkezési képernyő](./media/kubernetes-dashboard/login.png)

**Kubeconfig használata**

Az Azure AD-t és a nem Azure AD-t engedélyező fürtök esetén is át lehet adni egy kubeconfig-t. Győződjön meg arról, hogy a hozzáférési jogkivonatok érvényesek, ha a jogkivonatok lejártak, a kubectl használatával frissítheti a jogkivonatokat.

1. A rendszergazdai kubeconfig beállítása a következővel: `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Válassza ki `Kubeconfig` a fájlt, `Choose kubeconfig file` és kattintson rá a fájlválasztó megnyitásához
1. Válassza ki a kubeconfig fájlt (alapértelmezés szerint $HOME/.kube/config)
1. Kattintson a következőre: `Sign In`

**Jogkivonat használata**

1. A **nem Azure AD-t használó fürtök esetén** futtassa és másolja ki a fürt felhasználói fiókjához társított `kubectl config view` jogkivonatot.
1. Bejelentkezéskor illessze be a token lehetőséget.    
1. Kattintson a következőre: `Sign In`

Az Azure AD-t engedélyező fürtök esetén az alábbi paranccsal olvassa be az AAD-jogkivonatot. Ellenőrizze, hogy lecserélte-e az erőforráscsoportot és a fürt nevét a parancsban.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

A sikeres művelet után megjelenik egy, az alábbihoz hasonló oldal.

![A Kubernetes webes irányítópultjának áttekintő oldala](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Alkalmazás létrehozása

A következő lépésekhez a felhasználónak engedélyekkel kell rendelkeznie a megfelelő erőforrásokhoz. 

Ahhoz, hogy lássuk, hogyan csökkentheti a Kubernetes-irányítópult a felügyeleti feladatok összetettségét, hozzunk létre egy alkalmazást. Alkalmazásokat a Kubernetes irányítópultján hozhat létre szövegbevitel, YAML-fájl vagy grafikus varázsló segítségével.

Alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Válassza a **Létrehozás** gombot a jobb felső ablakban.
1. A grafikus varázslót úgy használhatja, hogy létrehoz **egy alkalmazást.**
1. Adja meg az üzemelő példány nevét, például *nginx*
1. Adja meg a használni használt tároló rendszerképének nevét, például *nginx:1.15.5*
1. Ha a 80-as portot webes forgalomhoz is elérhetővé teszi, hozzon létre egy Kubernetes-szolgáltatást. A **Szolgáltatás alatt** válassza a **Külső** lehetőséget, majd adja meg a **80-as** portot a port és a célport számára is.
1. Ha elkészült, válassza az Üzembe **helyezés** lehetőséget az alkalmazás létrehozásához.

![Alkalmazás üzembe helyezése a Kubernetes webes irányítópultján](./media/kubernetes-dashboard/create-app.png)

Egy-két percet vesz igénybe, hogy egy nyilvános külső IP-cím hozzá legyen rendelve a Kubernetes-szolgáltatáshoz. A bal oldali méretben, a Felderítés és **terheléselosztás** alatt válassza a Szolgáltatások **lehetőséget.** Megjelenik az alkalmazás szolgáltatása, beleértve a *külső* végpontokat is, ahogy az alábbi példában látható:

![Szolgáltatások és végpontok listájának megtekintése](./media/kubernetes-dashboard/view-services.png)

Válassza ki a végpont címét az alapértelmezett NGINX-oldal webböngészőablakának megnyitásához:

![Az üzembe helyezett alkalmazás alapértelmezett NGINX oldalának megtekintése](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Podok információinak megtekintése

A Kubernetes irányítópultja alapvető monitorozási metrikákat és hibaelhárítási információkat, például naplókat biztosít.

Az alkalmazáspodokkal kapcsolatos további információkért válassza a **podok** lehetőséget a bal oldali menüben. Megjelenik az elérhető podok listája. Válassza ki *az nginx-podot* az információk, például az erőforrás-használat megtekintéséhez:

![Podok információinak megtekintése](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Az alkalmazások létrehozása és megtekintése mellett a Kubernetes-irányítópult is használható az alkalmazástelepítések szerkesztésére és frissítésére. Az alkalmazás további redundanciának biztosításához növeljük az NGINX-replikák számát.

Üzemelő példány szerkesztése:

1. Válassza **az Üzemelő példányok** elemet a bal oldali menüben, majd válassza ki *az nginx üzemelő példányát.*
1. A **jobb felső** navigációs sávon válassza a Szerkesztés lehetőséget.
1. Keresse meg `spec.replica` az értéket a 20. sor körül. Az alkalmazás replikái számának növeléséhez módosítsa ezt az értéket *1-ről* *3-ra.*
1. Ha **készen áll,** válassza a Frissítés lehetőséget.

![Az üzemelő példány szerkesztése a replikák számának frissítéséhez](./media/kubernetes-dashboard/edit-deployment.png)

Az új podok létrehozása egy replikakészleten belül néhány percet vesz igénybe. A bal oldali menüben válassza a **Replikakészletek** lehetőséget, majd válassza ki *az nginx replikakészletet.* A podok listája most már tükrözi a frissített replikaszámokat, ahogy az a következő példakimenetben látható:

![A replikakészlet információinak megtekintése](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes-irányítópultról: [Kubernetes webes felhasználói felületi irányítópult.][kubernetes-dashboard]

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
[az-account-get-access-token]: /cli/azure/account#az_account_get-access-token
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
