---
title: API-hozzáférési különbségek Azure Media Services v2 és V3 API közötti hozzáférés között | Microsoft Docs
description: Ez a cikk a Azure Media Services v2 és v3 közötti API-hozzáférési különbségeket ismerteti.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 746f2d61d05b5b721e86aaea9ef56e2ca0621280
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690442"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>API-hozzáférési különbségek Azure Media Services v2 és V3 API között

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-2.svg)

Ez a cikk a Azure Media Services v2 és v3 közötti API-hozzáférési különbségeket ismerteti.

## <a name="api-access"></a>API-hozzáférés

Minden Media Services fiók hozzáférhet a V3 API-hoz. A frissített kód meglévő v2-fiókra való alkalmazása előtt azonban erősen ajánlott az áttelepítés fejlesztése egy új fiókban. Ennek az az oka, hogy a v3 entitások nem kompatibilisek visszafelé a v2-vel. Néhány v2 entitás, például az eszközök, a v3-kompatibilisek.
Továbbra is használhatja a meglévő fiókokat, ha nem keveri össze a v2 és a V3 API-t, majd próbálja meg ismét a v2-re lépni, de ez nem ajánlott.

A v2 API-hoz való hozzáférés addig lesz elérhető, amíg az 2024-es verzióban megszűnik.

Az áttelepítés során létrehozhat egy v3-fiókot, amely továbbra is hozzáfér a v2-hez.  A fiók létrehozása a következőket teheti:

- A REST API és a régebbi verzió
- Jelölje be a jelölőnégyzetet a portálon.

> [!div class="mx-imgBorder"]
> [![fiók létrehozása a portálon ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Az összes .NET, CLI és más SDK a legújabb 2020-05-01 API-t célozza meg, így megkeresheti vagy konfigurálhatja a régebbi API-verziókat.

> [!NOTE]
> A 2020-05-01 API-val létrehozott új fiókok nem használhatják a v2 API-kat.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
