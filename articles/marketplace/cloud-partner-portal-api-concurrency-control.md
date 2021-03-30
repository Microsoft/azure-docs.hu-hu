---
title: Egyidejűség-vezérlés – Azure Marketplace
description: A Cloud Partner Portal közzétételi API-k Egyidejűség-vezérlési stratégiái.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88031648"
---
# <a name="concurrency-control"></a>Egyidejűség-vezérlés

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

A Cloud Partner Portal közzétételi API-k minden hívásának explicit módon meg kell határoznia, hogy melyik Egyidejűség-vezérlési stratégiát kell használni. Az **IF-Match** fejléc megadása sikertelen lesz a http 400-es hiba miatt. Két stratégiát biztosítunk a Egyidejűség-vezérléshez.

-   **Optimista** – a frissítést végrehajtó ügyfél ellenőrzi, hogy az adatok módosultak-e az adatok utolsó beolvasása óta.
-   **Utolsó egy WINS** – az ügyfél közvetlenül frissíti az adatmennyiséget, függetlenül attól, hogy egy másik alkalmazás módosította-e a legutóbbi olvasási idő óta.

<a name="optimistic-concurrency-workflow"></a>Optimista egyidejűségi munkafolyamat
-------------------------------

Javasoljuk, hogy az optimista egyidejűségi stratégiát a következő munkafolyamattal használva garantálni lehessen, hogy az erőforrásokon nem történt váratlan módosítás.

1.  Entitás beolvasása az API-k használatával. A válasz tartalmaz egy ETag értéket, amely az entitás jelenleg tárolt verzióját azonosítja (a válasz időpontjában).
2.  A frissítés időpontjában ugyanazt a ETag értéket adja meg a kötelező **IF-Match** kérelem fejlécében.
3.  Az API összehasonlítja a kérelemben kapott ETag értéket egy atomi tranzakcióban lévő entitás aktuális ETag értékével.
    *   Ha a ETag értékei eltérnek, az API http- `412 Precondition Failed` választ ad vissza. Ez a hiba azt jelzi, hogy egy másik folyamat frissítette az entitást, mivel az ügyfél utoljára lekérte, vagy hogy a kérelemben megadott ETag érték helytelen.
    *  Ha a ETag értékei megegyeznek, vagy az **IF-Match** fejléc tartalmazza a helyettesítő karakteres csillag karaktert ( `*` ), az API végrehajtja a kért műveletet. Az API-művelet az entitás tárolt ETag értékét is frissíti.


> [!NOTE]
> A helyettesítő karakter (*) megadása a **IF-Match** fejlécben az API-t az utolsó-egy WINS egyidejűségi stratégia használatával eredményezi. Ebben az esetben a ETag összehasonlítása nem történik meg, és az erőforrás ellenőrzés nélkül frissül. 
