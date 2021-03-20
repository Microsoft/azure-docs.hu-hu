---
title: Az Azure Traffic Manager működése | Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Traffic Manager irányítja a forgalmat a webalkalmazások nagy teljesítményének és rendelkezésre állásának biztosítása érdekében
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: a1e1bd107e8b3b9209f99d1abfc4d7e391c3c4a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184339"
---
# <a name="how-traffic-manager-works"></a>A Traffic Manager működése

Az Azure Traffic Manager segítségével szabályozhatja az alkalmazás-végpontok forgalmának eloszlását. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás.

Traffic Manager két fő előnyt biztosít:

- Forgalom eloszlása a különböző [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) egyike szerint
- [Végpontok állapotának](traffic-manager-monitoring.md) és automatikus feladatátvételének folyamatos figyelése, ha a végpontok meghiúsulnak

Amikor egy ügyfél megkísérel a szolgáltatáshoz kapcsolódni, először IP-címmé kell feloldania a szolgáltatás DNS-nevét. Az ügyfél ez után ehhez az IP-címhez kapcsolódva éri el a szolgáltatást.

**A legfontosabb szempont, hogy Traffic Manager a DNS szintjén működik.**  Traffic Manager a DNS használatával irányítja az ügyfeleket bizonyos szolgáltatási végpontokra a forgalom-útválasztási módszer szabályai alapján. Az ügyfelek **közvetlenül** csatlakoznak a kiválasztott végponthoz. Traffic Manager nem proxy vagy átjáró. Traffic Manager nem látja az ügyfél és a szolgáltatás közötti adatforgalmat.

## <a name="traffic-manager-example"></a>Traffic Manager példa

A contoso Corp egy új Partner portált fejlesztett ki. A portál URL-címe: `https://partners.contoso.com/login.aspx` . Az alkalmazás az Azure három régiójában fut. A rendelkezésre állás javítása és a globális teljesítmény maximalizálása érdekében a Traffic Manager segítségével terjesztik az ügyfelek forgalmát a legközelebbi elérhető végpontra.

Ennek a konfigurációnak a megvalósításához a következő lépéseket kell végrehajtania:

1. A szolgáltatás három példányának üzembe helyezése. A központi telepítések DNS-nevei: "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" és "contoso-asia.cloudapp.net".
1. Hozzon létre egy "contoso.trafficmanager.net" nevű Traffic Manager-profilt, és konfigurálja úgy, hogy a "Performance" Traffic-Routing metódust használja a három végponton.
1. Konfigurálja a "partners.contoso.com" nevű hiúság-tartománynevet, hogy a "contoso.trafficmanager.net" értékre mutasson egy DNS CNAME rekord használatával.

![Traffic Manager DNS-konfiguráció][1]

> [!NOTE]
> Ha a hiúság tartományát az Azure Traffic Manager használatával használja, CNAME-tartománynevet kell használnia a Traffic Manager tartománynevére. A DNS-szabványok nem teszik lehetővé, hogy a tartomány "APEX" (vagy root) részén hozzon létre egy CNAME-t. Így nem hozhat létre CNAME-t a "contoso.com" (más néven "Naked") tartományhoz. A "contoso.com" (például "www.contoso.com") alatti tartományhoz csak CNAME-t lehet létrehozni. A korlátozás megkerülése érdekében javasoljuk, hogy a DNS-tartományt [Azure DNS](../dns/dns-overview.md) és alias- [rekordok](../dns/tutorial-alias-tm.md) használatával a Traffic Manager-profilra mutasson. Másik lehetőségként egy egyszerű HTTP-átirányítást is használhat a "contoso.com" egy másik névre (például "www.contoso.com") irányuló kérések közvetlen elérésére.

### <a name="how-clients-connect-using-traffic-manager"></a>Hogyan csatlakozhatnak az ügyfelek a Traffic Manager

Az előző példát követve, amikor egy ügyfél a lapot kéri `https://partners.contoso.com/login.aspx` , az ügyfél a következő lépéseket hajtja végre a DNS-név feloldásához és a kapcsolat létesítéséhez:

![Traffic Manager használó kapcsolatok létrehozása][2]

