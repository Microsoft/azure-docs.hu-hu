---
title: Emelt szintű tárolók futtatása Azure Red Hat OpenShift-fürtben | Microsoft Docs
description: A biztonság és a megfelelőség figyeléséhez futtassa a privilegizált tárolókat.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: ARO, openshift, aquasec, twistlock, Red Hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633562"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Kiemelt tárolók futtatása Azure Red Hat OpenShift-fürtön

> [!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 2022. június 30-án megszűnik. Az új Azure Red Hat OpenShift 3,11-fürtök létrehozásának támogatása az 2020. november 30-ig folytatódik. A biztonsági rések elkerülése érdekében a rendszer leállítja a fennmaradó Azure Red Hat OpenShift 3,11-fürtöket a kivonulást követően.
> 
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:arofeedback@microsoft.com).

Nem futtathat tetszőleges jogosultságú tárolót az Azure Red Hat OpenShift-fürtökön.
Az ARO-fürtökön két biztonsági figyelési és megfelelőségi megoldás futhat.
Ez a dokumentum ismerteti a biztonsági termékek gyártóinak általános OpenShift üzembe helyezési dokumentációjának különbségeit.


A gyártó utasításainak követése előtt olvassa el ezeket az utasításokat.
Az alábbi, termékspecifikus lépések című szakasza a szállítók dokumentációjának szakaszait mutatja be közvetlenül.

## <a name="before-you-begin"></a>Előkészületek

A legtöbb biztonsági termék dokumentációja feltételezi, hogy rendelkezik a fürt rendszergazdai jogosultságokkal.
Az ügyfelek rendszergazdái nem rendelkeznek minden jogosultsággal az Azure Red Hat OpenShift. A fürtre kiterjedő erőforrások módosításához szükséges engedélyek korlátozottak.

Először is győződjön meg arról, hogy a felhasználó az ügyfél-rendszergazdaként van bejelentkezve a fürtbe. Ehhez futtassa a következőt: `oc get scc` . Az ügyfél-felügyeleti csoport tagjainak minden felhasználója rendelkezik engedéllyel a fürtön található biztonsági környezeti korlátozások (SCCs) megtekintéséhez.

Ezután győződjön meg arról, hogy a bináris verziója a következő: `oc` `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Az Aqua Security termékspecifikus lépései
A módosítandó alapszintű utasítások az [Aqua Security Deployment dokumentációjában](https://docs.aquasec.com/docs/openshift-red-hat)találhatók. Az itt ismertetett lépések az Aqua telepítési dokumentációjában is futnak.

Az első lépés a frissítendő szükséges SCCs megjegyzése. Ezek a jegyzetek megakadályozzák, hogy a fürt szinkronizálási Pod ne térjen át a SSCs változásaira.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>1. lépés: előfeltételek előkészítése
Ne felejtse el, hogy a fürt – rendszergazda szerepkör helyett ARO ügyfél-rendszergazdaként jelentkezzen be a fürtbe.

Hozza létre a projektet és a szolgáltatásfiókot.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

A fürt-olvasó szerepkör kiosztása helyett rendelje hozzá az ügyfél-rendszergazda-fürt szerepkört az Aqua-fiókhoz a következő paranccsal.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Folytassa az 1. lépésben megjelenő további utasítások követésével.  Ezek az utasítások ismertetik a titkos kód beállítását az Aqua beállításjegyzékben.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>2. lépés: az Aqua-kiszolgáló, az adatbázis és az átjáró üzembe helyezése
Az Aqua-Console. YAML telepítéséhez kövesse az Aqua dokumentációjában ismertetett lépéseket.

Módosítsa a megadottat `aqua-console.yaml` .  Távolítsa el az első két, címkézett objektumot, `kind: ClusterRole` és `kind: ClusterRoleBinding` .  Ezek az erőforrások nem jönnek létre, mert az ügyfél rendszergazdája jelenleg nem rendelkezik engedéllyel a módosításhoz `ClusterRole` és az `ClusterRoleBinding` objektumokhoz.

A második módosítás a `kind: Route` része lesz `aqua-console.yaml` . Cserélje le a következő YAML a `kind: Route` fájlban lévő objektumra `aqua-console.yaml` .
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Kövesse a további utasításokat.

### <a name="step-3-login-to-the-aqua-server"></a>3. lépés: bejelentkezés az Aqua-kiszolgálóra
Ez a szakasz semmilyen módon nincs módosítva.  Kövesse az Aqua dokumentációját.

Használja az alábbi parancsot az Aqua Console-címek lekéréséhez.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>4. lépés: Aqua-végrehajtók üzembe helyezése
Állítsa be a következő mezőket a végrehajtók telepítésekor:

| Mező          | Érték         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | Aqua – fiók  |
| Project        | Aqua – biztonság |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Termékspecifikus lépések a Prisma-felhőhöz/Twistlock

A módosítandó alapszintű utasítások a [Prisma Cloud Deployment dokumentációjában](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html) találhatók.

Először telepítse az `twistcli` eszközt a "Prisma-felhő telepítése" és a "Prisma Cloud Software letöltése" című szakaszban leírtak szerint.

Új OpenShift-projekt létrehozása
```
oc new-project twistlock
```

Hagyja ki a "Prisma Cloud images to an Private Registry" (nem kötelező) szakaszát. Az Azure Red Hat OpenShift nem fog működni. Használja helyette az online beállításjegyzéket.

Az alábbiakban ismertetett javítások alkalmazásával követheti a hivatalos dokumentációt.
Kezdje a "konzol telepítése" szakasszal.

### <a name="install-console"></a>Konzol telepítése

`oc create -f twistlock_console.yaml`A 2. lépésben a névtér létrehozásakor hibaüzenet jelenik meg.
Nyugodtan figyelmen kívül hagyhatja a névteret, mert korábban a paranccsal lett létrehozva `oc new-project` .

`azure-disk`A tárolási típushoz használható.

### <a name="create-an-external-route-to-console"></a>Külső útvonal létrehozása a konzolon

Követheti a dokumentációt, vagy az alábbi utasításokat, ha az oC-parancsot szeretné előnyben részesíteni.
Másolja a következő útvonal-definíciót egy twistlock_route. YAML nevű fájlba a számítógépen
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
Ezután futtassa a parancsot:
```
oc create -f twistlock_route.yaml
```

A Twistlock-konzolhoz rendelt URL-cím a következő paranccsal kérhető le: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konzol konfigurálása

Kövesse a Twistlock dokumentációját.

### <a name="install-defender"></a>A Defender telepítése

`oc create -f defender.yaml`A 2. lépésben a fürt szerepkör és a fürt szerepkör kötésének létrehozásakor hibaüzeneteket kap.
Figyelmen kívül hagyhatja őket.

A védők csak számítási csomópontokon lesznek telepítve. A csomópont-választóval nem kell korlátoznia őket.
