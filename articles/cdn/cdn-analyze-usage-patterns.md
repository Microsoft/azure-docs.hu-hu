---
title: Alapvető jelentések a Verizontól | Microsoft Docs
description: Megtudhatja, hogyan érheti el és tekintheti meg a Verizon Core-jelentéseket a Verizon felügyeleti portálján keresztül.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90985542"
---
# <a name="core-reports-from-verizon"></a>Alapvető jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Verizon Core-jelentések a Verizon felügyeleti portálon keresztül történő használatával megtekintheti a CDN használati mintáit a következő jelentésekkel:

* Sávszélesség
* Továbbított adatátvitel
* Találatok
* Gyorsítótár állapota
* Gyorsítótár-találati arány
* IPV4/IPV6-adatátvitel

## <a name="accessing-verizon-core-reports"></a>A Verizon Core-jelentések elérése
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil kezelése gomb](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye az egérmutatót az **elemzés** lapra, majd vigye az egérmutatót az **alapszintű jelentések** menü fölé. Kattintson egy jelentésre a menüben.
   
    ![CDN felügyeleti portál – alapvető jelentések menü](./media/cdn-reports/cdn-core-reports.png)

3. Minden jelentés esetében válasszon ki egy dátumtartományt a **dátumtartomány** listából. Kiválaszthat egy előre meghatározott dátumtartományt, például a **mai** vagy **az ezen a héten**, vagy kijelölhet **Egyéni** értéket, és manuálisan is megadhat egy dátumtartományt a naptár ikonjaira kattintva. 

4. Miután kiválasztott egy dátumtartományt, kattintson az **Ugrás** gombra a jelentés létrehozásához. 

4. Ha Excel-formátumban szeretné exportálni az adatformátumot, kattintson az **Ugrás** gomb fölötti Excel ikonra.

## <a name="bandwidth"></a>Sávszélesség
A sávszélesség jelentés egy gráfból és adattáblából áll, amely a CDN sávszélesség használatát jelzi a HTTP és a HTTPS számára egy adott időtartamon belül (MB/s). Megtekintheti a sávszélesség-használatot az összes pop-ban vagy egy adott POP-ban. Ez a jelentés lehetővé teszi, hogy megtekintse a pop-forgalomra vonatkozó tüskéket és eloszlást.

Az **Edge-csomópontok** listából válassza ki az összes **peremhálózati csomópontot** az összes csomópont forgalmának megtekintéséhez, vagy válasszon ki egy adott régiót.

A jelentés öt percenként frissül.

![Sávszélesség-jelentés](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Továbbított adatátvitel
Ez a jelentés egy gráf-és adattáblaből áll, amely a CDN-forgalom HTTP-és HTTPS-alapú használatát jelzi egy adott időszakon belül, GB-ban. Megtekintheti a forgalmi használatot az összes pop-ban vagy egy adott POP-on. Ez a jelentés lehetővé teszi, hogy megtekintse a forgalmi csúcsokat és a terjesztést a pop-on keresztül.

Az **Edge-csomópontok** listából válassza ki az összes **peremhálózati csomópontot** az összes csomópont forgalmának megtekintéséhez, vagy válasszon ki egy adott régiót.

A jelentés öt percenként frissül.

![Adattovábbítási jelentés](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Találatok (állapotkódok)
Ez a jelentés a tartalomhoz tartozó kérelmek állapot-kódjainak eloszlását ismerteti. Minden tartalomra vonatkozó kérelem HTTP-állapotkódot generál. Az állapotkód azt írja le, hogyan kezeli a peremhálózati pop a kérést. Egy 2xx például azt jelzi, hogy a kérést sikerült kézbesíteni egy ügyfélnek, míg a 4xx állapotkód azt jelzi, hogy hiba történt. A HTTP-állapotkódok részletes ismertetését lásd: [http](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)-állapotkódok listája.

![Jelentés a találatokról](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Gyorsítótár állapota
Ez a jelentés a gyorsítótár-találatok és a gyorsítótár-kihagyott ügyfelek kéréseinek eloszlását ismerteti. Mivel a gyorsítótárbeli találatok közül a leggyorsabb a teljesítmény, a gyorsítótár-lemaradás és a gyorsítótár-találatok minimalizálása érdekében optimalizálhatja az adatátviteli sebességet. 

A gyorsítótár-kihagyás csökkentése érdekében konfigurálja a forrás-kiszolgálót a következők használatának minimalizálása érdekében: 
 * `no-cache` Válasz fejlécei
 * Lekérdezés-karakterlánc gyorsítótárazása, kivéve, ha szigorúan szükséges  
 * Nem gyorsítótárazható válaszok kódjai

A gyorsítótár-találatok számának csökkentéséhez állítson be egy adategységet `max-age` hosszú időtartamra, hogy minimálisra csökkentse a kérelmek számát a forráskiszolgálón.

![Gyorsítótár-állapotok jelentés](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>A gyorsítótár fő állapotai a következők:
* TCP_HIT: a peremhálózati kiszolgálóról kézbesítve. Az objektum a gyorsítótárban volt, és nem lépte túl a maximális kort.
* TCP_MISS: a forrás-kiszolgálóról szolgált. Az objektum nem volt a gyorsítótárban, és a válasz visszatért a forráshoz.
* TCP_EXPIRED _MISS: a forrás kiszolgálóról kiszolgált, a forrással való újraérvényesítés után. Az objektum a gyorsítótárban volt, de túllépte a maximális kort. A forrással való újbóli érvényesítés eredményeként a gyorsítótár objektum helyére új válasz származik.
* TCP_EXPIRED _HIT: a rendszer szélétől kiszolgálja a forrással való újraérvényesítés után. Az objektum a gyorsítótárban volt, de túllépte a maximális kort. A forrás-kiszolgálóval való újraérvényesítés eredményeként a gyorsítótár objektum nem módosult.

### <a name="full-list-of-cache-statuses"></a>A gyorsítótár állapotának teljes listája
* TCP_HIT – ez az állapot akkor jelenik meg, ha egy kérést közvetlenül a POP-ból az ügyfélnek kézbesítenek. A rendszer azonnal kiszolgálja az eszközt egy POP-ról, ha az az ügyfélhez legközelebb lévő POP-gyorsítótárban van, és érvényes élettartammal (TTL) rendelkezik. Az ÉLETTARTAMot a következő válasz fejlécek határozzák meg:
  
  * Gyorsítótár-vezérlő: s-maxage
  * Cache-Control: Max-Age
  * Lejárat
* TCP_MISS: ez az állapot azt jelzi, hogy a kért eszköz gyorsítótárazott verziója nem található az ügyfélhez legközelebb lévő POP-ban. Az eszközt egy forrás-vagy egy Origin Shield-kiszolgálótól kéri a rendszer. Ha a forrás-vagy a forrás-védelmi kiszolgáló egy adategységet ad vissza, a rendszer az ügyfél és a peremhálózati kiszolgálón is gyorsítótárazza. Ellenkező esetben a rendszer nem 200-as állapotkódot (például 403 Tiltott vagy 404 nem található) ad vissza.
* TCP_EXPIRED_HIT: ez az állapot akkor jelenik meg, ha egy olyan kérést, amely egy lejárt TTL-értékkel rendelkező eszközt céloz meg közvetlenül a POP-ból az ügyfélnek. Például, ha az eszköz maximális életkora lejárt. 
  
   Egy lejárt kérelem általában a forráskiszolgáló újraérvényesítési kérelmét eredményezi. TCP_EXPIRED_HIT állapot esetén a forrás-kiszolgálónak jeleznie kell, hogy az eszköz újabb verziója nem létezik. Ez a helyzet általában az eszköz Cache-Control frissítését eredményezi, és a fejléceket lejár.
* TCP_EXPIRED_MISS: ez az állapot akkor jelenik meg, ha egy lejárt gyorsítótárazott eszköz újabb verzióját kézbesíti a rendszer a POP-ból az ügyfélnek. Ez az állapot akkor fordul elő, ha egy gyorsítótárazott eszköz ÉLETTARTAMa lejárt (például lejárt a maximális korhatár), és a forráskiszolgáló az adott eszköz újabb verzióját adja vissza. Az eszköz ezt az új verzióját a gyorsítótárazott verzió helyett az ügyfél szolgáltatja. Emellett a rendszer gyorsítótárazza a peremhálózati kiszolgálón és az ügyfélen.
* CONFIG_NOCACHE: ez az állapot azt jelzi, hogy egy ügyfél-specifikus konfiguráció a peremhálózati POP megakadályozta az eszköz gyorsítótárazását.
* NINCS – ez az állapot azt jelzi, hogy a gyorsítótár tartalmának frissességi ellenőrzését nem hajtották végre.
* TCP_CLIENT_REFRESH_MISS: ez az állapot akkor jelenik meg, ha egy HTTP-ügyfél (például egy böngésző) a peremhálózati POP-ra kényszeríti egy elavult eszköz új verziójának lekérését a forrás-kiszolgálóról. Alapértelmezés szerint a kiszolgálók megakadályozzák, hogy egy HTTP-ügyfél kényszerítse a peremhálózati kiszolgálókat az eszköz új verziójának a forrás-kiszolgálóról való lekérésére.
* TCP_PARTIAL_HIT: ez az állapot akkor kerül jelentésre, ha egy bájtos tartományra vonatkozó kérelem egy részlegesen gyorsítótárazott objektum találatot eredményez. A kért bájt-tartomány azonnal kiszolgálható a POP-ból az ügyfélnek.
* Nem GYORSÍTÓTÁRAZható: ez az állapot akkor jelenik meg, ha egy adott eszköz `Cache-Control` és `Expires` fejléc azt jelzi, hogy nem szabad gyorsítótárazni a pop-on vagy a http-ügyfélen. Az ilyen típusú kérelmeket a rendszer a forráskiszolgálón kézbesíti.

## <a name="cache-hit-ratio"></a>Gyorsítótár-találati arány
Ez a jelentés a gyorsítótárból közvetlenül kiszolgált gyorsítótárazott kérelmek százalékos arányát jelzi.

A jelentés a következő adatokat tartalmazza:

* A kért tartalom gyorsítótárazva lett a kérelmezőhöz legközelebb lévő POP-on.
* A kérést közvetlenül a hálózat szélétől kézbesítették.
* A kérelem nem igényelt újraérvényesítést a forrás-kiszolgálóval.

A jelentés nem tartalmazza a következőket:

* Ország/régió szűrési lehetőségei miatt megtagadott kérelmek.
* Olyan eszközökre vonatkozó kérelmek, amelyek fejlécei azt jelzik, hogy nem szabad gyorsítótárazni őket. A, a `Cache-Control: private` `Cache-Control: no-cache` , a vagy a fejléc például `Pragma: no-cache` megakadályozza egy eszköz gyorsítótárazását.
* A részlegesen gyorsítótárazott tartalomhoz tartozó bájtos tartományra vonatkozó kérelmek.

A képlet a következő: (TCP_ találat/(TCP_ HIT + TCP_MISS)) * 100

![Gyorsítótár-találatok aránya jelentés](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6-adatátvitel
Ez a jelentés a forgalom használatának eloszlását mutatja az IPV4-ben és az IPV6-ban.

![IPV4/IPV6-adatátvitel](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Megfontolandó szempontok
A jelentéseket csak az elmúlt 18 hónap során lehet létrehozni.

