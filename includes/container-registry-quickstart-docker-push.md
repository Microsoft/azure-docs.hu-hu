---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149011"
---
## <a name="push-image-to-registry"></a>Rendszerkép leküldése a beállításjegyzékbe

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nem rendelkezik helyi rendszerképe sem, futtassa a következő [docker pull] [ docker-pull] parancsot egy meglévő rendszerkép lekéréshez a Docker Hubból. Ebben a példában kérje le a `hello-world` kép.

```
docker pull hello-world
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A bejelentkezési kiszolgáló nevét a következő formátumban kell  *\<beállításjegyzék-name\>. azurecr.io* (csak kisbetűkkel), például *mycontainerregistry007.azurecr.io*.

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét. Ez a példa létrehoz a **hello-world** adattárban tartalmazó a `hello-world:v1` kép.

```
docker push <acrLoginServer>/hello-world:v1
```

A rendszerkép leküldése a tárolóregisztrációs adatbázisba, után távolítsa el a `hello-world:v1` lemezképet a helyi Docker-környezetben. (Vegye figyelembe, hogy ez [docker rmi] [ docker-rmi] parancs nem távolítja el a lemezképet a **hello-world** az Azure container registry-tárház.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

