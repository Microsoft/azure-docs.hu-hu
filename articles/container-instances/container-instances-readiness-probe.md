---
title: Készültségi mintavétel beállítása a tároló-példányon
description: Megtudhatja, hogyan konfigurálhat mintavételt úgy, hogy a tárolók Azure Container Instances fogadása csak akkor legyen elérhető, amikor készen állnak
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169663"
---
# <a name="configure-readiness-probes"></a>Készültségi tesztek konfigurálása

A forgalmat kiszolgáló tároló alkalmazások esetében érdemes ellenőrizni, hogy a tároló készen áll-e a bejövő kérések kezelésére. Azure Container Instances támogatja a készültségi mintavételt a konfigurációk belefoglalásához, hogy a tároló bizonyos körülmények között nem érhető el. A készültségi mintavétel [Kubernetes-készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)mintavételhez hasonlóan viselkedik. Előfordulhat például, hogy egy tároló alkalmazásnak be kell töltenie egy nagyméretű adatkészletet az indítás során, és nem szeretné, hogy a rendszer ebben az időszakban fogadja a kéréseket.

Ez a cikk azt ismerteti, hogyan helyezhető üzembe egy készültségi vizsgálatot magában foglaló tároló-csoport, hogy a tárolók csak a mintavétel sikeressége után kapják meg a forgalmat.

A Azure Container Instances támogatja az [élettartam](container-instances-liveness-probe.md)-mintavételt is, amelyet beállíthatja úgy, hogy a nem kifogástalan állapotú tárolót automatikusan újrainduljon.

> [!NOTE]
> Jelenleg nem használhat készenléti mintavételt egy virtuális hálózatra központilag telepített tároló csoportba.

## <a name="yaml-configuration"></a>YAML-konfiguráció

Például hozzon létre egy `readiness-probe.yaml` fájlt a következő kódrészlettel, amely tartalmazza a készültségi vizsgálatot. Ez a fájl olyan tároló csoportot határoz meg, amely egy kisméretű webalkalmazást futtató tárolóból áll. Az alkalmazás üzembe helyezése a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` rendszerképből történik. Ez a tároló alkalmazás az Azure CLI-vel és más gyors útmutatókkal is mutatja be [a Container példány üzembe helyezését az Azure](container-instances-quickstart.md) -ban.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Start parancs

Az üzemelő példány egy olyan `command` tulajdonságot határoz meg, amely a tároló első indításakor futtatott indítási parancsot határozza meg. Ez a tulajdonság karakterláncok tömbjét fogadja el. Ez a parancs egy olyan időpontot szimulál, amikor a webalkalmazás fut, de a tároló nem áll készen. 

Először indít el egy rendszerhéj-munkamenetet, és futtat egy `node` parancsot a webalkalmazás elindításához. Egy parancsot is elkezd 240 másodpercig alvó állapotba, amely után létrejön egy nevű fájl `ready` a `/tmp` címtárban:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Készültségi parancs

Ez a YAML-fájl meghatározza a készültségi `readinessProbe` `exec` vizsgálatként szolgáló készültségi parancsot. Ez a példa a készültségi parancs tesztelését vizsgálja a fájl meglétét a `ready` `/tmp` könyvtárban.

Ha a `ready` fájl nem létezik, a készültségi parancs nem nulla értékkel kilép, a tároló tovább fut, de nem érhető el. Ha a parancs sikeresen kilép a 0. kilépési kóddal, a tároló készen áll a hozzáférésre. 

A `periodSeconds` tulajdonság kijelöli a készültségi parancsot 5 másodpercenként. A készültségi mintavétel a tároló csoport élettartamára fut.

## <a name="example-deployment"></a>Példa központi telepítésre

Futtassa a következő parancsot egy tároló csoport üzembe helyezéséhez az előző YAML-konfigurációval:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Készenléti ellenőrzések megtekintése

Ebben a példában az első 240 másodpercben a készültségi parancs meghiúsul, amikor ellenőrzi a `ready` fájl létezését. A visszaadott állapotkód azt jelzi, hogy a tároló nem áll készen.

Ezek az események a Azure Portal vagy az Azure CLI-ből is megtekinthetők. A portálon például a következő típusú események `Unhealthy` jelennek meg, amikor a készültségi parancs sikertelen lesz. 

![Portál sérült eseménye][portal-unhealthy]

## <a name="verify-container-readiness"></a>Tároló készültségének ellenőrzése

A tároló elindítása után ellenőrizheti, hogy a szolgáltatás kezdetben nem érhető el. A kiépítés után szerezze be a tároló csoport IP-címét:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Próbáljon meg hozzáférni a webhelyhez, miközben a készültségi mintavétel meghiúsul:

```bash
wget <ipAddress>
```

A kimenet azt mutatja, hogy a hely kezdetben nem érhető el:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 másodperc elteltével a készültségi parancs sikeres lesz, és jelzi, hogy a tároló készen áll. Most, amikor futtatja a `wget` parancsot, a következő lesz:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Ha a tároló elkészült, elérheti a webalkalmazást úgy is, hogy az IP-címet egy webböngészővel böngészi.

> [!NOTE]
> A készültségi mintavétel továbbra is fut a tároló csoport élettartama során. Ha a készültségi parancs későbbi időpontban meghiúsul, a tároló újra elérhetetlenné válik. 
> 

## <a name="next-steps"></a>Következő lépések

A készültségi mintavétel hasznos lehet olyan helyzetekben, amelyek függő tárolók álló többtárolós csoportokat érintenek. További információ a többtárolós forgatókönyvekről: [Container groups in Azure Container instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
