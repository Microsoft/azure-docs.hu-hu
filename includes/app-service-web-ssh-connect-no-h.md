---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179598"
---
Ahhoz, hogy nyissa meg a tároló egy közvetlen SSH-munkamenetet, az alkalmazás kell futtatnia.

Illessze be a következő URL-címet a böngészőben, és cserélje le \<alkalmazás-neve > az alkalmazás nevére:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Ha még nincs hitelesítve, meg kell hitelesítést az Azure-előfizetésében való csatlakozáshoz. A hitelesítést követően látni egy böngészőben rendszerhéj, ahol futtathat parancsokat a tárolóban.

![SSH-kapcsolatot](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
