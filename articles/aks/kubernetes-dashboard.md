---
title: Azure Kubernetes Service-fürt kezelése a webes irányítópulttal
description: Ismerje meg, hogyan kezelheti az Azure Kubernetes Service (ak) fürtjét a beépített Kubernetes webes felhasználói felületi irányítópult használatával
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: acaeaa2e5338c86fa59d0e2941719f8fa2708ef1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176821"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>A Kubernetes web Dashboard elérése az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes tartalmaz egy webes irányítópultot, amely alapszintű felügyeleti műveletekhez használható. Ez az irányítópult lehetővé teszi az alkalmazások alapvető állapotának és mérőszámának megtekintését, szolgáltatások létrehozását és üzembe helyezését, valamint meglévő alkalmazások szerkesztését. Ez a cikk bemutatja, hogyan érheti el a Kubernetes-irányítópultot az Azure CLI használatával, majd végigvezeti az irányítópultok alapszintű műveletein.

A Kubernetes-irányítópulttal kapcsolatos további információkért lásd: [Kubernetes webes felhasználói felület irányítópultja][kubernetes-dashboard]. Az AK a 2,0-es és újabb verziójú nyílt forráskódú irányítópultot használja.

> [!WARNING]
> **Az AK-irányítópult bővítmény az elavultság beállítására van beállítva. Ehelyett használja a [Azure Portal (előzetes verzió) Kubernetes erőforrás nézetét][kubernetes-portal] .** 
> * A Kubernetes irányítópultja alapértelmezés szerint engedélyezve van a 1,18-nál kisebb Kubernetes-verziót futtató fürtök esetében.
> * Az irányítópult-bővítmény alapértelmezés szerint le lesz tiltva az 1,18-es vagy újabb Kubernetes létrehozott összes új fürt esetében. 
 > * Az előzetes verzióban az Kubernetes 1,19-es verziójától kezdve az AK többé nem támogatja a felügyelt Kube-irányítópult-bővítmény telepítését. 
 > * A bővítményt engedélyező meglévő fürtöket nem érinti a rendszer. A felhasználók továbbra is képesek lesznek manuálisan telepíteni a nyílt forráskódú irányítópultot felhasználó által telepített szoftverként.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban részletezett lépések feltételezik, hogy létrehozott egy AK-fürtöt, és létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat. Ha AK-fürtöt kell létrehoznia, tekintse meg a rövid útmutató [: Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI használatával][aks-quickstart]című témakört.

Szüksége lesz az Azure CLI-es vagy újabb verziójára is, amely telepítve van és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>A Kubernetes irányítópult letiltása

A Kube-irányítópult addon **alapértelmezés szerint engedélyezve van a K8s 1,18-nál régebbi fürtökön**. Az addon le lehet tiltani a következő parancs futtatásával.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>A Kubernetes irányítópult elindítása

> [!WARNING]
> Az AK-irányítópult bővítmény elavult a 1.19 + verzióban. Ehelyett használja a [Azure Portal (előzetes verzió) Kubernetes erőforrás nézetét][kubernetes-portal] . 
> * A következő parancs most megnyitja az Azure Portal erőforrás nézetét a 1,19-es és újabb verziók kubernetes irányítópultja helyett.

A Kubernetes-irányítópult fürtön való elindításához használja az az [AK Browse][az-aks-browse] parancsot. Ehhez a parancshoz a Kube-Dashboard addon telepítése szükséges a fürtön, amely alapértelmezés szerint a Kubernetes 1,18-nál régebbi verziót futtató fürtökön található.

Az alábbi példa megnyitja az irányítópultot a *myAKSCluster* nevű fürthöz az *myResourceGroup* nevű erőforráscsoport:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Ez a parancs létrehoz egy proxyt a fejlesztői rendszer és a Kubernetes API között, és egy webböngészőt nyit meg a Kubernetes-irányítópulton. Ha egy webböngésző nem nyílik meg a Kubernetes irányítópultra, másolja és illessze be az Azure CLI-ben feljegyzett URL-címet, jellemzően `http://127.0.0.1:8001` .

> [!NOTE]
> Ha nem látja az irányítópultot, akkor `http://127.0.0.1:8001` manuálisan is átirányíthatja a következő címekre. A 1,16-es vagy újabb fürtökön HTTPS protokollt használnak, és külön végpontot igényelnek.
> * K8s 1,16 vagy újabb: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 és alacsonyabb: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Bejelentkezés az irányítópultra (kubernetes 1.16 +)

