---
title: fájlbefoglalás
description: fájlbefoglalás
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/06/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1b7c8487eb42204f2741679c9ef6eb2717c272cd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057354"
---
A parancs kimenetében a `identity` szakasz egy típusú identitást mutat be `SystemAssigned` a feladatban. A a `principalId` feladat identitásának elsődleges azonosítója:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Az az [ACR Task show][az-acr-task-show] paranccsal tárolja a principalId egy változóban, a későbbi parancsokban való használatra. A következő parancsban helyettesítse be a feladat és a beállításjegyzék nevét:

```azurecli
principalID=$(az acr task show \
  --name <task_name> --registry <registry_name> \
  --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
