---
title: A Azure CDN Rules Engine HTTP-változói | Microsoft Docs
description: További információ a HTTP-változókról, amelyek lehetővé teszik a HTTP-kérések és a válaszok metaadatainak beolvasását bizonyos szabályokhoz. Metaadatok használata kérelem/válasz megváltoztatásához.
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
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192673"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN szabályok motorjának HTTP-változói
A HTTP-változók biztosítják a HTTP-kérések és-válaszok metaadatainak beolvasására szolgáló eszközöket. Ez a metaadatok ezután a kérések és válaszok dinamikus módosítására használhatók. A HTTP-változók használata a következő szabályok motor-funkcióihoz korlátozódik:

- [Gyorsítótár – kulcs újraírása](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Ügyfél-kérelem fejlécének módosítása](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Ügyfél-válasz fejlécének módosítása](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [URL-átirányítás](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [URL-újraírás](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Definíciók
A következő táblázat a támogatott HTTP-változókat ismerteti. A rendszer üres értéket ad vissza, ha a GEO-metaadatok (például postai kód) nem érhetők el egy adott kéréshez.


| Name | Változó | Leírás | Mintaérték |
| ---- | -------- | ----------- | ------------ |
| ASN (kérelmező) | % {geo_asnum} | Megadja a kérelmező AS-számát. <br /><br />**Elavult:** % {virt_dst_asnum}. <br />Ez a változó a (z)% {geo_asnum} helyett elavult. Habár ez az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatára. | AS15133 |
| Város (kérelmező) | % {geo_city} | A kérelmező városát jelzi. | Los Angeles |
| Kontinens (kérelmező) | % {geo_continent} | Azt jelzi, hogy a kérelmező kontinense a rövidítése. <br />Az érvényes értékek a következők: <br />AF: Afrika<br />AS: Ázsia<br />EU: Európa<br />NA: Észak-Amerika<br />OC: Ausztrália és Óceánia<br />SA: Dél-Amerika<br /><br />**Elavult:** % {virt_dst_continent}. <br />Ez a változó a (z)% {geo_continent} helyett elavult. <br />Habár ez az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatára.| N/A |
| Cookie értéke | % {cookie_Cookie} | A cookie-kifejezés által azonosított cookie-kulcsnak megfelelő értéket adja vissza. | Minta használata: <br />% {cookie__utma}<br /><br />Minta értéke:<br />111662281.2.10.1222100123 |
| Ország/régió (kérelmező) | % {geo_country} | Azt jelzi, hogy a kérelmező országa/régiója az ország/régió kódja alapján van-e. <br />**Elavult:** % {virt_dst_country}. <br /><br />Ez a változó a (z)% {geo_country} helyett elavult. Habár ez az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatára. | USA |
| Kijelölt piaci térség (kérelmező) | % {geo_dma_code} |A kérelmező adathordozó-piacát jelzi a régió kódja alapján. <br /><br />Ez a mező csak a Egyesült Államokból származó kérelmekre vonatkozik.| 745 |
| HTTP-kérési metódus | % {request_method} | A HTTP-kérés módszerét jelzi. | GET |
| HTTP-állapotkód | % {Status} | Megadja a válasz HTTP-állapotkódot. | 200 |
| IP-cím (kérelmező) | % {virt_dst_addr} | A kérelmező IP-címét jelzi. | 192.168.1.1 |
| Földrajzi szélesség (kérelmező) | % {geo_latitude} | A kérelmező szélességét jelzi. | 34,0995 |
| Hosszúság (kérelmező) | % {geo_longitude} | A kérelmező hosszúságát jelzi. | – 118,4143 |
| Nagyvárosi statisztikai körzet (kérelmező) | % {geo_metro_code} | A kérelmező nagyvárosi régióját jelzi. <br /><br />Ez a mező csak a Egyesült Államokból származó kérelmekre vonatkozik.<br />| 745 |
| Port (kérelmező) | % {virt_dst_port} | A kérelmező ideiglenes portját jelzi. | 55885 |
| Irányítószám (kérelmező) | % {geo_postal_code} | A kérelmező postai irányítószámát jelzi. | 90210 |
| Lekérdezési karakterlánc található | % {is_args} | A változó értéke attól függően változik, hogy a kérelem tartalmaz-e lekérdezési karakterláncot.<br /><br />-Lekérdezési karakterlánc található:?<br />-Nincs lekérdezési karakterlánc: NULL | ? |
| A lekérdezési karakterlánc paraméterét találta | % {is_amp} | A változó értéke attól függően változik, hogy a kérelem legalább egy lekérdezési karakterlánc paramétert tartalmaz-e.<br /><br />-Paraméter található: &<br />-Nincsenek paraméterek: NULL | & |
| Lekérdezési karakterlánc paraméterének értéke | % {arg_ &lt; paraméter &gt; } | A &lt; paraméter kifejezésével azonosított lekérdezési karakterlánc paraméterének megfelelő értéket adja vissza &gt; . | Minta használata: <br />% {arg_language}<br /><br />Példa lekérdezési karakterlánc paraméterre: <br />? Language = en<br /><br />Minta értéke: en |
| Lekérdezési karakterlánc értéke | % {query_string} | A kérelem URL-címében definiált teljes lekérdezési karakterlánc értéket jelzi. |key1 = val1&key2 = val2&key3 = val3 |
| Hivatkozó tartomány | % {referring_domain} | A hivatkozói kérelem fejlécében definiált tartományt jelzi. | <www.google.com> |
| Régió (kérelmező) | % {geo_region} | Megadja a kérelmező régióját (például az államot vagy a tartományt) az alfanumerikus rövidítése alapján. | CA |
| Kérelem fejlécének értéke | % {http_RequestHeader} | A RequestHeader kifejezéssel azonosított kérelem fejlécének megfelelő értéket adja vissza. <br /><br />Ha a kérelem fejlécének neve kötőjelet tartalmaz (például felhasználói ügynök), cserélje le aláhúzásra (például User_Agent).| Minta használata:% {http_Connection}<br /><br />Minta értéke: Keep-Alive | 
| Kérelem gazdagépe | % {gazdagép} | A kérelem URL-címében definiált gazdagépet jelzi. | <www.mydomain.com> |
| Kérelem protokollja | % {request_protocol} | A kérelem protokollját jelzi. | HTTP/1.1 |
| Kérési séma | % {séma} | A kérelem sémáját jelzi. |http |
| Kérelem URI-ja (relatív) | % {request_uri} | Megadja a relatív elérési utat, beleértve a lekérdezési karakterláncot is, amely a kérelem URI-ja alapján van meghatározva. | /marketing/foo.js? loggedin = True |
| Kérelem URI-ja (relatív lekérdezési karakterlánc nélkül) | % {URI} | A kért tartalom relatív elérési útját jelzi. <br /><br/>Legfontosabb információk:<br />– Ez a relatív elérési út kizárja a lekérdezési karakterláncot.<br />– Ez a relatív elérési út az URL-címek újraírását tükrözi. Az URL-cím a következő feltételekkel lesz újraírva:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL-Újraírási funkció: Ez a szolgáltatás átírja a kérelem URI-jében definiált relatív elérési utat.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL-cím: az ilyen típusú kérést a rendszer a megfelelő CDN URL-címre írja át. |/800001/corigin/rewrittendir/foo.js |
| Kérés URI-ja | % {kérelem} | Leírja a kérelmet. <br />Szintaxis: &lt; http-metódus &gt; &lt; relatív elérési útja &gt; &lt; http protokoll&gt; | /Marketing/-foo.js beolvasása? loggedin = True HTTP/1.1 |
| Válasz fejlécének értéke | % {resp_ &lt; ResponseHeader &gt; } | A ResponseHeader kifejezés által azonosított válasz fejlécének megfelelő értéket adja vissza &lt; &gt; . <br /><br />Ha a válasz fejlécének neve kötőjelet tartalmaz (például felhasználói ügynök), cserélje le aláhúzásra (például User_Agent). | Minta használata:% {resp_Content_Length}<br /><br />Minta értéke: 100 |

## <a name="usage"></a>Használat
Az alábbi táblázat a HTTP-változó megadásának megfelelő szintaxisát ismerteti.


| Syntax | Példa | Leírás |
| ------ | -------- | ---------- |
| % { &lt; HTTPVariable &gt; } | % {gazdagép} | Használja ezt a szintaxist a megadott HTTPVariable megfelelő teljes érték beolvasásához &lt; &gt; . |
| % { &lt; HTTPVariableDelimiter &gt; } | % {gazdagép,} | Ezzel a szintaxissal állíthatja be a megadott HTTPVariableDelimiter megfelelő teljes értékhez tartozó esetet  &lt; &gt; . |
| % { &lt; HTTPVariableDelimiterExpression &gt; } | % {Host/= ^ www \. ([^ \. ] +) \. ([^ \. :] +)/CDN. $2. $3:80} | Használjon reguláris kifejezést a &lt; &gt; http-változó értékének lecserélése, törlése és módosítása HTTPVariableDelimiterExpression. |

A HTTP-változók nevei csak betűket és aláhúzást támogatnak. A nem támogatott karakterek konvertálása aláhúzásra.

## <a name="delimiter-reference"></a>Határoló hivatkozás
Ha egy HTTP-változó a következő hatások bármelyikét elérheti, elválasztó karakter adható meg:

- Alakítsa át a változóhoz társított értéket.

     Példa: alakítsa át a teljes értéket kisbetűsre.

- Törölje a változóhoz társított értéket.

- A változóhoz társított érték módosítása.

     Példa: használjon reguláris kifejezéseket a HTTP-változóhoz társított érték módosításához.

Az elhatárolókat az alábbi táblázat ismerteti.

| Elválasztó | Description |
| --------- | ----------- |
| := | Azt jelzi, hogy egy alapértelmezett érték lesz hozzárendelve a változóhoz, ha a következők valamelyike: <br />-Hiányzó <br />-NULL értékűre van állítva. |
| :+ | Azt jelzi, hogy egy alapértelmezett érték lesz hozzárendelve a változóhoz, ha hozzá van rendelve egy érték. |
| : | Azt jelzi, hogy a rendszer kibontja a változóhoz rendelt érték egy alsztringjét. |
| # | Azt jelzi, hogy az elválasztó karakter után megadott mintát törölni kell, ha a változóhoz társított érték elején található. |
| % | Azt jelzi, hogy az elválasztó karakter után megadott mintát törölni kell, ha a változóhoz társított érték végén található. <br />Ez a definíció csak akkor alkalmazható, ha a (z)% Symbol határolójelként van használatban. |
| / | Egy HTTP-változó vagy egy minta elértékének korlátozása. |
| // | A megadott minta összes példányának megkeresése és cseréje. |
| /= | A megadott minta összes előfordulásának megkeresése, másolása és újraírása. |
| , | Alakítsa át a HTTP-változóhoz társított értéket kisbetűs értékre. |
| ^ | Alakítsa át a HTTP-változóhoz társított értéket nagybetűs értékre. |
| ,, | A megadott karakter összes példányának átalakítása kisbetűs értékre a HTTP-változóhoz társított értékben. |
| ^^ | A megadott karakter összes példányának konvertálása a HTTP-változóhoz tartozó érték nagybetűvel. |

## <a name="exceptions"></a>Kivételek
Az alábbi táblázat azokat a körülményeket ismerteti, amelyekben a megadott szöveg nem a HTTP-változóként van kezelve.

| Feltétel | Leírás | Példa |
| --------- | ----------- | --------|
| Escape-% szimbólum | A százalékos szimbólum egy fordított perjel használatával lehet megszökni. <br />A jobb oldali minta értéket literál értékként, nem pedig HTTP-változóként kezeli a rendszer.| \%gazdagép |
| Ismeretlen változók | A rendszer mindig üres karakterláncot ad vissza az ismeretlen változókhoz. | % {unknown_variable} |
| Érvénytelen karakterek vagy szintaxis | Az érvénytelen karaktereket vagy szintaxist tartalmazó változókat literál értékként kezeli a rendszer. <br /><br />Példa #1: a megadott érték érvénytelen karaktert (például:-) tartalmaz. <br /><br />Példa #2: a megadott érték kapcsos zárójelek dupla készletét tartalmazza. <br /><br />Példa #3: a megadott értékből hiányzik egy záró kapcsos zárójel.<br /> | Példa #1:% {resp_user-Agent} <br /><br />Példa #2:% {{host}} <br /><br />Példa #3:% {gazdagép |
| Hiányzó változó neve | A rendszer mindig NULL értéket ad vissza, ha nincs megadva változó. | %{} |
| Záró karakterek | Az a karakter, amelynél a változót a rendszer literál értékként kezeli. <br />A jobb oldali mintavételi érték olyan záró kapcsos zárójelet tartalmaz, amely literál értékként lesz kezelve. | % {gazdagép}} |

## <a name="setting-default-header-values"></a>Alapértelmezett fejléc értékeinek beállítása
Egy alapértelmezett érték rendelhető hozzá egy fejléchez, ha az megfelel a következő feltételek bármelyikének:
- Hiányzó/nem beállított
- NULL értékűre van állítva.

Az alábbi táblázat leírja, hogyan határozhat meg alapértelmezett értéket.

| Feltétel | Syntax | Példa | Leírás |
| --------- | ------ | --------| ----------- |
| Állítsa be az alapértelmezett érték fejlécét, ha az megfelel a következő feltételek bármelyikének: <br /><br />-Hiányzó fejléc <br /><br />– A fejléc értéke NULL értékre van állítva.| % {Változó: = érték} | % {http_referrer: = meghatározatlan} | A hivatkozó fejléc csak akkor lesz *meghatározatlan* , ha hiányzik vagy NULL értékűre van állítva. Ha be van állítva, semmilyen művelet nem kerül sor. |
| Ha hiányzik, állítsa be a fejlécet az alapértelmezett értékre. | % {Változó = érték} | % {http_referrer = meghatározatlan} | A hivatkozó fejléc csak akkor lesz *meghatározatlan* értékre állítva, ha hiányzik. Ha be van állítva, semmilyen művelet nem kerül sor. |
| Állítsa be a fejlécet alapértelmezett értékre, ha az nem felel meg a következő feltételek egyikének: <br /><br />-Hiányzó<br /><br /> -NULL értékűre van állítva. | % {Változó: + érték} | % {http_referrer: + meghatározatlan} | A hivatkozó fejléc csak *meghatározatlan* értékre lesz állítva, ha hozzá van rendelve egy érték. Ha hiányzik vagy NULL értékűre van állítva, a művelet nem kerül sor. |

## <a name="manipulating-variables"></a>Változók módosítása
A változók a következő módokon kezelhetők:

- Alsztringek kibontása
- Mintázatok eltávolítása

### <a name="substring-expansion"></a>Alsztring bővítése
Alapértelmezés szerint a változó a teljes értékre lesz kibontva. A következő szintaxissal csak a változó értékének egy alsztringjét bontsa ki.

`%<Variable>:<Offset>:<Length>}`

Legfontosabb információk:

- Az eltolási kifejezéshez rendelt érték határozza meg az alsztring kezdő karakterét:

     - Pozitív: az alsztring kezdő karaktere a karakterlánc első karaktere alapján lesz kiszámítva.
     - Nulla: az alsztring kezdő karaktere a karakterlánc első karaktere.
     - Negatív: az alsztring kezdő karakterének kiszámítása a karakterlánc utolsó karaktere alapján történik.

- Az alsztring hosszának meghatározása a *Hossz* kifejezéssel történik:

     - Kihagyva: a hossz kifejezés kihagyása lehetővé teszi, hogy az alkarakterlánc a kezdő karakter és a karakterlánc vége közötti összes karaktert tartalmazza.
     - Pozitív: a kezdő karakterből jobbra lévő alsztring hosszát határozza meg.
     - Negatív: a kiinduló karakterből balra lévő alsztring hosszát határozza meg.

#### <a name="example"></a>Példa:

A következő példa a minta kérelem URL-címére támaszkodik:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A következő sztring különböző módszereket mutat be a változók módosításához:

https: \/ /www%{HTTP_HOST: 3}/mobile/%{REQUEST_URI: 7:10}/% {REQUEST_URI:-5:-8}. htm

A mintául szolgáló kérelem URL-címe alapján a fenti változó manipuláció a következő értéket fogja eredményezni:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Minta eltávolítása
Egy adott mintázatnak megfelelő szöveg eltávolítható egy változó értékének elejéről vagy végéről.

| Syntax | Művelet |
| ------ | ------ |
| % {Változó # minta} | Szöveg eltávolítása, ha a megadott minta a változó értékének elején található. |
| % {Változó% Pattern} | A szöveg eltávolítása, ha a megadott minta a változó értékének végén található. |

#### <a name="example"></a>Példa:

Ebben a példában a *REQUEST_URI* változó a következőre van beállítva:

`/800001/myorigin/marketing/product.html?language=en-US`

A következő táblázat bemutatja, hogyan működik ez a szintaxis.

| Minta szintaxisa | Results (Eredmények) | Description |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html? Language = en-US | Mivel a változó a mintával kezdődik, lecserélték. |
| % {request_uri% HTML} htm | /800001/myorigin/marketing/product.html? Language = en-US | Mivel a változó nem a mintával végződik, nem történt változás.|

### <a name="find-and-replace"></a>Keresés és csere
A Find és a Replace szintaxist az alábbi táblázat ismerteti.

| Syntax | Művelet |
| ------ | ------ |
| % {Változó/Keresés/csere} | A megadott minta első előfordulásának megkeresése és cseréje. |
| % {Változó//Keresés/csere} | A megadott minta összes előfordulásának megkeresése és cseréje. |
| % {Változó ^} |A teljes érték nagybetűssé konvertálása. |
| % {Változó ^ keresés} | A megadott minta első előfordulásának átalakítása nagybetűs értékre. |
| % {Változó,} | A teljes értéket kisbetűsre konvertálja. |
| % {Változó, keresés} | A megadott minta első előfordulásának konvertálása kisbetűsre. |

### <a name="find-and-rewrite"></a>Keresés és újraírás
A keresés és csere megváltoztatásához használja a megadott mintának megfelelő szöveget az újraíráskor. A keresés és az újraírás szintaxisát az alábbi táblázat ismerteti.

| Syntax | Művelet |
| ------ | ------ |
| % {Változó/= keresés/újraírás} | A megadott minta összes előfordulásának megkeresése, másolása és újraírása. |
| % {Változó/^ keresés/újraírás} | A megadott minta megkeresése, másolása és újraírása a változó elején. |
| % {Változó/$Find/rewrite} | Keresse meg, másolja és írja át a megadott mintát, amikor az a változó végén fordul elő. |
| % {Változó/keresés} | Megkeresheti és törölheti a megadott minta összes előfordulását. |

Legfontosabb információk:

- A megadott mintának megfelelő szöveg kibontásához adja meg a dollár-aláírást, majd egy egész számot (például $1).

- Több minta is megadható. A minta meghatározásának sorrendje határozza meg azt az egész számot, amely hozzá lesz rendelve. Az alábbi példában az első minta megegyezik a "www." értékkel, a második mintának felel meg a második szintű tartománynak, és a harmadik minta megegyezik a legfelső szintű tartománnyal:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Az újraírásos érték szöveg és ezek helyőrzői tetszőleges kombinációját tartalmazhatja.

    Az előző példában az állomásnév újra lett írva `cdn.$2.$3:80` (például CDN.mydomain.com:80).

- A mintázat helyőrzője (például $1) a következő jelzők használatával módosítható:
     - U: a kibontott érték nagybetűvel.

         Mintaszintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: a kibontott érték kisbetűs.

         Mintaszintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- A minta előtt meg kell adni egy operátort. A megadott operátor határozza meg a minta-rögzítési viselkedést:

     - `=`: Azt jelzi, hogy a megadott minta összes előfordulását rögzíteni kell, és újra kell írni.
     - `^`: Azt jelzi, hogy csak a megadott mintával kezdődő szövegek lesznek rögzítve.
     - `$`: Azt jelzi, hogy csak a megadott mintával végződő szöveg lesz rögzítve.
 
- Ha kihagyja a */rewrite* értéket, a rendszer törli a mintázattal egyező szöveget.


