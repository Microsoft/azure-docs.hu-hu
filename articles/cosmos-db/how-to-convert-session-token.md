---
title: A munkamenet-jogkivonat formátumának konvertálása a .NET SDK-ban – Azure Cosmos DB
description: Ismerje meg, hogyan alakíthatja át a munkamenet-jogkivonat formátumait a különböző .NET SDK-verziók közötti kompatibilitás érdekében
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342045"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>A munkamenet-jogkivonat formátumának konvertálása a .NET SDK-ban
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan lehet konvertálni a különböző munkamenet-tokenek formátumait az SDK-verziók közötti kompatibilitás érdekében.

> [!NOTE]
> Alapértelmezés szerint az SDK automatikusan nyomon követi a munkamenet-tokent, és a legutóbbi munkamenet-tokent fogja használni.  További információért látogasson el a [munkamenet-tokenek](how-to-manage-consistency.md#utilize-session-tokens)használatára. A cikkben szereplő utasítások csak az alábbi feltételek teljesülése esetén érvényesek:
> * Az Azure Cosmos DB-fiók munkamenet-konzisztenciát használ.
> * A munkamenet-tokenek kezelése manuálisan történik.
> * Egyszerre több verziót használ az SDK-ban.

## <a name="session-token-formats"></a>Munkamenet-jogkivonat formátuma

Kétféle munkamenet-jogkivonat-formátum létezik: **egyszerű** és **vektoros**.  Ez a két formátum nem felcserélhető, ezért a formátumot át kell alakítani, ha az ügyfélalkalmazás más verziókkal való továbbításra kerül.
- Az **egyszerű** munkamenet-token formátumát a .net SDK V1 (Microsoft.Azure.DocumentDB-Version 1. x) használja
- A **vektoros** munkamenet jogkivonatának formátumát a .net SDK V2 (Microsoft.Azure.DocumentDB-Version 2. x) használja

### <a name="simple-session-token"></a>Egyszerű munkamenet-jogkivonat

Egy egyszerű munkamenet-jogkivonat formátuma a következő: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Vektoros munkamenet tokenje

A vektoros munkamenet-token formátuma a következő: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Átalakítás egyszerű munkamenet-tokenre

Ha egy munkamenet-jogkivonatot szeretne átadni az ügyfélnek a .NET SDK v1 használatával, használjon egy **egyszerű** munkamenet-token formátumot.  Például a következő mintakód használatával alakítsa át.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Konvertálás vektoros munkamenet-tokenre

Ha egy munkamenet-jogkivonatot szeretne átadni a .NET SDK V2 használatával az ügyfélnek, használja a **vektoros** munkamenet-token formátumot.  Például a következő mintakód használatával alakítsa át.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikkeket:

* [Munkamenet-tokenek használata a Azure Cosmos DB konzisztenciájának kezeléséhez](how-to-manage-consistency.md#utilize-session-tokens)
* [Válassza ki a megfelelő konzisztencia-szintet Azure Cosmos DB](./consistency-levels.md)
* [A konzisztencia, a rendelkezésre állás és a teljesítménybeli kompromisszumok Azure Cosmos DB](./consistency-levels.md)
* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](./consistency-levels.md)