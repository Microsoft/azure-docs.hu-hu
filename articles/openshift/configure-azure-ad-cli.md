---
title: OpenShift futó Azure Red Hat-OpenShift 4 – Azure Active Directory hitelesítés konfigurálása a parancssor használatával
description: Ismerje meg, hogyan konfigurálható Azure Active Directory hitelesítés a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtökhöz a parancssor használatával
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 06f7bfea9a88627733eb9ce9166e05d05790e23a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213062"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Active Directory hitelesítés konfigurálása Azure Red Hat OpenShift 4 fürthöz (CLI)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.6.0 vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A Azure Active Directory alkalmazás konfigurálásához használni kívánt fürtözött URL-címek beolvasása.

Adja meg az erőforráscsoport és a fürt nevének változóit.

Cserélje le az **\<resource_group>** csoportot az erőforráscsoport nevére, és a **\<aro_cluster>** fürt nevére.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Hozza létre a fürt OAuth visszahívási URL-címét, és tárolja egy változó **oauthCallbackURL**. 

> [!NOTE]
> A `AAD` OAuth visszahívási URL-címében szereplő szakasznak meg kell egyeznie a OAuth-identitás szolgáltatójának nevével.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

A oauthCallbackURL formátuma némileg eltér az egyéni tartományokkal:

* Ha egyéni tartományt használ, futtassa a következő parancsot, például `contoso.com` :. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Ha nem egyéni tartományt használ, akkor a `$domain` lesz egy nyolc karakteres alnum karakterlánc, amelyet a kiterjeszt `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> A `AAD` OAuth visszahívási URL-címében szereplő szakasznak meg kell egyeznie a OAuth-identitás szolgáltatójának nevével.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Azure Active Directory alkalmazás létrehozása hitelesítéshez

Cserélje le az **\<client_secret>** alkalmazást egy biztonságos jelszóra az alkalmazáshoz.

```azurecli-interactive
client_secret=<client_secret>
```

Hozzon létre egy Azure Active Directory alkalmazást, és kérje le a létrehozott alkalmazás azonosítóját.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Kérje le az alkalmazást birtokló előfizetés bérlői AZONOSÍTÓját.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Jegyzékfájl létrehozása az azonosító jogkivonatban szerepeltetni kívánt választható jogcímek meghatározásához

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban [választható jogcímeket](../active-directory/develop/active-directory-optional-claims.md) annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak.

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A OpenShift a jogcím használatára konfigurálja, `email` és visszatérhet a következőre `upn` : az előnyben részesített Felhasználónév beállításához adja hozzá a `upn` Azure Active Directory által visszaadott azonosító jogkivonat részét.

Hozzon létre egy **manifest.js** fájlt a Azure Active Directory alkalmazás konfigurálásához.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>A Azure Active Directory alkalmazás optionalClaims frissítése jegyzékkel

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>A Azure Active Directory alkalmazás-hatókör engedélyeinek frissítése

Ahhoz, hogy el tudja olvasni a felhasználói adatokat Azure Active Directoryból, meg kell határoznia a megfelelő hatóköröket.

Adjon hozzá engedélyt a **Azure Active Directory Graph. user. Read** hatókörhöz a bejelentkezés engedélyezéséhez és a felhasználói profil olvasásához.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Az üzenetet nyugodtan figyelmen kívül hagyhatja a jóváhagyás megadásához, ha a Azure Active Directory globális rendszergazdájaként hitelesíti. A standard szintű tartományi felhasználókat a rendszer arra kéri, hogy adja meg a jóváhagyást, amikor először bejelentkeznek a fürtbe a HRE hitelesítő adataik használatával.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Felhasználók és csoportok társítása a fürthöz (nem kötelező)

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők. Az Azure AD lehetővé teszi, hogy a bérlői rendszergazdák és fejlesztők egy alkalmazást a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára korlátozzák.

A [felhasználók és csoportok alkalmazáshoz való hozzárendeléséhez](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)kövesse az Azure Active Directory dokumentációjának utasításait.

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID-hitelesítés konfigurálása

A `kubeadmin` hitelesítő adatok beolvasása. Futtassa a következő parancsot a felhasználó jelszavának megkereséséhez `kubeadmin` .

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Jelentkezzen be a OpenShift-fürt API-kiszolgálójára a következő parancs használatával. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Hozzon létre egy titkos OpenShift a Azure Active Directory alkalmazás titkos kódjának tárolásához.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Hozzon létre egy **oidc. YAML** fájlt a OpenShift OpenID-hitelesítés konfigurálásához Azure Active Directoryon. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Alkalmazza a konfigurációt a fürtre.

```azurecli-interactive
oc apply -f oidc.yaml
```

Az alábbihoz hasonló választ fog kapni.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Bejelentkezés ellenőrzése Azure Active Directory

Ha most kijelentkezik a OpenShift webkonzolon, és megpróbál újra bejelentkezni, megjelenik egy új lehetőség a **HRE** való bejelentkezéshez. Előfordulhat, hogy néhány percet várnia kell.

![Bejelentkezési képernyő Azure Active Directory lehetőséggel](media/aro4-login-2.png)
