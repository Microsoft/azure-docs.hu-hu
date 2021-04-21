---
title: A Azure Active Directory integrálása Azure Kubernetes Service (örökölt)
description: Megtudhatja, hogyan hozhat létre és hozhat létre Azure Active Directory azure Azure Kubernetes Service (örökölt) fürtöt az Azure CLI használatával
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: cb92f84560a88d406f0d519459c27b5d916ec5ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769570"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integráció Azure Active Directory az Azure Kubernetes Service Azure CLI használatával (örökölt)

Azure Kubernetes Service (AKS) konfigurálható úgy, hogy az Azure Active Directory (AD) használja a felhasználói hitelesítéshez. Ebben a konfigurációban bejelentkezhet egy AKS-fürtbe egy Azure AD-hitelesítési jogkivonattal. A fürtüzemeltetők a Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC) is konfigurálhatja a felhasználó identitása vagy címtárcsoport-tagsága alapján.

Ez a cikk bemutatja, hogyan hozhatja létre a szükséges Azure AD-összetevőket, hogyan helyezhet üzembe egy Azure AD-kompatibilis fürtöt, és hogyan hozhat létre alapszintű Kubernetes-szerepkört az AKS-fürtben.

A cikkben használt teljes mintaszk szkriptért lásd: [Azure CLI-minták – AKS-integráció az Azure AD-val.][complete-script]

> [!Important]
> Az AKS új, [AKS által felügyelt Azure AD-élményt][managed-aad] kínál, amely nem igényel kiszolgáló- vagy ügyfélalkalmazás-kezelést. Az áttelepítéshez kövesse az itt található [utasításokat.][managed-aad-migrate]

## <a name="the-following-limitations-apply"></a>Az alábbi korlátozások érvényesek:

