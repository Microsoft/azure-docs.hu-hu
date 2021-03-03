---
title: A tárolók beszerzése – problémamegoldás | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a tároló-felismeréssel kapcsolatos problémákat.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 60a6e76d43d954b27336b9631c48328aeff0b69b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708305"
---
# <a name="troubleshooting-container-insights"></a>A tároló-felismerés hibaelhárítása

Ha az Azure Kubernetes szolgáltatás (ak) fürtjét tároló-elemzésekkel konfigurálja, az adatgyűjtési vagy jelentéskészítési állapotot megakadályozó probléma merülhet fel. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Engedélyezési hiba az előkészítési vagy frissítési művelet során

Miközben a tárolók elemzését vagy a fürt frissítését támogatja a metrikák gyűjtésének támogatásához, a következőhöz hasonló hibaüzenet jelenhet meg: *az ügyfél <a felhasználó identitását> a (z) azonosítójú "<felhasználó objectId>" nem jogosult a (z) "Microsoft. Authorization/roleAssignments/Write" művelet végrehajtására a hatókörben.*

A bevezetési vagy frissítési folyamat során a rendszer a fürt erőforrásán kísérli meg a **figyelési metrikák közzétevő** szerepkör-hozzárendelésének megadását. A felhasználónak a tároló-elemzések engedélyezésére vagy a metrikák gyűjtését támogató frissítésre való hozzáférését kezdeményező felhasználónak hozzáféréssel kell rendelkeznie a **Microsoft. Authorization/roleAssignments/Write** engedélyhez az AK fürterőforrás-hatókörén. Ehhez az engedélyhez csak a **tulajdonos** és a **felhasználói hozzáférés rendszergazdai** beépített szerepköreinek tagjai kapnak hozzáférést. Ha a biztonsági szabályzatok részletességi szintű engedélyek hozzárendelését igénylik, javasoljuk, hogy [Egyéni szerepköröket](../../role-based-access-control/custom-roles.md) tekintse meg, és rendelje hozzá azokat a felhasználókat, akiknek szükségük van rá.

Ezt a szerepkört manuálisan is megadhatja a Azure Portal az alábbi lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Kubernetes**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Azure Kubernetes** lehetőséget.
3. A Kubernetes-fürtök listájában válasszon egyet a listából.
2. A bal oldali menüben kattintson a **hozzáférés-vezérlés (iam)** elemre.
3. Válassza a **+ Hozzáadás** lehetőséget egy szerepkör-hozzárendelés hozzáadásához, majd válassza ki a **figyelési metrikák közzétevői** szerepkört, és a **válassza** ki a négyzetet a **következő típussal** : a rendszer az előfizetésben definiált fürtök egyszerű szolgáltatásai alapján szűri az eredményeket. Válassza ki azt a listából, amely az adott fürtre vonatkozik.
4. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>A tároló-felismerések engedélyezve vannak, de nem jelentenek semmilyen információt

Ha a tároló-elemzések sikeresen engedélyezve és konfigurálva vannak, de nem tekintheti meg az állapotadatokat, vagy a rendszer nem ad vissza eredményeket egy napló lekérdezésből, a következő lépésekkel diagnosztizálhatja a problémát:

1. Az ügynök állapotának ellenőrzéséhez futtassa a parancsot:

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie, amely azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Ha Windows Server-csomópontokkal rendelkezik, ellenőrizze az ügynök állapotát a parancs futtatásával:

    `kubectl get ds omsagent-win --namespace=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie, amely azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. A parancs használatával keresse meg a központi telepítés állapotát az ügynök *06072018* -as vagy újabb verziójával:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie, amely azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Ellenőrizze a pod állapotát annak ellenőrzéséhez, hogy fut-e a parancs használatával: `kubectl get pods --namespace=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie a omsagent *futtatási* állapotával:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Hibaüzenetek

Az alábbi táblázat összefoglalja az ismert hibákat, amelyekkel a tároló-felismerések használata során találkozhat.

