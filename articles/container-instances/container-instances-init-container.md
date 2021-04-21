---
title: Init-tárolók futtatása
description: Futtassa az init-tárolókat a Azure Container Instances, hogy telepítési feladatokat hajtson végre egy tárolócsoportban az alkalmazástárolók futtatása előtt.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763936"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Init-tároló futtatása a tárolócsoportban található beállítási feladatokhoz

Azure Container Instances támogatja az *init tárolókat* egy tárolócsoportban. Az init-tárolók az alkalmazástároló vagy -tárolók elindul előtt futnak le a befejezésig. A [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)init-tárolóihoz hasonlóan használjon egy vagy több inicializálási tárolót az alkalmazástárolók inicializálási logikájának végrehajtásához, például fiókok beállításához, beállítási szkriptek futtatásához vagy adatbázisok konfigurálásához.

Ez a cikk bemutatja, hogyan konfigurálható egy tárolócsoport Azure Resource Manager egy init tárolóval egy sablonnal.

## <a name="things-to-know"></a>Tudnivalók

* **API-verzió** – Az init-tárolók üzembe helyezéséhez legalább Azure Container Instances API 2019-12-01-es verziójára van szükség. Üzembe helyezés `initContainers` [YAML-fájlban található tulajdonság vagy](container-instances-multi-container-yaml.md) Resource Manager [használatával.](container-instances-multi-container-group.md)
* **Végrehajtási sorrend** – Az Init-tárolók végrehajtása a sablonban megadott sorrendben, valamint a többi tároló előtt megadva. Alapértelmezés szerint tárolócsoportonként legfeljebb 59 init tároló adható meg. Legalább egy nem init tárolónak a csoportban kell lennie.
* **Gazdakörnyezet** – Az Init-tárolók ugyanazon a hardveren futnak, mint a csoport többi tárolója.
* **Erőforrások** – Az init-tárolókhoz nem adhatók meg erőforrások. Ők kapják meg a tárolócsoport számára elérhető összes erőforrást, például a processzorokat és a memóriát. Amíg egy init-tároló fut, a csoportban nem fut más tároló.
* **Támogatott tulajdonságok –** Az Init-tárolók olyan csoporttulajdonságokat használhatnak, mint a kötetek, titkos kulcsok és felügyelt identitások. Ha azonban a tárolócsoporthoz van konfigurálva, nem használhatnak portokat vagy IP-címeket. 
* **Újraindítási szabályzat** – A csoport következő tárolója előtt minden init tárolónak sikeresen ki kell lépnie. Ha egy init-tároló nem tud sikeresen kilépni, [](container-instances-restart-policy.md) annak újraindítási művelete a csoporthoz konfigurált újraindítási szabályzattól függ:

    |Szabályzat a csoportban  |Szabályzat init  |
    |---------|---------|
    |Mindig     |OnFailure (Hiba esetén)         |
    |OnFailure (Hiba esetén)     |OnFailure (Hiba esetén)         |
    |Soha     |Soha         |
* **Díjak** – A tárolócsoport költségei az init tároló első üzembe helyezéséből adnak költségeket.

## <a name="resource-manager-template-example"></a>Resource Manager példasablonra

Először másolja a következő JSON-t egy nevű új `azuredeploy.json` fájlba. A sablon egy tárolócsoportot állít be egy init és két alkalmazástárolóval:

* Az *init1 tároló* futtatja [a busybox](https://hub.docker.com/_/busybox) rendszerképet a Docker Hub. 60 másodpercig alvó üzemmódban van, majd egy parancssori sztringet ír egy [emptyDir kötetben található fájlba.](container-instances-volume-emptydir.md)
* Mindkét alkalmazástároló futtatja a `aci-wordcount` Microsoft-tároló rendszerképét:
    * A *hamlet tároló* az alapértelmezett konfigurációban futtatja a wordcount alkalmazást, és megszámlálja a szavak gyakoriságát a *Hamlet (Hamlet) játékában.*
    * A *parancsalkalmazás* tárolója beolvassa a parancssori sztringet az emptDir kötetből, hogy a wordcount alkalmazást futtassa a Következő és a Következő *könyvtárban:*.

A rendszerkép használatával kapcsolatos további információkért és `aci-wordcount` példákért lásd: Set environment variables in container instances (Környezeti változók [beállítása tárolópéldányban).](container-instances-environment-variables.md)

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

Telepítse a sablont az [az deployment group create paranccsal.][az-deployment-group-create]

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Egy init-tárolóval üzemelő csoportban az üzembe helyezési idő az init tároló vagy tárolók befejezéséhez szükséges idő miatt növekszik.


## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Annak ellenőrzéséhez, hogy az init tároló sikeresen lefutott-e, tekintse meg az alkalmazástárolók naplókimenetét [az az container logs paranccsal.][az-container-logs] A `--container-name` argumentum határozza meg azt a tárolót, amelyből a naplókat le kell lekérte. Ebben a példában lekérte a hamlet és *a container* tároló *naplóit,* amelyek eltérő parancskimenetet mutatnak:

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

Az inicializálási tárolók segítenek az alkalmazástárolók beállítási és inicializálási feladatainak elvégzésében. További információ a feladatalapú tárolók futtatásáról: Tárolóalapú feladatok futtatása [újraindítási szabályzatokkal.](container-instances-restart-policy.md)

Azure Container Instances más lehetőségeket is kínál az alkalmazástárolók viselkedésének módosítására. Példák:

* [Környezeti változók beállítása tárolópéldányban](container-instances-environment-variables.md)
* [Állítsa be a parancssort egy tárolópéldányban az alapértelmezett parancssori művelet felülbírálása érdekében](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
