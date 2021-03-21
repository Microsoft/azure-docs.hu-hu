---
title: Azure CDN a Verizon Premium Rules Engine funkcióival
description: A Verizon Premium Rules Engine funkcióinak Azure CDN dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020415"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN a Verizon Premium Rules Engine funkcióival

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)elérhető szolgáltatásainak részletes leírását tartalmazza.

A szabály harmadik része a szolgáltatás. A szolgáltatás határozza meg, hogy milyen típusú műveletet alkalmaz a rendszer az egyeztetési feltételek alapján azonosított kérelem típusára.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Azure CDN a Verizon Premium Rules Engine funkcióinak ismertetése

Az elérhető szolgáltatások típusai a következők:

* [Hozzáférés](#access)
* [Gyorsítótárazás](#caching)
* [Megjegyzés](#comment)
* [Fejlécek](#headers)
* [Naplók](#logs)
* [Optimalizálása](#optimize)
* [Származási](#origin)
* [Speciális](#specialty)
* [URL-cím](#url)
* [Webalkalmazási tűzfal](#waf)

### <a name="access"></a><a name="access"></a>Access

Ezek a funkciók a tartalmakhoz való hozzáférés szabályozására szolgálnak.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Hozzáférés megtagadása (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Meghatározza, hogy az összes kérés visszautasítva van-e egy 403 Tiltott válasz esetén. |
| [Jogkivonat-hitelesítés](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Meghatározza, hogy a rendszer a Token-Based hitelesítést alkalmazza-e a kérelemre. |
| [Jogkivonat-hitelesítési elutasítási kód](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Meghatározza, hogy a rendszer milyen típusú választ adjon vissza a felhasználónak, ha Token-Based hitelesítés miatt megtagadja a kérelmet. |
| [Jogkivonat-hitelesítés figyelmen kívül hagyása URL-eset](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Meghatározza, hogy a Token-Based hitelesítéssel végzett URL-összehasonlítás kis-és nagybetűket is megkülönböztet-e. |
| [Jogkivonat-hitelesítési paraméter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Meghatározza, hogy a Token-Based Authentication lekérdezési karakterlánc paraméterét át kell-e átnevezni. |

**[Vissza a tetejére](#top)**

### <a name="caching"></a><a name="caching"></a>Gyorsítótárazás

Ezek a funkciók úgy vannak kialakítva, hogy testre szabják a tartalom gyorsítótárazásának idejét és módját.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Sávszélesség-paraméterek](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Meghatározza, hogy a sávszélesség-szabályozási paraméterek (azaz ec_rate és ec_prebuf) aktívak-e. |
| [Sávszélesség-szabályozás](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | A peremhálózati kiszolgálók által biztosított válasz sávszélességének szabályozása. |
| [Gyorsítótár megkerülése](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Meghatározza, hogy a kérelem kihasználhatja-e a gyorsítótárazási technológiát. |
| [Cache-Control fejléc kezelése](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Azt szabályozza, hogy a peremhálózati kiszolgáló Cache-Control fejléceket generáljon, ha a külső Max-Age funkció aktív. |
| [Gyorsítótár – kulcs lekérdezési karakterlánca](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Meghatározza, hogy a **gyorsítótár-kulcs** _ tartalmazza-e a kérelemhez társított lekérdezési karakterlánc-paramétereket. <br> __ Relatív elérési út, amely egyedileg azonosít egy eszközt a gyorsítótárazás céljára.  A peremhálózati kiszolgálók ezt a relatív elérési utat használják a gyorsítótárazott tartalom keresésekor.  Alapértelmezés szerint a gyorsítótár-kulcs nem tartalmaz lekérdezési karakterláncot parameters._ |
| [Gyorsítótár – kulcs újraírása](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | A kérelemhez társított gyorsítótár-kulcs újraírása. |
| [Gyorsítótár kitöltésének befejezése](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Meghatározza, hogy mi történjen, ha egy kérelem részleges gyorsítótárat eredményez egy peremhálózati kiszolgálón. |
| [Fájltípusok tömörítése](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Azokat a fájlformátumokat határozza meg, amelyek a kiszolgálón lesznek tömörítve. | 
| [Alapértelmezett belső max. Age](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Meghatározza az Edge-kiszolgáló alapértelmezett maximális élettartamának intervallumát a forrás kiszolgáló gyorsítótárának újraérvényesítéséhez. |
| [A fejléc kezelése lejár](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Azt szabályozza, hogy a külső Max-Age funkció aktív-e, ha egy peremhálózati kiszolgáló lejárati fejlécei lejárnak. |
| [Külső Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Meghatározza a böngésző és az Edge kiszolgáló gyorsítótárának újraérvényesítésének maximális életkori intervallumát. |
| [A belső Max-Age kényszerítése](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Meghatározza az Edge-kiszolgáló és a forrás-kiszolgáló gyorsítótár-újraérvényesítésének maximális életkori intervallumát. |
| [H. 264 támogatás (HTTP Progressive letöltés)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Meghatározza a tartalom továbbítására használható H. 264 fájlformátumok típusait. |
| [H. 264 támogatás – videó-keresési paraméterek](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Felülbírálja a H. 264 adathordozón való keresést vezérlő paraméterekhez rendelt neveket a HTTP progresszív letöltés használatakor. |
| [Honor No-Cache kérelem](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Meghatározza, hogy a rendszer továbbítsa-e a HTTP-ügyfél nem gyorsítótárazott kérelmeit a forráskiszolgáló felé. |
| [Kihagyott forrás – gyorsítótár](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Meghatározza, hogy a CDN figyelmen kívül hagyja-e a forráskiszolgáló által kiszolgált bizonyos irányelveket. |
| [Unsatisfiable tartományok figyelmen kívül hagyása](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Meghatározza azt a választ, amelyet a rendszer az ügyfeleknek küld, amikor egy kérelem létrehoz egy 416 kért tartományt, amely nem teljesíthető. |
| [Belső maximális – elavult](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Azt szabályozza, hogy mennyi ideig tart a lejárati idő, amikor egy gyorsítótárazott eszköz kiszolgálható egy peremhálózati kiszolgálóról, ha a peremhálózati kiszolgáló nem tudja újraérvényesíteni a gyorsítótárazott eszközt a forrás-kiszolgálóval. |
| [Részleges gyorsítótár megosztása](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Meghatározza, hogy egy kérelem képes-e részlegesen gyorsítótárazott tartalom előállítására. |
| [Gyorsítótárazott tartalom előérvényesítése](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Meghatározza, hogy a gyorsítótárazott tartalom jogosult-e a korai újraérvényesítésre az ÉLETTARTAMa lejárta előtt. |
| [Zero-Byte gyorsítótár fájljainak frissítése](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Azt határozza meg, hogy a rendszer hogyan kezeli a HTTP-ügyfél egy 0 bájtos gyorsítótár-eszközre vonatkozó kérését a peremhálózati kiszolgálókon. |
| [Gyorsítótárazható állapotkódok beállítása](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Meghatározza a gyorsítótárazott tartalmat okozó állapotkódok készletét. |
| [Elavult tartalom kézbesítése hiba esetén](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Meghatározza, hogy a rendszer a lejárt gyorsítótárazott tartalmat fogja-e továbbítani, ha hiba történik a gyorsítótár-ismétlődés során, vagy amikor a kért tartalmat az ügyfél-kiszolgálóról kéri le. | 
| [Elavult az újraellenőrzés során](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Javítja a teljesítményt, mivel lehetővé teszi, hogy a peremhálózat-kiszolgálók elavult ügyfelet szolgáljanak a kérelmezőnek az újraérvényesítés során. |

**[Vissza a tetejére](#top)**

### <a name="comment"></a><a name="comment"></a>Megjegyzés

A Megjegyzés funkció lehetővé teszi, hogy egy Megjegyzés egy szabályon belül legyen hozzáadva.

**[Vissza a tetejére](#top)**

### <a name="headers"></a><a name="headers"></a>Fejlécek

Ezek a funkciók a kérelemből vagy válaszból származó fejlécek hozzáadására, módosítására és törlésére szolgálnak.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Életkor válaszának fejléce](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Meghatározza, hogy a rendszer a kérelmezőnek küldött válaszban tartalmazza-e az életkor válaszának fejlécét. |
| [Hibakeresési gyorsítótár válaszának fejlécei](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Meghatározza, hogy a válasz tartalmazhatja-e az [X-EC-debug válasz fejlécét](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) , amely információt nyújt a kért eszköz gyorsítótár-házirendjéről. |
| [Ügyfél-kérelem fejlécének módosítása](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Felülírja, hozzáfűzi vagy törli egy fejlécet egy kérelemből. |
| [Ügyfél-válasz fejlécének módosítása](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Egy válasz fejlécének felülírása, hozzáfűzése vagy törlése. |
| [Ügyfél IP-címének egyéni fejlécének beállítása](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Lehetővé teszi, hogy a kérelmező ügyfél IP-címe a kérelemhez egyéni kérelem fejlécként legyen hozzáadva. |

**[Vissza a tetejére](#top)**

### <a name="logs"></a><a name="logs"></a>Naplók

Ezek a funkciók úgy vannak kialakítva, hogy testre szabják a nyers naplófájlokban tárolt adatfájlokat.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Egyéni napló mezője 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Meghatározza a nyers naplófájl egyéni napló mezőjéhez hozzárendelni kívánt formátumot és tartalmat. |
| [Napló lekérdezési karakterlánca](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Meghatározza, hogy a rendszer a lekérdezési karakterláncot a hozzáférési naplók URL-címével együtt tárolja-e. |

**[Vissza a tetejére](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimalizálása

Ezek a funkciók határozzák meg, hogy egy kérelem a Edge-optimalizáló által biztosított optimalizálásokra fog-e esni.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Edge-optimalizáló](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Meghatározza, hogy alkalmazható-e az Edge-optimalizáló egy kérelemre. |
| [Edge-optimalizáló – konfiguráció példánya](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Egy helyhez társított Edge-optimalizáló konfigurációjának példánya vagy aktiválása. |

**[Vissza a tetejére](#top)**

### <a name="origin"></a><a name="origin"></a>Származási

Ezek a funkciók úgy vannak kialakítva, hogy a CDN hogyan kommunikáljon a forrás-kiszolgálóval.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Keep-Alive kérelmek maximális száma](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | A Keep-Alive-kapcsolatra vonatkozó kérelmek maximális számát határozza meg a lezárás előtt. |
| [Proxy speciális fejlécei](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Meghatározza azon [CDN-specifikus kérelmek fejléceit](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) , amelyek egy peremhálózati kiszolgálóról a forrás-kiszolgálóra lesznek továbbítva. |

**[Vissza a tetejére](#top)**

### <a name="specialty"></a><a name="specialty"></a>Speciális

Ezek a funkciók olyan speciális funkciókat biztosítanak, amelyeket csak a speciális felhasználók használhatnak.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Gyorsítótárazható HTTP-metódusok](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Meghatározza a hálózaton gyorsítótárazható további HTTP-metódusok készletét. |
| [Gyorsítótárazható kérelem törzsének mérete](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Meghatározza azt a küszöbértéket, amely meghatározza, hogy a POST válasz gyorsítótárazható-e. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Meghatározza, hogy az ügyfél tájékoztatni fogja-e a CDN szolgáltatás által támogatott QUIC. |
| [Folyamatos átvitel optimalizálása](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Az élő streamek teljesítményének optimalizálása és a forráskiszolgáló terhelésének csökkentése érdekében hangolja a gyorsítótárazási konfigurációt. |
| [Felhasználói változó](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Egy értéket rendel egy felhasználó által definiált változóhoz, amelyet a rendszer a testre szabott forgalom-feldolgozási megoldásnak továbbít. |

**[Vissza a tetejére](#top)**

### <a name="url"></a><a name="url"></a>URL-cím

Ezek a funkciók lehetővé teszik a kérés átirányítását vagy átírását egy másik URL-címre.

| Name       | Cél                                                           |
|------------|-------------------------------------------------------------------|
| [Átirányítások követése](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Meghatározza, hogy a kérések átirányíthatók-e az ügyfél-kiszolgáló által visszaadott Location fejlécben megadott állomásnévre. |
| [URL-átirányítás](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Átirányítja a kéréseket a Location fejléc használatával. |
| [URL-újraírás](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | A kérelem URL-címének újraírása. |

**[Vissza a tetejére](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Webalkalmazási tűzfal

A [webalkalmazási tűzfal](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) szolgáltatás azt határozza meg, hogy a rendszer a webalkalmazási tűzfal által beérkező kéréseket fogja-e átvizsgálni.

**[Vissza a tetejére](#top)**

A legújabb funkciókért tekintse meg a [Verizon Rules Engine dokumentációját](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Következő lépések

- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
