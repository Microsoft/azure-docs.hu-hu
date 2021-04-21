---
title: HTTP-fejlécek és URL-címek átírása Azure Application Gateway | Microsoft Docs
description: Ez a cikk áttekintést nyújt a HTTP-fejlécek és URL-címek újraírásának Azure Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789446"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>HTTP-fejlécek és URL-címek átírása Application Gateway

Application Gateway lehetővé teszi a kérések és válaszok kiválasztott tartalmának újraírását. Ezzel a funkcióval lefordíthatja az URL-címeket, a lekérdezési sztring paramétereit, valamint módosíthatja a kérés- és válaszfejléceket. Emellett feltételek hozzáadását is lehetővé teszi annak biztosításához, hogy az URL-cím vagy a megadott fejlécek csak bizonyos feltételek teljesülte esetén legyen átírva. Ezek a feltételek a kérelem és a válasz információin alapulnak.

>[!NOTE]
>A HTTP-fejléc és az URL-átírási funkciók csak a [2-es Application Gateway termékváltozathoz érhetők el](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Támogatott átírási típusok

### <a name="request-and-response-headers"></a>Kérés- és válaszfejlécek

A HTTP-fejlécek lehetővé teszik, hogy az ügyfelek és a kiszolgálók további információkat adjanak át kéréssel vagy válaszokkal. A fejlécek újraírásával olyan fontos feladatokat is elvégezhet, mint például a biztonsággal kapcsolatos fejlécmezők hozzáadása, például a HSTS/X-XSS-Protection, a bizalmas adatokat felfedő válaszfejléc-mezők eltávolítása, valamint a portinformációk eltávolítása az X-Forwarded-For fejlécből.

Az Application Gateway lehetővé teszi HTTP-kérelmek és -válaszok fejlécének hozzáadását, eltávolítását vagy frissítését, miközben a kérelem- és válaszcsomagok az ügyfél és a háttérkészlet között mozognak.

A kérés- és válaszfejlécek a Application Gateway használatával való átírásának Azure Portal lásd [itt:](rewrite-url-portal.md).

![kép](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Támogatott fejlécek**

A kérések és válaszok minden fejlécét átírhatja, kivéve a Kapcsolat és a Frissítés fejléceket. Az Application Gateway használatával egyéni fejléceket is létrehozhat, és hozzáadhatja őket az azon keresztül átkért kérelmekhez és válaszokhoz.

### <a name="url-path-and-query-string"></a>URL-cím és lekérdezési sztring

Az URL-átírási funkcióval a Application Gateway a következő lehetőségeket használhatja:

* Írja át a kérés URL-címének állomásnevét, elérési útját és lekérdezési sztringjét 

* Válassza ki, hogy átírja-e a figyelő összes kérésének URL-címét, vagy csak azokat a kéréseket, amelyek megfelelnek egy vagy több megadott feltételnek. Ezek a feltételek a kérés és a válasz tulajdonságain alapulnak (kérés, fejléc, válaszfejléc és kiszolgálóváltozók).

* Válassza a kérelem útválasztását (válassza ki a háttérkészletet) az eredeti URL-cím vagy az átírt URL-cím alapján

Ha többet szeretne megtudni arról, hogyan írhatja át az URL-Application Gateway a Azure Portal használatával, tekintse meg [a következőt:](rewrite-url-portal.md).

![Az URL-cím újraírásának folyamatát leíró diagram Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Átírási műveletek

Az átírási műveletekkel megadhatja az átírni kívánt URL-címet, kérelemfejléceket vagy válaszfejléceket, valamint azt az új értéket, amelyre át szeretné írni őket. Egy URL-cím vagy egy új vagy meglévő fejléc értéke a következő típusú értékekre beállítható:

* Szöveg
* Kérelemfejléc. Kérelemfejléc megadásához a következő szintaxist kell használnia: {http_req_ *headerName*}
* Válaszfejléc. Válaszfejléc megadásához a következő szintaxist kell használnia: {http_resp_ *headerName*}
* Kiszolgálóváltozó. Kiszolgálóváltozó megadásához a *{var_variable*} szintaxist kell használnia. Tekintse meg a támogatott kiszolgálóváltozók listáját
* Szöveg, kérelemfejléc, válaszfejléc és kiszolgálóváltozó kombinációja. 

## <a name="rewrite-conditions"></a>Újraírási feltételek

Az átírási feltételek ( opcionális konfiguráció) használatával kiértékelheti a HTTP(S) kérések és válaszok tartalmát, és csak akkor hajthat végre újraírást, ha egy vagy több feltétel teljesül. Az Application Gateway az alábbi típusú változókat használja a kérések és válaszok tartalmának kiértékeléséhez:

* HTTP-fejlécek a kérésben
* HTTP-fejlécek a válaszban
* Application Gateway kiszolgáló változói

Egy feltétel használatával kiértékelheti, hogy egy adott változó jelen van-e, hogy egy adott változó megfelel-e egy adott értéknek, vagy hogy egy adott változó megfelel-e egy adott mintának. 


### <a name="pattern-matching"></a>Mintaegyezés 

Application Gateway feltételben reguláris kifejezéseket használ a mintaegyeztetéshez. A [Perl Compatible Regular Expressions (PCRE)](https://www.pcre.org/) kódtár használatával reguláriskifejezés-mintaegyeztetést állíthat be a feltételekben. A reguláris kifejezések szintaxisával kapcsolatos további információkért lásd a [perl reguláris kifejezések főoldalát.](https://perldoc.perl.org/perlre.html)

### <a name="capturing"></a>Elfog

Ha későbbi használatra rögzítenie kell egy alsztringet, zárójeleket helyezzen a feltétel regex definíciójának megfelelő részpatter köré. Az első zárójelpár a részsztringet 1-ben, a második párt 2-ben tárolja, és így tovább. Annyi zárójelet használhat, amennyit csak szeretne; A Perl csak több számmal számolt változót definiál, hogy ezeket a rögzített sztringeket képviselje. Példák a [következőre:](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm) 

*  /(\d)(\d)/ # Két számjegyre illeszkedik, és az 1. és a 2. csoportba rögzíti őket 

* /(\d+)/ # Egyezés egy vagy több számjegyre, mindegyik rögzítése az 1. csoportba 

* /(\d)+/ # Egyező számjegy egy vagy több alkalommal, az utolsó rögzítése az 1. csoportba

A rögzített műveletkészletben a következő formátumban hivatkozhat rájuk:

* Kérelemfejléc rögzítéséhez {http_req_headerName_groupNumber}. Például {http_req_User-Agent_1} vagy {http_req_User-Agent_2}
* Válaszfejléc rögzítéséhez {http_resp_headerName_groupNumber}. Például {http_resp_Location_1} vagy {http_resp_Location_2}
* Kiszolgálóváltozók esetében a {var_serverVariableName_groupNumber} paramétert kell használnia. Például {var_uri_path_1} vagy {var_uri_path_2}

Ha a teljes értéket szeretné használni, ne említse meg a számot. Egyszerűen használja a {http_req_headerName}, stb. formátumot a groupNumber nélkül.

## <a name="server-variables"></a>Kiszolgálóváltozók

Application Gateway kiszolgálóváltozók használatával tárolja a kiszolgálóval, az ügyféllel való kapcsolattal és a kapcsolathoz aktuális kéréssel kapcsolatos hasznos információkat. A tárolt információk közé tartozik például az ügyfél IP-címe és a webböngésző típusa. A kiszolgálóváltozók dinamikusan változnak, például egy új oldal betöltésekor vagy űrlap közzétenésekor. Ezekkel a változókval kiértékelheti az átírási feltételeket, és átírhatja a fejléceket. Ahhoz, hogy a fejléceket kiszolgálóváltozók értékével írhatja át, ezeket a változókat a {var_ *serverVariableName*} szintaxisban kell megadnia.

Az Application Gateway a következő kiszolgálóváltozókat támogatja:

|   Változó neve    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Az X-Forwarded-For ügyfélkérés fejlécmezője, amely a változót tartalmazza (lásd a táblázat későbbi szakaszát), és a következő formátumban van hozzáfűzve hozzá: `client_ip` IP1, IP2, IP3 stb. Ha az X-Forwarded-For mező nem található meg az ügyfélkérés fejlécében, a változó `add_x_forwarded_for_proxy` egyenlő a `$client_ip` változóval.   Ez a változó különösen hasznos, ha át szeretné írni az X-Forwarded-For fejlécet a Application Gateway által beállítottak szerint, hogy a fejléc csak a portinformációk nélküli IP-címet tartalmazza. |
| ciphers_supported         | Az ügyfél által támogatott cipherek listája.               |
| ciphers_used              | A létrehozott TLS-kapcsolathoz használt ciphers sztring. |
| client_ip                 | Annak az ügyfélnek az IP-címe, amelytől az alkalmazásátjáró megkapta a kérést. Ha az Application Gateway és a kezdeményező ügyfél előtt fordított proxy van, a a fordított proxy `client_ip` IP-címét adja vissza. |
| client_port               | Az ügyfélport.                                             |
| client_tcp_rtt            | Az ügyfél TCP-kapcsolatának adatai. A szoftvercsatorna-TCP_INFO rendszereken érhető el. |
| client_user               | HTTP-hitelesítés használata esetén a hitelesítéshez megadott felhasználónév. |
| gazda                      | Ebben a elsőbbségi sorrendben: a kéréssor állomásneve, a gazdagép neve a Gazdagép kérésfejléce mezőben, vagy a kérésnek megfelelő kiszolgálónév. Példa: A kérésben a `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` gazdagép értéke a következő lesz: `contoso.com` |
| cookie_ *neve*             | A *cookie neve.*                                           |
| http_method               | Az URL-kérelem igényléséhez használt metódus. Például: GET vagy POST. |
| http_status               | A munkamenet állapota. Például: 200, 400 vagy 403.           |
| http_version              | A kérelem protokollja. Általában HTTP/1.0, HTTP/1.1 vagy HTTP/2.0. |
| query_string              | A kért URL-címben a "?" után következő változó/érték párok listája. Példa: A kérelemben a `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` következő query_string lesz: `id=123&title=fabrikam` |
| received_bytes            | A kérelem hossza (beleértve a kérelemsort, a fejlécet és a kérelem törzsét). |
| request_query             | A kérelemsor argumentumai.                           |
| request_scheme            | A kérésséma: http vagy https.                           |
| request_uri               | Az eredeti kérelem teljes URI-ja (argumentumokkal). Példa: a kérelemben a `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` következő request_uri lesz: `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Az ügyfélnek küldött bájtok száma.                        |
| server_port               | A kérést elfogadó kiszolgáló portja.              |
| ssl_connection_protocol   | A létrehozott TLS-kapcsolat protokollja.               |
| ssl_enabled               | "Be", ha a kapcsolat TLS módban működik. Ellenkező esetben egy üres sztring. |
| uri_path                  | Azonosítja a gazdagépnek azt az erőforrását, amelyhez a webes ügyfél hozzá szeretne férni. Ez a kérelem URI-nak az argumentumok nélküli része. Példa: A `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` kérésben a uri_path érték lesz `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Kölcsönös hitelesítési kiszolgáló változói (előzetes verzió)

Application Gateway a következő kiszolgálóváltozókat támogatja a kölcsönös hitelesítési forgatókönyvekhez. Ezeket a kiszolgálóváltozókat a fentiekhez hasonló módon használja, mint a többi kiszolgálóváltozót. 

|   Változó neve    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Az ügyfél tanúsítványa PEM formátumban egy létrehozott SSL-kapcsolathoz. |
| client_certificate_end_date| Az ügyfél-tanúsítvány záró dátuma. |
| client_certificate_fingerprint| A létrehozott SSL-kapcsolat ügyfél-tanúsítványának SHA1 ujjlenyomata. |
| client_certificate_issuer | A létrehozott SSL-kapcsolat ügyfél-tanúsítványának "kiállító DN" sztringe. |
| client_certificate_serial | A létrehozott SSL-kapcsolat ügyfél-tanúsítványának sorozatszáma.  |
| client_certificate_start_date| Az ügyfél-tanúsítvány kezdő dátuma. |
| client_certificate_subject| A létrehozott SSL-kapcsolat ügyfél-tanúsítványának "tulajdonosi DN" sztringe. |
| client_certificate_verification| Az ügyfél-tanúsítvány ellenőrzésének eredménye: *SUCCESS,* *<reason> FAILED:*, vagy *NINCS,* ha nem volt jelen tanúsítvány. | 

## <a name="rewrite-configuration"></a>Konfiguráció átírása

Az újraírási szabály konfiguráláshoz létre kell hoznia egy újraírási szabálykészletet, és hozzá kell adni az újraírási szabály konfigurációját.

Az átírási szabálykészlet a következő adatokat tartalmazza:

* **Útválasztási szabályok társításának kérése:** Az átírási konfiguráció az útválasztási szabályon keresztül van társítva a forrás listenerhez. Ha alapszintű útválasztási szabályt használ, az átírási konfiguráció egy forrás figyelőhöz van társítva, amely egy globális fejlécátírás. Elérésiút-alapú útválasztási szabály használata esetén az átírási konfiguráció az URL-útvonaltérképen van definiálva. Ebben az esetben csak a hely adott elérési útjának területére vonatkozik. Több átírási készletet is létrehozhat, és mindegyik átírási készletet több figyelőre alkalmazhatja. Egy adott figyelőre azonban csak egy átírási készletet alkalmazhat.

* **Átírási feltétel:** Nem kötelező konfiguráció. Az átírási feltételek kiértékelik a HTTP(S) kérések és válaszok tartalmát. Az újraírási művelet akkor történik, ha a HTTP(S) kérése vagy válasza megfelel az átírási feltételnek. Ha egy művelethez egynél több feltételt társít, a művelet csak akkor történik meg, ha az összes feltétel teljesül. Más szóval a művelet egy logikai ÉS művelet.

* **Átírási típus:** 3 újraírási típus érhető el:
   * Kérelemfejlécek újraírása 
   * Válaszfejlécek újraírása
   * URL-összetevők újraírása
      * **URL-cím** elérési útja: Az az érték, amelyre az elérési utat át kell írni. 
      * **URL-lekérdezési sztring:** Az az érték, amelyre a lekérdezési sztringet át kell írni. 
      * **Elérésiút-térkép újraértékelése:** Annak meghatározására használatos, hogy az URL-útvonaltérkép újra ki legyen-e értékelve. Ha nincs bejelölve, a rendszer az eredeti URL-címútvonalat használja az URL-útvonaltérkép elérési útjának mintája alapján. Ha true (igaz) érték van beállítva, a rendszer újra kiértékeli az URL-útvonaltérképet, hogy ellenőrizze az egyezést az átírt elérési útkal. Ennek a kapcsolónak az engedélyezése segít a kérés átirányításában egy másik háttérkészletbe az átírás után.

## <a name="rewrite-configuration-common-pitfalls"></a>Konfiguráció átírása – gyakori buktatók

* Az "Útvonaltérkép újraértékelése" nem engedélyezett az alapszintű kérés-útválasztási szabályokhoz. Ezzel megakadályozhatja egy alapszintű útválasztási szabály végtelen kiértékelési ciklusát.

* Legalább 1 feltételes újraírási szabálynak vagy 1 újraírási szabálynak kell lennie, amelynél nincs engedélyezve az elérésiút-alapú útválasztási szabályok újraértékelése, hogy megakadályozza az elérésiút-alapú útválasztási szabályok végtelen kiértékelési ciklusát.

* A bejövő kérések egy 500-as hibakóddal megszakadnak, ha az ügyfélbemenetek alapján dinamikusan jön létre hurok. A Application Gateway továbbra is kiszolgálni fogja a többi kérést anélkül, hogy teljesítménycsökkenést adna vissza egy ilyen forgatókönyvben.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>URL-átírás vagy állomásfejléc átírásának használata Web Application Firewall (WAF_v2 termékváltozat)

Az URL-átírás vagy a gazdagépfejléc átírásának konfigurálásakor a WAF kiértékelése a kérelemfejléc vagy az URL-paraméterek módosítása (az átírás utáni) után történik meg. Ha eltávolítja az URL-átírást vagy a gazdagépfejléc átírásának konfigurációját a Application Gateway, a WAF kiértékelése a fejléc átírása előtt (előírás) történik meg. Ez a sorrend biztosítja, hogy a RENDSZER alkalmazza a WAF-szabályokat a háttérkészlet által fogadott végső kérésre.

Tegyük fel például, hogy a fejléchez a következő fejlécátírási szabálya van: ha a fejléc értéke egyenlő a értékével, írja át az értéket a `"Accept" : "text/html"` `"Accept"` következőre: `"text/html"` `"image/png"` .

Itt, ha csak fejlécátírás van konfigurálva, a WAF kiértékelése a következőn történik: `"Accept" : "text/html"` . Ha azonban konfigurálja az URL-átírást vagy a gazdafejléc átírását, a WAF kiértékelése a következőn történik: `"Accept" : "image/png"` .

>[!NOTE]
> Az URL-átírási műveletek várhatóan kisebb növekedést okoznak a WAF-kiszolgáló processzorkihasználtságának Application Gateway. Javasoljuk, hogy rövid [](high-traffic-support.md) időre figyelje a CPU-kihasználtsági metrikát, miután engedélyezte az URL-átírási szabályokat a WAF-Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>A fejlécek átírásának gyakori forgatókönyvei

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Portinformációk eltávolítása az X-Forwarded-For fejlécből

Application Gateway beszúr egy X-Forwarded-For fejlécet az összes kérelembe, mielőtt továbbítja a kéréseket a háttérnek. Ez a fejléc az IP-portok vesszővel elválasztott listája. Előfordulhatnak olyan forgatókönyvek, amelyekben a háttérkiszolgálóknak csak a fejlécekkel kell IP-címeket tartalmazni. Fejlécátírással eltávolíthatja a portadatokat az X-Forwarded-For fejlécből. Ennek egyik módja, ha a fejlécet a add_x_forwarded_for_proxy kiszolgálói változóra. Másik lehetőségként használhatja az client_ip:

![Port eltávolítása](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Átirányítási URL-cím módosítása

Amikor egy háttéralkalmazás átirányítási választ küld, előfordulhat, hogy a háttéralkalmazás által megadott URL-címtől eltérő URL-címre szeretné átirányítani az ügyfelet. Ezt például akkor érdemes megtenni, ha az App Service-t egy Alkalmazásátjáró mögött üzemeltetik, és az ügyfélnek átirányítást kell megtennie a relatív útvonalára. (Például átirányítás a contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2.)

Mivel App Service egy több-bérlős szolgáltatás, a kérésben található állomásfejlécet használja a kérés megfelelő végpontra való útválasztására. Az alkalmazásszolgáltatások alapértelmezett tartományneve .azurewebsites.net (például contoso.azurewebsites.net), amely eltér az alkalmazásátjáró tartománynevéhez \* (például contoso.com). Mivel az ügyféltől származó eredeti kérés gazdaneve az Application Gateway tartományneve (contoso.com), az application gateway a gazdagépnevet contoso.azurewebsites.net. Ez azért van így, hogy az App Service a megfelelő végpontra irányíthatja a kérést.

Amikor az App Service átirányítási választ küld, ugyanazt az állomásnevet használja a válasz helyfejlécében, mint az Application Gatewaytől kapott kérelemben. Így az ügyfél közvetlenül a-hoz fogja a kérést, nem pedig az `contoso.azurewebsites.net/path2` Application Gatewayen ( `contoso.com/path2` ) keresztülmenni. Az Application Gateway megkerülése nem kívánatos.

A probléma megoldásához a helyfejlécben az állomásnevet az Alkalmazásátjáró tartománynevére kell konfigurálni.

Az állomásnév cseréjének lépései a következőek:

1. Hozzon létre egy újraírási szabályt egy olyan feltétellel, amely kiértékeli, hogy a válaszban található helyfejléc tartalmaz-e azurewebsites.net. Adja meg a `(https?):\/\/.*azurewebsites\.net(.*)$` mintát.
2. Hajtson végre egy műveletet a helyfejléc átírásához, hogy az tartalmazza az Application Gateway állomásnevét. Ezt úgy tegye meg, hogy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` fejlécértékként a értéket ad meg. Másik lehetőségként a kiszolgálóváltozóval is beállíthatja az állomásnevet az `host` eredeti kérésnek megfelelően.

![Helyfejléc módosítása](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági HTTP-fejlécek implementációja a biztonsági rések elkerülése érdekében

Számos biztonsági rést kijavíthat, ha az alkalmazás válaszában a szükséges fejléceket használja. Ezek a biztonsági fejlécek közé tartozik az X-XSS-Protection, a Strict-Transport-Security és a Content-Security-Policy. Ezeket a fejléceket Application Gateway a válaszokhoz.

![Biztonsági fejléc](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Nemkívánatos fejlécek törlése

Érdemes lehet eltávolítani azokat a fejléceket, amelyek bizalmas információkat fednek fel egy HTTP-válaszból. Előfordulhat például, hogy olyan információkat szeretne eltávolítani, mint a háttérkiszolgáló neve, az operációs rendszer vagy a könyvtár részletei. Az Application Gateway használatával eltávolíthatja a következő fejléceket:

![Fejléc törlése](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Fejléc jelenlétének ellenőrzése

Kiértékelheti a HTTP-kérést vagy -válaszfejlécet, hogy van-e fejléc vagy kiszolgálóváltozó. Ez a kiértékelés akkor hasznos, ha csak akkor szeretne fejléc-átírást végrehajtani, ha van ilyen fejléc.

![Fejléc jelenlétének ellenőrzése](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Gyakori URL-átírási forgatókönyvek

#### <a name="parameter-based-path-selection"></a>Paraméteralapú elérési út kiválasztása

Ha olyan forgatókönyveket szeretne elérni, amelyekben a háttérkészletet a fejléc, az URL-cím egy része vagy a lekérdezési sztring értéke alapján szeretné kiválasztani a kérésben, használhatja az URL-átírási képesség és az útvonalalapú útválasztás kombinációját. Ha például van egy vásárlási webhelye, és a termékkategória lekérdezési sztringként van átküldve az URL-címben, és a kérést a háttérhez szeretné irányítatni a lekérdezési sztring alapján, akkor:

**1. lépés:**  Hozzon létre egy elérésiút-térképet az alábbi képen látható módon

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="1–1. forgatókönyv URL-átírása.":::

**2. lépés (a):** Hozzon létre egy újraírási készletet, amely 3 átírási sszabályokkal rendelkezik: 

* Az első szabály olyan feltétellel rendelkezik, amely ellenőrzi a *query_string* *category=shoes* változót, és rendelkezik egy olyan műveletpel, amely átírja a */listing1* **URL-címét,** és engedélyezve van az Elérési út újraértékelése térkép

* A második szabály olyan feltétellel rendelkezik, amely ellenőrzi a *query_string* *változót a category=bags* változóban, és rendelkezik egy olyan műveletével, amely újraírja a */listing2* **URL-címét,** és engedélyezve van az Útvonaltérkép újraértékelése lehetőség

* A harmadik szabály olyan feltétellel rendelkezik, amely ellenőrzi a *query_string* *változót a category=accessories* változóban, és rendelkezik egy olyan műveletkel, amely átírja a */listing3* **URL-címét,** és engedélyezve van az Elérési út újraértékelése térkép

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="1–2. forgatókönyv URL-átírása.":::

 

**2. lépés (b):** Társítsa az újraírási készletet a fenti elérésiút-alapú szabály alapértelmezett elérési úthoz

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="1–3. forgatókönyv URL-átírása.":::

Ha a felhasználó a contoso.com/listing?category=any *,* akkor az az alapértelmezett elérési úthoz lesz megfeleltetve, mivel az elérésiút-térképen (/listing1, /listing2, /listing3) egyik elérésiút-mintázat sem fog egyezni. Mivel a fenti átírási készletet ehhez az elérési úthoz társította, a rendszer kiértékeli ezt az átírási készletet. Mivel a lekérdezési sztring nem felel meg az átírási készletben található 3 átírási szabály feltételének, nem történik újraírási művelet, ezért a kérés változatlanul lesz az alapértelmezett elérési úthoz társított háttérhez *(GenericList).*

Ha a felhasználó a *contoso.com/listing?category=shoes,* akkor ismét az alapértelmezett elérési út lesz megfeleltetve. Ebben az esetben azonban az első szabályban megadott feltétel meg fog egyezni, ezért a feltételhez társított művelet végre lesz hajtva, amely átírja a */listing1*  URL-útvonalát, és újra kiértékeli az útvonaltérképet. Az elérésiút-térkép újra kiértékelése után a kérés megegyezik a */listing1* mintához társított elérési úttal, és a kérés a mintához társított háttérhez lesz irányítva, amely a ShoesListBackendPool.

>[!NOTE]
>Ez a forgatókönyv bármely fejléc- vagy cookie-értékre, URL-címre, lekérdezési sztringre vagy kiszolgálóváltozóra kiterjeszthető a meghatározott feltételek alapján, és lehetővé teszi a kérések ezen feltételek alapján való útválasztását.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Lekérdezési sztring paramétereinek átírása URL-cím alapján

Veteksünk egy vásárlási webhelyet, ahol a felhasználó számára látható hivatkozásnak egyszerűnek és olvashatónak kell lennie, de a háttérkiszolgálónak szüksége van a lekérdezési sztring paramétereire a megfelelő tartalom megjelenítése érdekében.

Ebben az esetben a Application Gateway rögzíthet paramétereket az URL-címből, és lekérdezési sztring kulcs-érték párokat adhat hozzá az URL-címből. Tegyük fel például, hogy a felhasználó át szeretné írni a címet a következő `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` URL-átírási konfigurációval.

**Feltétel** – Ha a kiszolgálóváltozó `uri_path` megegyezik a mintával `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-átírási forgatókönyv – 2–1.":::

**Művelet** – Állítsa az URL-címet a következőre: `buy.aspx` , a lekérdezési sztringet pedig a következőre: `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-átírási forgatókönyv– 2–2.":::

A fent leírt forgatókönyv eléréséhez szükséges részletes útmutatóért lásd: URL átírása Application Gateway [használatával Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL-átírás és URL-átirányítás

URL-átírás esetén a Application Gateway átírja az URL-címet, mielőtt a rendszer elküldi a kérést a háttérnek. Ez nem módosítja a böngészőben látható módosításokat, mivel a módosítások rejtve vannak a felhasználó elől.

URL-átirányítás esetén a Application Gateway átirányítási választ küld az ügyfélnek az új URL-cím használatával. Ez azt jelenti, hogy az ügyfélnek újra kell küldenie a kérését az átirányításban megadott új URL-címre. A felhasználó által a böngészőben látható URL-cím az új URL-címre frissül.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Átírás és átirányítás.":::

## <a name="limitations"></a>Korlátozások

- Ha egy válasz több, azonos nevű fejléccel rendelkezik, akkor ezen fejlécek egyikének újraírása a válasz többi fejlécének elejtéséhez vezet. Ez általában a fejléc Set-Cookie, mivel egy válaszban több Set-Cookie is lehet. Az egyik ilyen forgatókönyv az, amikor app service-t használ egy Alkalmazásátjáróval, és konfigurálta a cookie-alapú munkamenet-affinitást az Alkalmazásátjárón. Ebben az esetben a válasz két fejlécet Set-Cookie tartalmaz: egyet az App Service, egyet például: és egy másikat az `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` Application Gateway-affinitáshoz, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` például: . Az ebben a forgatókönyvben Set-Cookie fejlécek újraírása azt eredményezheti, hogy a Set-Cookie fejlécet eltávolítja a válaszból.
- Az újraírások nem támogatottak, ha az Alkalmazásátjáró úgy van konfigurálva, hogy átirányítsa a kérelmeket, vagy egyéni hibaoldalt mutasson.
- A fejlécnevek bármilyen alfanumerikus karaktert és meghatározott szimbólumot tartalmazhatnak az [RFC 7230 szabványban meghatározottak szerint.](https://tools.ietf.org/html/rfc7230#page-27) A fejlécnevekben jelenleg nem támogatott az aláhúzásjel ( ) speciális \_ karaktere.
- A kapcsolat- és frissítési fejlécek nem írhatók át

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan írhatja át a HTTP-fejléceket Application Gateway a Azure Portal](rewrite-http-headers-portal.md)
- [Megtudhatja, hogyan írhatja át az URL-címet Application Gateway a Azure Portal](rewrite-url-portal.md)
