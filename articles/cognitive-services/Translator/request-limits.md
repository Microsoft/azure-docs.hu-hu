---
title: Kérelemkorlátok – Translator
titleSuffix: Azure Cognitive Services
description: Ez a cikk felsorolja a Translator kérelmekre vonatkozó korlátait. A díjakat a karakterszám alapján kell fizetni, nem a kérelmek gyakorisága alapján, kérésenként legfeljebb 5000 karakter hosszú lehet. A karakterkorlátok előfizetés-alapúak, és az F0 legfeljebb 2 millió karaktert tartalmazhat óránként.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727941"
---
# <a name="request-limits-for-translator"></a>A Translatorra vonatkozó kérelemkorlátok

Ez a cikk szabályozási korlátokat tartalmaz a Translator-fordításhoz, -átíráshoz, mondathossz-észleléshez, nyelvfelismeréshez és alternatív fordításhoz.

## <a name="character-and-array-limits-per-request"></a>Karakter- és tömbkorlátok kérésenként

Minden fordítási kérelem legfeljebb 10 000 karakterből állhat az összes lefordított célnyelven. Ha például egy 3000 karakter hosszúságú fordítási kérést küld három különböző nyelvre, a kérés mérete 3000x3 = 9000 karakter lesz, amely megfelel a kérelemkorlátnak. A díjat karakterenként kell fizetni, nem a kérelmek száma alapján. Javasoljuk, hogy rövidebb kérelmeket küldjön.

Az alábbi táblázat a Translator egyes műveleteinek tömbelem- és karakterkorlátokat sorolja fel.

| Művelet | Tömbelem maximális mérete |    Tömbelemek maximális száma |    Kérelem maximális mérete (karakter) |
|:----|:----|:----|:----|
| Translate | 10,000| 100| 10,000 |
| Átírás | 5000| 10| 5000 |
| Észlelés | 50,000 |100 |50,000 |
| BreakSentence | 50,000| 100 |50,000 |
| Keresés a szótárban| 100 |10| 1,000 |
| Szótár – példák | 100 szöveg, 100 fordítás (összesen 200)| 10|2000 |

## <a name="character-limits-per-hour"></a>Karakterkorlátok óránként

Az óránkénti karakterkorlát a Translator-előfizetési szinten alapul.

Az óránkénti kvótát egyenletesen kell felhasználta az óra során. Az F0 csomag óránkénti 2 millió karakteres korlátja mellett például a karaktereket nem szabad gyorsabban felosztani, mint percenként 33 300 karaktert (2 millió karakter 60 perccel elosztva).

Ha eléri vagy túllépi ezeket a korlátokat, vagy rövid időn belül túl nagy mennyiségű kvótát küld, valószínűleg a kvóta túllépését kapja válaszként. Az egyidejű kérések nincsenek korlátozva.

| Szint | Karakterkorlát |
|------|-----------------|
| F0 | Óránként 2 millió karakter |
| S1 | 40 millió karakter/óra |
| S2 / C2 | 40 millió karakter/óra |
| S3 / C3 | Óránként 120 millió karakter |
| S4 / C4 | Óránként 200 millió karakter |

A [többszolgáltatású előfizetések korlátai](./reference/v3-0-reference.md#authentication) ugyanazok, mint az S1 csomag esetében.

Ezek a korlátok a Microsoft szabványos fordítási modelljeire korlátozódnak. Az egyéni fordítási modellek Custom Translator legfeljebb 1800 karakter/másodperc lehet modellenként.

## <a name="latency"></a>Késés

A Translator maximális késése 15 másodperc, az egyéni modellek használata esetén pedig 120 másodperc. A *100 karakteren* belüli szövegre adott válaszokat általában 150 ezredmásodperc és 300 ezredmásodperc között juk vissza. Az egyéni fordítómodellek hasonló késési jellemzőkkel rendelkeznek a tartós kérések arányában, és magasabb késéssel is rendelkeznek, ha a kérések sebessége időszakos. A válaszidők a kérés és a nyelvpár méretétől függően változnak. Ha nem kap fordítási vagy [](./reference/v3-0-reference.md#errors) hibaválaszt az adott időkereten belül, ellenőrizze a kódot, a hálózati kapcsolatot, majd próbálja újra.

## <a name="sentence-length-limits"></a>Mondathosszkorlátok

A [BreakSentence](./reference/v3-0-break-sentence.md) függvény használata esetén a mondatok hossza legfeljebb 275 karakter lehet. A következő nyelvek alól kivételek vannak:

| Nyelv | Code | Karakterkorlát |
|----------|------|-----------------|
| Kínai | Zh | 166 |
| Német | de | 800 |
| Olasz | it | 800 |
| Japán | ja | 166 |
| Portugál | pt | 800 |
| spanyol | es | 800 |
| Thai | Th | 180 |

> [!NOTE]
> Ez a korlát nem vonatkozik a fordításra.

## <a name="next-steps"></a>Következő lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionális elérhetőség](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [3-as verziójú Translator-referencia](./reference/v3-0-reference.md)