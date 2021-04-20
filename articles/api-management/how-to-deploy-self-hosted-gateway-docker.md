---
title: Saját üzemeltetett átjáró üzembe helyezése Docker-| Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe az Azure API Management egy saját üzemeltetett átjáró-összetevőjét a Dockerben
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725605"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure-beli API Management üzembe helyezése a Dockerben

Ez a cikk az Azure-beli virtuális gép saját üzemeltetett átjáró-összetevőjét API Management Docker-környezetben.

> [!NOTE]
> A saját üzemeltetésű átjáró Dockerben való üzemeltetése a legjobb kiértékelési és fejlesztési célokra. Éles környezetben a Kubernetes használata javasolt. Ebből [a dokumentumból](how-to-deploy-self-hosted-gateway-kubernetes.md) megtudhatja, hogyan helyezhet üzembe saját maga által üzemeltetett átjárót a Kubernetesben.

## <a name="prerequisites"></a>Előfeltételek

- A következő rövid útmutató befejezése: [Azure-beli virtuális API Management létrehozása](get-started-create-service-instance.md)
- Docker-környezet létrehozása. [A Docker for Desktop](https://www.docker.com/products/docker-desktop) fejlesztési és kiértékelési célokra jó választás. Az [összes Docker-kiadással](https://docs.docker.com) és azok funkcióival kapcsolatos információkért tekintse meg a Docker dokumentációját, valamint magára a Dockerre vonatkozó átfogó dokumentációt.
- [Átjáróerőforrás kiépítése a API Management példányban](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> A saját üzemeltetett átjáró x86-64 Linux-alapú Docker-tárolóként van csomagolva.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>A saját üzemeltetett átjáró üzembe helyezése a Dockerben

1. Az **Üzembe helyezés és** infrastruktúra alatt válassza az **Átjárók lehetőséget.**
2. Válassza ki az üzembe helyezni kívánt átjáró-erőforrást.
3. Válassza az **Üzembe helyezés lehetőséget.**
4. Vegye figyelembe, hogy a **Jogkivonat** szövegmezőben automatikusan létrehozott egy hozzáférési jogkivonatot az alapértelmezett **Lejárat** és Titkos kulcs **értékekkel.** Ha szükséges, válassza ki a kívánt értékeket az egyik vagy mindkét vezérlőben egy új jogkivonat létrehozásához.
5. Győződjön meg arról, **hogy a Docker** van kiválasztva az Üzembe **helyezési szkriptek alatt.**
6. A fájl letöltéséhez válassza az **env.conf** fájl hivatkozást **a Környezet** mellett.
7. A **Docker-parancs** vágólapra  másoláshoz válassza a Futtatás szövegmező jobb végén található másolás ikont.
8. Illessze be a parancsot a terminál (vagy parancs) ablakába. Szükség szerint állítsa be a portleképezéseket és a tároló nevét. Vegye figyelembe, hogy a parancs feltételezi, hogy a letöltött környezeti fájl jelen van az aktuális könyvtárban.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Hajtsa végre a parancsot. A parancs arra utasítja a Docker-környezetet, hogy futtassa a tárolót az Microsoft Container Registry-ból letöltött tároló rendszerképével, és leképezi a tároló HTTP-portját (8080) és HTTPS-portját (8081) a gazdagép 80-as és 443-as portjaira. [](https://aka.ms/apim/sputnik/dhub)
10. Az alábbi parancs futtatásával ellenőrizze, hogy fut-e az átjárótároló:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Vissza a Azure Portal kattintson az Áttekintés  elemre, és győződjön meg arról, hogy az üzembe helyezett saját üzemeltetett átjárótároló kifogástalan állapotot jelent.

    ![átjáró állapota](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> A <code>console docker container logs <gateway-name></code> parancs használatával pillanatképet készíthet a saját üzemeltetett átjáró naplóiról.
>
> Az <code>docker container logs --help</code> paranccsal az összes napló-megtekintési lehetőséget láthatja.

## <a name="next-steps"></a>Következő lépések

* A saját üzemeltetett átjáróval kapcsolatos további információkért lásd: [Az Azure API Management saját üzemeltetett átjáró áttekintése.](self-hosted-gateway-overview.md)
* [Konfigurálja az egyéni tartománynevet a saját üzemeltetett átjáróhoz.](api-management-howto-configure-custom-domain-gateway.md)