- Az Azure AD csak a Kubernetes RBAC-kompatibilis fürtön engedélyezhető.
- Az Örökölt Azure AD-integráció csak a fürt létrehozása során engedélyezhető.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61-es vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Nyissa meg [https://shell.azure.com](https://shell.azure.com) a Cloud Shell a böngészőben.

A konzisztencia érdekében és a cikkben olvasható parancsok futtatásához hozzon létre egy változót a kívánt AKS-fürtnévhez. Az alábbi példa a *myakscluster nevet használja:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

Az Azure AD-hitelesítést az AKS-fürtök biztosítják OpenID Connect. OpenID Connect az OAuth 2.0 protokollra épülő identitásréteg. További információt a OpenID Connect Open [ID Connect dokumentációjában talál.][open-id-connect]

A Kubernetes-fürtön belül a webhook-jogkivonatok hitelesítése a hitelesítési jogkivonatok ellenőrzésére használható. A webhook-jogkivonatok hitelesítése az AKS-fürt részeként van konfigurálva és kezelhető. A webhook-jogkivonatok hitelesítésével kapcsolatos további információkért tekintse meg [a webhook hitelesítési dokumentációját.][kubernetes-webhook]

> [!NOTE]
> Az Azure AD AKS-hitelesítésre való konfigurálásakor két Azure AD-alkalmazás van konfigurálva. Ezt a műveletet egy Azure-bérlői rendszergazdának kell végrehajtania.

## <a name="create-azure-ad-server-component"></a>Azure AD-kiszolgáló-összetevő létrehozása

Az AKS-sel való integrációhoz létre kell hoznia és használnia kell egy Azure AD-alkalmazást, amely végpontként szolgál az identitáskérések számára. Az első szükséges Azure AD-alkalmazás Azure AD-csoporttagságot kap egy felhasználóhoz.

Hozza létre a kiszolgálóalkalmazás-összetevőt az [az ad app create][az-ad-app-create] paranccsal, majd frissítse a csoporttagsági jogcímeket az az [ad app update paranccsal.][az-ad-app-update] Az alábbi példa az Első lépések szakaszban [](#before-you-begin) meghatározott *aksname* változót használja, és létrehoz egy változót

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Most hozzon létre egy egyszerű szolgáltatást a kiszolgálóalkalmazáshoz az [az ad sp create paranccsal.][az-ad-sp-create] Ezzel a szolgáltatásnévvel hitelesítheti magát az Azure platformon. Ezután az [az ad sp credential reset][az-ad-sp-credential-reset] paranccsal szerezze be a szolgáltatásnév titkos adatait, és rendelje hozzá a *serverApplicationSecret* nevű változóhoz a következő lépések egyikéhez:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Az Azure AD-szolgáltatásnévnek engedélyekre van szüksége a következő műveletek végrehajtásához:

* Címtáradatok olvasása
* Bejelentkezés és felhasználói profil olvasása

Rendelje hozzá ezeket az engedélyeket [az az ad app permission add paranccsal:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Végül adja meg az előző lépésben hozzárendelt engedélyeket a kiszolgálóalkalmazás számára az [az ad app permission grant paranccsal.][az-ad-app-permission-grant] Ez a lépés sikertelen lesz, ha az aktuális fiók nem bérlői rendszergazda. Emellett engedélyeket kell hozzáadnia az Azure AD-alkalmazáshoz az olyan információk lekéréséhez, amelyekhez egyébként rendszergazdai jóváhagyásra lenne szükség az [az ad app permission admin-consent használatával:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD-ügyfélösszetevő létrehozása

A második Azure AD-alkalmazást akkor használja a rendszer, amikor egy felhasználó a Kubernetes CLI () használatával jelentkezik be az AKS-fürtbe. `kubectl` Ez az ügyfélalkalmazás a felhasználótól származó hitelesítési kérést veszi át, és ellenőrzi a hitelesítő adatait és engedélyeiket. Hozza létre az Azure AD-alkalmazást az ügyfélösszetevő számára [az az ad app create paranccsal:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Hozzon létre egy egyszerű szolgáltatást az ügyfélalkalmazáshoz [az az ad sp create paranccsal:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Az [az ad app show][az-ad-app-show] paranccsal szerezze be a kiszolgálóalkalmazás oAuth2-azonosítóját, hogy engedélyezze a két alkalmazás-összetevő közötti hitelesítési folyamatot. Ezt az oAuth2-azonosítót a következő lépésben használjuk.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adja hozzá az oAuth2 kommunikációs folyamat használatára vonatkozó engedélyeket az ügyfélalkalmazás és a kiszolgálóalkalmazás összetevői számára az [az ad app permission add paranccsal.][az-ad-app-permission-add] Ezután adjon engedélyeket az ügyfélalkalmazásnak a kiszolgálóalkalmazással való kommunikációhoz [az az ad app permission grant paranccsal:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>A fürt üzembe helyezése

A két Azure AD-alkalmazás létrehozása után hozza létre magát az AKS-fürtöt. Először hozzon létre egy erőforráscsoportot [az az group create paranccsal.][az-group-create] Az alábbi példa az *EastUS* régióban hozza létre az erőforráscsoportot:

Hozzon létre egy erőforráscsoportot a fürthöz:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Az Az account show paranccsal szerezze be az Azure-előfizetése [bérlőazonosítóját.][az-account-show] Ezután hozza létre az AKS-fürtöt [az az aks create paranccsal.][az-aks-create] Az AKS-fürt létrehozására vonatkozó parancs biztosítja a kiszolgáló és az ügyfélalkalmazás azonosítóját, a kiszolgálóalkalmazás egyszerű szolgáltatásának titkos nevét és a bérlőazonosítót:

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

Végül az [az aks get-credentials][az-aks-get-credentials] paranccsal szerezze be a fürt rendszergazdai hitelesítő adatait. Az alábbi lépések egyikében lekért hitelesítő adatokkal láthatja az Azure AD-hitelesítési folyamatot. 

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Kubernetes RBAC-kötés létrehozása

Ahhoz, Azure Active Directory fiók használható legyen az AKS-fürttel, létre kell hozva egy szerepkörkötést vagy fürtszerepk szerepkörkötést. *A* szerepkörök határozzák meg az engedélyek megadását, és *a kötések* a kívánt felhasználókra alkalmazzák őket. Ezek a hozzárendelések alkalmazhatók egy adott névtérre vagy a teljes fürtre. További információ: [A Kubernetes RBAC-hitelesítés használata.][rbac-authorization]

Az [az ad signed-in-user show][az-ad-signed-in-user-show] paranccsal szerezze be az aktuálisan bejelentkezett felhasználó egyszerű felhasználónevét (UPN). Ez a felhasználói fiók a következő lépésben lesz engedélyezve az Azure AD-integrációhoz.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Ha a kubernetes RBAC-kötést megadó felhasználó ugyanabban az Azure AD-bérlőben található, rendeljen engedélyeket a *userPrincipalName alapján.* Ha a felhasználó egy másik Azure AD-bérlőben van, akkor ehelyett az *objectId* tulajdonságot kell lekérdezni és használni.

Hozzon létre egy nevű YAML-jegyzékfájlt, `basic-azure-ad-binding.yaml` és illessze be az alábbi tartalmat. Az utolsó sorban cserélje le a *userPrincipalName_or_objectId*  az előző parancs UPN- vagy objektumazonosító-kimenetére:

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

Hozza létre a ClusterRoleBinding parancsot a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Fürt elérése az Azure AD-val

Most teszteljük az Azure AD-hitelesítés integrációját az AKS-fürthöz. Állítsa be a konfigurációs környezetet normál `kubectl` felhasználói hitelesítő adatok használatára. Ez a környezet az összes hitelesítési kérést az Azure AD-nek továbbítja.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Most használja a [kubectl get pods parancsot][kubectl-get] a podok megtekintéséhez az összes névtérben:

```console
kubectl get pods --all-namespaces
```

Egy bejelentkezési kérést kap az Azure AD hitelesítő adataival való hitelesítéshez egy webböngészőben. A sikeres hitelesítést követően a parancs megjeleníti az `kubectl` AKS-fürt podját az alábbi kimenetben látható módon:

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

A rendszer gyorsítótárazza a számára `kubectl` kapott hitelesítési jogkivonatot. Csak akkor kell újra bejelentkeznie, ha a jogkivonat lejárt, vagy a Kubernetes konfigurációs fájlját újra létrehozták.

Ha engedélyezési hibaüzenet jelenik meg, miután sikeresen bejelentkezett egy webböngészővel, ahogyan az alábbi példakimenetben is látható, ellenőrizze a következő lehetséges problémákat:

```output
error: You must be logged in to the server (Unauthorized)
```

* Meghatározta a megfelelő objektumazonosítót vagy UPN-t, attól függően, hogy a felhasználói fiók ugyanabban az Azure AD-bérlőben van-e.
* A felhasználó nem tagja több mint 200 csoportnak.
* A kiszolgáló alkalmazásregisztrációja során meghatározott titkos érték megegyezik a használatával konfigurált értékkel `--aad-server-app-secret`

## <a name="next-steps"></a>Következő lépések

A cikkben bemutatott parancsokat tartalmazó teljes szkriptet az AKS-mintatára Azure [AD-integrációs szkriptben talál.][complete-script]

Az Azure AD-felhasználók és -csoportok fürterőforrásokhoz való hozzáférésének szabályozásával kapcsolatban lásd: Fürterőforrások hozzáférésének szabályozása Kubernetes szerepköralapú hozzáférés-vezérléssel és [Azure AD-identitásokkal az AKS-ban.][azure-ad-rbac]

A Kubernetes-fürtök biztonságának beállításával kapcsolatos további információkért lásd: Hozzáférési és identitásbeállítások [az AKS-hez).][rbac-authorization].

Az identitással és az erőforrás-vezérléssel kapcsolatos ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való hitelesítéshez [és engedélyezéshez.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
