---
title: HTTP-fejlécek újraírása az Azure Application Gateway-vel | Microsoft Docs
description: Ez a cikk áttekintést nyújt a HTTP-fejlécek újraírásáról az Azure-ban Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 7c5b4f0d5d4b153684683963c56b7506e76d963e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575653"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>HTTP-fejlécek újraírása Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A HTTP-fejlécek lehetővé teszik, hogy az ügyfél és a kiszolgáló további adatokat adjon át egy kérésnek vagy válasznak. A fejlécek újraírásával fontos feladatokat hajthat végre, például a biztonsággal kapcsolatos fejléc-mezőket (például HSTS/X-XSS-Protection), a válasz fejlécének a bizalmas adatokat felderítő mezőinek eltávolítását, valamint a portok információinak eltávolítását az X által továbbított fejlécek esetében.

Az Application Gateway lehetővé teszi HTTP-kérelmek és -válaszok fejlécének hozzáadását, eltávolítását vagy frissítését, miközben a kérelem- és válaszcsomagok az ügyfél és a háttérkészlet között mozognak. Ezenkívül lehetővé teszi feltételek megadását, hogy a megadott fejlécek átírására csak ezen feltételek teljesülése esetén legyen lehetőség.

A Application Gateway számos [kiszolgálói változót](#server-variables) is támogat, amelyek segítenek a kérelmekkel és a válaszokkal kapcsolatos további információk tárolásában. Így könnyebben hozhat létre hatékony Újraírási szabályokat.

> [!NOTE]
>
> A HTTP-fejléc újraírásának támogatása csak a [Standard_V2 és WAF_V2 SKU](application-gateway-autoscaling-zone-redundant.md)esetében érhető el.

![Fejlécek újraírása](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Támogatott fejlécek

A kérések és válaszok összes fejlécét újraírhatja, kivéve a gazdagép, a kapcsolatok és a frissítés fejléceit. Az Application Gateway használatával egyéni fejléceket is létrehozhat, és hozzáadhatja azokat az átirányított kérésekhez és válaszokhoz.

## <a name="rewrite-conditions"></a>Újraírási feltételek

Az Újraírási feltételek használatával kiértékelheti a HTTP (S) kérelmek és válaszok tartalmát, és csak akkor hajthat végre újraírást, ha egy vagy több feltétel teljesül. Az Application Gateway ezeket a típusú változókat használja a HTTP (S) kérelmek és válaszok tartalmának kiértékeléséhez:

- A kérelemben szereplő HTTP-fejlécek.
- A válaszban szereplő HTTP-fejlécek.
- Application Gateway kiszolgálói változók.

Feltételt használhat egy adott változó jelenlétének kiértékeléséhez, hogy egy adott változó megfelel-e egy adott értéknek, vagy hogy egy adott változó megfelel-e egy adott mintának. A Perl- [kompatibilis reguláris kifejezések (PCRE) függvénytárával](https://www.pcre.org/) állíthatja be a reguláris kifejezési mintákat a feltételek szerint. A reguláris kifejezés szintaxisáról a [Perl reguláris kifejezések főoldala](https://perldoc.perl.org/perlre.html)című témakörben olvashat bővebben.

## <a name="rewrite-actions"></a>Újraírási műveletek

Az Újraírási műveletek segítségével megadhatja az újraírni kívánt kérelem és válasz fejléceit, valamint a fejlécek új értékét. Létrehozhat egy új fejlécet, módosíthatja egy meglévő fejléc értékét, vagy törölhet egy meglévő fejlécet is. Az új fejléc vagy egy meglévő fejléc értéke az alábbi típusú értékekre állítható be:

- Szöveg.
- Kérelem fejléce A kérelem fejlécének megadásához a következő szintaxist kell használnia: {http_req_ *headerName*}.
- Válaszfejléc. A válasz fejlécének megadásához a következő szintaxist kell használnia: {http_resp_ *headerName*}.
- Kiszolgálói változó. A kiszolgálói változó megadásához a {var_ *serverVariable*} szintaxist kell használnia.
- A szöveg, a kérelem fejléce, a válasz fejléce és a kiszolgálói változó kombinációja.

## <a name="server-variables"></a>Kiszolgálói változók

A Application Gateway kiszolgálói változók használatával tárolja a kiszolgálóval kapcsolatos hasznos információkat, az ügyféllel létesített kapcsolatokat és a jelenlegi kérést a kapcsolatban. A tárolt információk közé tartoznak például az ügyfél IP-címe és a webböngésző típusa. A kiszolgálói változók dinamikusan változnak, például új lap betöltésekor vagy űrlap közzétételekor. Ezeket a változókat használhatja az Újraírási feltételek kiértékeléséhez és a fejlécek újraírásához. Ha a kiszolgálói változók értékét szeretné használni a fejlécek újraírásához, ezeket a változókat a {var_ *serverVariable*} szintaxisban kell megadnia.

Az Application Gateway a következő kiszolgálói változókat támogatja:

| Változó neve | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Az X-Forwarded-For Client Request fejléc mező a `client_ip` (z) változóval (lásd a táblázat későbbi részében) az IP1, a IP2, a IP3 stb. formátumban. Ha az X-Forwardd-for mező nem szerepel az ügyfél-igénylési fejlécben, `add_x_forwarded_for_proxy` akkor a változó egyenlő a `$client_ip` változóval. Ez a változó különösen akkor hasznos, ha újra szeretné írni az X által továbbított fejlécet a Application Gateway, hogy a fejléc csak az IP-címet tartalmazza a port adatai nélkül. |
| ciphers_supported          | Az ügyfél által támogatott titkosítások listája.          |
| ciphers_used               | Egy létesített TLS-kapcsolatban használt titkosítási karakterlánc. |
| client_ip                  | Annak az ügyfélnek az IP-címe, amelyről az Application Gateway fogadta a kérelmet. Ha van fordított proxy az Application Gateway és a kezdeményező ügyfél előtt, *client_ip* a fordított proxy IP-címét fogja visszaadni. |
| client_port                | Az ügyfél portja.                                                  |
| client_tcp_rtt             | Az ügyfél TCP-kapcsolataival kapcsolatos információk. Az TCP_INFO socket beállítást támogató rendszereken érhető el. |
| client_user                | A HTTP-hitelesítés használatakor a rendszer a hitelesítéshez megadott felhasználónevet adja meg. |
| gazda                       | A sorrend sorrendjében: az állomásnév a kérelem sorából, az állomásnév a gazdagép-kérelem fejléce mezőből, vagy egy kérelemnek megfelelő kiszolgálónév. Példa: a kérelemben a `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` gazdagép értéke *contoso.com* lesz. |
| cookie_ *neve*              | A *név* cookie.                                            |
| http_method                | Az URL-kérelem elvégzéséhez használt metódus. Például: GET vagy POST. |
| http_status                | A munkamenet állapota. Például: 200, 400 vagy 403.                       |
| http_version               | A kérelem protokollja. Általában HTTP/1.0, HTTP/1.1 vagy HTTP/2.0. |
| query_string               | A kért URL-cím "?" értékét követő változó/érték párok listája. Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING érték *azonosító = 123&title = Fabrikam* |
| received_bytes             | A kérelem hossza (beleértve a kérelmek sorát, a fejlécet és a kérelem törzsét). |
| request_query              | A kérés sorában szereplő argumentumok                                |
| request_scheme             | A kérési séma: http vagy HTTPS.                            |
| request_uri                | A teljes eredeti kérelem URI-ja (argumentumokkal). Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` REQUEST_URI érték lesz a */Article.aspx? id = 123&title = Fabrikam*   |
| sent_bytes                 | Az ügyfélnek eljuttatott bájtok száma.                             |
| server_port                | A kérelmet fogadó kiszolgáló portja.                 |
| ssl_connection_protocol    | Egy létesített TLS-kapcsolat protokollja.        |
| ssl_enabled                | "On", ha a kapcsolatok TLS módban működnek. Ellenkező esetben üres karakterláncot kell megadni. |
| uri_path                   | Annak a gazdagépnek az adott erőforrását azonosítja, amelyet a webes ügyfél szeretne elérni. Ez az argumentumok nélküli kérelem URI-ja. Példa: a kérelemben `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path érték lesz a */Article.aspx*  |

## <a name="rewrite-configuration"></a>Konfiguráció újraírása

A HTTP-fejléc újraírásának konfigurálásához el kell végeznie ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **Újraírási művelet**: a kérelem és a kérelem fejlécének azon mezőinek megadására szolgál, amelyeket át szeretne írni, valamint a fejlécek új értékét. Egy vagy több Újraírási feltétel is társítható egy Újraírási művelettel.

   - **Újraírási feltétel**: opcionális konfiguráció. Az Újraírási feltételek kiértékelik a HTTP (S) kérelmek és válaszok tartalmát. Az újraírás művelet akkor fordul elő, ha a HTTP (S) kérelem vagy válasz megfelel az Újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor lép fel, ha az összes feltétel teljesül. Más szóval a művelet logikai és művelet.

   - **Újraírási szabály**: több Újraírási művelet/Újraírási feltétel kombinációt tartalmaz.

   - **Szabály sorrendje**: segít meghatározni, hogy az Újraírási szabályok hogyan legyenek végrehajtva. Ez a konfiguráció akkor hasznos, ha több Újraírási szabály található egy Újraírási készletben. Az alacsonyabb szabálykészlet-értékkel rendelkező Újraírási szabály először fut. Ha ugyanezt a szabályt két Újraírási szabályhoz rendeli hozzá, a végrehajtás sorrendje nem determinisztikus.

   - **Újraírási készlet**: több Újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatolja az Újraírási készletet (*rewriteRuleSet*) egy útválasztási szabályhoz. Az Újraírási konfiguráció a forrás figyelőhöz van csatolva az útválasztási szabály segítségével. Alapszintű útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció egy forrás-figyelőhöz van társítva, és a globális fejléc újraírása. Elérésiút-alapú útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció az URL-cím elérési útja alapján van definiálva. Ebben az esetben csak a hely adott elérési útja területére vonatkozik.
   > [!NOTE]
   > URL-újraírás módosítja a fejléceket; nem változtatja meg az elérési út URL-címét.

Több, a HTTP-fejléc Újraírási készletét is létrehozhatja, és az egyes újraírásokat több figyelőre alkalmazhatja. De csak egy Újraírási készletet alkalmazhat egy adott figyelőre.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Íme néhány gyakori forgatókönyv a fejléc újraírásának használatához.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>A port adatainak eltávolítása az X által továbbított fejlécből

Application Gateway beszúr egy X-vel továbbított-fejlécet az összes kérelembe, mielőtt továbbítja a kéréseket a háttérbe. Ez a fejléc az IP-portok vesszővel tagolt listája. Előfordulhatnak olyan helyzetek, amikor a háttér-kiszolgálóknak csak az IP-címeket tartalmazó fejlécekre van szükségük. A fejléc újraírásával eltávolíthatja a port információit az X által továbbított-for fejlécből. Ennek egyik módja, ha a fejlécet a add_x_forwarded_for_proxy Server változóra állítja be:

![Port eltávolítása](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Átirányítási URL-cím módosítása

Ha egy háttérbeli alkalmazás átirányítási választ küld, előfordulhat, hogy át szeretné irányítani az ügyfelet egy másik URL-címre, mint amelyet a háttérbeli alkalmazás adott meg. Tegyük fel például, hogy ezt akkor érdemes megtenni, ha egy app Service-t egy Application Gateway mögött futtat, és azt igényli, hogy az ügyfél átirányítsa a relatív elérési útra. (Például egy átirányítás a contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2-be.)

Mivel a App Service egy több-bérlős szolgáltatás, a kérelemben szereplő állomásfejléc használatával irányítja át a kérést a megfelelő végpontra. Az App Services alapértelmezett tartományneve *. azurewebsites.net (Say contoso.azurewebsites.net), amely eltér az Application Gateway tartománynevétől (mondjuk contoso.com). Mivel az ügyfél eredeti kérelme az Application Gateway tartománynevét (contoso.com) adja meg állomásnévként, az Application Gateway megváltoztatja az állomásnevet a contoso.azurewebsites.net. Ez a módosítás lehetővé teszi, hogy az App Service átirányítsa a kérést a megfelelő végpontra.

Amikor az App Service átirányítási választ küld, ugyanazt a gazdagépet használja a válaszának Location fejlécében, mint az Application gatewaytől kapott kérelemben szereplőnek. Így az ügyfél közvetlenül a contoso.azurewebsites.net/path2 küldi a kérést az Application Gateway (contoso.com/path2) helyett. Az Application Gateway megkerülése nem kívánatos.

A probléma megoldásához állítsa be a hostname értéket a Location (hely) fejlécben az Application Gateway tartománynevére.

Az állomásnév lecserélésének lépései a következők:

1. Hozzon létre egy Újraírási szabályt egy feltétellel, amely kiértékeli, hogy a válaszban található azurewebsites.net tartalmaz-e. Adja meg a mintát `(https?):\/\/.*azurewebsites\.net(.*)$` .
1. Hajtson végre egy műveletet a hely fejlécének újraírásához, hogy az Application Gateway állomásneve legyen. Ezt a fejléc értékének megadásával teheti meg `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` .

![Hely fejlécének módosítása](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Biztonsági HTTP-fejlécek implementálása a sebezhetőségek megelőzése érdekében

Számos biztonsági rést kijavíthat, ha a szükséges fejléceket az alkalmazás válaszában hajtja végre. Ezek a biztonsági fejlécek a következők: X-XSS-Protection, Strict Transport-Security és Content-Security-Policy. Az összes válaszhoz a Application Gateway használatával állíthatja be ezeket a fejléceket.

![Biztonsági fejléc](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Nemkívánatos fejlécek törlése

Előfordulhat, hogy el szeretné távolítani azokat a fejléceket, amelyek bizalmas adatokat tárnak fel egy HTTP-válasz alapján. Előfordulhat például, hogy el szeretné távolítani az adatokat, például a háttér-kiszolgáló nevét, az operációs rendszert vagy a könyvtár részleteit. Az Application Gateway használatával eltávolíthatja ezeket a fejléceket:

![Fejléc törlése](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Fejléc jelenlétének keresése

Kiértékelheti a HTTP-kérések vagy a válasz fejléceit egy fejléc vagy kiszolgálói változó jelenlétéhez. Ez a kiértékelés akkor lehet hasznos, ha a fejlécet csak akkor szeretné elvégezni, ha egy adott fejléc szerepel.

![Fejléc meglétének ellenőrzése](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Korlátozások

- Ha egy válasznak több fejléce is van ugyanazzal a névvel, akkor az egyik fejléc értékének átírása miatt a rendszer eldobja a válasz többi fejlécét. Ez általában Set-Cookie fejlécnél fordulhat elő, mert egy válaszban több Set-Cookie-fejléc is szerepelhet. Az egyik ilyen eset az, amikor app Service-t használ egy Application Gateway-vel, és konfigurálta a cookie-alapú munkamenet-affinitást az Application gatewayben. Ebben az esetben a válasz két Set-Cookie fejlécet fog tartalmazni: az egyiket az App Service használja, például: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` és egy másikat az Application Gateway-affinitáshoz, például: `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Ebben a forgatókönyvben a Set-Cookie fejlécek egyikének újraírásával a másik Set-Cookie fejlécet is eltávolíthatja a válaszból.

- Az újraírások nem támogatottak, ha az Application Gateway úgy van konfigurálva, hogy átirányítsa a kéréseket, vagy megjelenjen egy egyéni hiba lap.

- A kapcsolatok, a verziófrissítés és a gazdagép-fejlécek újraírása jelenleg nem támogatott.

- A fejlécek nevei a 7230-as [számú RFC-dokumentumban](https://tools.ietf.org/html/rfc7230#page-27)meghatározott alfanumerikus karaktereket és egyedi szimbólumokat tartalmazhatnak. Jelenleg nem támogatjuk az aláhúzásjel ( \_ ) speciális karaktert a fejlécek neveiben.

## <a name="next-steps"></a>Következő lépések

A HTTP-fejlécek újraírásának megismeréséhez tekintse meg a következő témakört:

- [HTTP-fejlécek átírása az Azure Portal használatával](./rewrite-http-headers-portal.md)
- [HTTP-fejlécek újraírása Azure PowerShell használatával](add-http-header-rewrite-rule-powershell.md)