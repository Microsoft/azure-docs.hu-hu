---
title: 'Azure bejárati ajtó: gyorsítótárazás'
description: Ez a cikk segít megérteni az Azure bejárati ajtó standard/Premium viselkedését olyan útválasztási szabályokkal, amelyeken engedélyezve van a gyorsítótárazás.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100156"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Gyorsítótárazás az Azure-előtérben standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ebből a cikkből megtudhatja, hogy a bejárati ajtó standard/prémium (előzetes) útvonalai és a szabálykészlet hogyan viselkedik, ha engedélyezve van a gyorsítótárazás. Az Azure bejárati ajtó egy modern Content Delivery Network (CDN), amely dinamikus hely gyorsításával és terheléselosztással rendelkezik.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="delivery-of-large-files"></a>Nagyméretű fájlok kézbesítése

A bejárati ajtó standard/prémium (előzetes verzió) nagy méretű fájlokat biztosít a fájlméret nélküli korláthoz. A bejárati ajtó egy objektum-darabolás nevű technikát használ. Nagyméretű fájl kérése esetén a bejárati ajtó lekéri a fájl kisebb részeit a forrásból. A teljes vagy bájtos fájlra vonatkozó kérelem kézhezvétele után az előtérben lévő környezet 8 MB méretű adattömbökben kéri a fájlt.

Miután a rendszer beolvasta a tömböt a bejárati ajtón, a rendszer gyorsítótárazza és azonnal kiszolgálja a felhasználót. A bejárati ajtó ezután előre lekéri a következő adatrészletet párhuzamosan. Ez az előzetes beolvasás biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), vagy az ügyfél lezárja a kapcsolatokat.

A byte-Range kérelemmel kapcsolatos további információkért olvassa el a következőt: [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A bejárati ajtó gyorsítótárba helyezi a kapott adattömböket, így a teljes fájlt nem kell gyorsítótárazni az előtérben lévő gyorsítótárban. A fájl-vagy byte-tartományokra vonatkozó kérelmeket a rendszer a gyorsítótárból kézbesíti. Ha a tömbök nem mindegyike gyorsítótárazva van, a rendszer a háttérbeli adattömböket kéri le. Ez az optimalizálás a forrás azon képességére támaszkodik, amely támogatja a bájtok közötti kérelmeket. Ha a forrás nem támogatja a bájtok közötti kérelmeket, ez az optimalizálás nem érvényes.

## <a name="file-compression"></a>Fájltömörítés

A teljesítmény javítása érdekében tömörítse a fájlokat az Azure-beli bejárati ajtón.

## <a name="query-string-behavior"></a>Lekérdezési karakterlánc viselkedése

A bejárati ajtó segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazza a fájlokat a lekérdezési karakterláncot tartalmazó webes kérelmek esetében. Lekérdezési karakterláncot tartalmazó webes kérelem esetén a lekérdezési karakterlánc a kérelemnek a kérdőjel (?) utáni részét jelöli. A lekérdezési karakterláncok tartalmazhatnak egy vagy több kulcs-érték párokat, amelyekben a mező nevét és értékét egy egyenlőségjel (=) választja el egymástól. A kulcs-érték párokat egy jel (&) választja el egymástól. Például: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár szerepel, a rendelésük nem számít.

* **Lekérdezési karakterláncok figyelmen kívül hagyása**: ebben a módban a bejárati ajtó továbbítja a lekérdezési karakterláncokat a kérelmezőtől az első kérelem forrására, és gyorsítótárazza az adategységet. Az adategység-környezetből kiszolgált összes, az eszközre irányuló kérelem figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz lejár.

* **Gyorsítótár – minden egyedi URL-cím**: ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot, a saját gyorsítótárral rendelkező egyedi objektumként lesz kezelve. Például a (z) rendszerre irányuló kérelem forrására adott válasz a `www.example.ashx?q=test1` bejárati ajtón található, és az ugyanazon lekérdezési sztringtel rendelkező gyorsítótárak esetében visszatért. A szolgáltatásra irányuló kérelmet `www.example.ashx?q=test2` külön eszközként gyorsítótárazza a saját élettartama beállítással.
* A szabálykészlet használatával is megadhatja a **gyorsítótár kulcs lekérdezési karakterláncának** viselkedését, a megadott paramétereket belefoglalhatja vagy kizárhatja a gyorsítótár kulcsának létrehozásakor. Az alapértelmezett gyorsítótár-kulcs például a következő:/Foo/Image/asset.html, a mintavételi kérelem pedig `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Van egy szabálykészlet szabály a "userid" lekérdezési karakterlánc kizárásához. Ezután a lekérdezési karakterlánc cache-Key lenne `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Gyorsítótár kiürítése

Tekintse át a gyorsítótár kiürítését ismertető témakört.

## <a name="cache-expiration"></a>Gyorsítótár lejárata
A következő fejlécek sorrendje határozza meg, hogy mennyi ideig tárolja a rendszer az elemeket a gyorsítótárban:</br>
1. Gyorsítótár-vezérlő: s-maxage =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Lejár \<http-date>

Cache-Control a válasz fejléceit, amelyek azt jelzik, hogy a válasz nem lesz gyorsítótárazva, például a Cache-Control: Private, Cache-Control: no-cache és Cache-Control: No-Store tiszteletben.  Ha nincs Cache-Control, az alapértelmezett viselkedés az, hogy a bejárati ajtó X időtartamra gyorsítótárazza az erőforrást. Ahol az X véletlenszerűen 1 – 3 nap között lesz kiválasztva.

## <a name="request-headers"></a>Kérésfejlécek

A következő kérelmek fejléceit a rendszer nem továbbítja a forrásnak a gyorsítótárazás használatakor.
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>Gyorsítótár időtartama

A gyorsítótár időtartama konfigurálható a Szabálykészletban. A gyorsítótár időtartamának beállítása a szabályok szerint beállítás a gyorsítótár valódi felülbírálása. Ez azt jelenti, hogy a felülbírálási értéket fogja használni, függetlenül attól, hogy milyen a forrás válasz fejléce.

## <a name="next-steps"></a>Következő lépések

* További információ a [szabálykészlet egyeztetési feltételeiről](concept-rule-set-match-conditions.md)
* További információ a [szabálykészlet műveleteiről](concept-rule-set-actions.md)