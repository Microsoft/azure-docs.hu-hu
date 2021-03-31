---
title: Egyéni jelentések a Verizontól | Microsoft Docs
description: 'A CDN használati mintáit a következő jelentések segítségével tekintheti meg: sávszélesség, átvitt adatok, találatok, gyorsítótár állapota, gyorsítótár találati aránya, IPV4/IPV6-adatok átvitele.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84888708"
---
# <a name="custom-reports-from-verizon"></a>Egyéni jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Verizon egyéni jelentéseinek használata a Verizon-profilok kezelése portálon keresztül meghatározhatja, hogy milyen típusú adatokat kell gyűjteni a peremhálózati CNAME-jelentésekhez.


## <a name="accessing-verizon-custom-reports"></a>A Verizon egyéni jelentéseinek elérése
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil kezelése gomb](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Vigye az egérmutatót az **elemzés** lapra, majd vigye az egérmutatót az **egyéni jelentések** menü fölé. Kattintson a **PEREMHÁLÓZATI CNAME** elemre.
   
    ![CDN felügyeleti portál – egyéni jelentések menü](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Edge-CNAME egyéni jelentés
A peremhálózati CNAME egyedi jelentés a peremhálózati CNAME-re vonatkozó látogatottsági és adatátviteli statisztikákat tartalmaz, amelyeken engedélyezve van az egyéni jelentések naplózása. A peremhálózati CNAME rekordok Azure CDN végponti állomásnévből és az összes hozzá tartozó egyéni tartománybeli állomásnévből állnak. 

Az egyéni jelentések adatnaplózása egy órával azután kezdődik, hogy engedélyezte a peremhálózati CNAME egyéni jelentéskészítési képességét. Megtekintheti a Jelentésadatok egy adott platformra vagy minden platformra vonatkozó peremhálózati CNAME-jelentés létrehozásával. A jelentés lefedettsége csak azokra a peremhálózati CNAME-re korlátozódik, amelyek esetében a megadott időszakban a rendszer az egyéni jelentési adatokat gyűjtötte. A peremhálózati CNAME-jelentés a metrikák beállításban meghatározott metrika szerint egy gráfból és egy adattáblából áll a 10 legfelső szintű CNAME rekordhoz. 

Hozzon létrehoz egy egyéni jelentést a következő jelentési lehetőségek meghatározásával:

- Metrikák: a következő lehetőségek támogatottak:

   - Hits: az egyéni jelentéskészítési képességet engedélyező peremhálózati CNAME-re irányuló kérelmek teljes számát jelzi. Ez a metrika nem tartalmazza az ügyfélnek visszaadott állapotkódot.

   - Átvitt adatok: az Edge-kiszolgálókról a HTTP-ügyfelekre (például Webböngészőkre) átvitt adatok teljes mennyiségét adja meg az egyéni jelentéskészítési képességet engedélyező peremhálózati CNAME-re irányuló kérésekhez. Az átvitt adatmennyiség kiszámítása a HTTP-válaszok fejlécének a válasz törzsbe való hozzáadásával történik. Ennek eredményeképpen az egyes eszközök számára továbbított adatok mennyisége nagyobb, mint a tényleges fájlméret.

- Csoportosítások: meghatározza, hogy milyen típusú statisztikai adatok jelenjenek meg a sávdiagram alatt. A következő lehetőségek támogatottak:

   - HTTP-válaszok kódjai: az ügyfélnek visszaadott HTTP-válasz kódja (például 200, 403 stb.) alapján rendezi a statisztikát. 

   - Gyorsítótár állapota: a statisztikákat a gyorsítótár állapota alapján rendezi.


A jelentés időtartományának beállításához válasszon ki egy előre meghatározott dátumtartományt, például a **mai** vagy **az ezen a héten** a legördülő listából, vagy válassza az **Egyéni** lehetőséget, és manuálisan adja meg a dátumtartományt a naptár ikonjaira kattintva. 

Miután kiválasztotta a dátumtartományt, kattintson az **Ugrás** gombra a jelentés létrehozásához.

Az Excel-formátumú adatexportáláshoz kattintson az **Ugrás** gomb jobb oldalán lévő Excel-szimbólumra.

![CNAME-jelentés](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME-rekordok egyéni jelentés mezői

| Mező                     | Leírás   |
|---------------------------|---------------|
| 2xx                       | A 2xx HTTP-állapotkódot (például 200 OK) eredményező, a peremhálózati CNAME azonosítóhoz (MB) tartozó kérelmek vagy továbbított adatmennyiségek teljes számát adja meg. |
| 3xx                       | A 3xx HTTP-állapotkódot (például 302 talált vagy 304 nem módosított kérelmek vagy továbbított adatmennyiségek (MB) teljes számát jelzi. |
| 4xx                       | A 4xx HTTP-állapotkódot (például 400 hibás kérés, 403 Tiltott vagy 404 nem található) a peremhálózati CNAME azonosítóhoz tartozó kérelmek vagy továbbított adatmennyiségek teljes számát jelzi. |
| 5xx                       | A 5xx HTTP-állapotkódot (például 500 belső kiszolgálóhiba vagy 502 hibás átjáró) eredményező peremhálózati CNAME-re vonatkozó kérelmek vagy továbbított adatmennyiség (MB) teljes számát jelzi. |
| Gyorsítótár találati aránya (%)               | A gyorsítótárból a kérelmezőnek kiszolgált gyorsítótárazható kérelmek százalékos arányát jelzi. |
| Gyorsítótár-találatok                | A gyorsítótárban találatot eredményező (például TCP_EXPIRED_HIT, TCP_HIT vagy TCP_PARTIAL_HIT) találatok vagy továbbított adatmennyiség (MB) teljes számát jelzi. A gyorsítótár találata akkor fordul elő, ha a kért tartalom gyorsítótárazott verziója található. |
| Továbbított adatátvitel (MB)     | Az átvitt adatok (MB) teljes mennyiségét jelzi a peremhálózati kiszolgálókról a peremhálózati CNAME-hez tartozó HTTP-ügyfelek (böngészők) számára. Az átvitt adatmennyiség kiszámítása úgy történik, hogy hozzáadja a HTTP-válasz fejléceit a válasz törzséhez. Ennek eredményeképpen az egyes eszközök számára továbbított adatok mennyisége nagyobb, mint a tényleges fájlméret. |
| Description               | A peremhálózati CNAME azonosító azonosítása az állomásnév alapján |
| Találatok                      | A peremhálózati CNAME-re irányuló kérelmek teljes számát jelzi. |
| Hiányzik                    | Azt a kérések vagy adatátviteli (MB) értéket adja meg a peremhálózati CNAME rekordhoz, amely a gyorsítótár-kihagyás következtében (például TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS vagy TCP_MISS). A gyorsítótárból való kihagyás akkor következik be, amikor a kért tartalom nem lett gyorsítótárazva a kérést tiszteletben lévő peremhálózati kiszolgálón. | 
| Nincs gyorsítótár                  | A CONFIG_NOCACHE gyorsítótár-állapotkódot eredményező, a peremhálózati CNAME azonosítóhoz tartozó kérelmek vagy továbbított adatátvitel (MB) teljes számát jelzi.  |
| Egyéb                     | Azt jelzi, hogy hány kérelem vagy adatátvitel (MB) van megadva a peremhálózati CNAME azonosítóhoz, amely egy HTTP-állapotkódot eredményez, amely a 2xx-5xx tartományon kívül esik. |
| Platform                  | A peremhálózati CNAME forgalmát kezelő platformot jelzi. |
| Nincs hozzárendelve               | Azt jelzi, hogy hány kérelem vagy adatátvitel (MB) van megadva a peremhálózati CNAME azonosítóhoz, amelynél a gyorsítótár állapotkód vagy a HTTP-állapotkód adatai nem lettek naplózva.  |
| Nem gyorsítótárazható               | A nem GYORSÍTÓTÁRAZható gyorsítótár-állapotkódot eredményező, a peremhálózati CNAME azonosítóhoz tartozó kérelmek vagy továbbított adatátvitel (MB) teljes számát adja meg.  |


## <a name="considerations"></a>Megfontolandó szempontok
A jelentések csak az elmúlt 18 hónapban hozhatók létre.

