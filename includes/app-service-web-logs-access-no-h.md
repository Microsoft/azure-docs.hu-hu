---
title: fájl belefoglalása
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997920"
---
Az alkalmazáskódból létrehozott konzolnaplók App Service-ben történő eléréséhez kapcsolja be a diagnosztikai naplózást a következő parancs a [Cloud Shellben](https://shell.azure.com) történő futtatásával:

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

A `--level` lehetséges értékei: `Error`, `Warning`, `Info` és `Verbose`. Minden szint tartalmazza az azt megelőző szintet. Például: az `Error` csak a hibaüzeneteket tartalmazza, a `Verbose` pedig az összes üzenetet.

Ha a diagnosztikai naplózás be van kapcsolva, futtassa a következő parancsot a naplóstream megtekintéséhez:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

A `Ctrl`+`C` billentyűparanccsal bármikor leállíthatja a naplóstreamelést.
