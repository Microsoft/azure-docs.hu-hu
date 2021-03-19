---
title: Saját üzemeltetésű átjáró üzembe helyezése a Docker-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure API Management saját üzemeltetésű átjáró-összetevőjét a Docker-ben
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82205091"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API Management saját üzemeltetésű átjáró üzembe helyezése a Docker-ben

Ez a cikk az Azure-API Management saját üzemeltetésű átjáró-összetevőjének Docker-környezetbe való telepítésének lépéseit ismerteti.

> [!NOTE]
> A saját üzemeltetésű átjáró a Docker-ben a legmegfelelőbb a kiértékelési és fejlesztési célú használati esetekhez. A Kubernetes éles használatra ajánlott. [Ebből](how-to-deploy-self-hosted-gateway-kubernetes.md) a dokumentumból megtudhatja, hogyan helyezhet üzembe saját üzemeltetésű átjárót a Kubernetes.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Hozzon létre egy Docker-környezetet. Az [asztali Docker](https://www.docker.com/products/docker-desktop) jó megoldás fejlesztési és értékelési célokra. A Docker-kiadásokról, azok szolgáltatásairól és a Docker-ről szóló átfogó dokumentációról a [Docker dokumentációjában](https://docs.docker.com) talál további információt.
- [Átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> A saját üzemeltetésű átjáró x86-64 Linux-alapú Docker-tárolóként van csomagolva.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>A saját üzemeltetésű átjáró üzembe helyezése a Docker-ben

1. Válassza az **átjárók** lehetőséget az **üzembe helyezés és az infrastruktúra** területen.
2. Válassza ki azt az átjáró-erőforrást, amelyet telepíteni kíván.
3. Válassza a **telepítés** lehetőséget.
4. Vegye figyelembe, hogy a **jogkivonat** szövegmezőben lévő hozzáférési jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékeinek használatával. Ha szükséges, válassza ki a kívánt értékeket mindkét vezérlőelemben egy új jogkivonat létrehozásához.
4. Győződjön meg arról, hogy a **Docker** ki van választva a **telepítési parancsfájlok** területen.
5. A fájl letöltéséhez válassza az **env. conf** fájl hivatkozását a **környezet** mellett.
6. A **Run (Futtatás** ) szövegmező jobb oldalán található **Másolás** ikonra kattintva másolja a Docker-parancsot a vágólapra.
7. Illessze be a parancsot a terminál (vagy a parancs) ablakába. Szükség szerint módosítsa a port-hozzárendeléseket és a tároló nevét. Vegye figyelembe, hogy a parancs feltételezi, hogy a letöltött környezeti fájl megtalálható az aktuális könyvtárban.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Hajtsa végre a parancsot. A parancs arra utasítja a Docker-környezetet, hogy a Microsoft Container Registry letöltött [tároló-rendszerkép](https://aka.ms/apim/sputnik/dhub) használatával futtassa a tárolót, és leképezi a tároló http-(8080-) és HTTPS-(8081-) portjait a gazdagépen lévő 80 és 443 portokra.
9. Az alábbi parancs futtatásával ellenőrizze, hogy fut-e az átjáró tárolója:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Lépjen vissza a Azure Portalre, kattintson az **Áttekintés** elemre, és ellenőrizze, hogy az imént telepített saját üzemeltetésű átjáró-tároló kifogástalan állapotú-e.

![átjáró állapota](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> A <code>console docker container logs <gateway-name></code> parancs használatával megtekintheti a saját üzemeltetésű átjáró naplójának pillanatképét.
>
> <code>docker container logs --help</code>A parancs használatával megtekintheti az összes naplózási megtekintési beállítást.

## <a name="next-steps"></a>Következő lépések

* További információ a saját üzemeltetésű átjáróról: az [Azure API Management saját üzemeltetésű átjárójának áttekintése](self-hosted-gateway-overview.md).
* [Konfigurálja az egyéni tartománynevet a saját üzemeltetésű átjáróhoz](api-management-howto-configure-custom-domain-gateway.md).
