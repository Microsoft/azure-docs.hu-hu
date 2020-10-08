---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822796"
---
Ahhoz, hogy közvetlen SSH-munkamenetet nyisson meg a tárolóval, az alkalmazásának futnia kell.

Illessze be a következő URL-címet a böngészőbe, és cserélje le az `<app-name>` kifejezést az alkalmazása nevére:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Ha még nem végezte el a hitelesítést, a csatlakozáshoz el kell végeznie a hitelesítést az Azure-előfizetésével. A hitelesítés után egy böngészőn belüli felület jelenik meg, ahol a tárolón belül futtathat parancsokat.

![SSH-kapcsolat](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
