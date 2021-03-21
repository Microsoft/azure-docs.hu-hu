---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrást az Azure CLI-vel.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 87007d3df3fe44ab04a330b09b8e495ec4b47e54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97865973"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure Container instance-erőforrás létrehozása az Azure CLI-ből

Az alábbi YAML határozza meg az Azure Container instance-erőforrást. Másolja és illessze be a tartalmat egy új fájlba, `my-aci.yaml` és a megnevezett értékeket cserélje le a saját megjegyzésére. Tekintse meg a [sablon formátumát][template-format] az érvényes YAML. Tekintse meg a [tároló adattárait és lemezképeit][repositories-and-images] a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz. A Container instances YAML-hivatkozásával kapcsolatos további információkért lásd [: YAML-hivatkozás: Azure Container instances][aci-yaml-ref].

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
> Nem minden helyen ugyanaz a processzor és a memória rendelkezésre állása. A tárolók és az operációs rendszerek számára elérhető erőforrások listázásához tekintse meg a [hely és erőforrások][location-to-resource] táblát.

A parancshoz létrehozott YAML-fájlra támaszkodunk [`az container create`][azure-container-create] . Az Azure CLI-ből futtassa a `az container create` parancsot, és cserélje le a alkalmazást `<resource-group>` sajátra. Emellett a YAML-telepítésben lévő értékek biztonságossá tételéhez tekintse meg a [biztonságos értékeket][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A parancs kimenete `Running...` Ha érvényes, egy kis ideig a kimenet egy JSON-karakterláncra változik, amely az újonnan létrehozott ACI-erőforrást jelképezi. A tároló képe több, mint valószínű, hogy egy ideig nem érhető el, de az erőforrás már telepítve van.

> [!TIP]
> Ügyeljen arra, hogy a nyilvános előzetes verziójú Azure kognitív szolgáltatás ajánlatait is figyelemmel kísérje, mivel a YAML ennek megfelelően kell módosítani, hogy az megfeleljen a helynek.

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
