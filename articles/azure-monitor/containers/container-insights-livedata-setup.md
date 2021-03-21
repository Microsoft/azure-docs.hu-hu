---
title: Tároló-elemzések élő adatértékének beállítása (előzetes verzió) | Microsoft Docs
description: Ez a cikk bemutatja, hogyan állíthatja be a tároló-naplók (StdOut/stderr) valós idejű nézetét és az eseményeket a kubectl és a tároló-elemzések használata nélkül.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: references_regions
ms.openlocfilehash: 4302bdbb3d71c890f7fb0cfb82ab5f8d5aecbd43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713779"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Az élő adatszolgáltatások (előzetes verzió) beállításának beállítása

Ha szeretné megtekinteni az Azure Kubernetes Service (ak)-fürtökből származó élő adatok (előzetes verzió) adatait, konfigurálnia kell a hitelesítést, hogy engedélyt adjon a Kubernetes adataihoz való hozzáférésre. Ez a biztonsági konfiguráció lehetővé teszi a valós idejű hozzáférést az adataihoz a Kubernetes API-n keresztül közvetlenül a Azure Portal.

Ez a szolgáltatás a következő módszereket támogatja a naplók, események és metrikák elérésének vezérléséhez:

- AK Kubernetes RBAC-hitelesítés nélkül engedélyezve
- AK engedélyezve a Kubernetes RBAC-engedélyezéssel
    - A fürt szerepkörének kötési **[CLUSTERMONITORINGUSER](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) konfigurált AK**
- Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel engedélyezett AK

Ezek az utasítások rendszergazdai hozzáférést igényelnek a Kubernetes-fürthöz, és ha úgy konfigurálja, hogy az Azure Active Directory (AD) felhasználó-hitelesítésre, rendszergazdai hozzáférés az Azure AD-hoz.

Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítést az élő adatok (előzetes verzió) szolgáltatáshoz való hozzáférés vezérléséhez a fürtből:

- Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) engedélyezett AK-alapú fürt
- Azure Active Directory integrált AK-fürt.


## <a name="authentication-model"></a>Hitelesítési modell

Az élő adatszolgáltatások (előzetes verzió) funkciói a Kubernetes API-t használják, amely megegyezik a `kubectl` parancssori eszközzel. A Kubernetes API-végpontok olyan önaláírt tanúsítványt használnak, amelyet a böngésző nem tud érvényesíteni. Ez a szolgáltatás egy belső proxy használatával ellenőrzi a tanúsítványt az AK szolgáltatással, így biztosítva a forgalom megbízhatóságát.

A Azure Portal megkéri, hogy ellenőrizze a Azure Active Directory-fürt bejelentkezési hitelesítő adatait, és átirányítsa Önt az ügyfél-regisztráció beállítására a fürt létrehozásakor (és ebben a cikkben újra konfigurálva). Ez a viselkedés hasonló a által igényelt hitelesítési folyamathoz `kubectl` .

>[!NOTE]
>A fürthöz való engedélyezést a Kubernetes és a szolgáltatással konfigurált biztonsági modell kezeli. A szolgáltatáshoz hozzáférő felhasználóknak engedélyt kell kérniük a futtatáshoz hasonló Kubernetes-konfiguráció (*kubeconfig*) letöltésére `az aks get-credentials -n {your cluster name} -g {your resource group}` . Ez a konfigurációs fájl az **Azure Kubernetes Service cluster felhasználói szerepkör** engedélyezési és hitelesítési jogkivonatát tartalmazza, az Azure RBAC-kompatibilis és az AK-alapú fürtök esetében, ha nincs engedélyezve a Kubernetes RBAC engedélyezése. Az Azure AD-vel és az ügyfél-regisztrációs adatokkal kapcsolatos információkat tartalmaz, ha az AK Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel van engedélyezve.

>[!IMPORTANT]
>A funkciók felhasználói az [Azure Kubernetes-fürt felhasználói szerepkörét](../../role-based-access-control/built-in-roles.md) igénylik a fürthöz a funkció letöltéséhez `kubeconfig` és használatához. A szolgáltatás használatához a felhasználóknak **nincs** szükségük közreműködői hozzáférésre a fürthöz.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>A clusterMonitoringUser használata a Kubernetes RBAC-kompatibilis fürtökkel

