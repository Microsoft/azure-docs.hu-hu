---
title: Az Azure bejárati ajtó standard/prémium konfigurációs problémáinak elhárítása
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet elhárítani az Azure bejárati ajtó standard/prémium példányával kapcsolatos gyakori problémák némelyikét.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100204"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Gyakori útválasztási problémák elhárítása az Azure bejárati ajtó standard/prémium csomaggal

Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli bejárati konfigurációval kapcsolatos gyakori útválasztási problémákat.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 válasz az Azure bejárati ajtót néhány másodperc múlva

### <a name="symptom"></a>Hibajelenség

* Az Azure-beli bejárati ajtón keresztül küldött rendszeres kérelmek nem lesznek sikeresek. Az Azure-beli bejárati ajtón keresztül az 503-es hibaüzenetek lesznek elérhetők.
* Az Azure-bejárati ajtó meghibásodása általában körülbelül 30 másodperc után jelenik meg.

### <a name="cause"></a>Ok

A probléma oka a következő két dolog egyike lehet:
 
* A forrás a beállított időkorlátnál hosszabb időt vesz igénybe (az alapértelmezett érték 30 másodperc), hogy megkapja az Azure bejárati ajtótól érkező kérést.
* Az Azure bejárati ajtótól érkező kérésre adott válasz küldéséhez szükséges idő az időtúllépési értéknél hosszabb időt vesz igénybe. 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

* Küldje el a kérést közvetlenül a háttérbe (az Azure bejárati ajtó nélkül). Megtudhatja, milyen hosszan tart a háttérbeli válaszadás.
* Küldje el a kérést az Azure bejárati ajtaján keresztül, és ellenőrizze, hogy van-e 503-válasz. Ha nem, lehetséges, hogy a probléma nem időtúllépési hiba. Vegye fel a kapcsolatot az ügyfélszolgálattal.
* Ha az Azure-beli bejárati ajtón keresztül halad, a 503-es hibakódot adja meg, konfigurálja az Azure-beli `sendReceiveTimeout` bejárati ajtó értékét. Az alapértelmezett időtúllépés akár 4 perc is kiterjeszthető (240 másodperc). A beállítás a `Endpoint Setting` és a nevű `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Az egyéni tartományba küldött kérések 400 állapotkódot adnak vissza.

### <a name="symptom"></a>Hibajelenség

* Létrehozott egy Azure-beli előtér-példányt, de a tartományra vagy a előtér-gazdagépre irányuló kérelem HTTP 400 állapotkódot ad vissza.
* Létrehozta az egyéni tartományhoz tartozó DNS-hozzárendelést a konfigurált előtér-gazdagéphez. Egy kérelem küldése az egyéni tartomány állomásneve számára azonban HTTP 400 állapotkódot ad vissza. Nem úgy tűnik, hogy a konfigurált háttérre irányítja.

### <a name="cause"></a>Ok

A probléma akkor fordul elő, ha nem konfigurált útválasztási szabályt a előtér-gazdagépként hozzáadott egyéni tartományhoz. Explicit módon hozzá kell adni egy útválasztási szabályt az adott előtér-gazdagéphez. Ez akkor is igaz, ha egy útválasztási szabály már konfigurálva van az előtér-gazdagéphez az Azure bejárati ajtó altartományában (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Adja hozzá az egyéni tartomány útválasztási szabályát a kiválasztott származási csoport felé irányuló közvetlen forgalomhoz.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Az Azure bejárati ajtó nem irányítja át a HTTP protokollt a HTTPS

### <a name="symptom"></a>Hibajelenség

Az Azure bejárati ajtaján olyan útválasztási szabály van, amely átirányítja a HTTP protokollt a HTTPS-re, de a tartományhoz való hozzáférés továbbra is a HTTP protokollt használja.

### <a name="cause"></a>Ok

Ez akkor fordulhat elő, ha nem megfelelően konfigurálta az útválasztási szabályokat az Azure-előtérben. Az aktuális konfiguráció alapvetően nem specifikus, és lehetnek ütköző szabályok.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>A előtér-állomásnévre irányuló kérelem a 411-as kódú állapotkódot adja vissza.

### <a name="symptom"></a>Hibajelenség

Létrehozott egy Azure Door standard/prémium példányt, és konfigurált egy előtér-gazdagépet, legalább egy kezdőpontot tartalmazó származási csoportot, valamint egy útválasztási szabályt, amely összekapcsolja a előtér-gazdagépet a forrás csoporttal. Úgy tűnik, hogy a tartalom nem érhető el, ha egy kérelem a beállított előtér-gazdagépre kerül, mert a rendszer HTTP 411-állapotkódot ad vissza.

Az ezekre a kérelmekre adott válaszok a válasz törzsében is tartalmazhatnak egy HTML-hibaüzenetet, amely tartalmaz egy magyarázó utasítást is. Példa: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Ok

Ennek a tünetnek több lehetséges oka van. Az általános ok az, hogy a HTTP-kérelem nem teljes mértékben RFC-kompatibilis. 

A nem megfelelőségi példa egy `POST` `Content-Length` vagy egy `Transfer-Encoding` fejléc (például a használata) nélkül elküldhető kérelem `curl -X POST https://example-front-door.domain.com` . Ez a kérelem nem felel meg az [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)-ben meghatározott követelményeknek. Az Azure-beli bejárati ajtó blokkolja a HTTP 411-es választ.

Ez a viselkedés elkülönül az Azure bejárati WAF (webalkalmazási tűzfal) funkcióival. Jelenleg nem lehet letiltani ezt a viselkedést. Az összes HTTP-kérelemnek meg kell felelnie a követelményeknek, még akkor is, ha a WAF funkció nincs használatban.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

- Győződjön meg arról, hogy a kérések megfelelnek a szükséges RFC-k követelményeinek.

- Jegyezze fel a kérelemre adott válaszként visszaadott HTML-üzenetek törzsét. Az üzenettörzs gyakran ismerteti pontosan, *hogy* a kérése nem megfelelő.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
