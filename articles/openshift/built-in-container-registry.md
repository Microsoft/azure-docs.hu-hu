---
title: Az Azure Red Hat OpenShift 4 beépített tárolóregisztrációs adatbázisának konfigurálása
description: Az Azure Red Hat OpenShift 4 beépített tárolóregisztrációs adatbázisának konfigurálása
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636379"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Az Azure Red Hat OpenShift 4 beépített tárolóregisztrációs adatbázisának konfigurálása

Az Azure Red Hat OpenShift egy [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) nevű integrált tároló-rendszerképet biztosít, amely lehetővé teszi, hogy igény szerint automatikusan kiépítse az új rendszerképek tárházát. Ez lehetővé teszi a felhasználók számára, hogy az alkalmazás-buildek számára beépített helyet adjanak ki az eredményül kapott lemezképek leküldéséhez.

Ebben a cikkben a beépített tárolók rendszerképének beállításjegyzékét fogja konfigurálni egy Azure Red Hat OpenShift (ARO) 4 fürthöz. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az Azure AD beállítása
> * OpenID Connect beállítása
> * A beépített tároló rendszerkép-beállításjegyzékének elérése

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő ARO-fürttel. Ha ARO-fürtre van szüksége, tekintse meg az ARO oktatóanyagot, [hozzon létre egy Azure Red Hat OpenShift 4-fürtöt](./tutorial-create-cluster.md). Győződjön meg arról, hogy a következő argumentummal hozza létre a fürtöt: `--pull-secret` `az aro create` .  Ez a Azure Active Directory hitelesítés és a beépített tároló-beállításjegyzék konfigurálásához szükséges.

Miután elvégezte a fürtöt, kapcsolódjon a fürthöz a [Kapcsolódás Azure Red Hat OpenShift 4-fürthöz](./tutorial-connect-cluster.md)című témakör lépéseit követve.
   * Ügyeljen arra, hogy kövesse a "OpenShift CLI telepítése" című szakasz lépéseit, mert a `oc` parancsot a cikk későbbi részében fogjuk használni.
   * Jegyezze fel a fürt konzoljának URL-címét, amely a következőképpen néz ki: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . A és a értékeit a `<random>` `<region>` cikk későbbi részében fogja használni.
   * Jegyezze fel a `kubeadmin` hitelesítő adatokat. Ezeket a cikk későbbi részében is használni fogjuk.

### <a name="configure-azure-active-directory-authentication"></a>Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása 

Azure Active Directory (Azure AD) az OpenID Connect (OIDC) megvalósítása. A OIDC lehetővé teszi, hogy az Azure AD használatával jelentkezzen be az ARO-fürtbe. A fürt beállításához kövesse a [Azure Active Directory hitelesítés konfigurálása](configure-azure-ad-cli.md) című témakör lépéseit.

## <a name="access-the-built-in-container-image-registry"></a>A beépített tároló rendszerkép-beállításjegyzékének elérése

Most, hogy beállította a hitelesítési módszereket az ARO-fürthöz, engedélyezze a hozzáférést a beépített beállításjegyzékhez.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Az Azure AD-felhasználó megadása rendszergazdaként

1. Jelentkezzen be a böngésző OpenShift webkonzolján egy Azure AD-felhasználó hitelesítő adataival. A OpenShift OpenID-hitelesítés kihasználása Azure Active Directory az OpenID használatával a rendszergazda definiálásához.

   1. A konzolba való bejelentkezéshez használjon InPrivate, inkognitóban vagy más, a böngészőablakhoz hasonló szolgáltatást. Az ablak megjelenése a OIDC engedélyezése után eltérő lesz.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect engedélyezve bejelentkezési ablak.":::
   1. **OpenID** kiválasztása

   > [!NOTE]
   > Jegyezze fel a bejelentkezéshez használt felhasználónevet és jelszót. Ez a Felhasználónév és jelszó rendszergazdaként fog működni a többi művelethez és más cikkekhez.
2. Jelentkezzen be az OpenShift CLI-vel a következő lépésekkel.  A vita esetében ez a folyamat ismert `oc login` .
   1. A webkonzol jobb felső részén bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a **bejelentkezési parancs másolása** lehetőséget.
   2. Ha szükséges, jelentkezzen be egy új Tab-ablakba.
   3. Válassza ki a **jogkivonat megjelenítése** elemet.
   4. Másolja az alábbi, a **tokent tartalmazó bejelentkezési** azonosítót a vágólapra, és futtassa azt egy rendszerhéjban az itt látható módon.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Futtassa a parancsot `oc whoami` a konzolon, és jegyezze fel a kimenetet **\<aad-user>** .  Ezt az értéket a cikk későbbi részében fogjuk használni.
4. Jelentkezzen ki a OpenShift webkonzolról. Válassza ki a böngészőablak jobb felső sarkában látható gombot, és válassza ki a **\<aad-user>** **kijelentkezés** lehetőséget.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Adja meg az Azure AD-felhasználó számára a beállításjegyzék-interakcióhoz szükséges szerepköröket

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében a `kubeadmin` hitelesítő adatok használatával.
1. Jelentkezzen be a OpenShift CLI-be a token `kubeadmin` használatával a fenti lépéseket követve `oc login` , de a webkonzolra való bejelentkezés után végezze el azokat `kubeadmin` .
1. Futtassa a következő parancsokat, hogy engedélyezze a hozzáférést a **HRE-felhasználó** beépített beállításjegyzékéhez.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>A tároló-beállításjegyzék URL-címének beszerzése

A `oc get route` tároló-beállításjegyzék URL-címének lekéréséhez használja a következő parancsot:.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Jegyezze fel **Container Registry URL-cím** konzoljának kimenetét. A rendszer ezt az útmutatót és az azt követőket is a teljes beállításjegyzék neveként fogja használni.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a beépített tároló lemezképének beállításjegyzékét, első lépésként telepítsen egy alkalmazást a OpenShift-on. A Java-alkalmazások esetében tekintse meg a [Java-alkalmazások üzembe helyezését a nyílt Liberty/WebSphere Liberty-szel egy Azure Red Hat OpenShift 4-fürtön](howto-deploy-java-liberty-app.md).