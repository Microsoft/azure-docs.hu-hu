---
title: Video Indexer – feladatátvétel és vészhelyreállítás
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan hajthat végre feladatátvételt másodlagos Video Indexer-fiókra, ha regionális adatközpont-meghibásodás vagy katasztrófa következik be.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87065402"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer – feladatátvétel és vészhelyreállítás

Az Azure Media Services Video Indexer nem biztosítja a szolgáltatás azonnali feladatátvételét a regionális adatközpont leállása vagy meghibásodása esetén. Ez a cikk azt ismerteti, hogyan konfigurálhatja a környezetet a feladatátvételhez, így biztosítva az alkalmazások optimális rendelkezésre állását és a lehető legkevesebb helyreállítási időt, ha katasztrófa következik be.

Javasoljuk, hogy az üzletmenet-folytonosság vészhelyreállítását (BCDR) regionális párokban konfigurálja az Azure elkülönítési és rendelkezésreállási szabályzataival járó előnyök kihasználásához. További információ: [Azure párosított régiók](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Feladatátvétel másodlagos fiókba

A BCDR megvalósításához két Video Indexer fiókkal kell rendelkeznie a redundancia kezeléséhez.

1. Hozzon létre két Video Indexer fiókot az Azure-hoz csatlakoztatva (lásd: [video Indexer fiók létrehozása](connect-to-azure.md)). Hozzon létre egy fiókot az elsődleges régióhoz, a másikat pedig a párosított Azure-régióhoz.
1. Ha az elsődleges régióban hiba történt, váltson át az indexelésre a másodlagos fiók használatával.

> [!TIP]
> A BCDR automatizálásához állítsa be a tevékenységek naplójának riasztásait a szolgáltatás állapotával kapcsolatos értesítésekhez, mint a [szolgáltatási értesítéseken a létrehozási tevékenység naplójának riasztásai](../../service-health/alerts-activity-log-service-notifications-portal.md).

További információ a több bérlő használatáról: [több bérlő kezelése](manage-multiple-tenants.md). A BCDR megvalósításához válasszon egyet a következő két lehetőség közül: [video Indexer fiók/bérlő](manage-multiple-tenants.md#video-indexer-account-per-tenant) vagy [Azure-előfizetés bérlőn](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Következő lépések

Az [Azure-hoz csatlakoztatott video Indexer-fiók kezelése](manage-account-connected-to-azure.md).
