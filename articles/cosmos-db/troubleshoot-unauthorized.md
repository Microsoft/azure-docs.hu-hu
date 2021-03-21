---
title: Jogosulatlan kivételek Azure Cosmos DB hibáinak megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja a jogosulatlan kivételeket.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411167"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Azure Cosmos DB jogosulatlan kivételek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: a HTTP-kérelemben található MAC-aláírás nem egyezik meg a számított aláírással.
Ha a 401-as hibaüzenetet kapta, "a HTTP-kérelemben található MAC-aláírás nem egyezik meg a számított aláírással:" a következő forgatókönyvek okozhatják.

A régebbi SDK-k esetében a kivétel érvénytelen JSON-kivételként jelenhet meg a megfelelő 401 jogosulatlan kivétel helyett. Az újabb SDK-k megfelelően kezelik ezt a forgatókönyvet, és érvényes hibaüzenetet adnak.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a jogosulatlan kivételek ismert okait és megoldásait tartalmazza.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>A kulcs nem megfelelően lett elforgatva a leggyakoribb forgatókönyv
A 401 MAC-aláírás röviddel a kulcs elforgatása után következik be, és végül leáll a módosítások nélkül. 

#### <a name="solution"></a>Megoldás:
A kulcs el lett forgatva, és nem követte az [ajánlott eljárásokat](secure-access-to-data.md#key-rotation). A Azure Cosmos DB fiók kulcsának elforgatása a Azure Cosmos DB fiók méretétől függően néhány másodperctől akár napokig is eltarthat.

### <a name="the-key-is-misconfigured"></a>A kulcs hibásan van konfigurálva 
Az 401 MAC-aláírási probléma konzisztens lesz, és minden, az adott kulcsot használó hívás esetében megtörténik.

#### <a name="solution"></a>Megoldás:
A kulcs helytelenül van konfigurálva az alkalmazásban, és a fiókhoz nem megfelelő kulcsot használ, vagy a teljes kulcs másolása nem történt meg.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Az alkalmazás csak olvasható kulcsokat használ az írási műveletekhez.
Az 401 MAC-aláírási probléma csak olyan írási műveletekhez fordul elő, mint a Create vagy a Replace, de az olvasási kérelmek sikeresek.

#### <a name="solution"></a>Megoldás:
A műveletek sikeres befejezésének engedélyezéséhez állítsa át az alkalmazást olvasási/írási kulcs használatára.

### <a name="race-condition-with-create-container"></a>Versenyhelyzet létrehozási tárolóval
A 401 MAC-aláírási probléma röviddel a tároló létrehozása után látható. Ez a probléma csak a tároló létrehozása után fordul elő.

#### <a name="solution"></a>Megoldás:
Létezik egy versenyhelyzet a tároló létrehozásával. Egy alkalmazás-példány megpróbál hozzáférni a tárolóhoz a tároló létrehozása után. A versenyhelyzet leggyakoribb forgatókönyve az, ha az alkalmazás fut, és a tároló törlődik, és ugyanazzal a névvel jön létre. Az SDK megpróbálja használni az új tárolót, de a tároló létrehozása még folyamatban van, így nem rendelkezik a kulcsokkal.

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.