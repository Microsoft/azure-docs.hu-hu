---
title: A szolgáltatások Azure Media Services v2 és v3 közötti hézagok
description: Ez a cikk a Azure Media Services v2 és v3 közötti különbségeket ismerteti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 07e43bdb0969c26120231013cc0c0354e6968ceb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567950"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>A szolgáltatások Azure Media Services v2 és v3 közötti hézagok

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-2.svg)

Az áttelepítési útmutató ezen része részletes információkat nyújt a v2 és V3 API-k közötti különbségekről.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Szolgáltatások közötti hézagok v2 és V3 API-k között

A V3 API a következő funkciókkal rendelkezik a v2 API-val. A v2 API-k Media Encoder Standard néhány speciális funkciója jelenleg nem érhető el a v3-as verzióban:

- Ha a bemenet nem rendelkezik hangtal, akkor egy csendes hangsávot szúr be, mert már nincs rá szükség a Azure Media Player.

- Video Track beszúrása, ha a bemenet nem tartalmaz videót.

- Az átkódolást használó élő események jelenleg nem támogatják az adatfolyam-beszúrási közép-és az ad-jelölő beszúrását az API-híváson keresztül.

- Az Azure Media Premium Encoder nem lesz támogatott a v2-ben. Ha 8 bites HEVC-kódolást használ, használja az új HEVC-támogatást a standard kódolóban. 
    - A hangcsatorna-hozzárendelés támogatását a standard kódolóhoz egészítették ki.  [A Media Services kódolással kapcsolatos dokumentációban](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)lásd: hang.
    - Ha a külső gyártótól származó licencelt termék (például a MXF vagy a ProRes) speciális funkcióit vagy kimeneti formátumait használta, használja az Azure partneri megoldást a webstreamből, amely a v2 nyugdíjazás időpontjában tranzakciós lesz. Használhat Imagine-kommunikációt vagy [Bitmovin](http://bitmovin.com)is.

- A v2-es streaming végpont "rendelkezésre állási csoport" tulajdonsága már nem támogatott. A [magas rendelkezésre állású VOD](./media-services-high-availability-encoding.md) -szállításról a V3 API-ban tekintse meg a minta projektet és útmutatást.

- Media Services v3-as verzióban nem adható meg a FairPlay IV. Habár nem befolyásolja az ügyfelek számára a csomagolási és a licences kézbesítés Media Servicesét, ez akkor lehet probléma, ha egy külső gyártótól származó DRM-rendszer használatával továbbítja a FairPlay-licenceket (hibrid mód).

- A rendszer eltávolította a (z) "rest" adategységek védelmére szolgáló ügyféloldali tároló-titkosítást a V3 API-ban, és a tárolási szolgáltatás titkosítása váltotta fel a nyugalmi állapotra. A V3 API-k továbbra is együttműködnek a meglévő tárolók titkosított eszközeivel, de nem teszik lehetővé újak létrehozását.

## <a name="terminology-and-entity-changes"></a>Terminológia és entitások módosításai

Az API további módosításaihoz lásd: [terminológia és entitások](migrate-v-2-v-3-differences-terminology.md) változásai.
