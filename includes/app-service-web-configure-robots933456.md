---
title: fájl belefoglalása
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "78255828"
---
## <a name="robots933456-in-logs"></a>robots933456 a naplókban

A következő üzenet jelenhet meg a tárolónaplókban:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Az üzenet biztonságosan figyelmen kívül hagyható. `/robots933456.txt` egy olyan próba URL-cím, amelyet az App Service annak a vizsgálatára használ, hogy a tároló képes-e a kérések kiszolgálására. A 404-es válasz egyszerűen azt jelenti, hogy a cím nem létezik, azonban jelzi az App Service számára, hogy a tároló kifogástalan állapotú, és készen áll a kérések megválaszolására.