| Hibaüzenetek  | Művelet |
| ---- | --- |
| Hibaüzenet `No data for selected filters`  | Az újonnan létrehozott fürtök figyelési adatfolyamának beállítása időt vehet idénybe. Legalább 10 – 15 percet is igénybe vehet, amíg az adatai megjelennek a fürt számára. |
| Hibaüzenet `Error retrieving data` | Az Azure Kubernetes Service-fürt állapota és a teljesítmény figyelése mellett kapcsolat jön létre a fürt és az Azure Log Analytics munkaterület között. A fürt összes figyelési adatait egy Log Analytics munkaterület használatával lehet tárolni. Ez a hiba akkor fordulhat elő, ha a Log Analytics munkaterület törölve lett. Ellenőrizze, hogy a munkaterület törölve lett-e, és ha igen, akkor újra engedélyeznie kell a fürt figyelését a tároló-megállapításokkal, és meg kell adnia egy meglévőt, vagy létre kell hoznia egy új munkaterületet. Az ismételt engedélyezéshez [le kell tiltania](container-insights-optout.md) a fürt figyelését, és újra [engedélyeznie](container-insights-enable-new-cluster.md) kell a tárolók bepillantást. |
| `Error retrieving data` Miután hozzáadta a tárolók bepillantást az az AK CLI használatával | Ha a használatával engedélyezi `az aks cli` a figyelést, előfordulhat, hogy a tároló-felismerések nem lesznek megfelelően telepítve. Győződjön meg arról, hogy telepítve van-e a megoldás. Az ellenőrzéshez lépjen a Log Analytics munkaterületre, és ellenőrizze, hogy elérhető-e a megoldás a bal oldali ablaktáblán a **megoldások** lehetőség kiválasztásával. A probléma megoldásához újra kell telepítenie a megoldást a tárolók bevezetésének [telepítésével](container-insights-onboard.md) kapcsolatos utasításokat követve. |

A probléma diagnosztizálásához [hibaelhárítási parancsfájlt](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)biztosítunk.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>A Container bepillantást nyerhető ügynök ReplicaSet hüvelye nem Azure Kubernetes-fürtön van ütemezve

A Container ReplicaSet-ügynök a következő csomópont-választókkal rendelkezik a munkavégző (vagy ügynök) csomópontokon az ütemezéshez:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Ha a munkavégző csomópontokhoz nincsenek csatolva csomópont-címkék, akkor az Agent ReplicaSet-hüvelyek nem lesznek ütemezve. A címke csatolására vonatkozó utasításokért tekintse meg a [Kubernetes hozzárendelése címke-választókat](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>A teljesítmény-diagramok nem jelenítik meg a csomópontok és tárolók PROCESSZORát és memóriáját egy nem Azure-fürtön

A Container bepillantást nyerhet a cAdvisor végpontot a csomópont-ügynökön a teljesítménymutatók összegyűjtéséhez. Ellenőrizze, hogy a csomóponton lévő tároló ügynök úgy van-e konfigurálva, hogy `cAdvisor port: 10255` a teljesítmény-metrikák gyűjtéséhez a fürt összes csomópontján meg lehessen nyitni.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>A nem Azure-beli Kubernetes-fürt nem jelenik meg a tárolók bepillantást

Ha meg szeretné tekinteni a nem Azure-beli Kubernetes-fürtöt a tároló-elemzésekben, olvasási hozzáférésre van szükség az ezen betekintést támogató Log Analytics munkaterületen, valamint a Container betekintési megoldás erőforrás- **ContainerInsights (*munkaterület*)**.

## <a name="next-steps"></a>Következő lépések

Ha a figyelés engedélyezve van az AK-fürtcsomópontok és-hüvelyek állapot-metrikáinak rögzítéséhez, ezek az állapot-mérőszámok a Azure Portal érhetők el. A Container-információk használatának megismeréséhez lásd: az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md).
