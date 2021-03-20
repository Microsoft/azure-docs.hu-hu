---
title: Az Azure bejárati ajtó konfigurációs problémáinak elhárítása
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el az Azure-beli előtérben lévő példányra vonatkozó gyakori problémák némelyikének elhárítását.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629988"
---
# <a name="troubleshooting-common-routing-problems"></a>Gyakori útválasztási problémák elhárítása

Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli bejárati konfigurációval kapcsolatos gyakori útválasztási problémákat.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 válasz az Azure bejárati ajtót néhány másodperc múlva

### <a name="symptom"></a>Hibajelenség

* Az Azure-beli bejárati ajtón keresztül küldött rendszeres kérelmek nem lesznek sikeresek. Az Azure-beli bejárati ajtón keresztül az 503-es hibaüzenetek lesznek elérhetők.
* Az Azure-bejárati ajtó meghibásodása általában körülbelül 30 másodperc után jelenik meg.

### <a name="cause"></a>Ok

A probléma oka a következő két dolog egyike lehet:
 
* A háttérrendszer a beállított időtúllépésnél hosszabb időt vesz igénybe (az alapértelmezett érték 30 másodperc), hogy megkapja az Azure bejárati ajtótól érkező kérést.
* Az Azure bejárati ajtótól érkező kérésre adott válasz küldéséhez szükséges idő az időtúllépési értéknél hosszabb időt vesz igénybe. 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

