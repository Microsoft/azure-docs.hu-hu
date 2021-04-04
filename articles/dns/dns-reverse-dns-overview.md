---
title: Fordított DNS áttekintése az Azure-ban – Azure DNS
description: Ebből a képzési tervből megtudhatja, hogyan működik a fordított DNS, és hogyan használható az Azure-ban
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965612"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>A fordított DNS és a támogatás áttekintése az Azure-ban

Ez a cikk áttekintést nyújt a fordított DNS működéséről, valamint az Azure-ban támogatott fordított DNS-forgatókönyvekről.

## <a name="what-is-reverse-dns"></a>Mi a fordított DNS?

A hagyományos DNS-rekordok lehetővé teszik a DNS-nevek (például a "www.contoso.com") IP-címmé való leképezését (például 64.4.6.100).  A fordított DNS lehetővé teszi, hogy egy IP-cím (64.4.6.100) fordítását egy névre ("www.contoso.com") adja vissza.

A fordított DNS-rekordok különböző helyzetekben használatosak. Például a fordított DNS-rekordok széles körben használatosak az e-mailes levélszemét elleni küzdelemben az e-mail üzenet küldőjének ellenőrzésével.  A fogadó levelezési kiszolgáló lekéri a küldő kiszolgáló IP-címének fordított DNS-rekordját, és ellenőrzi, hogy a gazdagép engedélyezte-e e-mailek küldését a kezdeményező tartományból. 

## <a name="how-reverse-dns-works"></a>A fordított DNS működése

A fordított DNS-rekordok speciális DNS-zónákban, más néven "ARPA" zónákban vannak tárolva.  Ezek a zónák egy különálló DNS-hierarchiát alkotnak a normál hierarchiát üzemeltető tartományokkal (például "contoso.com") párhuzamosan.

Például a "www.contoso.com" DNS-rekord a "contoso.com" zónában a "www" névvel rendelkező "A" DNS-rekord használatával valósul meg.  Ez a rekord a megfelelő IP-címhez mutat, ebben az esetben a 64.4.6.100.  A névlekérdezés külön implementálva van, egy "100" nevű PTR rekord használatával a (z) "6.4.64.in-addr. arpa" zónában (vegye figyelembe, hogy az IP-címek az ARPA zónákban vannak sztornírozva.)  Ez a PTR-rekord, ha helyesen lett konfigurálva, a "www.contoso.com" névre mutat.

Ha egy szervezet IP-blokkhoz van hozzárendelve, akkor a megfelelő ARPA zóna felügyeletére is jogosult. Az Azure által használt IP-címekhez tartozó ARPA zónákat a Microsoft üzemelteti és kezeli. Az INTERNETSZOLGÁLTATÓ a saját IP-címei számára is üzemeltetheti a ARPA zónát, vagy engedélyezheti az ARPA zóna üzemeltetését egy tetszőleges DNS-szolgáltatásban, például Azure DNS.

> [!NOTE]
> A DNS-lekérdezések és a fordított DNS-keresések továbbítása külön, párhuzamos DNS-hierarchiákban történik. A (z) "www.contoso.com" névlekérdezése **nem** a (z) "contoso.com" zónában található, hanem a megfelelő IP-címterület arpa zónájában van tárolva. Az IPv4-és IPv6-címtartomány külön zónákat használ.

### <a name="ipv4"></a>IPv4

Egy IPv4 névkeresési zóna nevének a következő formátumúnak kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Ha például egy fordított zónát hoz létre, amely a 192.0.2.0/24 előtagban található IP-címekkel rendelkező gazdagépek rekordjait tárolja, a zóna neve a cím hálózati előtagjának (192.0.2) elkülönítésével jön létre, majd megfordítja a sorrendet (2.0.192), és hozzáadja az utótagot `.in-addr.arpa` .

|Alhálózat osztálya|Hálózati előtag  |Fordított hálózati előtag  |Szabványos utótag  |Fordított zóna neve |
|-------|----------------|------------|-----------------|---------------------------|
|A osztály|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|B osztály|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|C osztály|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Osztály nélküli IPv4-delegálás

Bizonyos esetekben a szervezet számára lefoglalt IP-címtartomány kisebb, mint a C osztály (/24) tartománya. Ebben az esetben az IP-címtartomány nem esik a zóna-hierarchián belüli zónára `.in-addr.arpa` , ezért nem delegálható alárendelt zónának.

