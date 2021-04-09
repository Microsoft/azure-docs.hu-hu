---
title: Migrálás Media Services v2-ről v3-be – bevezetés
description: Ez a cikk bevezetést mutat be a Media Services v2-ről v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 32e502ff175a9222faa0eb79fb53f2cc3f76bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559740"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migrálás Media Services v2-ről v3-be – bevezetés

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

A Media Services áttelepítési útmutató segítséget nyújt a Media Services v2 API-k és a V3 API-k áttelepítéséhez egy olyan áttelepítés alapján, amely kihasználja a most elérhető új szolgáltatásokat és funkciókat. Érdemes használni a legjobb ítéletet, és meg kell határozni, hogy melyik illik legjobban a forgatókönyvhöz.

## <a name="how-to-use-this-guide"></a>Az útmutató használata

### <a name="navigating"></a>Navigáció

Az útmutató során a következő ábra jelenik meg.

![áttelepítési lépések](./media/migration-guide/steps.svg)<br/>

A lépést a lépés egy színváltozása jelzi, amely a következőhöz hasonló:

![2. áttelepítési lépések](./media/migration-guide/steps-2.svg)<br/>

Az egyes lapok végén a **következő lépések** fejléc alatt olvasható további áttelepítési dokumentumokra mutató hivatkozások jelennek meg.

### <a name="guidance"></a>Útmutató

Az itt megadott útmutató *általános*. Tartalmakat tartalmaz, amelyekkel jobban megismerheti, hogy mi is elérhető a v3-as verzióban, valamint hogy mi változott a Media Services munkafolyamatokban.

Részletesebb útmutatást, például képernyőképeket és fogalmi grafikát is talál az egyes forgatókönyvek, oktatóanyagok, gyors útmutatók, minták és API-referenciák című témakörben. Olyan mintákat is felsorolunk, amelyek segítenek összehasonlítani a v2 API-t a V3 API-val.

## <a name="migration-guidance-overview"></a>Áttelepítési útmutató – Áttekintés

Az áttelepítés során négy általános lépést kell követnie:

## <a name="step-1-benefits"></a>1. lépés – előnyök

<hr color="#5ea0ef" size="10">

Ismerje meg a Media Services API v3-re való Migrálás [előnyeit](migrate-v-2-v-3-migration-benefits.md) .

## <a name="step-2-differences"></a>2. lépés – különbségek

<hr color="#5ea0ef" size="10">

Megismerheti a Media Services v2 API és a V3 API közötti különbségeket.

- [API-hozzáférés](migrate-v-2-v-3-differences-api-access.md)
- [Szolgáltatások hiányosságainak](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminológia és entitások módosításai](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>3. lépés SDK-telepítő

<hr color="#5ea0ef" size="10">

Ismerje meg az SDK-különbségeket, és [állítsa be az áttelepítéset a V3 REST API vagy az ügyfél-SDK-ba](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>4. lépés forgatókönyv-alapú útmutató

<hr color="#5ea0ef" size="10">

Media Services v2 alkalmazása egyedi lehet. Ezért olyan forgatókönyv-alapú útmutatást kaptunk, hogy a múltban milyen *módon használta a* Media Services szolgáltatást, és a szolgáltatás egyes funkcióinak lépései, például a következők:

- [Kódolás](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Live streaming (Élő adatfolyam)](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Csomagolás és kézbesítés](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Tartalomvédelem](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Media szolgáltatás számára fenntartott egységek (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
