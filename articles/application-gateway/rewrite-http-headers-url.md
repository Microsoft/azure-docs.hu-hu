---
title: HTTP-fejlécek és URL-cím újraírása az Azure Application Gateway-vel | Microsoft Docs
description: Ez a cikk áttekintést nyújt a HTTP-fejlécek és URL-címek újraírásáról az Azure-ban Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: 3e7bdc92dc6268c712eecbd69ff014e2229b3b84
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490964"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>HTTP-fejlécek és URL-cím újraírása Application Gateway

 Application Gateway lehetővé teszi a kérések és válaszok kiválasztott tartalmának újraírását. Ezzel a szolgáltatással lefordíthatja az URL-címeket, a lekérdezési karakterlánc paramétereit, valamint módosíthatja a kérések és válaszok fejléceit. Lehetővé teszi olyan feltételek hozzáadását is, amelyek biztosítják, hogy az URL-cím vagy a megadott fejlécek csak akkor legyenek újraírva, ha bizonyos feltételek teljesülnek. Ezek a feltételek a kérelem és a válasz információin alapulnak.

>[!NOTE]
>A HTTP-fejléc és az URL-átírási funkciók csak a [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) -hoz érhetők el

## <a name="rewrite-types-supported"></a>Újraírási típusok támogatottak

### <a name="request-and-response-headers"></a>Kérés- és válaszfejlécek

A HTTP-fejlécek lehetővé teszik, hogy az ügyfél és a kiszolgáló további adatokat adjon át egy kérésnek vagy válasznak. A fejlécek újraírásával fontos feladatokat hajthat végre, például a biztonsággal kapcsolatos fejléc-mezőket (például HSTS/X-XSS-Protection), a válasz fejlécének a bizalmas adatokat felderítő mezőinek eltávolítását, valamint a portok információinak eltávolítását az X által továbbított fejlécek esetében.

Az Application Gateway lehetővé teszi HTTP-kérelmek és -válaszok fejlécének hozzáadását, eltávolítását vagy frissítését, miközben a kérelem- és válaszcsomagok az ügyfél és a háttérkészlet között mozognak.

Ha meg szeretné tudni, hogyan írhatók át a kérések és válaszok fejlécei Application Gateway Azure Portal használatával [, tekintse](rewrite-url-portal.md)meg a következőt:.

