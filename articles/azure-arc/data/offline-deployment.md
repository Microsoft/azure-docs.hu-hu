---
title: Offline üzembe helyezés
description: Az offline üzembe helyezés lehetővé teszi a tárolók lemezképének lekérését egy privát tároló-beállításjegyzékből a Microsoft Container Registry húzása helyett.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955365"
---
# <a name="offline-deployment-overview"></a>Offline üzembe helyezés – áttekintés

Az Azure arc-adatvezérlő létrehozásához használt tároló-lemezképek jellemzően az SQL felügyelt példányainak és a PostgreSQL nagy kapacitású-kiszolgálói csoportoknak a Microsoft Container Registry (MCR) szolgáltatásból való létrehozásakor közvetlenül elérhetők. Bizonyos esetekben az üzembe helyezett környezet nem fog kapcsolódni a Microsoft Container Registryhoz.  Ilyen helyzetekben lekérheti a tároló lemezképeit egy olyan számítógéppel _, amely hozzáfér_ a Microsoft Container Registry, majd felcímkézi és leküldi azokat egy olyan privát tároló-beállításjegyzékbe, amely az Azure arc-kompatibilis adatszolgáltatások üzembe helyezéséhez _használt környezetből csatlakoztatható._

Mivel az Azure arc-kompatibilis adatszolgáltatások esetében havi frissítések vannak megadva, és nagy számú tároló-lemezkép van, a legjobb megoldás a tároló lemezképének lekérése és lekérése egy privát tároló-beállításjegyzékbe parancsfájl használatával.  A parancsfájl automatizálható vagy manuálisan is futtatható.

Egy [minta parancsfájl](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) az Azure arc GitHub-tárházban található.

> [!NOTE]
> Ehhez a parancsfájlhoz a Python és a [Docker parancssori](https://docs.docker.com/install/)felület telepítése szükséges.

A parancsfájl interaktívan kéri a következő információkat.  Ha azt szeretné, hogy a parancsfájl interaktív kérések nélkül fusson, a parancsfájl futtatása előtt beállíthatja a megfelelő környezeti változókat.

|Adatkérés|Környezeti változó|Jegyzetek|
|---|---|---|
|A forrás tároló beállításjegyzékének megadása – nyomja le az ENTER billentyűt a következő használatához: `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|A rendszerképeket általában a Microsoft Container Registryból kell lekérnie, de ha egy privát előzetes verzióban vesz részt egy másik beállításjegyzékben, az előzetes program részeként megadott információkat használhatja.|
|Adja meg a forrás tároló beállításjegyzékének tárházát – nyomja le az ENTER billentyűt a következő használatára `arcdata` :|SOURCE_DOCKER_REPOSITORY|Ha a Microsoft Container Registryra húz, a tárház a következő lesz: `arcdata` .|
|Adja meg a forrás tároló beállításjegyzékének felhasználónevét – nyomja meg az ENTER billentyűt a none használata esetén:|SOURCE_DOCKER_USERNAME|Csak akkor adjon meg értéket, ha olyan forrásból húz le tároló-lemezképeket, amelyekhez bejelentkezés szükséges.  A Microsoft Container Registry nem igényel bejelentkezési azonosítót.|
|Adja meg a forrás tároló beállításjegyzékének jelszavát – nyomja le az ENTER billentyűt a none használata esetén:|SOURCE_DOCKER_PASSWORD|Csak akkor adjon meg értéket, ha olyan forrásból húz le tároló-lemezképeket, amelyekhez bejelentkezés szükséges.  A Microsoft Container Registry nem igényel bejelentkezési azonosítót. Ez egy maszkolt jelszó kérése.  Ha begépeli vagy beilleszti a jelszót, a jelszó nem jelenik meg.|
|Adja meg a tároló képcímkéjét a képekhez a forrásnál – nyomja meg az ENTER billentyűt a következő használatához `<current monthly release tag>` :|SOURCE_DOCKER_TAG|A rendszer havonta frissíti az alapértelmezett címke nevét, hogy tükrözze a Microsoft Container Registry aktuális kiadásának hónapját és évét.|
|Adja meg a tároló-beállításjegyzék DNS-nevét vagy IP-címét:|TARGET_DOCKER_REGISTRY|A célként megadott beállításjegyzék DNS-neve vagy IP-címe.  Ezt a beállításjegyzéket fogja leküldeni a lemezképek _számára_.|
|Adja meg a tároló beállításjegyzékének tárházát:|TARGET_DOCKER_REPOSITORY|A tárolót a cél beállításjegyzékben, hogy a rendszer leküldse a lemezképeket.|
|Adja meg a tároló beállításjegyzékének felhasználónevét – nyomja le az ENTER billentyűt a none használata esetén:|TARGET_DOCKER_USERNAME|A tároló-beállításjegyzékbe való bejelentkezéshez használt Felhasználónév (ha van ilyen).|
|Adja meg a tároló beállításjegyzékének jelszavát – nyomja le az ENTER billentyűt a none használata esetén:|TARGET_DOCKER_PASSWORD|A tároló-beállításjegyzékbe való bejelentkezéshez használt jelszó (ha van ilyen). Ez egy maszkolt jelszó kérése.  Ha begépeli vagy beilleszti a jelszót, a jelszó nem jelenik meg.|
|Adja meg a tároló rendszerképének címkéjét a célhelyen található képekhez:|TARGET_DOCKER_TAG|A félreértések elkerülése érdekében általában ugyanazt a címkét használja, mint a forrás.|