---
title: Titkos kötet csatlakoztatása tárolócsoporthoz
description: Megtudhatja, hogyan csatlakoztatható titkos kötet a tárolópéldányok számára elérhető bizalmas információk tárolására
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770998"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Titkos kötet csatlakoztatása a Azure Container Instances

Titkos kötet *használatával* bizalmas adatokat ad meg a tárolócsoportban található tárolók számára. A *titkos* kötet a titkos adatokat a köteten belüli fájlokban tárolja, amelyek a tárolócsoportban lévő tárolók számára érhetők el. Ha titkos kulcsokat tárol egy *titkos köteten,* elkerülheti, hogy bizalmas adatokat, például SSH-kulcsokat vagy adatbázis-hitelesítő adatokat ad az alkalmazáskódhoz.

* A titkos kulcsok tárolócsoportban való üzembe helyezése után a titkos kötet csak *olvasható* lesz.
* Minden titkos kötet [tmpfs,egy][tmpfs]RAM-ra háttérrendszerre van ásva; A tartalmaik soha nem felejtő tárolóba vannak írva.

> [!NOTE]
> *A* titkos kötetek jelenleg Linux-tárolókra vannak korlátozva. A Set environment variables (Környezeti változók beállítása) cikkből megtudhatja, hogyan lehet biztonságos környezeti változókat átadni Windows- és [Linux-tárolókhoz.](container-instances-environment-variables.md) Miközben dolgozunk a Windows-tárolók összes szolgáltatásának elérhető elérhetőségen, a jelenlegi platformbeli különbségeket a áttekintésben [találja.](container-instances-overview.md#linux-and-windows-containers)

## <a name="mount-secret-volume---azure-cli"></a>Titkos kötet csatlakoztatása – Azure CLI

Ha egy tárolót egy vagy több titkos okkal helyez üzembe az Azure CLI használatával, az az container create parancsba foglalja bele a és `--secrets` `--secrets-mount-path` [a][az-container-create] paramétert. Ez a példa egy titkos *kötetet csatlakoztat,* amely két, titkos adatokat tartalmazó fájlból áll: "mysecret1" és "mysecret2": `/mnt/secrets`

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Az [alábbi az container exec][az-container-exec] kimenet egy rendszerhéjat nyit meg a futó tárolóban, felsorolja a titkos köteten lévő fájlokat, majd megjeleníti azok tartalmát:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Titkos kötet csatlakoztatása – YAML

Tárolócsoportokat az Azure CLI és egy [YAML-sablon használatával is üzembe helyezhet.](container-instances-multi-container-yaml.md) A yaml-sablonnal történő üzembe helyezés az előnyben részesített módszer a több tárolóból álló tárolócsoportok üzembe helyezésekor.

YaML-sablonnal való üzembe helyezéskor a titkos értékeknek **Base64 kódolásúnak** kell lennie a sablonban. A titkos értékek azonban egyszerű szövegként jelennek meg a tárolóban lévő fájlokban.

Az alábbi YAML-sablon meghatároz egy tárolócsoportot egy tárolóval, amely *csatlakoztat* egy titkos kötetet a következőn: `/mnt/secrets` . A titkos kötet két, titkos adatokat tartalmazó fájlból áll: "mysecret1" és "mysecret2".

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

A YAML-sablonnal való üzembe helyezéshez mentse az előző YAML-fájlt egy nevű fájlba, majd hajtsa végre az az container create parancsot a `deploy-aci.yaml` [][az-container-create] `--file` paraméterrel:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Titkos kötet csatlakoztatása – Resource Manager

A CLI és a YAML üzembe helyezése mellett üzembe helyezhet egy tárolócsoportot egy Azure [Resource Manager sablon használatával.](/azure/templates/microsoft.containerinstance/containergroups)

Először töltse ki a tömböt a sablon `volumes` tárolócsoport `properties` szakaszában. Ha az üzembe helyezést Resource Manager sablonnal, a titkos értékeknek **Base64 kódolásúnak** kell lennie a sablonban. A titkos értékek azonban egyszerű szövegként jelennek meg a tárolóban lévő fájlokban.

Ezután a tárolócsoport minden olyan tárolójában, amelyben csatlakoztatni  szeretné a titkos kötetet, töltse ki a tárolódefiníció szakaszában található `volumeMounts` `properties` tömböt.

Az alábbi Resource Manager sablon meghatároz egy tárolócsoportot, amely egy tárolóval csatlakoztat egy *titkos* kötetet a következőn: `/mnt/secrets` . A titkos kötet két titkos okkal rendelkezik: "mysecret1" és "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

A Resource Manager sablonnal való üzembe helyezéshez mentse az előző JSON-t egy nevű fájlba, majd hajtsa végre az az deployment group create parancsot a `deploy-aci.json` [][az-deployment-group-create] `--template-file` paraméterrel:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Következő lépések

### <a name="volumes"></a>Kötetek

Ismerje meg, hogyan csatlakoztatható más kötettípusok a Azure Container Instances:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir kötet csatlakoztatása a Azure Container Instances](container-instances-volume-emptydir.md)
* [GitRepo-kötet csatlakoztatása a Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Biztonságos környezeti változók

A tárolók (köztük a Windows-tárolók) számára bizalmas adatok biztosításának másik módja a biztonságos környezeti [változók használata.](container-instances-environment-variables.md#secure-values)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
