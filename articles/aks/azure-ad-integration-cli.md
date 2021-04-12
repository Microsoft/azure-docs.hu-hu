---
title: Azure Active Directory integrálása az Azure Kubernetes szolgáltatással (örökölt)
description: Ismerje meg, hogyan használhatja az Azure CLI-t az Azure Kubernetes Service (ak) fürt létrehozásához és Azure Active Directoryéhez (örökölt)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 8b76646ef992d220bf44aa4723895f64fab90e9e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105289"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Azure Active Directory integrálása az Azure Kubernetes szolgáltatással az Azure CLI használatával (örökölt)

Az Azure Kubernetes Service (ak) konfigurálható úgy, hogy Azure Active Directory (AD) használatát használja a felhasználói hitelesítéshez. Ebben a konfigurációban egy Azure AD-hitelesítési jogkivonat használatával tud bejelentkezni egy AK-fürtbe. A Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) is konfigurálhat a felhasználó identitása vagy a címtár csoportjának tagsága alapján.

Ez a cikk bemutatja, hogyan hozhatja létre a szükséges Azure AD-összetevőket, hogyan helyezhet üzembe egy Azure AD-kompatibilis fürtöt, és hogyan hozhat létre alapszintű Kubernetes-szerepkört az AK-fürtben.

Az ebben a cikkben használt teljes minta szkripttel kapcsolatban lásd: [Azure CLI-minták – AK-integráció az Azure ad-vel][complete-script].

> [!Important]
> Az AK egy új, továbbfejlesztett [AK által felügyelt Azure ad-][managed-aad] felülettel rendelkezik, amely nem igényli a kiszolgáló vagy az ügyfélalkalmazás felügyeletét. Ha át szeretné telepíteni az áttelepítést, kövesse az [itt][managed-aad-migrate]található utasításokat.

## <a name="the-following-limitations-apply"></a>Az alábbi korlátozások érvényesek:

- Az Azure AD-t csak a Kubernetes RBAC-kompatibilis fürtön lehet engedélyezni.
- Az Azure AD örökölt integrációja csak a fürt létrehozásakor engedélyezhető.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.61 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

