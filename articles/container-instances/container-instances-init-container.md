---
title: Init-tárolók futtatása
description: Futtassa az init-tárolókat a Azure Container Instancesban a telepítési feladatok elvégzéséhez egy tároló csoportban az alkalmazás-tárolók futtatása előtt.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85954281"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Inicializálási tároló futtatása a telepítési feladatokhoz egy tároló csoportban

Azure Container Instances támogatja az *init-tárolókat* egy tároló csoportban. Az init-tárolók az alkalmazás tárolójának vagy tárolóinak elindítása előtt futnak a befejezésig. Az [init-tárolók Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)hasonlóan egy vagy több init-tárolót használ az alkalmazás-tárolók inicializálási logikájának elvégzéséhez, például fiókok beállításához, telepítési parancsfájlok futtatásához vagy adatbázisok konfigurálásához.

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Resource Manager sablont egy, az init-tárolóval rendelkező tároló csoport konfigurálásához.

## <a name="things-to-know"></a>Tudnivalók

* **API-verzió** – az init-tárolók telepítéséhez legalább Azure Container Instances API 2019-12-01-es verziójúnak kell lennie. Üzembe helyezés `initContainers` egy [YAML-fájlban](container-instances-multi-container-yaml.md) vagy egy [Resource Manager-sablonban](container-instances-multi-container-group.md)található tulajdonság használatával.
* **Végrehajtási sorrend** – az init-tárolók a sablonban megadott sorrendben és más tárolók előtt lesznek végrehajtva. Alapértelmezés szerint legfeljebb 59 init tárolót adhat meg. Legalább egy nem init tárolónak a csoportban kell lennie.
* **Gazdagép-környezet** – az init-tárolók ugyanazon a hardveren futnak, mint a csoport többi tárolója.
* **Erőforrások** – az init-tárolók erőforrásai nem adhatók meg. Az összes erőforrást, például a processzorokat és a memóriát biztosítják a tároló csoport számára. Egy init-tároló futtatása közben nem futnak más tárolók a csoportban.
* **Támogatott tulajdonságok** – az init-tárolók használhatnak a csoport tulajdonságait, például a köteteket, a titkos kulcsokat és a felügyelt identitásokat. Azonban nem használhatnak portokat vagy IP-címet, ha a tároló csoportra vannak konfigurálva. 
* **Újraindítási szabályzat** – minden init-tárolónak sikeresen ki kell lépnie a csoport következő tárolójának indításakor. Ha egy init-tároló nem indul el sikeresen, az újraindítási művelet a csoportra konfigurált [Újraindítási házirendtől](container-instances-restart-policy.md) függ:

    |Csoport szabályzata  |Inicializálási szabályzat  |
    |---------|---------|
    |Mindig     |OnFailure (Hiba esetén)         |
    |OnFailure (Hiba esetén)     |OnFailure (Hiba esetén)         |
    |Soha     |Soha         |
* **Díjak** – a tároló csoport az init-tároló első üzemelő példányának terheit terheli.

## <a name="resource-manager-template-example"></a>Példa Resource Manager-sablonra

Először másolja a következő JSON-t egy nevű új fájlba `azuredeploy.json` . A sablon egy init tárolóval és két alkalmazás-tárolóval állítja be a tároló csoportot:

* A *init1* -tároló futtatja a [BusyBox](https://hub.docker.com/_/busybox) -rendszerképet a Docker hub-ból. 60 másodpercig alvó állapotba lép, majd egy parancssori karakterláncot ír egy [emptyDir-kötetben](container-instances-volume-emptydir.md)lévő fájlba.
* Mindkét alkalmazás-tároló futtatja a Microsoft `aci-wordcount` Container-rendszerképet:
    * A *Hamlet* -tároló az alapértelmezett konfigurációban futtatja a WordCount alkalmazást, és megszámolja a Word-gyakoriságokat a Shakespeare Play *Hamletben*.
    * A *Júlia* app Container beolvassa a parancssori karakterláncot a emptDir kötetről, hogy a WordCount alkalmazást a Shakespeare *Romeo és Júlia* helyett futtassa.

További információt és példákat a `aci-wordcount` rendszerkép használatával kapcsolatban a [környezeti változók beállítása a Container instances](container-instances-environment-variables.md)szolgáltatásban című témakörben talál.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Egy init-tárolóval rendelkező csoportban a központi telepítési idő az inicializálási tároló vagy tárolók befejezéséhez szükséges idő miatt megnövekszik.


## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Az init-tároló sikeres futtatásának ellenőrzéséhez tekintse meg az alkalmazás-tárolók naplózási kimenetét az az [Container logs][az-container-logs] paranccsal. Az `--container-name` argumentum megadja azt a tárolót, amelyből a naplófájlokat le szeretné húzni. Ebben a példában lekéri a *Hamlet* és *Júlia* tároló naplóit, amelyek különböző parancs kimenetet mutatnak:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Kimenet:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Kimenet:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Következő lépések

Az init-tárolók segítségével elvégezheti az alkalmazás-tárolók telepítési és inicializálási feladatait. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [a tárolózott feladatok futtatása újraindítási szabályzatokkal](container-instances-restart-policy.md).

A Azure Container Instances további lehetőségeket biztosít az alkalmazás-tárolók viselkedésének módosítására. Példák:

* [Környezeti változók beállítása a Container instances szolgáltatásban](container-instances-environment-variables.md)
* [A parancssor beállítása tároló-példányban az alapértelmezett parancssori művelet felülbírálásához](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