![kép](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Támogatott fejlécek**

A kérelmekben és a válaszokban lévő összes fejlécet újraírhatja, kivéve a kapcsolatokat, és frissíti a fejléceket. Az Application Gateway használatával egyéni fejléceket is létrehozhat, és hozzáadhatja azokat az átirányított kérésekhez és válaszokhoz.

### <a name="url-path-and-query-string"></a>URL elérési útja és lekérdezési karakterlánca

A Application Gateway URL-re való Újraírási képességével a következőket teheti:

* Írja át a kérelem URL-címének állomásnevét, elérési útját és lekérdezési karakterláncát. 

* Válassza az összes kérelem URL-címének újraírását egy figyelőn, vagy csak azokat a kérelmeket, amelyek megfelelnek egy vagy több megadott feltételnek. Ezek a feltételek a kérelem és válasz tulajdonságai (kérelem, fejléc, válasz fejléc és kiszolgálói változók) alapján jelennek meg.

* Válassza ki a kérés irányítását (a háttér-készlet kiválasztása) az eredeti URL-cím vagy az újraírt URL-cím alapján

Az Application Gateway Azure Portal használatával történő újraírásának megismeréséhez lásd [itt](rewrite-url-portal.md).

![A Application Gateway tartalmazó URL-cím újraírásának folyamatát ismertető diagram.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Újraírási műveletek

Az Újraírási műveletek segítségével megadhatja az átírni kívánt URL-címet, a kérelmek fejléceit vagy a válasz fejléceit, valamint azt az új értéket, amelyre át kívánja írni azokat. Az URL-cím vagy egy új vagy meglévő fejléc értéke az alábbi típusú értékekre állítható be:

* Szöveg
* Kérelem fejléce A kérelem fejlécének megadásához a következő szintaxist kell használnia ({http_req_ *headerName*})
* Válaszfejléc. A válasz fejlécének megadásához a {http_resp_ *headerName*} szintaxist kell használnia.
* Kiszolgálói változó. A kiszolgálói változó megadásához a {var_ *serverVariable*} szintaxist kell használnia. Tekintse meg a támogatott kiszolgálói változók listáját
* A szöveg, a kérelem fejléce, a válasz fejléce és a kiszolgálói változó kombinációja. 

## <a name="rewrite-conditions"></a>Újraírási feltételek

Újraírási feltételeket, választható konfigurációt használhat a HTTP (S) kérelmek és válaszok tartalmának kiértékeléséhez, és csak akkor hajthat végre újraírást, ha egy vagy több feltétel teljesül. Az Application Gateway ezeket a típusú változókat használja a kérelmek és válaszok tartalmának kiértékeléséhez:

* A kérelemben szereplő HTTP-fejlécek
* A válaszban szereplő HTTP-fejlécek
* Application Gateway kiszolgálói változók

Feltételt használhat egy adott változó jelenlétének kiértékeléséhez, hogy egy adott változó megfelel-e egy adott értéknek, vagy hogy egy adott változó megfelel-e egy adott mintának. 


### <a name="pattern-matching"></a>Minta egyeztetése 

A Application Gateway reguláris kifejezéseket használ a feltételnek megfelelő mintázathoz. A [Perl kompatibilis reguláris kifejezések (PCRE) függvénytárával](https://www.pcre.org/) beállíthatja a feltételekhez tartozó reguláris kifejezési mintákat. A reguláris kifejezés szintaxisáról a [Perl reguláris kifejezések főoldala](https://perldoc.perl.org/perlre.html)című témakörben olvashat bővebben.

### <a name="capturing"></a>Rögzítése

Ha későbbi használatra szeretne létrehozni egy alkarakterláncot, helyezze a zárójeleket a feltétel regex-definíciójában szereplő alminta köré. A zárójelek első párja az 1., a második pár 2. és így tovább. Tetszőleges számú zárójelet használhat; A Perl csak több számú változót határoz meg a rögzített karakterláncok megjelenítéséhez. Néhány példa a [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# két számjegy egyeztetése, rögzítésük az 1. és 2. csoportba 

* /(\d +)/# egyezik egy vagy több számjegytel, rögzítheti őket az 1. csoportba 

* /(\d) +/# egyezés egy vagy több alkalommal, rögzítés az utolsó csoportba 1

A rögzítés után a következő formátumban hivatkozhat rájuk a műveleti készletben:

* A kérelmek fejlécének rögzítéséhez a következőt kell használnia: {http_req_headerName_groupNumber}. Például: {http_req_User-Agent_1} vagy {http_req_User-Agent_2}
* A válasz fejlécének rögzítéséhez a következőt kell használnia: {http_resp_headerName_groupNumber}. Például: {http_resp_Location_1} vagy {http_resp_Location_2}
* Kiszolgálói változók esetében {var_serverVariableName_groupNumber} használata szükséges. Például: {var_uri_path_1} vagy {var_uri_path_2}

Ha a teljes értéket szeretné használni, ne említse meg a számot. Egyszerűen használja a {http_req_headerName} formátumot, a groupNumber nélkül.

## <a name="server-variables"></a>Kiszolgálói változók

A Application Gateway kiszolgálói változók használatával tárolja a kiszolgálóval kapcsolatos hasznos információkat, az ügyféllel létesített kapcsolatokat és a jelenlegi kérést a kapcsolatban. A tárolt információk közé tartoznak például az ügyfél IP-címe és a webböngésző típusa. A kiszolgálói változók dinamikusan változnak, például új lap betöltésekor vagy űrlap közzétételekor. Ezeket a változókat használhatja az Újraírási feltételek kiértékeléséhez és a fejlécek újraírásához. Ha a kiszolgálói változók értékét szeretné használni a fejlécek újraírásához, ezeket a változókat a {var_ *serverVariableName*} szintaxisban kell megadnia.

Az Application Gateway a következő kiszolgálói változókat támogatja:

|   Változó neve    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Az X-Forwarded-For Client Request fejléc mező a `client_ip` (z) változóval (lásd a táblázat későbbi részében) az IP1, a IP2, a IP3 stb. formátumban. Ha az X-Forwardd-for mező nem szerepel az ügyfél-igénylési fejlécben, `add_x_forwarded_for_proxy` akkor a változó egyenlő a `$client_ip` változóval.   Ez a változó különösen akkor hasznos, ha újra szeretné írni az X által továbbított fejlécet a Application Gateway, hogy a fejléc csak az IP-címet tartalmazza a port adatai nélkül. |
| ciphers_supported         | Az ügyfél által támogatott titkosítások listája.               |
| ciphers_used              | Egy létesített TLS-kapcsolatban használt titkosítási karakterlánc. |
| client_ip                 | Annak az ügyfélnek az IP-címe, amelyről az Application Gateway fogadta a kérelmet. Ha fordított proxy van hátra az Application Gateway és a kezdeményező ügyfél előtt, a a `client_ip` fordított proxy IP-címét fogja visszaadni. |
| client_port               | Az ügyfél portja.                                             |
| client_tcp_rtt            | Az ügyfél TCP-kapcsolataival kapcsolatos információk. Az TCP_INFO socket beállítást támogató rendszereken érhető el. |
| client_user               | A HTTP-hitelesítés használatakor a rendszer a hitelesítéshez megadott felhasználónevet adja meg. |
| gazda                      | A sorrend sorrendjében: az állomásnév a kérelem sorából, az állomásnév a gazdagép-kérelem fejléce mezőből, vagy egy kérelemnek megfelelő kiszolgálónév. Példa: a kérelemben a `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` Host érték lesz `contoso.com` |
| cookie_ *neve*             | A *név* cookie.                                           |
| http_method               | Az URL-kérelem elvégzéséhez használt metódus. Például: GET vagy POST. |
| http_status               | A munkamenet állapota. Például: 200, 400 vagy 403.           |
| http_version              | A kérelem protokollja. Általában HTTP/1.0, HTTP/1.1 vagy HTTP/2.0. |
| query_string              | A kért URL-cím "?" értékét követő változó/érték párok listája. Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING érték lesz `id=123&title=fabrikam` |
| received_bytes            | A kérelem hossza (beleértve a kérelmek sorát, a fejlécet és a kérelem törzsét). |
| request_query             | A kérés sorában szereplő argumentumok                           |
| request_scheme            | A kérési séma: http vagy HTTPS.                           |
| request_uri               | A teljes eredeti kérelem URI-ja (argumentumokkal). Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` REQUEST_URI érték lesz `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Az ügyfélnek eljuttatott bájtok száma.                        |
| server_port               | A kérelmet fogadó kiszolgáló portja.              |
| ssl_connection_protocol   | Egy létesített TLS-kapcsolat protokollja.               |
| ssl_enabled               | "On", ha a kapcsolatok TLS módban működnek. Ellenkező esetben üres karakterláncot kell megadni. |
| uri_path                  | Annak a gazdagépnek az adott erőforrását azonosítja, amelyet a webes ügyfél szeretne elérni. Ez az argumentumok nélküli kérelem URI-ja. Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path érték lesz `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Kölcsönös hitelesítési kiszolgálói változók (előzetes verzió)

A Application Gateway a következő kiszolgálói változókat támogatja a kölcsönös hitelesítési helyzetekben. Ezeket a kiszolgálói változókat ugyanúgy használhatja, mint a többi kiszolgálói változót. 

|   Változó neve    |                   Description                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | A PEM-ügyféltanúsítvány a létesített SSL-kapcsolathoz. |
| client_certificate_end_date| Az ügyféltanúsítvány záró dátuma. |
| client_certificate_fingerprint| Az ügyféltanúsítvány SHA1 ujjlenyomata egy létesített SSL-kapcsolathoz. |
| client_certificate_issuer | Az ügyféltanúsítvány "kiállító megkülönböztető neve" karakterlánca egy létesített SSL-kapcsolathoz. |
| client_certificate_serial | Az ügyféltanúsítvány sorozatszáma egy létesített SSL-kapcsolathoz.  |
| client_certificate_start_date| Az ügyféltanúsítvány kezdő dátuma. |
| client_certificate_subject| Az ügyféltanúsítvány tulajdonos DN-karakterlánca egy létesített SSL-kapcsolathoz. |
| client_certificate_verification| Az ügyféltanúsítvány-ellenőrzés eredménye: *sikeres*, *sikertelen: <reason>*, vagy *nincs* , ha egy tanúsítvány nem volt jelen. | 

## <a name="rewrite-configuration"></a>Konfiguráció újraírása

Egy Újraírási szabály konfigurálásához létre kell hoznia egy Újraírási szabályt, és hozzá kell adnia az Újraírási szabály konfigurációját.

Az újraírható szabálykészlet A következőket tartalmazza:

* **Kérelem-útválasztási szabály társítása:** Az Újraírási konfiguráció a forrás-figyelőhöz az útválasztási szabály használatával van társítva. Alapszintű útválasztási szabály használata esetén az Újraírási konfiguráció egy forrás-figyelőhöz van társítva, és a globális fejléc újraírása. Elérésiút-alapú útválasztási szabály használata esetén az Újraírási konfiguráció az URL-cím elérési útja alapján van meghatározva. Ebben az esetben csak a hely adott elérési útja területére vonatkozik. Több Újraírási készlet is létrehozható, és az egyes Újraírási csoportok több figyelőre is alkalmazhatók. De csak egy Újraírási készletet alkalmazhat egy adott figyelőre.

* **Újraírási feltétel**: opcionális konfiguráció. Az Újraírási feltételek kiértékelik a HTTP (S) kérelmek és válaszok tartalmát. Az újraírás művelet akkor fordul elő, ha a HTTP (S) kérelem vagy válasz megfelel az Újraírási feltételnek. Ha egynél több feltételt társít egy művelethez, a művelet csak akkor lép fel, ha az összes feltétel teljesül. Más szóval a művelet logikai és művelet.

* **Újraírás típusa**: 3 típusú újraírás érhető el:
   * Kérések fejlécének újraírása 
   * Válasz-fejlécek újraírása.
   * URL-cím újraírása: az URL-átírási típus 3 összetevőből áll
      * **URL-cím elérési útja**: az az érték, amelynek az elérési útját újra kell írni. 
      * **URL-lekérdezési karakterlánc**: az az érték, amelyre a lekérdezési karakterláncot újra kell írni. 
      * **Elérésiút-hozzárendelés újraértékelése**: annak megállapítására szolgál, hogy az URL-cím elérési útjának újraértékelése megtörténjen-e, vagy sem. Ha nincs bejelölve, a rendszer az eredeti URL-cím elérési útját fogja használni az URL-cím elérési útjának elérési útjának megfeleltetéséhez. Ha igaz értékre van állítva, a rendszer újraértékeli az URL-cím elérési útját, hogy ellenőrizze az átírásos útvonal egyezését. A kapcsoló engedélyezése lehetővé teszi, hogy a kérést egy másik háttérbeli készletbe irányítsa át az újraírás után.

## <a name="rewrite-configuration-common-pitfall"></a>A konfiguráció közös buktatóinak újraírása

* Az újraértékelési elérésiút-hozzárendelés engedélyezése nem engedélyezett az alapszintű kérelmek útválasztási szabályaihoz. Ennek célja, hogy megakadályozza az alapszintű útválasztási szabály végtelen kiértékelési ciklusát.

* Legalább 1 feltételes Újraírási szabálynak vagy 1 Újraírási szabálynak kell lennie, amely nem rendelkezik "újraértékelési elérésiút-hozzárendeléssel" az elérésiút-alapú útválasztási szabályokhoz, hogy megakadályozza a végtelen kiértékelési hurok használatát egy elérésiút-alapú útválasztási szabályhoz.

* A bejövő kérelmeket a 500 hibakód határozza meg, ha a hurok dinamikusan jön létre az ügyfél bemenetei alapján. A Application Gateway továbbra is más kérelmeket fog kiszolgálni, az ilyen forgatókönyvek romlása nélkül.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>URL-újraírás vagy állomásfejléc-újraírás használata webalkalmazási tűzfallal (WAF_v2 SKU)

Ha az URL-újraírást vagy a állomásfejléc újraírását konfigurálja, a WAF kiértékelése a kérelem fejlécére vagy URL-címére (az újraírás után) való módosítás után fog történni. Ha eltávolítja az URL-újraírást vagy a állomásfejléc Újraírási konfigurációját a Application Gatewayon, akkor a WAF kiértékelése a fejléc újraírása előtt történik (előre írás). Ez a sorrend biztosítja, hogy a WAF-szabályok a háttér-készlet által fogadott végső kérelemre legyenek alkalmazva.

Tegyük fel például, hogy az alábbi fejléc-Újraírási szabályt adja meg a fejléchez `"Accept" : "text/html"` – Ha a fejléc értéke `"Accept"` egyenlő `"text/html"` , majd írja át az értéket a következőre: `"image/png"` .

Itt csak a fejléc újraírására van konfigurálva, a WAF kiértékelése történik `"Accept" : "text/html"` . Ha azonban beállítja az URL-újraírást vagy a gazdagép fejlécének újraírását, akkor a WAF kiértékelése történik `"Accept" : "image/png"` .

>[!NOTE]
> Az URL-írási műveletek várhatóan kisebb növekedést okoznak a WAF CPU-kihasználtsága Application Gateway. Azt javasoljuk, hogy egy rövid ideig figyelje a [CPU-kihasználtság metrikáját](high-traffic-support.md) , miután engedélyezte az URL-címek Újraírási szabályait a WAF Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>A fejléc újraírásának gyakori forgatókönyvei

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>A port adatainak eltávolítása az X által továbbított fejlécből

Application Gateway beszúr egy X-vel továbbított-fejlécet az összes kérelembe, mielőtt továbbítja a kéréseket a háttérbe. Ez a fejléc az IP-portok vesszővel tagolt listája. Előfordulhatnak olyan helyzetek, amikor a háttér-kiszolgálóknak csak az IP-címeket tartalmazó fejlécekre van szükségük. A fejléc újraírásával eltávolíthatja a port információit az X által továbbított-for fejlécből. Ennek egyik módja, ha a fejlécet a add_x_forwarded_for_proxy Server változóra állítja be. Azt is megteheti, hogy a client_ip változót is használja:

![Port eltávolítása](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Átirányítási URL-cím módosítása

Ha egy háttérbeli alkalmazás átirányítási választ küld, előfordulhat, hogy át szeretné irányítani az ügyfelet egy másik URL-címre, mint amelyet a háttérbeli alkalmazás adott meg. Tegyük fel például, hogy ezt akkor érdemes megtenni, ha egy app Service-t egy Application Gateway mögött futtat, és azt igényli, hogy az ügyfél átirányítsa a relatív elérési útra. (Például egy átirányítás a contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2-be.)

Mivel a App Service egy több-bérlős szolgáltatás, a kérelemben szereplő állomásfejléc használatával irányítja át a kérést a megfelelő végpontra. Az App Services alapértelmezett tartományneve *. azurewebsites.net (Say contoso.azurewebsites.net), amely eltér az Application Gateway tartománynevétől (mondjuk contoso.com). Mivel az ügyfél eredeti kérelme az Application Gateway tartománynevét (contoso.com) adja meg állomásnévként, az Application Gateway megváltoztatja az állomásnevet a contoso.azurewebsites.net. Ez a módosítás lehetővé teszi, hogy az App Service átirányítsa a kérést a megfelelő végpontra.

Amikor az App Service átirányítási választ küld, ugyanazt a gazdagépet használja a válaszának Location fejlécében, mint az Application gatewaytől kapott kérelemben szereplőnek. Így az ügyfél a kérést közvetlenül az `contoso.azurewebsites.net/path2` Application Gateway () szolgáltatáson keresztül teszi elérhetővé `contoso.com/path2` . Az Application Gateway megkerülése nem kívánatos.

A probléma megoldásához állítsa be a hostname értéket a Location (hely) fejlécben az Application Gateway tartománynevére.

Az állomásnév lecserélésének lépései a következők:

1. Hozzon létre egy Újraírási szabályt egy feltétellel, amely kiértékeli, hogy a válaszban található azurewebsites.net tartalmaz-e. Adja meg a mintát `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Hajtson végre egy műveletet a hely fejlécének újraírásához, hogy az Application Gateway állomásneve legyen. Ezt a fejléc értékének megadásával teheti meg `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` . Azt is megteheti, hogy a kiszolgálói változót is használja az `host` eredeti kérelemnek megfelelő állomásnév beállítására.

![Hely fejlécének módosítása](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági HTTP-fejlécek implementálása a sebezhetőségek megelőzése érdekében

Számos biztonsági rést kijavíthat, ha a szükséges fejléceket az alkalmazás válaszában hajtja végre. Ezek a biztonsági fejlécek a következők: X-XSS-Protection, Strict Transport-Security és Content-Security-Policy. Az összes válaszhoz a Application Gateway használatával állíthatja be ezeket a fejléceket.

![Biztonsági fejléc](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Nemkívánatos fejlécek törlése

Előfordulhat, hogy el szeretné távolítani azokat a fejléceket, amelyek bizalmas adatokat tárnak fel egy HTTP-válasz alapján. Előfordulhat például, hogy el szeretné távolítani az adatokat, például a háttér-kiszolgáló nevét, az operációs rendszert vagy a könyvtár részleteit. Az Application Gateway használatával eltávolíthatja ezeket a fejléceket:

![Fejléc törlése](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Fejléc jelenlétének keresése

Kiértékelheti a HTTP-kérések vagy a válasz fejléceit egy fejléc vagy kiszolgálói változó jelenlétéhez. Ez a kiértékelés akkor lehet hasznos, ha a fejlécet csak akkor szeretné elvégezni, ha egy adott fejléc szerepel.

![Fejléc meglétének ellenőrzése](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>URL-átírás gyakori forgatókönyvei

#### <a name="parameter-based-path-selection"></a>Paraméter alapú útvonal kiválasztása

Ha olyan forgatókönyveket szeretne elérni, amelyekben a háttér-készletet a fejléc értéke alapján szeretné kiválasztani, a kérelemben szereplő URL-cím vagy lekérdezési karakterlánc egy részét használhatja az URL-írási képesség és az elérésiút-alapú útválasztás kombinációjával.  Ha például van egy bevásárló webhelye, és a termék kategóriáját a rendszer lekérdezési karakterláncként adja át az URL-címben, és a lekérdezési karakterlánc alapján szeretné átirányítani a kérést a háttérbe, akkor:

**1. lépés:**  Hozzon létre egy elérésiút-térképet az alábbi képen látható módon

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL-Újraírási forgatókönyv 1-1.":::

**2. lépés (a):** Hozzon létre egy Újraírási készletet, amelynek 3 Újraírási szabálya van: 

* Az első szabály olyan feltétellel rendelkezik, amely ellenőrzi a *QUERY_STRING*  változót a *Kategória = cipők* esetében, és olyan művelettel rendelkezik, amely átírja az URL-útvonalat a/*listing1* , és **újraértékeli az elérésiút-leképezést** engedélyezve

* A második szabály olyan feltétellel rendelkezik, amely ellenőrzi a *QUERY_STRING*  változót a *Kategória = táskák* esetében, és olyan művelettel rendelkezik, amely átírja az URL-útvonalat a/*listing2*  , és **újraértékeli az elérésiút-leképezést** engedélyezve

* A harmadik szabály olyan feltételt tartalmaz, amely ellenőrzi a *Kategória = kellékek* *QUERY_STRING* változóját, és olyan műveletet tartalmaz, amely átírja az URL-címet/*listing3* , és **újraértékeli az elérésiút-leképezést** .

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL-Újraírási forgatókönyv 1-2.":::

 

**2. lépés (b):** Az Újraírási készlet hozzárendelése a fenti elérésiút-alapú szabály alapértelmezett elérési útjával

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL-Újraírási forgatókönyv 1-3.":::

Ha a felhasználó *contoso.com/Listing?category=any* kér, akkor a rendszer az alapértelmezett elérési utat fogja egyeztetni, mivel az elérésiút-leképezésben (/listing1,/listing2,/listing3) található egyik elérésiút-minta sem egyezik. Mivel ezzel az útvonallal társította a fenti Újraírási készletet, a rendszer kiértékeli ezt az Újraírási készletet. Mivel a lekérdezési karakterlánc nem felel meg az Újraírási készletben szereplő 3 Újraírási szabály feltételének, ezért nem kerül sor újraírásra, így a kérést a rendszer változatlanul az alapértelmezett elérési úttal ( *GenericList*) társított háttérre irányítja.

 Ha a felhasználó *contoso.com/Listing?category=Shoes kér,* akkor a rendszer az alapértelmezett elérési utat fogja megfeleltetni. Ebben az esetben azonban az első szabály feltétele megegyezik, és ezért a feltételhez társított művelet lesz végrehajtva, amely újraírja az URL-útvonalat a/*listing1*  , és újraértékeli az elérési út térképét. Ha a rendszer újraértékeli az elérési utat, a kérelem most már megegyezik a minta */listing1* társított útvonallal, és a kérést a rendszer az ehhez a mintához társított háttérre irányítja, amely ShoesListBackendPool

>[!NOTE]
>Ez a forgatókönyv kiterjeszthető bármely fejlécre vagy cookie-értékre, URL-útvonalra, lekérdezési sztringre vagy kiszolgálói változóra a megadott feltétel alapján, és alapvetően lehetővé teszi a kérelmek ezen feltételek alapján történő átirányítását.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Lekérdezési karakterlánc paramétereinek újraírása az URL-cím alapján

Vegyünk egy olyan forgatókönyvet, amelyben a felhasználó számára látható hivatkozás egyszerű és olvasható, de a háttér-kiszolgálónak a lekérdezési karakterlánc paramétereit kell megadnia a megfelelő tartalom megjelenítéséhez.

Ebben az esetben Application Gateway rögzíthet paramétereket az URL-címről, és hozzáadhat lekérdezési karakterlánc kulcs-érték párokat az URL-címről. Tegyük fel például, hogy a felhasználó újra szeretné írni a- `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` t, a következő URL-írási konfiguráción keresztül.

**Feltétel** – ha a kiszolgálói változó `uri_path` megegyezik a mintázattal `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-Újraírási forgatókönyv 2-1.":::

**Művelet** – az URL-cím `buy.aspx` és a lekérdezési karakterlánc beállítása a következőre: `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-Újraírási forgatókönyv 2-2.":::

A fentiekben ismertetett forgatókönyvhöz kapcsolódó lépésenkénti útmutató: az [URL-cím újraírása Application Gateway használatával Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL-újraírás vs URL-átirányítás

URL-cím újraírása esetén Application Gateway a kérésnek a háttérbe való küldése előtt átírja az URL-címet. Ez nem változtatja meg, hogy a felhasználók mit látnak a böngészőben, mert a módosítások rejtve vannak a felhasználó elől.

URL-átirányítás esetén Application Gateway átirányítási választ küld az ügyfélnek az új URL-címmel. Ehhez viszont az ügyfélnek újra el kell küldenie a kérését az átirányításban megadott új URL-címre. Az URL-cím, amelyet a felhasználó lát a böngészőben, frissíti az új URL-címet

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Újraírás vs átirányítás.":::

## <a name="limitations"></a>Korlátozások

- Ha egy válasznak több fejléce is van ugyanazzal a névvel, akkor az egyik fejléc értékének átírása miatt a rendszer eldobja a válasz többi fejlécét. Ez általában Set-Cookie fejlécnél fordulhat elő, mert egy válaszban több Set-Cookie-fejléc is szerepelhet. Az egyik ilyen eset az, amikor app Service-t használ egy Application Gateway-vel, és konfigurálta a cookie-alapú munkamenet-affinitást az Application gatewayben. Ebben az esetben a válasz két Set-Cookie fejlécet fog tartalmazni: az egyiket az App Service használja, például: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` és egy másikat az Application Gateway-affinitáshoz, például: `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Ebben a forgatókönyvben a Set-Cookie fejlécek egyikének újraírásával a másik Set-Cookie fejlécet is eltávolíthatja a válaszból.
- Az újraírások nem támogatottak, ha az Application Gateway úgy van konfigurálva, hogy átirányítsa a kéréseket, vagy megjelenjen egy egyéni hiba lap.
- A fejlécek nevei a 7230-as [számú RFC-dokumentumban](https://tools.ietf.org/html/rfc7230#page-27)meghatározott alfanumerikus karaktereket és egyedi szimbólumokat tartalmazhatnak. Jelenleg nem támogatjuk az aláhúzás (_) speciális karaktert a fejlécek neveiben.
- A kapcsolatok és a frissítési fejlécek nem írhatók át

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan írhatók át a HTTP-fejlécek Application Gateway használatával Azure Portal](rewrite-http-headers-portal.md)
- [Megtudhatja, hogyan írhatja át az URL-címet Application Gateway használatával Azure Portal](rewrite-url-portal.md)
