---
title: Munkaterhelések védelme a Kubernetes számítási feladataihoz
description: Ismerje meg, hogyan használhatja Azure Security Center Kubernetes munkaterhelés-védelmi biztonsági javaslatait
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 6d0e660ecce1d45dab4d6003cdba391ba2eb4ee9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095595"
---
# <a name="protect-your-kubernetes-workloads"></a>A Kubernetes számítási feladatok védelme

Ez az oldal azt ismerteti, hogyan használhatók Azure Security Center biztonsági javaslatai a Kubernetes munkaterhelések védelme érdekében.

További információ ezekről a funkciókról: a [Kubernetes belépésvezérlés használatával kapcsolatos ajánlott eljárások a számítási feladatok védelméhez](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

Security Center több tároló biztonsági funkciót kínál, ha engedélyezi az Azure Defender használatát. Ezek konkrétan a következők:

- A tároló-beállításjegyzékek vizsgálata az [Azure Defender által a Container-jegyzékek](defender-for-container-registries-introduction.md) számára a biztonsági rések felderítéséhez
- Valós idejű veszélyforrások észlelésére vonatkozó riasztások a K8s-fürtök [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> A Kubernetes-fürtökhöz és-csomópontokhoz esetlegesen megjelenő *biztonsági javaslatok listáját az* ajánlásokat ismertető táblázat [számítási szakasza](recommendations-reference.md#recs-compute) tartalmazza.



## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|Egy hozzárendelés szerkesztéséhez a **tulajdonos** vagy a **biztonsági rendszergazda**<br>**Olvasó** a javaslatok megtekintéséhez|
|Környezeti követelmények:|Kubernetes v 1.14 (vagy újabb) szükséges<br>Nincs PodSecurityPolicy-erőforrás (régi PSP-modell) a fürtökön<br>A Windows-csomópontok nem támogatottak|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="set-up-your-workload-protection"></a>A munkaterhelések védelmének beállítása

Azure Security Center tartalmaz olyan javaslatokat, amelyek akkor érhetők el, ha telepítette a **Kubernetes Azure Policy bővítményét**.

### <a name="step-1-deploy-the-add-on"></a>1. lépés: a bővítmény telepítése

A javaslatok konfigurálásához telepítse a  **Kubernetes Azure Policy bővítményét**. 

- Ezt a bővítményt automatikusan telepítheti az [log Analytics-ügynök és-bővítmények automatikus kiépítése lehetővé tétele](security-center-enable-data-collection.md#auto-provision-mma)című részben leírtak szerint. Ha a bővítmény automatikus kiépítés értéke "on", a bővítmény alapértelmezés szerint engedélyezve van az összes meglévő és jövőbeli fürtben (amelyek megfelelnek a bővítmény telepítési követelményeinek).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Az Security Center automatikus kiépítési eszközének használata a Kubernetes házirend-bővítményének telepítéséhez":::

- A bővítmény manuális telepítése:

    1. A javaslatok lapon keresse meg a "**Azure Policy bővítmény a Kubernetes telepítéséhez és engedélyezéséhez a fürtökön**" című javaslatot. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="A (z) * * Azure Policy bővítmény Kubernetes telepítéséhez és engedélyezéséhez a fürtökön * *":::

        > [!TIP]
        > A javaslat öt különböző biztonsági vezérlőben szerepel, és nem számít, hogy melyiket választotta ki a következő lépésben.

    1. A biztonsági vezérlők bármelyikén válassza ki a javaslatot azon erőforrások megtekintéséhez, amelyekre telepíteni tudja a bővítményt.
    1. Válassza ki a megfelelő fürtöt, és javítsa ki a **szervizelést**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="A (z) * * Azure Policy bővítmény Kubernetes való telepítéséhez és engedélyezéséhez telepíteni és engedélyezni kell a fürtökön * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>2. lépés: a 13 javaslat csomagjának megtekintése és konfigurálása

1. A bővítmény telepítése után körülbelül 30 perccel a Security Center megjeleníti a fürtök állapotát a következő javaslatok esetében, amelyek mindegyike a megfelelő biztonsági vezérlőben látható:

    > [!TIP]
    > Bizonyos javaslatok olyan paraméterekkel rendelkeznek, amelyeket az Azure Policy használatával kell testreszabni a hatékony használat érdekében. Ahhoz például, hogy az ajánlati **tároló lemezképeit csak megbízható beállításjegyzékből lehessen telepíteni**, meg kell határoznia a megbízható beállításjegyzéket.
    > 
    > Ha nem adja meg a konfigurációt igénylő javaslatok szükséges paramétereit, a munkaterhelések nem kifogástalan állapotú jelennek meg.

    | Javaslat neve                                                         | Biztonsági ellenőrzés                         | Konfiguráció szükséges |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | A tároló CPU-és memória-korlátozásait kényszeríteni kell                          | Alkalmazások elleni védelem a DDoS-támadásokkal szemben | Nem                     |
    | Az emelt szintű tárolókat el kell kerülni                                     | Hozzáférés és engedélyek kezelése            | Nem                     |
    | Nem módosítható (csak olvasható) rendszerindító fájlrendszert kell kikényszeríteni a tárolók számára     | Hozzáférés és engedélyek kezelése            | Nem                     |
    | A jogosultság-eszkalációs tárolót el kell kerülni                       | Hozzáférés és engedélyek kezelése            | Nem                     |
    | A tárolók futtatását root felhasználóként el kell kerülni                           | Hozzáférés és engedélyek kezelése            | Nem                     |
    | A bizalmas gazdagépek névtereit megosztó tárolókat el kell kerülni              | Hozzáférés és engedélyek kezelése            | Nem                     |
    | A minimálisan privilegizált Linux-funkciókat kell kikényszeríteni a tárolók számára       | Hozzáférés és engedélyek kezelése            | **Igen**                |
    | A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni    | Hozzáférés és engedélyek kezelése            | **Igen**                |
    | A tárolóknak csak az engedélyezett portok figyelésére kell figyelniük                              | Jogosulatlan hálózati hozzáférés korlátozása     | **Igen**                |
    | A szolgáltatásoknak csak a megengedett portok figyelésére kell figyelniük                                | Jogosulatlan hálózati hozzáférés korlátozása     | **Igen**                |
    | A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell                     | Jogosulatlan hálózati hozzáférés korlátozása     | **Igen**                |
    | A tárolók AppArmor-profiljának felülbírálását vagy letiltását korlátozni kell | Biztonsági konfigurációk javítása        | **Igen**                |
    | A tároló lemezképeit csak a megbízható kibocsátásiegység-forgalmi jegyzékből kell telepíteni            | Biztonsági rések szervizelése                | **Igen**                |
    |||


1. A paraméterekkel rendelkező ajánlásokat a következő paraméterekkel kell megadni:

    1. A Security Center menüjében válassza a **biztonsági szabályzat** elemet.
    1. Válassza ki az adott előfizetést.
    1. Az **Security Center alapértelmezett házirend** szakaszban válassza a **hatályos házirend megtekintése** lehetőséget.
    1. Válassza az "ASC alapértelmezett" lehetőséget.
    1. Nyissa meg a **Parameters (paraméterek** ) fület, és szükség szerint módosítsa az értékeket.
    1. Válassza a **felülvizsgálat + mentés** lehetőséget.
    1. Kattintson a **Mentés** gombra.


1. A javaslatok bármelyikének érvénybe léptetéséhez 

    1. Nyissa meg a javaslat részletei lapot, és válassza a **Megtagadás** lehetőséget:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Azure Policy paraméter megtagadási beállítása":::

        Ekkor megnyílik a panel, amelyen be kell állítania a hatókört. 

    1. A hatókör beállítása után válassza a **módosítás megtagadásra** lehetőséget.

1. Annak megtekintéséhez, hogy mely javaslatok vonatkoznak a fürtökre:

    1. Nyissa meg Security Center [eszközének leltárát](asset-inventory.md) , és használja az erőforrástípus szűrőt a **Kubernetes Services szolgáltatáshoz**.

    1. Válasszon ki egy fürtöt a vizsgálathoz, és tekintse át a rendelkezésre álló elérhető javaslatokat. 

1. Ha a munkaterhelés-védelmi készletből származó javaslatot tekint meg, látni fogja, hogy a fürttel együtt hány érintett hüvely ("Kubernetes Components") szerepel. Az adott hüvelyek listájához jelölje ki a fürtöt, majd válassza a **művelet elvégzése** lehetőséget.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Az érintett hüvelyek megtekintése K8s javaslathoz"::: 

1. A kényszerítés teszteléséhez használja az alábbi két Kubernetes üzemelő példányt:

    - Az egyik a munkaterhelések elleni védelem ajánlásainak megfelelő, kifogástalan állapotú üzembe helyezés.
    - A másik a nem megfelelő állapotú üzembe helyezés *, amely nem* felel meg a javaslatoknak.

    Telepítse a example. YAML-fájlokat, vagy használja hivatkozásként a saját számítási feladatok szervizeléséhez (VIII. lépés)  


## <a name="healthy-deployment-example-yaml-file"></a>Kifogástalan üzembe helyezési példa. YAML-fájl

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Sérült telepítési példa. YAML-fájl

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja a Kubernetes munkaterhelés-védelmet. 

Más kapcsolódó anyagok esetében tekintse meg a következő lapokat: 

- [Security Center javaslatok a számítási feladatokhoz](recommendations-reference.md#recs-compute)
- [AK-fürt szintű riasztások](alerts-reference.md#alerts-akscluster)
- [A Container Host szintű riasztások](alerts-reference.md#alerts-containerhost)