Ehelyett egy másik mechanizmust használ a névkeresési (PTR) rekordok vezérlésének átadására egy dedikált DNS-zónára. Ez a mechanizmus minden IP-címtartomány esetében delegál egy alárendelt zónát, majd a tartomány egyes IP-címeit az adott alárendelt zónához tartozó CNAME rekordok használatával képezi le.

Tegyük fel például, hogy egy szervezet az INTERNETSZOLGÁLTATÓja által biztosított IP-címtartomány 192.0.2.128/26. Ez 64 IP-címet képvisel a 192.0.2.128 és a 192.0.2.191 között. A fordított DNS ehhez a tartományhoz a következőképpen valósítható meg:
- A szervezet létrehoz egy 128-26.2.0.192.in-addr. arpa nevű névkeresési zónát. A (z) "128-26" előtag a (z) C (/24) tartományba tartozó szervezethez rendelt hálózati szegmenst jelöli.
- Az INTERNETSZOLGÁLTATÓ NS-rekordokat hoz létre a fenti zónához tartozó DNS-delegálás beállításához a C osztály szülő zónájából. Emellett CNAME rekordokat hoz létre a szülő (C osztályú) névkeresési zónában, és az IP-címtartomány minden IP-címét hozzárendeli a szervezet által létrehozott új zónához:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- A szervezet ezután kezeli az egyes PTR-rekordokat a saját alárendelt zónáján belül.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
A "192.0.2.129" IP-cím fordított megkeresése egy "129.2.0.192.in-addr. arpa" nevű PTR-rekord lekérdezése. Ez a lekérdezés a szülő zónában lévő CNAME rekordon keresztül oldja fel a feladatait a gyermek zónában lévő PTR-rekordra.

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna nevének a következő formátumúnak kell lennie: `<IPv6 network prefix in reverse order>.ip6.arpa`

Például:. Ha fordított zónát hoz létre a 2001: DB8:1000: ABDC::/64 előtaggal rendelkező gazdagépek rekordjainak tárolására, a zóna neve a cím hálózati előtagjának elkülönítésével jön létre (2001: DB8: ABDC::). Ezután bontsa ki az IPv6-hálózati előtagot a [nulla tömörítés](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))eltávolításához, ha az IPv6-cím előtagjának lerövidítését használta (2001:0db8: ABDC: 0000::). Megfordíthatja a sorrendet, ha az előtagban lévő összes hexadecimális szám között elválasztó pontot használ a fordított hálózati előtag ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) létrehozásához és az utótag hozzáadásához `.ip6.arpa` .


|Hálózati előtag  |Kibontott és fordított hálózati előtag |Szabványos utótag |Fordított zóna neve  |
|---------|---------|---------|---------|
|2001: DB8: ABDC::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . IP6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: DB8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . IP6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-támogatás fordított DNS-hez

Az Azure két különálló forgatókönyvet támogat a fordított DNS-sel kapcsolatban:

**Az IP-cím blokkjának megfelelő névkeresési zóna üzemeltetése.**
A Azure DNS használhatók a [névkeresési zónák üzemeltetéséhez és az egyes fordított DNS-keresésekhez tartozó PTR-rekordok kezeléséhez](dns-reverse-dns-hosting.md)az IPv4 és az IPv6 esetében is.  A névkeresési (ARPA) zóna létrehozásának folyamata, a delegálás beállítása és a PTR-rekordok konfigurálása megegyezik a normál DNS-zónákhoz.  Az egyetlen különbség, hogy a delegálást a DNS-regisztráló helyett az INTERNETSZOLGÁLTATÓn keresztül kell konfigurálni, és csak a PTR típusú bejegyzéstípust kell használni.

**Konfigurálja a fordított DNS-rekordot az Azure-szolgáltatáshoz rendelt IP-címhez.** Az Azure lehetővé teszi [Az Azure-szolgáltatáshoz lefoglalt IP-címek fordított keresésének konfigurálását](dns-reverse-dns-for-azure-services.md).  Ezt a névkeresési lekérdezést az Azure a megfelelő ARPA zónában lévő PTR-rekordként konfigurálja.  Az Azure által használt összes IP-tartománynak megfelelő ARPA zónákat a Microsoft üzemelteti

## <a name="next-steps"></a>Következő lépések

A fordított DNS-sel kapcsolatos további információkért lásd: [fordított DNS-keresés a wikipedia-ben](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [futtathatja a névkeresési zónát az internetszolgáltató által hozzárendelt IP-címtartomány Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Ismerje meg, hogyan [kezelheti az Azure-szolgáltatások fordított DNS-rekordjait](dns-reverse-dns-for-azure-services.md).