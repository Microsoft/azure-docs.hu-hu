---
title: fájlbefoglalás
description: fájlbefoglalás
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b10bf18fde850223bda80a597f448747558113f1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752202"
---
## <a name="push-image-to-registry"></a>Rendszerkép leküldése a beállításjegyzékbe

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nem rendelkezik helyi tároló lemezképekkel, futtassa a következő [Docker pull][docker-pull] parancsot egy meglévő rendszerkép lekéréséhez a Docker hub-ból. Ebben a példában a rendszerkép lekérése `hello-world` .

```
docker pull hello-world
```

Mielőtt leküldi a rendszerképet a beállításjegyzékbe, fel kell címkéznie a beállításjegyzék bejelentkezési kiszolgálójának teljes nevével. A bejelentkezési kiszolgáló nevét a következő formátumban kell megadni: * \<registry-name\> . azurecr.IO* (mind kisbetűs), például *mycontainerregistry007.azurecr.IO*.

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<login-server>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```
docker tag hello-world <login-server>/hello-world:v1
```

Végül a [Docker push][docker-push] paranccsal küldje le a rendszerképet a beállításjegyzék-példányra. Cserélje le a helyére `<login-server>` a beállításjegyzék-példány bejelentkezési kiszolgálójának nevét. Ez a példa a **Hello-World** tárházat hozza létre, amely tartalmazza a `hello-world:v1` képet.

```
docker push <login-server>/hello-world:v1
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, távolítsa el a `hello-world:v1` rendszerképet a helyi Docker-környezetből. (Vegye figyelembe, hogy ez a [Docker RMI][docker-rmi] -parancs nem távolítja el a rendszerképet a **Hello-World** adattárból az Azure Container registryben.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