* Küldje el a kérést közvetlenül a háttérbe (az Azure bejárati ajtó nélkül). Megtudhatja, milyen hosszan tart a háttérbeli válaszadás.
* Küldje el a kérést az Azure bejárati ajtaján keresztül, és ellenőrizze, hogy van-e 503-válasz. Ha nem, lehetséges, hogy a probléma nem időtúllépési hiba. Vegye fel a kapcsolatot az ügyfélszolgálattal.
* Ha az Azure-beli bejárati ajtón keresztül halad, a 503-es hibakódot adja meg, konfigurálja az Azure-beli `sendReceiveTimeout` bejárati ajtó értékét. Az alapértelmezett időtúllépés akár 4 perc is kiterjeszthető (240 másodperc). A beállítás a `backendPoolSettings` és a nevű `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Az egyéni tartományba küldött kérések 400 állapotkódot adnak vissza.

### <a name="symptom"></a>Hibajelenség

* Létrehozott egy Azure-beli előtér-példányt, de a tartományra vagy a előtér-gazdagépre irányuló kérelem HTTP 400 állapotkódot ad vissza.
* Létrehozta az egyéni tartományhoz tartozó DNS-hozzárendelést a konfigurált előtér-gazdagéphez. Egy kérelem küldése az egyéni tartomány állomásneve számára azonban HTTP 400 állapotkódot ad vissza. Nem úgy tűnik, hogy a konfigurált háttérre irányítja.

### <a name="cause"></a>Ok

A probléma akkor fordul elő, ha nem konfigurált útválasztási szabályt a előtér-gazdagépként hozzáadott egyéni tartományhoz. Explicit módon hozzá kell adni egy útválasztási szabályt az adott előtér-gazdagéphez. Ez akkor is igaz, ha egy útválasztási szabály már konfigurálva van az előtér-gazdagéphez az Azure bejárati ajtó altartományában (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Adja hozzá az egyéni tartomány útválasztási szabályát a kiválasztott háttérbeli készletre irányuló közvetlen forgalomhoz.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Az Azure bejárati ajtó nem irányítja át a HTTP protokollt a HTTPS

### <a name="symptom"></a>Hibajelenség

Az Azure bejárati ajtaján olyan útválasztási szabály van, amely átirányítja a HTTP protokollt a HTTPS-re, de a tartományhoz való hozzáférés továbbra is a HTTP protokollt használja.

### <a name="cause"></a>Ok

Ez akkor fordulhat elő, ha nem megfelelően konfigurálta az útválasztási szabályokat az Azure-előtérben. Az aktuális konfiguráció alapvetően nem specifikus, és lehetnek ütköző szabályok.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>A előtér-állomásnévre irányuló kérelem egy 404 állapotkódot ad vissza.

### <a name="symptom"></a>Hibajelenség

Létrehozott egy Azure-beli előtér-példányt egy előtér-gazdagép, egy háttér-készlet és legalább egy háttérrendszer használatával, valamint egy útválasztási szabályt, amely összekapcsolja a frontend-gazdagépet a háttér-készlettel. A tartalom nem érhető el, ha a konfigurált előtér-gazdagépre irányuló kérést végez. Ennek eredményeképpen a kérelem egy HTTP 404 állapotkódot ad vissza.

### <a name="cause"></a>Ok

A tünet több lehetséges oka is lehet:

* A háttér nem egy nyilvános háttérrendszer, és nem látható az Azure bejárati ajtaján.
* Helytelenül van konfigurálva a háttérrendszer, így az Azure bejárati ajtaja nem megfelelő kérést küld. Más szóval a háttérrendszer csak HTTP-t fogad el, és nem engedélyezte a HTTPS-t. Így az Azure bejárati ajtaja HTTPS-kérelmek továbbítására tesz kísérletet.
* A háttérrendszer elutasítja a háttérben a kérelemmel továbbított állomásfejléc-fejlécet.
* A háttérrendszer konfigurációja még nincs teljesen telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

* A központi telepítés időpontjának megtekintése:
   * Győződjön meg arról, hogy legalább 10 percet várt a konfiguráció üzembe helyezése előtt.

* Keresse meg a háttér beállításait:
    * Nyissa meg azt a háttér-készletet, amelyhez a kérést át kell irányítani. (Ez attól függ, hogy az útválasztási szabály hogyan van konfigurálva.) Ellenőrizze, hogy helyesek-e a háttérbeli gazdagép típusa és a háttérbeli gazdagép neve. Ha a háttérrendszer egy egyéni gazdagép, győződjön meg arról, hogy helyesen írta-e be. 

    * Keresse meg a HTTP-és HTTPS-portokat. A legtöbb esetben a 80-es és a 443-as (ill.) érték helyes, és nincs szükség módosításra. Van azonban esély arra, hogy a háttérrendszer ne legyen így konfigurálva, és egy másik portot figyel.

    * Győződjön meg arról, hogy a háttérrendszer azon hátteréhez van konfigurálva, amelyhez a előtér-gazdagépnek útválasztási művelettel kell rendelkeznie. A legtöbb esetben ez a fejléc nem egyezhet meg a háttér-gazdagép nevével. A helytelen érték azonban különféle HTTP-4xx okozhat, ha a háttérrendszer más értéket vár el. Ha megadja a háttér IP-címét, lehetséges, hogy a háttérbeli állomásfejléc-fejlécet be kell állítania a háttér állomásneve.

* Keresse meg az útválasztási szabály beállításait:
    * Nyissa meg azt az útválasztási szabályt, amely a kérdéses előtér-állomásnévről a háttér-készletre irányítja. Győződjön meg arról, hogy az elfogadott protokollok megfelelően vannak konfigurálva a kérés továbbításakor. Az **elfogadott protokollok** mező határozza meg, hogy az Azure bejárati ajtó mely kérelmeket fogadja el. A továbbítási protokoll meghatározza, hogy az Azure-beli bejárati ajtó milyen protokollt használjon a kérésnek a háttérbe való továbbításához.
      
      Ha például a háttérrendszer csak HTTP-kérelmeket fogad el, a következő konfigurációk érvényesek lesznek:
            
      * Az elfogadott protokollok a HTTP és a HTTPS. A továbbítási protokoll a HTTP. Egy egyezési kérelem nem fog működni, mert a HTTPS egy engedélyezett protokoll. Ha egy kérelem HTTPS-ként jelentkezett be, az Azure-beli bejárati ajtó a HTTPS használatával próbálkozik a továbbítással.

      * Az elfogadott protokoll a HTTP. A továbbítási protokoll vagy egy egyezési kérelem vagy HTTP.
    - Az **URL-cím újraírása** alapértelmezés szerint le van tiltva. Ez a mező csak akkor használható, ha le szeretné szűkíteni az elérhetővé tenni kívánt háttérrendszer-erőforrások hatókörét. Ha ez a mező le van tiltva, az Azure bevezető ajtaja továbbítja ugyanazt a kérési útvonalat, amelyet fogad. Ezt a mezőt helytelenül lehet beállítani. Így ha az Azure bevezető ajtaja olyan erőforrást kér a háttérből, amely nem érhető el, egy HTTP 404 állapotkódot ad vissza.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>A előtér-állomásnévre irányuló kérelem a 411-as kódú állapotkódot adja vissza.

### <a name="symptom"></a>Hibajelenség

Létrehozott egy Azure Front Door-példányt, és konfigurált egy előtér-gazdagépet, egy háttér-készletet legalább egy háttérrel, valamint egy útválasztási szabályt, amely összekapcsolja a frontend-gazdagépet a háttér-készlettel. Úgy tűnik, hogy a tartalom nem érhető el, ha egy kérelem a beállított előtér-gazdagépre kerül, mert a rendszer HTTP 411 állapotkódot ad vissza.

Az ezekre a kérelmekre adott válaszok a válasz törzsében is tartalmazhatnak egy HTML-hibaüzenetet, amely tartalmaz egy magyarázó utasítást is. Példa: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Ok

Ennek a tünetnek több lehetséges oka van. Ennek általános oka, hogy a HTTP-kérelem nem teljes mértékben RFC-kompatibilis. 

A nem megfelelőségi példa egy `POST` `Content-Length` vagy egy `Transfer-Encoding` fejléc (például a használata) nélkül elküldhető kérelem `curl -X POST https://example-front-door.domain.com` . Ez a kérelem nem felel meg az [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)-ben meghatározott követelményeknek. Az Azure-beli bejárati ajtó blokkolja a HTTP 411-es választ.

Ez a viselkedés elkülönül az Azure bejárati WAF (webalkalmazási tűzfal) funkcióival. Jelenleg nem lehet letiltani ezt a viselkedést. Az összes HTTP-kérelemnek meg kell felelnie a követelményeknek, még akkor is, ha a WAF funkció nincs használatban.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

- Győződjön meg arról, hogy a kérések megfelelnek a szükséges RFC-k követelményeinek.

- Jegyezze fel a kérelemre adott válaszként visszaadott HTML-üzenetek törzsét. Az üzenettörzs gyakran ismerteti pontosan, *hogy* a kérése nem megfelelő.
