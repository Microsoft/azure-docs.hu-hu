---
title: ELAVULT Azure Swarm-fürt kezelése a Docker API-val
description: Tárolók üzembe helyezése Docker Swarm-fürtön Azure Container Service
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: c76701ce74aafcccdbb2f1a2454f9528b52fc096
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79136227"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>ELAVULT Tárolók kezelése a Docker Swarmmal

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A Docker Swarm olyan környezetet biztosít, amelyben tárolóalapú számítási feladatokat helyezhet üzembe egy Docker-gazdagépekből álló készletben. A Docker Swarm a natív Docker API-t használja. A Docker Swarm tárolókezelésének munkafolyamata majdnem azonos az egyetlen tároló-gazdagépen elvégzendő munkafolyamattal. Ez a dokumentum egyszerű példák segítségével ismerteti, hogy miként helyezhetők üzembe a tárolóalapú munkafolyamatok a Docker Swarm Azure tárolószolgáltatás-példányaiban. További részletes dokumentációt a Docker Swarmról a [ Docker.com](https://docs.docker.com/swarm/) webhelyen talál.

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

A dokumentumban szereplő gyakorlatok előfeltételei:

[Swarm-fürt létrehozása az Azure tárolószolgáltatásban](container-service-deployment.md)

[Csatlakozás a Swarm-fürthöz az Azure tárolószolgáltatásban](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Új tároló üzembe helyezése
Ha új tárolót szeretne létrehozni a Docker Swarmban, használja a `docker run` parancsot (ügyeljen arra, hogy a fenti előfeltételeknek megfelelően nyisson meg egy SSH-alagutat a főkiszolgálók felé). Az alábbi példa létrehoz egy tárolót a `yeasy/simple-web` lemezképből:

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

A tároló létrehozása után használja a `docker ps` parancsot a tárolóinformációk megjelenítéséhez. Az információkban a tárolót tartalmazó Swarm ügynök is szerepel:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

A tárolóban futó alkalmazást a Swarm ügynök terheléselosztójának nyilvános DNS-nevén keresztül érheti el. Ezeket az információkat megtalálja az Azure Portalon:  

![Valós látogatási eredmények](./media/container-service-docker-swarm/real-visit.jpg)  

Alapértelmezés szerint a Load Balancer a 80-as, 8080-as és 443-as portot nyitja meg. Ha másik porton keresztül szeretne csatlakozni, akkor az Azure Load Balancerben meg kell nyitnia a portot az ügynökkészlet számára.

## <a name="deploy-multiple-containers"></a>Több tároló üzembe helyezése
Amikor több tároló is elindul a „docker run” többszöri végrehajtása után, a `docker ps` paranccsal megtekintheti, mely állomásokon futnak a tárolók. Az alábbi példában három tároló oszlik el egyenlően a három Swarm-ügynökön:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Tároló üzembe helyezése a Docker Compose-zal
A Docker Compose-zal automatizálhatja a több tároló telepítését és konfigurálását. Ehhez hozzon létre egy Secure Shell- (SSH-) alagutat, és állítsa be a DOCKER_HOST változót (lásd a feni előfeltételeket).

Hozzon létre egy docker-compose.yml fájlt a helyi számítógépen. Ehhez használja a következő mintát:

```dockerfile
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

A tárolók üzembe helyezéséhez futtassa a `docker-compose up -d` parancsot:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Végül megjelenik a futó tárolók listája. Ebben a listában a Docker Compose-zal üzembe helyezett tárolók szerepelnek:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

A `docker-compose ps` használatával természetesen megvizsgálhatja a csak a `compose.yml` fájlban megadott tárolókat.

## <a name="next-steps"></a>További lépések
[További információ a Docker Swarmról](https://docs.docker.com/swarm/)

