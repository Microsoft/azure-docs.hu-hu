---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Azure Container Instance-erőforrást az Azure CLI-ről.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800199"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure Container Instance-erőforrás létrehozása az Azure CLI-ről

Az alábbi YAML határozza meg az Azure Container Instance-erőforrást. Másolja és illessze be a tartalmat egy nevű új fájlba, és cserélje le a megjegyzésbe tett `my-aci.yaml` értékeket a saját értékeire. Tekintse meg az [érvényes][template-format] YAML sablonformátumát. Tekintse meg [a tároló adattárakat][repositories-and-images] és rendszerképeket az elérhető rendszerképek neveiért és a hozzájuk tartozó adattárért. A tárolópéldányokhoz használható YAML-referenciával kapcsolatos további információkért [lásd: YAML-referencia: Azure Container Instances.][aci-yaml-ref]

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nem minden hely rendelkezik ugyanazokkal a processzor- és memória-elérhetőségtel. A [tárolókhoz hely és][location-to-resource] operációs rendszer szerint elérhető erőforrások listáját a hely és az erőforrások táblázatában tekintse meg.

A parancshoz létrehozott YAML-fájlra fogunk [`az container create`][azure-container-create] támaszkodni. Az Azure CLI-ről hajtsa végre a parancsot, és cserélje le a kódot `az container create` `<resource-group>` a saját elemére. Emellett a YAML-környezetek értékeinek biztonságossá tétele érdekében lásd a [biztonságos értékeket.][secure-values]

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs kimenete érvényes, és egy idő után a kimenet egy JSON-sztringre változik, amely az újonnan létrehozott `Running...` ACI-erőforrást képviseli. A tároló rendszerképe valószínűleg egy ideig nem érhető el, de az erőforrás most már üzembe van helyezni.

> [!TIP]
> Fordítson különös figyelmet a nyilvános előzetes verziójú Azure Cognitive Service-ajánlatok helyeit, mivel a YAML-t a helynek megfelelően kell módosítani.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
