---
title: Mik azok az Azure Arc-kompatibilis adatszolgáltatások?
description: Az Azure arc-kompatibilis adatszolgáltatások bemutatása
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97609139"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Mik azok az Azure Arc-kompatibilis adatszolgáltatások (előzetes verzió)?

Az Azure arc lehetővé teszi az Azure-beli adatszolgáltatások helyszíni, peremhálózati és nyilvános felhőben történő futtatását a Kubernetes és az Ön által választott infrastruktúra használatával.

Jelenleg a következő Azure arc-kompatibilis adatszolgáltatások érhetők el előzetes verzióban:

- SQL Managed Instance
- PostgreSQL nagy kapacitású

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Mindig aktuális

Az Azure arc-kompatibilis adatszolgáltatások, például az Azure arc-kompatibilis SQL felügyelt példány és az Azure arc-kompatibilis PostgreSQL-nagy kapacitású rendszeresen frissítéseket kapnak, beleértve a karbantartási javításokat és az Azure-hoz hasonló új szolgáltatásokat. A Microsoft Container Registry frissítései az Ön számára biztosítottak, és az üzembe helyezési lépésszám a szabályzatoknak megfelelően van beállítva. Így a helyszíni adatbázisok naprakészek maradhatnak, miközben biztosítjuk a vezérlés fenntartását. Mivel az Azure arc-kompatibilis adatszolgáltatások egy előfizetési szolgáltatás, többé nem fog megszűnni az adatbázisainak támogatásával kapcsolatos helyzetek.

## <a name="elastic-scale"></a>Rugalmas méretezés

A helyszíni rugalmas rugalmasság lehetővé teszi, hogy az adatbázisok méretezése dinamikusan, ugyanúgy, mint az Azure-ban, az infrastruktúra rendelkezésre álló kapacitása alapján. Ezzel a képességgel az illékony igényekkel rendelkező burst forgatókönyvek is megtalálhatók, beleértve azokat a forgatókönyveket, amelyekben valós időben kell bevenni az adatok betöltését és lekérdezését, és a másodpercben megadott időpontig. Emellett az adatbázis-példányokat az Azure Database for PostgreSQL nagy kapacitású egyedi Hyper-skála üzembe helyezési lehetőségével is felskálázással bővítheti. Ez a funkció lehetővé teszi az adatok számítási feladatait a kapacitás optimalizálásának növelésére, az egyedi kibővített *olvasási és írási műveletek* használatával.

## <a name="self-service-provisioning"></a>Önkiszolgáló kiépítés

Az Azure arc más Felhőbeli előnyöket is biztosít, például a gyors üzembe helyezést és az automatizálást a skálán. A Kubernetes-alapú előkészítésnek köszönhetően másodpercek alatt üzembe helyezhet egy adatbázist a grafikus felhasználói felületen vagy a CLI-eszközökkel.

## <a name="unified-management"></a>Egységes felügyelet

A jól ismert eszközök, például a Azure Portal, a Azure Data Studio és a segítségével az [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure arc használatával üzembe helyezett adategységek egyesített nézetét is megtekintheti. A környezetében és az Azure-ban nem csupán számos különböző, a környezetben és az Azure-ban található, valamint a Kubernetes API-kkal kapcsolatos napló-és telemetria lehet megtekinteni és elemezni a mögöttes infrastruktúra kapacitásának és állapotának elemzéséhez. A honosított log Analytics és a teljesítmény monitorozása mellett mostantól kihasználhatja a Azure Monitort a teljes hagyatékon keresztüli átfogó operatív elemzésekhez.

## <a name="disconnected-scenario-support"></a>Leválasztott forgatókönyvek támogatása

A szolgáltatások számos szolgáltatása, például az önkiszolgáló üzembe helyezés, az automatizált biztonsági mentések/visszaállítás és a figyelés helyileg is futtatható az infrastruktúrában az Azure-hoz való közvetlen kapcsolódással vagy anélkül. A közvetlenül az Azure-hoz való csatlakozás további lehetőségeket biztosít a más Azure-szolgáltatásokkal való integrációhoz, például a Azure Monitorhoz, és lehetővé teszi, hogy a világ bármely pontjáról a Azure Portal és Azure Resource Manager API-kat használja az Azure arc-kompatibilis adatszolgáltatások kezeléséhez.

## <a name="next-steps"></a>Következő lépések

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.

[Az ügyféleszközök telepítése](install-client-tools.md)

[Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)

[Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)

[Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)
