---
title: Azure CDN gyorsítótárazási viselkedés szabályozása lekérdezési karakterláncokkal – standard szint
description: Azure CDN lekérdezési karakterlánc gyorsítótárazása szabályozza, hogy a rendszer hogyan gyorsítótárazza a fájlokat, amikor egy webes kérelem lekérdezési karakterláncot tartalmaz. Ez a cikk a lekérdezési karakterláncok gyorsítótárazását ismerteti Azure CDN standard termékekben.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018579"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Azure CDN gyorsítótárazási viselkedés szabályozása lekérdezési karakterláncokkal – standard szint
> [!div class="op_single_selector"]
> * [Standard csomag](cdn-query-string.md)
> * [Prémium szintű csomag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Áttekintés
Az Azure Content Delivery Network (CDN) segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazza a fájlokat egy olyan webes kérelem esetében, amely egy lekérdezési karakterláncot tartalmaz. Lekérdezési karakterláncot tartalmazó webes kérelem esetén a lekérdezési karakterlánc a kérelemnek a kérdőjel (?) utáni részét jelöli. A lekérdezési karakterláncok tartalmazhatnak egy vagy több kulcs-érték párokat, amelyekben a mező nevét és értékét egy egyenlőségjel (=) választja el egymástól. A kulcs-érték párokat egy jel (&) választja el egymástól. Például: http: \/ /www.contoso.com/content.mov?field1=value1&mező = érték2. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár szerepel, a rendelésük nem számít. 

> [!IMPORTANT]
> A Azure CDN standard és a prémium szintű termékek ugyanazt a lekérdezési karakterlánc gyorsítótárazási funkcióját biztosítják, a felhasználói felület azonban eltérő. Ez a cikk a **Microsoft Azure CDN standard** felületét ismerteti, amely a Akamai és a **Azure CDN standard** szintről **Azure CDN** a verizontól. A **Verizon Azure CDN Premium** csomaggal rendelkező lekérdezési karakterlánc gyorsítótárazásával kapcsolatban lásd: [Azure CDN gyorsítótárazási viselkedés vezérlése a lekérdezési karakterláncokkal – prémium szint](cdn-query-string-premium.md).

Három lekérdezési karakterlánc mód érhető el:

- **Lekérdezési karakterláncok figyelmen kívül hagyása**: alapértelmezett mód. Ebben a módban a CDN-pont – jelenlét (POP) csomópont továbbítja a lekérdezési karakterláncokat a kérelmezőtől az első kérelemben szereplő forrás-kiszolgálónak, és gyorsítótárazza az eszközt. A POP-ból kiszolgált összes további kérelem figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz lejár.

- A **lekérdezési karakterláncok gyorsítótárazásának mellőzése**: ebben a módban a lekérdezési karakterláncokat tartalmazó kérelmeket a rendszer nem gyorsítótárazza a CDN pop-csomóponton. A POP-csomópont közvetlenül a forrás-kiszolgálóról kérdezi le az adategységet, és minden kéréssel átadja azt a kérelmezőnek.

- **Gyorsítótár – minden egyedi URL-cím**: ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot, a saját gyorsítótárral rendelkező egyedi objektumként lesz kezelve. Például a forráskiszolgáló válasza egy kérelemhez (például. ashx? q = test1) gyorsítótárazva van a POP-csomóponton, és a későbbi gyorsítótárak esetében ugyanezt a lekérdezési karakterlánccal adja vissza. A (például. ashx? q = teszt2) kérelem egy különálló eszközként van gyorsítótárazva, amely a saját élettartama beállítással van ellátva.
   
    >[!IMPORTANT] 
    > Ne használja ezt a módot, ha a lekérdezési karakterlánc olyan paramétereket tartalmaz, amelyek minden kérelemre (például egy munkamenet-AZONOSÍTÓra vagy egy felhasználónévre) változnak, mert az alacsony gyorsítótár-találati arányt eredményez.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>A lekérdezési karakterlánc gyorsítótárazási beállításainak módosítása a szabványos CDN-profilokhoz
1. Nyisson meg egy CDN-profilt, majd válassza ki a felügyelni kívánt CDN-végpontot.
   
   ![CDN-profil végpontjai](./media/cdn-query-string/cdn-endpoints.png)
   
2. A bal oldali ablaktábla beállítások területén kattintson a **gyorsítótárazási szabályok** elemre.
   
    ![CDN-gyorsítótárszabályok gomb](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. A **lekérdezési karakterlánc gyorsítótárazási viselkedése** listán válassza ki a lekérdezési karakterlánc módot, majd kattintson a **Mentés** gombra.
   
   ![CDN lekérdezési karakterlánc gyorsítótárazási beállításai](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Mivel időt vesz igénybe, hogy a regisztráció továbbítható legyen Azure CDN, előfordulhat, hogy a gyorsítótár-karakterlánc-beállítások módosításai nem láthatók azonnal:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 