Annak érdekében, hogy ne kelljen további konfigurációs módosításokat alkalmaznia ahhoz, hogy a Kubernetes felhasználói szerepköre **clusterUser** az élő adat (előzetes verzió) szolgáltatáshoz a [Kubernetes RBAC](#configure-kubernetes-rbac-authorization) engedélyezésének engedélyezése után, az AK felvette a **Kubernetes** nevű új clusterMonitoringUser. Ez a fürtcsomópont-kötés minden szükséges engedéllyel rendelkezik a Kubernetes API és a végpontok eléréséhez az élő adat (előzetes verzió) funkció kihasználása érdekében.

Ahhoz, hogy az élő adat (előzetes verzió) funkció ezzel az új felhasználóval használható legyen, az [Azure Kubernetes Service cluster felhasználói](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) vagy [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkör tagjának kell lennie az AK-fürt erőforrásán. Ha engedélyezve van a tárolók elemzése, úgy van beállítva, hogy alapértelmezés szerint a clusterMonitoringUser használja a hitelesítést. Ha a clusterMonitoringUser szerepkör-kötés nem létezik a fürtön, a rendszer a **clusterUser** használja a hitelesítéshez. A közreműködő hozzáférést biztosít a clusterMonitoringUser (ha létezik), és az Azure Kuberenetes Service-fürt felhasználója hozzáférést biztosít a clusterUser. Ezen két szerepkör bármelyike elegendő hozzáférést biztosít a szolgáltatás használatához.

Az AK január 2020-én kiadta ezt az új szerepkör-kötést, így a január 2020 előtt létrehozott fürtök nem rendelkeznek. Ha olyan fürtöt hoz létre, amely a január 2020 előtt lett létrehozva, akkor az új **clusterMonitoringUser** hozzáadhatja egy meglévő fürthöz, ha VÉGREHAJT egy Put műveletet a fürtön, vagy bármilyen más műveletet hajt végre a fürtön, amely egy Put műveletet végez a fürtön, például frissíti a fürt verzióját.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Kubernetes-fürt Kubernetes-RBAC nélkül engedélyezve

Ha olyan Kubernetes-fürttel rendelkezik, amely nincs Kubernetes RBAC-hitelesítéssel konfigurálva, vagy az Azure AD egyszeri bejelentkezéssel van integrálva, nem kell végrehajtania ezeket a lépéseket. Ennek az az oka, hogy a rendszergazdai engedélyek alapértelmezés szerint nem RBAC konfigurációban vannak.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC-hitelesítés konfigurálása

Ha engedélyezi a Kubernetes RBAC-engedélyezést, a rendszer két felhasználót használ: a **clusterUser** és a **CLUSTERADMIN** a Kubernetes API eléréséhez. Ez `az aks get-credentials -n {cluster_name} -g {rg_name}` a felügyeleti lehetőség nélküli futtatáshoz hasonló. Ez azt jelenti, hogy a **clusterUser** hozzáférést kell biztosítani a Kubernetes API végponti pontjaihoz.

Az alábbi példák bemutatják, hogyan konfigurálhatja a fürt szerepkörének kötését ebből a YAML-konfigurációs sablonból.

1. Másolja és illessze be a YAML fájlt, és mentse LogReaderRBAC. YAML néven.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
       name: containerHealth-log-reader
    rules:
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"]
          resources:
             - "pods/log"
             - "events"
             - "nodes"
             - "pods"
             - "deployments"
             - "replicasets"
          verbs: ["get", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
       name: containerHealth-read-logs-global
    roleRef:
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io
    subjects:
    - kind: User
      name: clusterUser
      apiGroup: rbac.authorization.k8s.io
    ```

2. A konfiguráció frissítéséhez futtassa a következő parancsot: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Ha a fájl egy korábbi verzióját alkalmazza `LogReaderRBAC.yaml` a fürtre, frissítse azt a fenti 1. lépésben bemutatott új kód másolásával és beillesztésével, majd futtassa a 2. lépésben látható parancsot a fürtön való alkalmazásához.

## <a name="configure-ad-integrated-authentication"></a>AD-integrált hitelesítés konfigurálása

A felhasználói hitelesítéshez Azure Active Directory (AD) használatára konfigurált AK-fürt a szolgáltatáshoz hozzáférő személy bejelentkezési hitelesítő adatait használja. Ebben a konfigurációban az Azure AD-hitelesítési token használatával tud bejelentkezni egy AK-fürtbe.

Az Azure AD-ügyfél regisztrációját újra be kell állítani, hogy a Azure Portal átirányítsa az engedélyezési lapokat megbízható átirányítási URL-címként. Az Azure AD-felhasználók közvetlenül a **ClusterRoles** és a **ClusterRoleBindings**-en keresztül kapnak hozzáférést ugyanahhoz a Kubernetes API-végponthoz.

A Kubernetes speciális biztonsági beállításaival kapcsolatos további információkért tekintse át a [Kubernetes dokumentációját](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Ha új Kubernetes RBAC-kompatibilis fürtöt hoz létre, tekintse meg a [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../../aks/azure-ad-integration-cli.md) című témakört, és kövesse az Azure ad-hitelesítés konfigurálásának lépéseit. Az ügyfélalkalmazás létrehozásának lépései során az ebben a szakaszban található Megjegyzés kiemeli a két átirányítási URL-címet, amelyeket az alábbi 3. lépésben megadott tároló-felismerésekhez kell létrehoznia.

### <a name="client-registration-reconfiguration"></a>Ügyfél-regisztráció újrakonfigurálása

1. Keresse meg a Kubernetes-fürthöz tartozó ügyfél-regisztrációt az Azure AD-ben, **Azure Active Directory > Alkalmazásregisztrációk** a Azure Portalban.

2. A bal oldali ablaktáblán válassza a **hitelesítés** lehetőséget.

3. Adjon hozzá két átirányítási URL-címet **a listához webalkalmazás-** típusokként. Az első alap URL-címnek a értéknek kell lennie `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` , a második URL-címnek pedig az értéknek kell lennie `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Ha ezt a funkciót az Azure China-ban használja, az első alap URL-értéknek a következőnek kell lennie, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` és a második alap URL-címnek kell lennie `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Az átirányítási URL-címek regisztrálását követően az **implicit engedélyezés** területen válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** lehetőséget, majd mentse a módosításokat.

>[!NOTE]
>Az egyszeri bejelentkezés Azure Active Directory használatával történő hitelesítés konfigurálása csak az új AK-fürtök kezdeti telepítése során hajtható végre. Az egyszeri bejelentkezés nem konfigurálható egy már üzembe helyezett AK-fürthöz.

>[!IMPORTANT]
>Ha a frissített URI használatával újrakonfigurálta az Azure AD-t a felhasználói hitelesítéshez, törölje a böngésző gyorsítótárát, és győződjön meg arról, hogy a frissített hitelesítési jogkivonat letöltése és alkalmazása megtörtént.

## <a name="grant-permission"></a>Engedély megadása

Minden Azure AD-fióknak engedéllyel kell rendelkeznie a megfelelő API-khoz a Kubernetes-ben az élő adat(előzetes verzió) funkció eléréséhez. A Azure Active Directory fiók megadásának lépései hasonlóak a [KUBERNETES RBAC hitelesítés](#configure-kubernetes-rbac-authorization) szakaszban leírt lépésekhez. Mielőtt alkalmazza a YAML-konfigurációs sablont a fürtre, cserélje le a **clusterUser** a **ClusterRoleBinding** elemre a kívánt felhasználóval.

>[!IMPORTANT]
>Ha a felhasználó a Kubernetes RBAC-kötést adja meg ugyanahhoz az Azure AD-bérlőhöz, a userPrincipalName alapján rendeljen engedélyeket. Ha a felhasználó egy másik Azure AD-bérlőben található, a objectId tulajdonság lekérdezése és használata.

Az AK-fürt **ClusterRoleBinding** konfigurálásával kapcsolatos további segítségért lásd: [Kubernetes RBAC-kötés létrehozása](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a hitelesítést, a fürtből valós időben megtekintheti a [metrikákat](container-insights-livedata-metrics.md), a [központi telepítéseket](container-insights-livedata-deployments.md), valamint az [eseményeket és a naplókat](container-insights-livedata-overview.md) .