1. Az ügyfél DNS-lekérdezést küld a konfigurált rekurzív DNS szolgáltatásnak a "partners.contoso.com" név feloldásához. A rekurzív DNS szolgáltatás, amelyet más néven "helyi DNS" szolgáltatásnak neveznek, nem futtatja közvetlenül a DNS-tartományokat. Ehelyett az ügyfél nem töltődik be a DNS-név feloldásához szükséges különböző mérvadó DNS-szolgáltatásokkal való kapcsolatfelvétel során.
2. A DNS-név feloldásához a rekurzív DNS szolgáltatás megkeresi a "contoso.com" tartományhoz tartozó névkiszolgálók nevét. Ezután kapcsolatba lép ezekkel a névkiszolgálókkal, hogy kérje a "partners.contoso.com" DNS-rekordot. A contoso.com DNS-kiszolgálók a contoso.trafficmanager.net-ra mutató CNAME rekordot adják vissza.
3. Ezután a rekurzív DNS szolgáltatás megkeresi az Azure Traffic Manager szolgáltatás által biztosított "trafficmanager.net" tartományhoz tartozó névkiszolgálók nevét. Ezután a "contoso.trafficmanager.net" DNS-rekordra vonatkozó kérelmet küld ezekre a DNS-kiszolgálókra.
4. A Traffic Manager névkiszolgálók kapják meg a kérést. A végpontok a következő alapján választhatnak:

    - Az egyes végpontok konfigurált állapota (letiltott végpontok nem lesznek visszaadva)
    - Az egyes végpontok aktuális állapota a Traffic Manager állapotának ellenőrzése alapján. További információ: [Traffic Manager Endpoint monitoring](traffic-manager-monitoring.md).
    - A kiválasztott forgalom – útválasztási módszer. További információ: [Traffic Manager útválasztási módszerek](traffic-manager-routing-methods.md).

5. A kiválasztott végpontot egy másik DNS-CNAME rekordként adja vissza a rendszer. Ebben az esetben tegyük fel, hogy a rendszer visszaadja a contoso-eu.cloudapp.net.
6. Ezután a rekurzív DNS szolgáltatás megkeresi a "cloudapp.net" tartományhoz tartozó névkiszolgálók nevét. Ezek a névkiszolgálók az "contoso-eu.cloudapp.net" DNS-rekord igénylésére szolgálnak. A rendszer egy olyan DNS-rekordot ad vissza, amely tartalmazza az EU-alapú szolgáltatási végpont IP-címét.
7. A rekurzív DNS szolgáltatás összevonja az eredményeket, és egyetlen DNS-választ ad vissza az ügyfélnek.
8. Az ügyfél megkapja a DNS-eredményeket, és csatlakozik a megadott IP-címhez. Az ügyfél közvetlenül kapcsolódik az Application Service-végponthoz, és nem a Traffic Manageron keresztül. Mivel ez egy HTTPS-végpont, az ügyfél elvégzi a szükséges SSL/TLS-kézfogást, majd egy HTTP GET kérelmet tesz elérhetővé az "/login.aspx" oldalon.

A rekurzív DNS szolgáltatás gyorsítótárazza a kapott DNS-válaszokat. Az ügyféleszközök DNS-feloldója is gyorsítótárazza az eredményt. A gyorsítótárazás lehetővé teszi, hogy a további DNS-lekérdezések gyorsabban megválaszolják a gyorsítótárból származó adatok használatával, nem pedig más névkiszolgálók lekérdezését. A gyorsítótár időtartamát az egyes DNS-rekordok élettartam (TTL) tulajdonsága határozza meg. A rövidebb értékek a gyorsítótár lejárati idejét eredményezik, így a Traffic Manager névkiszolgálók felé irányuló több időpontot is igénybe vehet. A hosszabb értékek azt jelentik, hogy hosszabb időt vehet igénybe a sikertelen végpontról érkező forgalom. Traffic Manager lehetővé teszi, hogy a Traffic Manager DNS-válaszokban használt TTL-t a 0 másodpercnél kisebb értékre konfigurálja, és az 2 147 483 647 másodpercet (az [RFC-1035-es](https://www.ietf.org/rfc/rfc1035.txt)verziónak megfelelő maximális tartományt) adja meg, így kiválaszthatja az alkalmazás igényeinek legmegfelelőbb egyensúlyt.

## <a name="faqs"></a>Gyakori kérdések

* [Milyen IP-címet Traffic Manager használni?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Milyen típusú forgalmat lehet irányítani a Traffic Manager használatával?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Traffic Manager támogatja a "Sticky" munkameneteket?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Miért látok HTTP-hibát a Traffic Manager használatakor?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Milyen hatással van a Traffic Manager használatának teljesítményére?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Milyen alkalmazás-protokollokat használhatok a Traffic Manager?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Használhatom a Traffic Managert "Naked" tartománynévvel?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [A DNS-lekérdezések kezelésekor az ügyfél alhálózati címének Traffic Manager kell lennie?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Mi a DNS-élettartam, és hogyan befolyásolja a felhasználókat?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Hogyan állítható be a TTL a Traffic Manager-válaszok esetében?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hogyan tudom megismerni a profilhoz tartozó lekérdezések mennyiségét?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Következő lépések

További információ a Traffic Manager [végpont monitorozásáról és az automatikus feladatátvételről](traffic-manager-monitoring.md).

További információ a Traffic Manager [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png