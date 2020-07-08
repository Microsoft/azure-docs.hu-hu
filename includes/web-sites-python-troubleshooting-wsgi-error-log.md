---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179723"
---
Ha a Python hibát észlel az alkalmazás elindítása közben, csak egy egyszerű hibaüzenet jelenik meg (például "a lap nem jeleníthető meg, mert belső kiszolgálóhiba történt.").

Python-alkalmazások hibáinak rögzítése:

1. A Azure Portal a webalkalmazásban válassza a **Beállítások**lehetőséget.
2. A **Beállítások** lapon válassza az **Alkalmazásbeállítások**lehetőséget.
3. Az **Alkalmazásbeállítások**területen adja meg a következő kulcs/érték párokat:
    * Kulcs: WSGI_LOG
    * Érték: D:\home\site\wwwroot\logs.txt (adja meg a választott fájlnevet)

Ekkor a WWWroot mappában található logs.txt fájlban hibákat kell látnia.
