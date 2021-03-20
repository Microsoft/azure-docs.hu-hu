---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84465054"
---
A következő CLI-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-bypass` paraméter van beállítva `AzureServices` . Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```