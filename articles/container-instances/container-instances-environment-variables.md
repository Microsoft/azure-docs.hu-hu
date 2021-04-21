---
title: Környezeti változók beállítása tárolópéldányban
description: Megtudhatja, hogyan állíthat be környezeti változókat a Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 9d95ee3d64460aa5e11f450c9e582cdc0de4f0ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790796"
---
# <a name="set-environment-variables-in-container-instances"></a>Környezeti változók beállítása tárolópéldányban

A tárolópéldányok környezeti változóinak beállítása lehetővé teszi a tárolóban futó alkalmazás vagy szkript dinamikus konfigurálását. Ez hasonló a `--env` parancssori `docker run` argumentumhoz. 

A környezeti változók tárolóban való beállítását a tárolópéldány létrehozásakor kell megadni. Ez a cikk a környezeti változók beállítását mutatja be, amikor tárolót indít el az [Azure CLI,](#azure-cli-example)a [Azure PowerShell](#azure-powershell-example)és a [Azure Portal.](#azure-portal-example) 

Ha például a Microsoft [aci-wordcount][aci-wordcount] tároló rendszerképét futtatja, a viselkedését a következő környezeti változók megadásával módosíthatja:

*NumWords:* Az STDOUT-nak küldött szavak száma.

*MinLength*(Minimális karakterhossz): A szavakban szereplő karakterek minimális száma a megszámláláshoz. A magasabb szám figyelmen kívül hagyja az olyan gyakori szavakat, mint a "of" és a "the".

Ha a titkos kulcsoknak környezeti változókként kell [](#secure-values) átadnia, a Azure Container Instances windowsos és Linux-tárolók biztonságos értékeit is támogatja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-példa

Az [aci-wordcount][aci-wordcount] tároló alapértelmezett kimenetének a futtatásához először futtassa az [az container create][az-container-create] paranccsal (nincsenek megadva környezeti változók):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimenet módosításához indítson el egy második tárolót a hozzáadott argumentummal, és adja meg a `--environment-variables` *NumWords* és *a MinLength* változó értékeit. (Ez a példa azt feltételezi, hogy a CLI-t egy Bash-rendszerhéjban vagy egy Azure Cloud Shell. Ha a Windows parancssort használja, a változókat idézőjelek között adja meg, például `--environment-variables "NumWords"="5" "MinLength"="8"` : .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Ha mindkét tároló Állapota *Terminated* (az [az container show][az-container-show] használatával ellenőrizze az állapotot), a naplókat az az container [logs][az-container-logs] használatával jelenítse meg a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A tárolók kimenete bemutatja, hogyan módosította a második tároló szkript viselkedését környezeti változók beállításával.

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

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell példa

A környezeti változók PowerShellben való beállítása hasonló a parancssori felülethez, de a `-EnvironmentVariable` parancssori argumentumot használja.

Először indítsa el [az aci-wordcount tárolót][aci-wordcount] az alapértelmezett konfigurációjában ezzel a [New-AzContainerGroup paranccsal:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Most futtassa a [következő New-AzContainerGroup][new-Azcontainergroup] parancsot. Ez határozza meg a *NumWords* és *a MinLength* környezeti változókat egy tömbváltozó () megadása `envVars` után:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Miután mindkét tároló állapota  Le lett ásva (a [Get-AzContainerInstanceLog][azure-instance-log] paranccsal ellenőrizze az állapotot), a [Get-AzContainerInstanceLog][azure-instance-log] paranccsal lekért naplókat.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Az egyes tárolók kimenete bemutatja, hogyan módosította a tároló által futtatott szkriptet környezeti változók beállításával.

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

Ha környezeti változókat kell beállítania, amikor elindít egy tárolót  a Azure Portal, a tároló létrehozásakor adja meg őket a Speciális lapon.

1. A Speciális **lapon** állítsa az Újraindítási **szabályzatot** Hiba *esetén beállításra.*
2. A **Környezeti változók alatt** adja meg a értéket az első változóhoz, a értéket pedig a második változó `NumWords` `5` `MinLength` `8` értékével. 
1. Válassza **az Áttekintés + létrehozás** lehetőséget a tároló ellenőrzéséhez, majd üzembe helyezéséhez.

![A portál oldala az Enable (Engedélyezés) környezeti változóval és szövegmezővel][portal-env-vars-01]

A tároló naplóinak megtekintéséhez a Beállítások alatt **válassza** a **Tárolók,** majd a **Naplók lehetőséget.** Az előző CLI- és PowerShell-szakaszokban látható kimenethez hasonlóan a környezeti változók is módosították a szkript viselkedését. Csak öt szó jelenik meg, amelyek legalább nyolc karakterből állnak.

![Tárolónapló kimenete a portálon][portal-env-vars-02]

## <a name="secure-values"></a>Biztonságos értékek

A biztonságos értékkel bíró objektumok bizalmas információkat, például jelszavakat vagy kulcsokat tartalmaznak az alkalmazáshoz. A biztonságos értékek környezeti változókhoz való használata biztonságosabb és rugalmasabb is, mint a tároló rendszerképében való használat. Egy másik lehetőség a titkos kötetek használata, amelyek leírása a Titkos kötet [csatlakoztatása a](container-instances-volume-secret.md)Azure Container Instances.

A biztonságos értékeket tartalmazó környezeti változók nem láthatók a tároló tulajdonságaiban – az értékük csak a tárolón belülről érhető el. A tároló tulajdonságai például a Azure Portal vagy az Azure CLI-ban csak egy biztonságos változó nevét jelenítik meg, az értékét nem.

Állítson be biztonságos környezeti változót úgy, hogy a változó típusának normál helyett `secureValue` a `value` tulajdonságot adja meg. Az alábbi YAML-ben definiált két változó a két változótípust mutatja be.

### <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon létre egy fájlt az `secure-env.yaml` alábbi kódrészletben.

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

Futtassa a következő parancsot a tárolócsoport YAML-lel való üzembe helyezéséhez (szükség szerint módosítsa az erőforráscsoport nevét):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Környezeti változók ellenőrzése

Futtassa [az az container show][az-container-show] parancsot a tároló környezeti változóinak lekérdezéséhez:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A JSON-válasz a nem biztonságos környezeti változó kulcsát és értékét is megjeleníti, de csak a biztonságos környezeti változó nevét:

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

Az [az container exec paranccsal,][az-container-exec] amely lehetővé teszi egy parancs futtatását egy futó tárolóban, ellenőrizheti, hogy a biztonságos környezeti változó be van-e állítva. Futtassa a következő parancsot egy interaktív Bash-munkamenet a tárolóban való futtatásához:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Miután megnyitott egy interaktív rendszerhéjat a tárolóban, hozzáférhet a `SECRET` változó értékhez:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Következő lépések

A feladatalapú forgatókönyvek, például a nagy méretű adatkészletek több tárolóval történő kötegelt feldolgozása hasznosak az egyéni környezeti változók futásidőben történő feldolgozása során. További információ a feladatalapú tárolók futtatásáról: Tárolóalapú feladatok futtatása [újraindítási szabályzatokkal.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
