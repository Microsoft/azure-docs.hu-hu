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
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84465120"
---
A következő PowerShell-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-Bypass` paraméter van beállítva `AzureServices` . Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```