[https://shell.azure.com](https://shell.azure.com)Nyissa meg a Cloud shellt a böngészőben.

A konzisztencia és a jelen cikkben szereplő parancsok futtatásának elősegítése érdekében hozzon létre egy változót a kívánt AK-fürt nevéhez. A következő példa a *myakscluster* nevet használja:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

A Kubernetes-fürtön belül a rendszer webhook jogkivonat-hitelesítést használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve. A webhook jogkivonat-hitelesítéssel kapcsolatos további információkért tekintse meg a [webhook hitelesítési dokumentációját][kubernetes-webhook].

> [!NOTE]
> Ha az Azure AD-t AK-hitelesítésre konfigurálja, két Azure AD-alkalmazás van konfigurálva. Ezt a műveletet egy Azure bérlői rendszergazdának kell elvégeznie.

## <a name="create-azure-ad-server-component"></a>Azure AD Server-összetevő létrehozása

Az AK-nal való integráláshoz hozzon létre és használjon egy Azure AD-alkalmazást, amely végpontként működik az azonosító kérésekhez. Az első Azure AD-alkalmazásnak szüksége lesz az Azure AD-csoporttagság beszerzésére egy felhasználó számára.

Hozza létre a kiszolgálóalkalmazás-összetevőt az az [AD App Create][az-ad-app-create] paranccsal, majd frissítse a csoporttagság jogcímeit az az [AD App Update][az-ad-app-update] parancs használatával. A következő [példa az alapismeretek szakaszban](#before-you-begin) definiált *aksname* változót használja, és létrehoz egy változót.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Most hozzon létre egy egyszerű szolgáltatásnevet a Server-alkalmazáshoz az az [ad SP Create][az-ad-sp-create] parancs használatával. Ez az egyszerű szolgáltatásnév az Azure platformon történő hitelesítésre szolgál. Ezután szerezze be a szolgáltatás egyszerű kulcsát az az [ad SP hitelesítőadat-visszaállítási][az-ad-sp-credential-reset] parancs használatával, és rendelje hozzá a *serverApplicationSecret* nevű változóhoz az alábbi lépések egyikében:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Az Azure AD szolgáltatásnak a következő műveletek végrehajtásához szükséges engedélyekkel kell rendelkeznie:

* Címtáradatok olvasása
* Bejelentkezés és felhasználói profil olvasása

Rendelje hozzá ezeket az engedélyeket az az [AD App engedély hozzáadása][az-ad-app-permission-add] paranccsal:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Végül adja meg az előző lépésben hozzárendelt engedélyeket a kiszolgálóalkalmazás számára az az [AD App Permission Grant][az-ad-app-permission-grant] parancs használatával. Ez a lépés meghiúsul, ha az aktuális fiók nem bérlői rendszergazda. Emellett hozzá kell adnia az Azure AD-alkalmazáshoz szükséges engedélyeket azon információk megadásához, amelyek egyéb esetekben rendszergazdai hozzájárulást igényelhetnek az az [AD App Permission admin – hozzájárulás][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD-ügyfél-összetevő létrehozása

A második Azure AD-alkalmazást akkor használja a rendszer, amikor egy felhasználó a Kubernetes CLI () használatával jelentkezik be az AK-fürtbe `kubectl` . Ez az ügyfélalkalmazás elvégzi a hitelesítési kérést a felhasználótól, és ellenőrzi a hitelesítő adatait és engedélyeit. Hozza létre az Azure AD-alkalmazást az ügyfél-összetevőhöz az az [AD App Create][az-ad-app-create] parancs használatával:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Hozzon létre egy egyszerű szolgáltatásnevet az ügyfélalkalmazás számára az az [ad SP Create][az-ad-sp-create] parancs használatával:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Szerezze be a oAuth2 AZONOSÍTÓját, hogy engedélyezze a két alkalmazás-összetevő közötti hitelesítési folyamatot az az [AD App show][az-ad-app-show] parancs használatával. Ezt a oAuth2-azonosítót a következő lépésben használja a rendszer.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adja hozzá az ügyfélalkalmazás és a kiszolgálóalkalmazás-összetevők engedélyeit az oAuth2 kommunikációs folyamat használatához az az [AD App Permission Add][az-ad-app-permission-add] paranccsal. Ezután adja meg az ügyfélalkalmazás számára a kiszolgálói alkalmazással folytatott kommunikációhoz szükséges engedélyeket az az [AD App Permission Grant][az-ad-app-permission-grant] parancs használatával:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>A fürt üzembe helyezése

A létrehozott két Azure AD-alkalmazással létrehozhatja magát az AK-fürtöt. Először hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal. A következő példa létrehozza az erőforráscsoportot a *EastUS* régióban:

Erőforráscsoport létrehozása a fürthöz:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Szerezze be az Azure-előfizetése bérlői AZONOSÍTÓját az az [Account show][az-account-show] parancs használatával. Ezután hozza létre az AK-fürtöt az az [AK Create][az-aks-create] paranccsal. Az AK-fürt létrehozásához szükséges parancs a kiszolgáló-és ügyfélalkalmazás-azonosítókat, a kiszolgálói alkalmazás egyszerű titkát és a bérlő AZONOSÍTÓját tartalmazza:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Végül szerezze be a fürt rendszergazdai hitelesítő adatait az az az az [AK Get-hitelesítőadats][az-aks-get-credentials] paranccsal. Az alábbi lépések egyikével megkapja a normál *felhasználói* fürt hitelesítő adatait az Azure ad-alapú hitelesítési folyamat működés közbeni megtekintéséhez.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Kubernetes RBAC-kötés létrehozása

Mielőtt egy Azure Active Directory fiókot lehessen használni az AK-fürthöz, létre kell hoznia egy szerepkör-kötést vagy egy fürt szerepkör-kötést. A *szerepkörök* határozzák meg a megadható engedélyeket, a *kötések* pedig a kívánt felhasználókra vonatkoznak. Ezek a hozzárendelések egy adott névtérre vagy a teljes fürtre is alkalmazhatók. További információ: [a KUBERNETES RBAC-hitelesítés használata][rbac-authorization].

Szerezze be az aktuálisan bejelentkezett felhasználó egyszerű felhasználónevét az az ad bejelentkezett [felhasználó által megjelenített][az-ad-signed-in-user-show] parancs használatával. Ez a felhasználói fiók engedélyezve van az Azure AD-integrációhoz a következő lépésben.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Ha a felhasználó a Kubernetes RBAC-kötést adja meg ugyanahhoz az Azure AD-bérlőhöz, a *userPrincipalName* alapján rendeljen engedélyeket. Ha a felhasználó egy másik Azure AD-bérlőben található, a *objectId* tulajdonság lekérdezése és használata.

Hozzon létre egy nevű YAML-jegyzékfájlt `basic-azure-ad-binding.yaml` , és illessze be a következő tartalmakat. Az utolsó sorban cserélje le a *userPrincipalName_or_objectId*  elemet az előző parancs UPN-vagy objektumazonosító-kimenetével:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Hozza létre a ClusterRoleBinding a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Fürt elérése az Azure AD-vel

Most teszteljük az Azure AD-hitelesítés az AK-fürthöz való integrálását. A `kubectl` konfigurációs környezet beállítása normál felhasználói hitelesítő adatok használatára. Ez a környezet továbbítja az összes hitelesítési kérést az Azure AD-n keresztül.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Most a [kubectl Get hüvely][kubectl-get] parancs használatával megtekintheti az összes névtéren belüli hüvelyeket:

```console
kubectl get pods --all-namespaces
```

Bejelentkezési kérést kap az Azure AD hitelesítő adataival történő hitelesítéshez egy webböngészővel. A sikeres hitelesítés után a `kubectl` parancs megjeleníti a hüvelyeket az AK-fürtben, ahogy az az alábbi példában látható:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

A rendszer gyorsítótárazza a által fogadott hitelesítési jogkivonatot `kubectl` . A rendszer csak akkor kéri a bejelentkezést, ha a jogkivonat lejárt, vagy a Kubernetes konfigurációs fájl újból létrejön.

Ha a következő példában szereplő kimenetben sikeresen bejelentkezett egy webböngészővel, akkor a következő lehetséges problémákkal kapcsolatos hibaüzenet jelenik meg:

```output
error: You must be logged in to the server (Unauthorized)
```

* A megfelelő objektumazonosítót vagy UPN-t definiálta attól függően, hogy a felhasználói fiók ugyanahhoz az Azure AD-bérlőhöz van-e társítva.
* A felhasználó nem tagja több mint 200 csoportnak.
* Az alkalmazás regisztrálásakor megadott titkos kód megegyezik a használatával konfigurált értékkel `--aad-server-app-secret`

## <a name="next-steps"></a>Következő lépések

Az ebben a cikkben látható parancsokat tartalmazó teljes parancsfájl esetében tekintse meg az [Azure ad-integrációs parancsfájlt az AK-minták][complete-script]tárházában.

Ha az Azure AD-felhasználókat és-csoportokat szeretné használni a fürterőforrások elérésének vezérléséhez, tekintse meg [a fürt erőforrásaihoz való hozzáférés szabályozása a Kubernetes szerepköralapú hozzáférés-vezérlés és az AK-beli Azure ad-identitások használatával című részt][azure-ad-rbac].

További információ a Kubernetes-fürtök védelméről: [a hozzáférési és identitási beállítások az AK-][rbac-authorization]ban.

Az identitás-és erőforrás-vezérléssel kapcsolatos ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
