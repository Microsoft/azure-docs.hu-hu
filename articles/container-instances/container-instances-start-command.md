---
title: Belépési pont felülbírálása a tárolópéldányban
description: Állítson be egy parancssort, hogy felülbírálja a tároló rendszerképének belépésipont-bejegyzését az Azure Container Instance üzembe helyezésekor
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 5898decbf4108d48bb9e84019d659075b18fd043
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771082"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Állítsa be a parancssort egy tárolópéldányban az alapértelmezett parancssori művelet felülbírálása érdekében

Tárolópéldány létrehozásakor opcionálisan megadhat egy parancsot, amely felülírja a tároló rendszerképében található alapértelmezett parancssori utasítást. Ez a viselkedés hasonló a `--entrypoint` parancssori argumentumhoz, mint `docker run` a .

A [tárolópéldányok környezeti változóihoz](container-instances-environment-variables.md) hasonló módon az indítási parancssor megadása olyan kötegelt feladatok esetében hasznos, ahol az egyes tárolókat dinamikusan, tevékenységspecifikus konfigurációval kell előkészíteni.

## <a name="command-line-guidelines"></a>Parancssori irányelvek

* Alapértelmezés szerint a parancssor egyetlen folyamatot ad meg, amely rendszerhéj *nélkül indul el a* tárolóban. Előfordulhat például, hogy a parancssor egy Python-szkriptet vagy egy végrehajtható fájlt futtat. A folyamat további paramétereket vagy argumentumokat is megadhat.

* Több parancs végrehajtásához kezdje a parancssort egy, a tároló operációs rendszerben támogatott rendszerhéj-környezet beállításával. Angol nyelvű Példák:

  |Operációs rendszer  |Alapértelmezett rendszerhéj  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpesi     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Kövesse a rendszerhéj konvencióit több parancs egymás után történő futtatásához.

* A tárolókonfigurációtól függően előfordulhat, hogy be kell állítania a parancssor végrehajtható fájljának vagy argumentumának teljes elérési útját.

* Állítson be [egy megfelelő újraindítási](container-instances-restart-policy.md) szabályzatot a tárolópéldányhoz attól függően, hogy a parancssor egy hosszan futó vagy egyszer futtatott feladatot ad-e meg. Ilyen lehet például a vagy a újraindítási szabályzata egy egyszer `Never` `OnFailure` futtatott feladathoz. 

* Ha információra van szüksége a tároló rendszerképében beállított alapértelmezett belépési pontról, használja a [docker image inspect parancsot.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Parancssori szintaxis

A parancssori szintaxis a példányok létrehozásához használt Azure API-tól vagy eszköztől függ. Ha héjkörnyezetet ad meg, figyelje meg a rendszerhéj parancsszintaxis-konvencióit is.

* [az container create][az-container-create] parancs: Átad egy sztringet a `--command-line` paraméterrel. Például: `--command-line "python myscript.py arg1 arg2"` ).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell parancsmag: Átad egy sztringet a `-Command` paraméterrel. Példa: `-Command "echo hello"`.

* Azure Portal: A tároló konfigurációjának Command **override** (Parancs felülbírálása) tulajdonságában adja meg a sztringek vesszővel elválasztott listáját idézőjelek nélkül. Például: `python, myscript.py, arg1, arg2` ). 

* Resource Manager sablon, YAML-fájl vagy az egyik Azure-beli SDK: Sztringek tömbjeként adja meg a parancssori tulajdonságot. Példa: egy JSON-tömb `["python", "myscript.py", "arg1", "arg2"]` egy Resource Manager sablonban. 

  Ha már ismeri a [Dockerfile](https://docs.docker.com/engine/reference/builder/) szintaxisát, ez a formátum hasonló a CMD-utasítás *exec-formájához.*

### <a name="examples"></a>Példák

|    |  Azure CLI   | Portál | Sablon | 
| ---- | ---- | --- | --- |
| **Egyetlen parancs** | `--command-line "python myscript.py arg1 arg2"` | **Parancs felülbírálása:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Több parancs** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Parancs felülbírálása:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-példa

Például módosítsa [a microsoft/aci-wordcount][aci-wordcount] tároló rendszerképének viselkedését, amely a *Hamletben* található szöveget elemzi, és megkeresi a leggyakrabban előforduló szavakat. A *Hamlet* elemzése helyett olyan parancssort is beállíthat, amely egy másik szöveges forrásra mutat.

A [microsoft/aci-wordcount][aci-wordcount] tároló kimenetét az alapértelmezett szöveg elemzésekor a következő az container create paranccsal futtatva [láthatja.][az-container-create] Nincs megadva start parancssor, ezért az alapértelmezett tárolóparancs fut. Szemléltetés céljából ez a példa úgy állítja be a környezeti változókat, hogy [megkeresse](container-instances-environment-variables.md) az első 3 szót, amelyek legalább öt betűből állnak:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Ha a tároló Állapota Le lett bontva *(az* [az container show][az-container-show] használatával ellenőrizze az állapotot), jelenítse meg a naplót az az container [logs][az-container-logs] használatával a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Kimenet:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Most állítson be egy második példatárolót különböző szövegek elemzéséhez egy másik parancssor megadásával. A tároló által végrehajtott *Python-szkript wordcount.py*, argumentumként egy URL-címet fogad el, és az alapértelmezett helyett feldolgozza az oldal tartalmát.

Például az első 3 olyan szó meghatározásához, amely legalább öt betű hosszú a *következőben:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ha a tároló le van *omlva,* tekintse meg a kimenetet a tároló naplóinak megjelenítésével:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kimenet:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Következő lépések

A feladatalapú forgatókönyvek, például a nagy méretű adatkészletek több tárolóval történő kötegelt feldolgozása hasznosak az egyéni parancssorok futásidejű használata esetén. További információ a feladatalapú tárolók futtatásáról: Tárolóalapú feladatok futtatása [újraindítási szabályzatokkal.](container-instances-restart-policy.md)

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
