---
title: Mik azok az Azure Arc-kompatibilis adatszolgáltatások?
description: A Azure Arc-kompatibilis adatszolgáltatások
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 2d866dcb5b2a0be9e6468b3d40258e37ac93834e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716096"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Mik azok az Azure Arc-kompatibilis adatszolgáltatások (előzetes verzió)?

Azure Arc lehetővé teszi az Azure-beli adatszolgáltatások helyszíni, peremhálózati és nyilvános felhőkben való futtatását a Kubernetes és a választott infrastruktúra használatával.

Jelenleg az alábbi Azure Arc-kompatibilis adatszolgáltatások érhető el előzetes verzióban:

- SQL Managed Instance
- PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Mindig aktuális

Azure Arc-kompatibilis adatszolgáltatások sql Azure Arc példány és a Azure Arc-kompatibilis PostgreSQL – rugalmas skálázás rendszeresen kap frissítéseket, beleértve a karbantartási javításokat és az Azure-beli élményhez hasonló új funkciókat. A rendszer Microsoft Container Registry frissítéseket biztosít Önnek, és az üzembe helyezési ütemet Ön állíthatja be a házirendek szerint. Így a helyszíni adatbázisok naprakészek maradhatnak, miközben gondoskodnak az irányítás fenntartásáról. Mivel Azure Arc-kompatibilis adatszolgáltatások előfizetési szolgáltatásról van szó, az adatbázisok támogatása többé nem fog véget érni.

## <a name="elastic-scale"></a>Rugalmas méretezés

A felhőhöz hasonló helyszíni rugalmasság lehetővé teszi, hogy az adatbázisokat dinamikusan, az infrastruktúra rendelkezésre álló kapacitásának megfelelő módon, dinamikusan skálázható fel vagy le, ugyanúgy, mint az Azure-ban. Ez a képesség megfelelhet az ideiglenes igényekkel rendelkező adatlökés-forgatókönyveknek, beleértve azokat az eseteket is, amelyekhez valós időben, bármilyen léptékben, másodpercnél másodpercnél nagyobb válaszidővel kell adatokat be- és lekérdezni. Emellett az adatbázispéldányokat horizontálisan fel is skálázhatja a hyperscale egyedi, Azure Database for PostgreSQL üzembe helyezési lehetőségének használatával. Ez a képesség tovább növeli az adatterhelések kapacitásoptimalizálását az egyedi horizontális *felskálásos* olvasások és írások használatával.

## <a name="self-service-provisioning"></a>Önkiszolgáló kiépítés

Azure Arc más felhőbeli előnyöket is kínál, például a gyors üzembe helyezést és a nagy léptékű automatizálást. A Kubernetes-alapú vezénylésnek köszönhetően másodpercek alatt üzembe helyezhet egy adatbázist grafikus felhasználói felülettel vagy CLI-eszközökkel.

## <a name="unified-management"></a>Egységes felügyelet

Az olyan ismerős eszközök használatával, mint a Azure Portal, a Azure Data Studio és a , mostantól egységes nézetet kaphat az összes, a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Arc. A környezetben és az Azure-ban számos különféle relációs adatbázist megtekinthet és kezelhet, de naplókat és telemetriai adatokat is le tud kapni a Kubernetes API-kból a mögöttes infrastruktúra kapacitásának és állapotának elemzéséhez. A honosított naplóelemzés és a teljesítményfigyelés mellett mostantól a Azure Monitor átfogó működési elemzésekhez is használhatja a teljes tulajdont.

## <a name="disconnected-scenario-support"></a>Leválasztott forgatókönyv támogatása

Számos szolgáltatás, például az önkiszolgáló kiépítés, az automatikus biztonsági mentések/visszaállítás és a monitorozás helyileg futtathatók az infrastruktúrában az Azure-hoz való közvetlen kapcsolattal vagy anélkül. Ha közvetlenül csatlakozik az Azure-hoz, további lehetőségeket tárhat fel a más Azure-szolgáltatásokkal, például az Azure Monitor-val való integrációhoz, valamint a Azure Portal- és Azure Resource Manager API-k a világ bármely részén való használatának Azure Arc-kompatibilis adatszolgáltatások.

## <a name="supported-regions"></a>Támogatott régiók

Az alábbi táblázat azokat a forgatókönyveket ismerteti, amelyek az Arc-kompatibilis adatszolgáltatások esetében jelenleg támogatottak.

|Azure-régiók  |Közvetlen csatlakoztatott mód  |Közvetett csatlakoztatott mód  |
|---------|---------|---------|
|USA keleti régiója|Elérhető|Elérhető
|Nyugat-Európa |Elérhető |Elérhető
|Észak-Európa|Elérhető|Elérhető

## <a name="next-steps"></a>Következő lépések

> **Csak ki szeretné próbálni a dolgokat?**  
> Az Azure Arc [Jump Azure Kubernetes Service start](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) (AKS), az AWS Elastic Kubernetes Service (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gép gyors üzembe helyezési útmutatója.

[Az ügyféleszközök telepítése](install-client-tools.md)

[A Azure Arc létrehozása](create-data-controller.md) (ehhez először telepítenie kell az ügyféleszközöket)

[Hozzon létre Azure SQL felügyelt](create-sql-managed-instance.md) példányt a Azure Arc (először létre kell hoznia egy Azure Arc adatkezelőt)

[Hozzon létre Azure Database for PostgreSQL hyperscale kiszolgálócsoportot](create-postgresql-hyperscale-server-group.md) a Azure Arc (először létre kell hoznia egy Azure Arc adatkezelőt)
