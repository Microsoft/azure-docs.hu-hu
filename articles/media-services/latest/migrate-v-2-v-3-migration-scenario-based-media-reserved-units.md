---
title: A Media szolgáltatás számára fenntartott egységek (MRUs) áttelepítési útmutatója
description: Ez a cikk olyan forgatókönyv-alapú útmutatást nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563072"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>A Media szolgáltatás számára fenntartott egységek (MRUs) forgatókönyv-alapú áttelepítési útmutatója

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk olyan forgatókönyv-alapú útmutatást nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.

- A Azure Portal vagy a V3 API 2020-05-01-es verziójával létrehozott új v3-fiókok esetében már nem szükséges a Media szolgáltatás számára fenntartott egységek (MRUs) beállítása. A rendszer ekkor automatikusan fel-és leskálázást végez a terhelés alapján.
- Ha rendelkezik egy, az API 2020-05-01-es verziója előtt létrehozott v3 vagy v2-fiókkal, továbbra is vezérelheti a feladatok egyidejűségét és teljesítményét a Media szolgáltatás számára fenntartott egységek használatával. További információért lásd a médiafeldolgozás skálázását ismertető cikket. A MRUs a parancssori felület 2,0-es verziójának használatával felügyelheti Media Services v3 vagy a Azure Portal használatával.  
- Ha nem látja a MRUs felügyeletének lehetőségét a Azure Portalban, akkor olyan fiókot futtat, amely az 2020-05-01 API-val vagy újabbtal lett létrehozva.
- Ha már ismeri az MRU típusának S3-ra való beállítását, akkor a teljesítmény javul, vagy változatlan marad.
- Ha Ön már v2-ügyfél, létre kell hoznia egy új v2-fiókot, hogy támogassa a meglévő alkalmazást az áttelepítés befejezése előtt. 
- Az indexelő v1-es vagy más olyan adathordozó-processzorokat, amelyek még nem teljesen elavultak, esetleg újra engedélyezni kell. 

További információ a MRUs: Media szolgáltatás [számára fenntartott egységek](concept-media-reserved-units.md) és [a Media szolgáltatás számára fenntartott egységek méretezése](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Fogalmak, oktatóanyagok és útmutatók

### <a name="concepts"></a>Alapelvek

[Media szolgáltatás számára fenntartott egységek](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Útmutatók

[A Media szolgáltatás számára fenntartott egységek méretezése](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).
