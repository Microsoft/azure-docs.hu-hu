---
title: Titkos kötet csatlakoztatása a tároló csoportjához
description: Megtudhatja, hogyan csatlakoztathat titkos kötetet a tároló példányaihoz való hozzáférés bizalmas adatainak tárolásához
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169561"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Titkos kötet csatlakoztatása Azure Container Instances

Használjon *titkos* kötetet bizalmas adatok megadásához egy tároló csoport tárolói számára. A *titkos* kötet a köteten belüli fájlokban tárolja a titkokat, a tároló csoportban lévő tárolók által elérhetővé. A *titkos* kulcsokban lévő titkok tárolásával elkerülhető a bizalmas adatok, például ssh-kulcsok vagy adatbázis-hitelesítő adatok hozzáadása az alkalmazás kódjához.

* Miután telepítette a titkokat egy tároló csoportba, a titkos kötet *írásvédett*.
* Az összes titkos kötetet a [tmpfs][tmpfs], egy RAM-alapú fájlrendszer támogatja. a tartalmuk soha nem felejtő tárolóba íródik.

> [!NOTE]
> A *titkos* kötetek jelenleg a Linux-tárolók számára vannak korlátozva. Megtudhatja, hogyan adhat hozzá biztonságos környezeti változókat Windows-és Linux-tárolók számára a [beállított környezeti változókban](container-instances-environment-variables.md). Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="mount-secret-volume---azure-cli"></a>Titkos kötet csatlakoztatása – Azure CLI

Ha egy vagy több titkos kulcsot tartalmazó tárolót szeretne üzembe helyezni az Azure CLI használatával, adja `--secrets` meg a és a `--secrets-mount-path` paramétereket az az [Container Create][az-container-create] parancsban. Ez a példa egy *titkos* kötetet csatlakoztat, amely két, Secrets, "mysecret1" és "mysecret2" tartalmú fájlból áll `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A következő az [Container exec][az-container-exec] kimenete egy rendszerhéj megnyitását mutatja a futó tárolóban, a titkos köteten található fájlokat listázva, majd a tartalmukat jeleníti meg:

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

A tároló csoportokat az Azure CLI-vel és egy YAML- [sablonnal](container-instances-multi-container-yaml.md)is üzembe helyezheti. A YAML sablon általi üzembe helyezés az előnyben részesített módszer, ha több tárolóból álló tároló-csoportokat helyez üzembe.

Ha YAML-sablonnal végzi a telepítést, a titkos értékeknek **Base64 kódolással** kell rendelkezniük a sablonban. A titkos értékek azonban a tárolóban lévő fájlokban jelennek meg a szöveges szövegben.

A következő YAML-sablon egy olyan tároló csoportot határoz meg, amely egy *titkos* kötetet csatlakoztat a (z) helyen `/mnt/secrets` . A titkos kötet két fájlt tartalmaz: Secrets, "mysecret1" és "mysecret2".

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

A YAML sablonnal való üzembe helyezéshez mentse az előző YAML egy nevű fájlba `deploy-aci.yaml` , majd hajtsa végre az az [Container Create][az-container-create] parancsot a következő `--file` paraméterrel:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Titkos kötet csatlakoztatása – Resource Manager

A CLI és a YAML üzembe helyezése mellett az Azure [Resource Manager-sablonok](/azure/templates/microsoft.containerinstance/containergroups)használatával is üzembe helyezhet egy tároló csoportot.

Először töltse `volumes` fel a tömböt a sablon tároló csoport `properties` szakaszában. Ha Resource Manager-sablonnal végzi a telepítést, a titkos értékeknek **Base64 kódolással** kell rendelkezniük a sablonban. A titkos értékek azonban a tárolóban lévő fájlokban jelennek meg a szöveges szövegben.

Ezután a tároló csoport minden olyan tárolójában, amelyben a *titkos* kötetet csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt a `properties` tároló definíciójának szakaszában.

A következő Resource Manager-sablon egy olyan tároló csoportot határoz meg, amely egy *titkos* kötetet csatlakoztat a alkalmazásban `/mnt/secrets` . A titkos kötet két titkot tartalmaz: "mysecret1" és "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

A Resource Manager-sablonnal történő üzembe helyezéshez mentse az előző JSON-fájlt egy nevű fájlba `deploy-aci.json` , majd hajtsa végre az az [üzembe helyezési csoport létrehozása][az-deployment-group-create] parancsot a következő `--template-file` paraméterrel:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Következő lépések

### <a name="volumes"></a>Kötetek

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir-kötet csatlakoztatása Azure Container Instances](container-instances-volume-emptydir.md)
* [Gitrepo típusú-kötet csatlakoztatása Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Biztonságos környezeti változók

A [biztonságos környezeti változók](container-instances-environment-variables.md#secure-values)használatával a tárolók (beleértve a Windows-tárolókat is) bizalmas információinak biztosítására szolgáló másik módszer.

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
