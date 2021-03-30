---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689555"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

A következő tulajdonságok érhetők el a ben `extensionBundle` :

| Tulajdonság | Leírás |
| -------- | ----------- |
| id | Microsoft Azure functions bővítmények névterei. |
| version | A telepítendő csomag verziószáma. A functions Runtime mindig kiválasztja a verziószám vagy az intervallum által meghatározott maximálisan megengedett verziót. A fenti verzió értéke lehetővé teszi, hogy az összes köteg verziója a 1.0.0-ból, de nem tartalmazza a 2.0.0. További információ: a [verziók tartományának megadására szolgáló intervallum jelölése](/nuget/reference/package-versioning#version-ranges). |