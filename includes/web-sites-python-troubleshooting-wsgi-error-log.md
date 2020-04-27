---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67179723"
---
Ha a Python hibát észlel az alkalmazás elindítása közben, csak egy egyszerű hibaüzenet jelenik meg (például "a lap nem jeleníthető meg, mert belső kiszolgálóhiba történt.").

Python-alkalmazások hibáinak rögzítése:

1. A Azure Portal a webalkalmazásban válassza a **Beállítások**lehetőséget.
2. A **Beállítások** lapon válassza az **Alkalmazásbeállítások**lehetőséget.
3. Az **Alkalmazásbeállítások**területen adja meg a következő kulcs/érték párokat:
    * Kulcs: WSGI_LOG
    * Érték: D:\home\site\wwwroot\logs.txt (adja meg a választott fájlnevet)

Ekkor a WWWroot mappában található logs. txt fájlban hibákat kell megjelennie.
