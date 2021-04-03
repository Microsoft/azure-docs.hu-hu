---
title: Azure bejárati ajtó – alkalmazás réteg biztonsága | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure bejárati ajtaja Hogyan védi és biztonságossá teszi az alkalmazási háttérrendszer védelmét
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89399939"
---
# <a name="application-layer-security-with-front-door"></a>Az alkalmazások rétegének biztonsága a bejárati ajtóval
Az Azure bejárati ajtaja webalkalmazás-védelmi képességet biztosít a webalkalmazások védelmére a hálózati támadásoktól és a gyakori webes sebezhetőségek, például az SQL-injektálás vagy a helyközi parancsfájlok (XSS) használatával. Engedélyezve van a http (s) előtérben, a bejárati ajtó alkalmazási rétegének biztonsága globálisan elosztott és always on, a rosszindulatú támadások leállítása az Azure hálózati szélén, távol a háttérben. A nagyobb biztonság és teljesítmény optimalizálása révén a bejárati ajtó gyors és biztonságos webes élményt biztosít a végfelhasználók számára.

## <a name="application-protection"></a>Alkalmazás védelme
A bejárati ajtó alkalmazás-védelme a világ minden peremhálózati környezetén konfigurálva van, az alkalmazásokkal összhangban, és automatikusan letiltja a nem http (s) forgalmat a webalkalmazások eléréséhez. A több-bérlős elosztott architektúra lehetővé teszi, hogy a globális védelmet a teljesítmény feláldozása nélkül lehessen méretezni. Http (s) munkaterhelések esetén a bejárati ajtó webalkalmazás-védelmi szolgáltatása az egyéni szabályokhoz, az előre konfigurált, a közös támadásokkal kapcsolatos szabályokat, valamint a szabálynak megfelelő összes kérelem részletes naplózását biztosítja. A rugalmas műveletek, például az engedélyezés, a Letiltás vagy a napló csak támogatottak.

## <a name="custom-access-control-rules"></a>Egyéni hozzáférés-vezérlési szabályok
- **IP-engedélyezési lista és blokkolási lista:** Egyéni szabályokat is beállíthat a webalkalmazásokhoz való hozzáférés vezérléséhez az ügyfél IP-címeinek listája alapján. Az IP v4 és az IP v6 is támogatott
- **Földrajzi alapú hozzáférés-vezérlés:** Egyéni szabályokat is beállíthat a webalkalmazásokhoz való hozzáférés vezérléséhez az ügyfél IP-címének megfelelő országkód alapján.
- **Http-paraméterek szűrése:** Az egyéni hozzáférési szabályok a http (s) kérelmek paramétereinek megfelelően konfigurálhatók, beleértve a fejléceket, az URL-címeket és a lekérdezési karakterláncokat is.

## <a name="azure-managed-rules"></a>Azure által felügyelt szabályok
- Alapértelmezés szerint engedélyezve van az előre konfigurált szabályok a leggyakoribb OWASP biztonsági rések ellen. Az előzetes verzióban a szabályok halmaza tartalmazza a SQLi és az XSS kérelmek ellenőrzését. További szabályok lesznek hozzáadva. Dönthet úgy, hogy csak naplózási műveletet indít el az előre konfigurált szabályok érvényesítéséhez az alkalmazásokban elvárt módon. 

## <a name="rate-limiting"></a>Sebességkorlátozás
- A díjszabás-ellenőrzési szabály az ügyfél IP-címétől érkező rendellenes nagy forgalom korlátozása.  Egy adott ügyfél IP-címére vonatkozó, egyperces időtartamú webes kérelmek esetében küszöbértéket állíthat be.

## <a name="centralized-protection-policy"></a>Központosított védelmi szabályzat
- Több védelmi szabályt is meghatározhat, és prioritási sorrendben adhatja hozzá őket a szabályzathoz. Az egyéni szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabályrendszert a kivételek engedélyezéséhez. Egyetlen házirend van társítva a webalkalmazáshoz.  Ugyanaz a webalkalmazás-védelmi házirend replikálódik minden peremhálózati kiszolgálóra minden helyen, és gondoskodjon arról, hogy az összes régióban konzisztens biztonsági házirend legyen

## <a name="configuration"></a>Konfiguráció
- Az előzetes verzióban a REST API-k, a PowerShell vagy a CLI használatával hozhatja létre és helyezheti üzembe a bejárati ajtó alkalmazás-védelmi szabályait és szabályzatait. A portál hozzáférését a szolgáltatás általánosan elérhetővé tétele előtt fogja támogatni. 


## <a name="monitoring"></a>Figyelés
A bejárati ajtó lehetővé teszi a webalkalmazások elleni támadások figyelését olyan valós idejű metrikák használatával, amelyek integrálva vannak Azure Monitor a riasztások nyomon követésére és a trendek egyszerű figyelésére.

## <a name="pricing"></a>Díjszabás
A bejárati ajtó alkalmazási rétegének biztonsága ingyenes az előzetes verzió alatt.


## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
