---
title: Üzembe helyezés dedikált gazdagépen
description: Dedikált gazdagép használata valódi gazdagépszintű elkülönítés eléréséhez a Azure Container Instances számára
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790832"
---
# <a name="deploy-on-dedicated-hosts"></a>Üzembe helyezés dedikált gazdagépeken

A "Dedikált" egy Azure Container Instances (ACI) termékváltozat, amely elkülönített és dedikált számítási környezetet biztosít a biztonságosan futó tárolók számára. A dedikált termékváltozat használata esetén minden tárolócsoportnak dedikált fizikai kiszolgálója van egy Azure-adatközpontban, ezzel biztosítva a számítási feladatok teljes elkülönítését a szervezet biztonsági és megfelelőségi követelményeinek való megfelelés érdekében. 

A dedikált termékváltozat olyan tárolók számítási feladataihoz megfelelő, amelyek a számítási feladatok fizikai kiszolgáló szempontjából való elkülönítését igénylik.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Néhány jelenlegi korlátozás miatt a rendszer nem garantálja, hogy az összes korlát növelésére vonatkozó kérést jóváhagyják.

* A dedikált termékváltozatot bármely előfizetés alapértelmezett korlátja 0. Ha ezt a termékváltozatot szeretné használni az éles tárolók üzemelő példányaihoz, hozzon létre egy Azure ügyfélszolgálata [a][azure-support] korlát növeléséhez.

## <a name="use-the-dedicated-sku"></a>Dedikált termékváltozat használata

> [!IMPORTANT]
> A dedikált termékváltozat használata csak a jelenleg elérhető legújabb API-verzióban (2019. 12. 01. verzió) érhető el. Adja meg ezt az API-verziót az üzembe helyezési sablonban.
>

A 2019-12-01-es API-verziótól kezdődően az üzembe helyezési sablon tárolócsoport tulajdonságainak szakaszában található egy tulajdonság, amely az ACI üzembe helyezéséhez `sku` szükséges. Ezt a tulajdonságot jelenleg az ACI üzembe helyezési sablonjának Azure Resource Manager használhatja. További információ az ACI-erőforrások sablonnal való üzembe [helyezéséről: Oktatóanyag:](./container-instances-multi-container-group.md)Többtárolós csoport üzembe helyezése Resource Manager sablon használatával. 

A `sku` tulajdonság az alábbi értékek egyikét tartalmazhatja:
* `Standard` – a szabványos ACI üzembe helyezési lehetőség, amely továbbra is garantálja a hipervizorszintű biztonságot 
* `Dedicated` – a tárolócsoport dedikált fizikai gazdagépekkel való munkaterhelés-szintű elkülönítésére használatos

## <a name="modify-your-json-deployment-template"></a>A JSON üzembe helyezési sablon módosítása

Az üzembe helyezési sablonban módosítsa vagy adja hozzá a következő tulajdonságokat:
* A `resources` alatt állítsa a `apiVersion` halmazt a (Beállítás) `2019-12-01` beállításra.
* A tárolócsoport tulajdonságai alatt adjon hozzá egy `sku` tulajdonságot `Dedicated` értékkel.

Az alábbi példa egy, a dedikált termékváltozatot használó tárolócsoport-üzembehelyző sablon resources (erőforrások) szakaszát tartalmazza:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Az alábbiakban egy teljes sablon található, amely egy tárolópéldányt futtató mintatárolócsoportot helyez üzembe:

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
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>A tárolócsoport üzembe helyezése

Ha az asztalon hozta létre és szerkesztette az üzembe helyezési sablonfájlt, a fájlt a saját könyvtárába Cloud Shell a fájl húzásával töltheti fel. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A sablon üzembe helyezése az [az deployment group create paranccsal.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A sikeres üzembe helyezés egy dedikált gazdagépen történik.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
