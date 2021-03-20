---
title: Környezeti változók beállítása a Container-példányban
description: Megtudhatja, hogyan állíthatja be a környezeti változókat a Azure Container Instancesban futtatott tárolókban.
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169765"
---
# <a name="set-environment-variables-in-container-instances"></a>Környezeti változók beállítása a Container instances szolgáltatásban

A tárolópéldányok környezeti változóinak beállítása lehetővé teszi a tárolóban futó alkalmazás vagy szkript dinamikus konfigurálását. Ez hasonló a `--env` parancssori argumentumhoz `docker run` . 

A tárolók környezeti változóinak megadásához adja meg őket a tároló-példány létrehozásakor. Ez a cikk példákat mutat be környezeti változók beállítására, amikor elindít egy tárolót az [Azure CLI](#azure-cli-example)-vel, [Azure PowerShell](#azure-powershell-example)és a [Azure Portal](#azure-portal-example). 

Ha például a Microsoft [ACI-WordCount][aci-wordcount] tároló rendszerképét futtatja, a következő környezeti változók megadásával módosíthatja a viselkedését:

*NumWords*: az stdout-ba küldendő szavak száma.

*MinLength*: a szavakban szereplő karakterek minimális számát kell megszámolni. A nagyobb szám figyelmen kívül hagyja a gyakori szavakat, például az "a" és a "The" kifejezést.

Ha környezeti változókként kell átadnia a titkokat, Azure Container Instances támogatja a Windows-és Linux-tárolók [biztonságos értékeit](#secure-values) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-példa

Az [ACI-WordCount][aci-wordcount] tároló alapértelmezett kimenetének megtekintéséhez futtassa először az [az Container Create][az-container-create] paranccsal (nincs megadva környezeti változó):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimenet módosításához indítson el egy második tárolót a `--environment-variables` hozzáadott argumentummal, adja meg a *NumWords* és a *MinLength* változók értékeit. (Ez a példa feltételezi, hogy a parancssori felületet egy bash-rendszerhéjban vagy Azure Cloud Shellban futtatja. Ha a Windows-parancssort használja, a változókat idézőjelek között kell megadni, például: `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Ha mindkét tároló állapota *Leálltként* jelenik meg (az [az Container show][az-container-show] to ellenőrizze State), akkor a kimenet megtekintéséhez jelenítse meg a naplókat az [az Container logs][az-container-logs] használatával.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A tárolók kimenete azt mutatja be, hogyan módosította a második tároló parancsfájl-viselkedését környezeti változók beállításával.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**tarolo2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell példa

A környezeti változók a PowerShellben való beállítása hasonló a CLI-hez, de a `-EnvironmentVariable` parancssori argumentumot használja.

Először indítsa el az [ACI-WordCount][aci-wordcount] tárolót az alapértelmezett konfigurációban ezzel a [New-AzContainerGroup][new-Azcontainergroup] paranccsal:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Most futtassa a következő [New-AzContainerGroup][new-Azcontainergroup] parancsot. Ez határozza meg a *NumWords* és a *MinLength* környezeti változókat a tömb változóinak feltöltése után `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Ha mindkét tároló állapota *le van állítva* (a [Get-AzContainerInstanceLog][azure-instance-log] használatával kell ellenőriznie az állapotot), a naplók a [Get-AzContainerInstanceLog][azure-instance-log] paranccsal hívhatók meg.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Az egyes tárolók kimenete azt mutatja be, hogyan módosította a tároló által futtatott parancsfájlt környezeti változók beállításával.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal példa

Ha környezeti változókat szeretne beállítani a Azure Portal tárolójának indításakor, a tároló létrehozásakor adja meg azokat a **speciális** lapon.

1. A **speciális** lapon állítsa be a *sikertelen* **Újraindítási szabályzatot** .
2. A **környezeti változók** területen adja meg az `NumWords` értéket az `5` első változóhoz, és adja meg a `MinLength` értékét `8` a második változó értékeként. 
1. A tároló ellenőrzéséhez és üzembe helyezéséhez válassza a **felülvizsgálat + létrehozás** elemet.

![A portál lap környezeti változó engedélyezése gomb és szövegmezők][portal-env-vars-01]

A tároló naplóinak megtekintéséhez a **Beállítások** területen válassza a **tárolók**, majd a **naplók** lehetőséget. Az előző CLI-és PowerShell-szakaszban bemutatott kimenethez hasonlóan láthatja, hogyan módosították a parancsfájl viselkedését a környezeti változók. Csak öt szó jelenik meg, amelyek mindegyike legalább nyolc karakter hosszú lehet.

![A tároló napló kimenetét bemutató portál][portal-env-vars-02]

## <a name="secure-values"></a>Biztonságos értékek

A biztonságos értékekkel rendelkező objektumok bizalmas adatok, például jelszavak vagy kulcsok tárolására szolgálnak. A környezeti változók biztonságos értékeinek használata biztonságosabb és rugalmasabb, mint például a tároló rendszerképében. Egy másik lehetőség a titkos kötetek használata a [titkos kötet csatlakoztatása Azure Container Instancesban](container-instances-volume-secret.md)című témakörben.

A biztonságos értékekkel rendelkező környezeti változók nem láthatók a tároló tulajdonságaiban – az értékük csak a tárolón belülről érhető el. A Azure Portal vagy az Azure CLI-ben megtekintett tároló tulajdonságai például csak a biztonságos változó nevét jelenítik meg, nem az értékét.

Állítsa be a biztonságos környezeti változót úgy, hogy megadja a `secureValue` tulajdonságot a `value` változó típusának normál helyett. A következő YAML meghatározott két változó mutatja be a két változó típusát.

### <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon létre egy `secure-env.yaml` fájlt a következő kódrészlettel.

```yaml
apiVersion: 2019-12-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Futtassa a következő parancsot a tároló csoport YAML való üzembe helyezéséhez (szükség szerint módosítsa az erőforráscsoport nevét):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Környezeti változók ellenőrzése

Futtassa az az [Container show][az-container-show] parancsot a tároló környezeti változóinak lekérdezéséhez:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A JSON-válasz a nem biztonságos környezeti változó kulcsát és értékét jeleníti meg, de csak a biztonságos környezeti változó nevét:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Az az [Container exec][az-container-exec] paranccsal, amely lehetővé teszi egy parancs futtatását egy futó tárolóban, ellenőrizheti, hogy a biztonságos környezeti változó be van-e állítva. Futtassa a következő parancsot egy interaktív bash-munkamenet elindításához a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Miután megnyitotta az interaktív felületet a tárolón belül, elérheti a `SECRET` változó értékét:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Következő lépések

A feladat-alapú forgatókönyvek, például a Batch több tárolóval rendelkező nagyméretű adathalmazok feldolgozásával az egyéni környezeti változók is kihasználhatják futásidőben. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [a tárolózott feladatok futtatása újraindítási szabályzatokkal](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
