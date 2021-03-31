---
title: Az élettartam mintavételének beállítása a Container-példányon
description: Megtudhatja, hogyan konfigurálhat élő mintavételt a nem megfelelő állapotú tárolók újraindításához Azure Container Instances
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169697"
---
# <a name="configure-liveness-probes"></a>Üzemelési tesztek konfigurálása

A tároló alkalmazások hosszabb ideig is futhatnak, így a tároló újraindításával megsérült állapotok jelenhetnek meg. Azure Container Instances támogatja az élő mintavételeket, így a tárolók csoportján belül is konfigurálhatja a tárolókat, hogy újrainduljon, ha a kritikus funkció nem működik. Az élettartam-mintavétel a [Kubernetes-élettartam](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)mintavételéhez hasonló.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe olyan tároló csoportot, amely tartalmaz egy élő vizsgálatot, amely egy szimulált, nem kifogástalan állapotú tároló automatikus újraindítását szemlélteti.

A Azure Container Instances támogatja a [készültségi](container-instances-readiness-probe.md)mintavételt is, amelyekkel beállíthatja, hogy a forgalom csak akkor legyen elérhető, amikor készen áll a tárolóra.

> [!NOTE]
> Jelenleg nem használhat élő mintavételt egy virtuális hálózatra központilag telepített tároló-csoportban.

## <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon létre egy `liveness-probe.yaml` fájlt a következő kódrészlettel. Ez a fájl egy olyan NGNIX-tárolót határoz meg, amely végül sérült állapotba kerül.

```yaml
apiVersion: 2019-12-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Futtassa a következő parancsot a tároló csoport a fenti YAML-konfigurációval való üzembe helyezéséhez:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start parancs

Az üzemelő példány egy olyan `command` tulajdonságot határoz meg, amely a tároló első indításakor futtatott indítási parancsot határozza meg. Ez a tulajdonság karakterláncok tömbjét fogadja el. Ez a parancs szimulálja a tárolót, és nem megfelelő állapotba kerül.

Először egy bash-munkamenetet indít el, és létrehoz egy nevű fájlt `healthy` a `/tmp` címtárban. Ezután 30 másodpercig alvó állapotba kerül, mielőtt törölné a fájlt, majd 10 perces alvó állapotba lép:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Az élettartam parancs

Ez az üzemelő példány olyan `livenessProbe` élet-ellenőrzési parancsot támogat, amely támogatja `exec` az élő vezérlést. Ha a parancs nem nulla értékkel kilép, a rendszer leállítja és újraindítja a tárolót, és jelzi, hogy a `healthy` fájl nem található. Ha a parancs sikeresen kilép a 0. kilépési kóddal, semmilyen műveletet nem végez.

`periodSeconds`Ha a tulajdonságot kijelöli, a Lives parancsot 5 másodpercenként végre kell hajtani.

## <a name="verify-liveness-output"></a>Az élettartam kimenetének ellenőrzése

Az első 30 másodpercen belül a `healthy` Start parancs által létrehozott fájl létezik. Ha az élettartam parancs megkeresi a `healthy` fájl létezését, az állapotkód 0 értéket ad vissza, ami sikeres, ezért nem történik újraindítás.

30 másodperc elteltével a parancs futása `cat /tmp/healthy` sikertelen lesz, ami nem megfelelő állapotot okoz, és az események megölése történik.

Ezek az események a Azure Portal vagy az Azure CLI-ből is megtekinthetők.

![Portál sérült eseménye][portal-unhealthy]

Ha megtekinti az eseményeket a Azure Portalban, a típusú események `Unhealthy` az élettartam parancs végrehajtása után aktiválódnak. A következő esemény típusa, amely `Killing` egy tároló törlését jelzi, így az újraindítás megkezdődhet. A tároló újraindítási száma az esemény bekövetkezésekor növekszik.

Az újraindítások helyben, így az erőforrások, például a nyilvános IP-címek és a csomópont-specifikus tartalmak megmaradnak.

![Portál újraindítási számlálója][portal-restart]

Ha az élettartam-mintavétel folyamatosan leáll, és túl sok újraindítást indít el, a tároló exponenciális visszatartási késleltetést ad vissza.

## <a name="liveness-probes-and-restart-policies"></a>Élettartam-vizsgálatok és újraindítási szabályzatok

Az újraindítási szabályzatok felülírják az élő tesztek által aktivált újraindítási viselkedést. Ha például beállít egy `restartPolicy = Never` *és* egy élő tesztet, a tároló csoport nem indul újra, mert sikertelen az élettartam-ellenőrzési művelet. A tároló csoport Ehelyett a tároló csoport újraindítási házirendjét követi `Never` .

## <a name="next-steps"></a>Következő lépések

A feladaton alapuló forgatókönyvek esetében szükség lehet az automatikus újraindításra, ha az előfeltételként szükséges függvény nem működik megfelelően. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [tárolós feladatok futtatása Azure Container Instancesban](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
