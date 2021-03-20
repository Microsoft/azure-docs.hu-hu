---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92743800"
---
Elérheti a tárolón belül létrehozott konzol naplóit.

Először kapcsolja be a tároló naplózását a következő parancs futtatásával:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Cserélje `<app-name>` le `<resource-group-name>` a és a nevet a webalkalmazásának megfelelő nevekre.

Miután bekapcsolta a tárolók naplózását, a következő parancs futtatásával tekintheti meg a log streamet:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + .

A naplófájlokat egy böngészőben is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