> [!IMPORTANT]
> [A Kubernetes-irányítópult](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) vagy a Kubernetes v 1.16-nek és a "Kubernetes-Dashboard" 1.10.1 a továbbiakban nem használhatók erőforrások lekérésére az [adott kiadásban található biztonsági javítás](https://github.com/kubernetes/dashboard/pull/3400)miatt. Ennek eredményeképpen a hitelesítési adatok nélküli kérések [401 jogosulatlan hibát](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998)adnak vissza. Egy szolgáltatásfiók által beolvasott tulajdonosi jogkivonat továbbra is használható ebben a Kubernetes- [irányítópulton](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), de ez hatással van az irányítópult-bővítmény bejelentkezési folyamatára a régebbi verziókhoz képest.
>
>Ha továbbra is a 1,16 előtti verziót futtatja, akkor továbbra is megadhatja a "kubernetes-Dashboard" szolgáltatásfiók engedélyeit, de ez **nem ajánlott**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

A kezdeti képernyőn egy kubeconfig vagy token szükséges. Mindkét beállításhoz erőforrás-engedély szükséges ahhoz, hogy megjelenjenek az erőforrások az irányítópulton.

![bejelentkezési képernyő](./media/kubernetes-dashboard/login.png)

**Kubeconfig használata**

Az Azure AD-t és a nem Azure AD-t használó fürtök esetében a kubeconfig is átadható. Győződjön meg arról, hogy a hozzáférési jogkivonatok érvényesek, ha a tokenek lejártak, a kubectl-on keresztül frissítheti a jogkivonatokat.

1. A rendszergazdai kubeconfig beállítása a `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Válassza ki `Kubeconfig` , majd kattintson `Choose kubeconfig file` a fájl-választó megnyitásához
1. Válassza ki a kubeconfig-fájlt (az alapértelmezett érték a $HOME/.Kube/config)
1. Kattintson a következőre: `Sign In`

**Token használata**

1. **Nem Azure ad-kompatibilis fürt** esetén futtassa `kubectl config view` és másolja a fürt felhasználói fiókjához társított jogkivonatot.
1. Illessze be a tokent a bejelentkezés elemre.    
1. Kattintson a következőre: `Sign In`

Az Azure AD-kompatibilis fürtök esetében a következő paranccsal kérje le az HRE tokent. Ellenőrizze, hogy lecserélte-e az erőforráscsoportot és a fürt nevét a parancsban.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

A sikeres művelet után az alábbihoz hasonló oldal jelenik meg.

![A Kubernetes webes irányítópultjának Áttekintés lapja](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Alkalmazás létrehozása

A következő lépések végrehajtásához a felhasználónak rendelkeznie kell a megfelelő erőforrásokhoz szükséges engedélyekkel. 

Ha szeretné megtudni, hogyan csökkentheti a Kubernetes irányítópultja a felügyeleti feladatok összetettségét, hozzon létre egy alkalmazást. Létrehozhat egy alkalmazást a Kubernetes-irányítópulton egy szövegbeviteli, egy YAML-fájl vagy egy grafikus varázsló segítségével.

Alkalmazás létrehozásához hajtsa végre a következő lépéseket:

1. Válassza a **Létrehozás** gombot a jobb felső ablakban.
1. A grafikus varázsló használatához válassza az **alkalmazás létrehozása** lehetőséget.
1. Adja meg az üzemelő példány nevét, például *Nginx*
1. Adja meg a használni kívánt tároló-rendszerkép nevét, például *Nginx: 1.15.5*
1. Ahhoz, hogy a 80-es port elérhető legyen a webes forgalom számára, létre kell hoznia egy Kubernetes szolgáltatást. A **szolgáltatás** területen válassza a **külső** lehetőséget, majd adja meg a **80** értéket a port és a célport esetében is.
1. Ha elkészült, válassza a **telepítés** lehetőséget az alkalmazás létrehozásához.

![Alkalmazás üzembe helyezése a Kubernetes webes irányítópultján](./media/kubernetes-dashboard/create-app.png)

A Kubernetes szolgáltatáshoz hozzárendelt nyilvános külső IP-címekhez egy-két percet vesz igénybe. A bal oldali méretnél a **felderítés és** terheléselosztás területen válassza a **szolgáltatások** lehetőséget. Az alkalmazás szolgáltatása szerepel a listáján, beleértve a *külső végpontokat* is, ahogy az az alábbi példában is látható:

![Szolgáltatások és végpontok listájának megtekintése](./media/kubernetes-dashboard/view-services.png)

Válassza ki a végpontot, és nyissa meg a webböngésző ablakát az alapértelmezett NGINX-lapra:

![Az üzembe helyezett alkalmazás alapértelmezett NGINX-lapjának megtekintése](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-adatok megtekintése

A Kubernetes irányítópulton alapszintű figyelési mérőszámok és hibaelhárítási információk, például naplók adhatók meg.

Ha többet szeretne megtudni az alkalmazás hüvelyéről, válassza a bal oldali menüben a **hüvelyek** lehetőséget. Megjelenik az elérhető hüvelyek listája. Válassza ki az *Nginx* Pod-t az információk, például az erőforrás-felhasználás megtekintéséhez:

![Pod-adatok megtekintése](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Az alkalmazások létrehozása és megtekintése mellett a Kubernetes irányítópultja is használható az alkalmazások központi telepítésének szerkesztéséhez és frissítéséhez. Az alkalmazás további redundancia biztosítása érdekében növeljük az NGINX-replikák számát.

Központi telepítés szerkesztése:

1. Válassza a **központi telepítések** lehetőséget a bal oldali menüben, majd válassza ki a *Nginx* -telepítést.
1. Válassza a **Szerkesztés** lehetőséget a jobb felső navigációs sávon.
1. Keresse meg az `spec.replica` értéket a következő helyen: 20. sor. Az alkalmazás replikáinak számának növeléséhez módosítsa az értéket *1* és *3* között.
1. Ha elkészült, válassza a **frissítés** lehetőséget.

![A központi telepítés szerkesztése a replikák számának frissítéséhez](./media/kubernetes-dashboard/edit-deployment.png)

Néhány percet vesz igénybe, hogy az új hüvelyek létre legyenek hozva egy replikakészlet-készleten belül. A bal oldali menüben válassza a **replika készletek** lehetőséget, majd válassza ki az *Nginx* -replikát. A hüvelyek listája most már a frissített replikák számát tükrözi, ahogy az a következő példában látható:

![A replikakészlet információinak megtekintése](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Következő lépések

A Kubernetes-irányítópulttal kapcsolatos további információkért tekintse meg a [Kubernetes webes felhasználói felületének irányítópultját][kubernetes-dashboard].

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
[az-account-get-access-token]: /cli/azure/account#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
