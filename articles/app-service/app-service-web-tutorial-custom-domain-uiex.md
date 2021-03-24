---
title: 'Oktatóanyag: meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (Vanity-tartományt) egy webalkalmazáshoz, egy mobil alkalmazás-háttérhez vagy egy API-alkalmazáshoz Azure App Service.
keywords: App Service, Azure app Service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, állomásnév, hiúság tartománya
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32ad6fa122083b40a948345e360bf5b9b0f09e96
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954853"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: meglévő egyéni DNS-név leképezése Azure App Service

Ez az oktatóanyag bemutatja, hogyan képezheti le a meglévőket <abbr title="Egy olyan tartománynév, amelyet egy olyan tartományi regisztrálótól vásárolt, mint a GoDaddy vagy a megvásárolt tartomány altartománya.">Egyéni DNS-tartománynév</abbr> felhasználóként a(z) <abbr title="HTTP-alapú szolgáltatás webes alkalmazások, REST API-k és mobil háttérbeli alkalmazások üzemeltetéséhez.">Azure App Service</abbr>.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Altartomány hozzárendelése a használatával <abbr title="Egy DNS-kanonikus név rekord egy tartománynevet rendel egy másikhoz.">CNAME-rekord</abbr>.
> * Legfelső szintű tartomány hozzárendelése a használatával <abbr title="A DNS-beli címtartomány egy állomásnevet rendel egy IP-címhez.">A-rekord</abbr>.
> * Helyettesítő karakteres tartomány hozzárendelése CNAME rekord használatával.
> * Átirányítja az alapértelmezett URL-címet egy egyéni könyvtárba.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Győződjön meg arról, hogy szerkesztheti az egyéni tartomány DNS-rekordjait. Ha még nem rendelkezik egyéni tartománnyal, [vásárolhat app Service tartományt](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Mi szükséges a DNS-rekordok szerkesztéséhez?</summary>
        Hozzáférést igényel a tartományi szolgáltató (például a GoDaddy) DNS-beállításjegyzékéhez. Például a <code>contoso.com</code> és a <code>www.contoso.com</code> DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a <code>contoso.com</code> gyökértartomány DNS-beállításainak konfigurálására.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. az alkalmazás előkészítése

Ha egyéni DNS-nevet szeretne hozzárendelni egy alkalmazáshoz, az alkalmazás <abbr title="Megadja az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és funkcióit.">App Service-csomag</abbr> fizetős szintűnek kell lennie (nem <abbr title="Egy Azure App Service szinten, amelyben az alkalmazás ugyanazon a virtuális gépeken fut, mint a többi alkalmazás, beleértve a többi ügyfél alkalmazást is. Ez a platform fejlesztési és tesztelési célokra szolgál.">**Ingyenes (F1)**</abbr>). További információ: [Azure app Service terv áttekintése](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a [Azure Portal](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.

#### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást a Azure Portal

1. Keresse meg és válassza ki a **app Services**.

   ![A App Services kijelölését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

   ![Képernyőfelvétel: a portál navigációs felülete egy Azure-alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Megjelenik az App Service-alkalmazás felügyeleti oldala.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1. Az alkalmazás lap bal oldali paneljén görgessen a **Beállítások** szakaszhoz, és válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

   ![A vertikális felskálázás (App Service terv) menüt bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** -es szinten van. Az egyéni DNS nem támogatott az **F1** -es szinten.

   ![Az ajánlott árképzési csomagokat bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Ha az App Service terv nem az **F1** -es szinten található, akkor a vertikális **Felskálázási** oldalt zárjuk be, és ugorjon a 3 értékre [. Tartomány-ellenőrzési azonosító beolvasása](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1. Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokért válassza a **További beállítások megjelenítése** lehetőséget.

1. Kattintson az **Alkalmaz** gombra.

   ![A díjszabási szintet bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

   ![A skálázási művelet megerősítését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. tartomány-ellenőrzési azonosító beszerzése

Ha egyéni tartományt szeretne felvenni az alkalmazásba, ellenőriznie kell a tartomány tulajdonjogát úgy, hogy egy ellenőrző azonosítót ad hozzá TXT-rekordként a tartományi szolgáltatóhoz. 

1. Az alkalmazás bal oldali ablaktábláján válassza az **Egyéni tartományok** lehetőséget. 
1. Másolja az azonosítót az egyéni **tartomány-ellenőrzési azonosító** mezőbe az **Egyéni tartományok** lapon a következő lépéshez.

    ![Képernyőkép, amely az azonosítót mutatja az egyéni tartomány-ellenőrzési azonosító mezőben.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Miért van erre szükség?</summary>
        Ha tartomány-ellenőrzési azonosítókat ad hozzá az egyéni tartományhoz, meggátolhatja a DNS-bejegyzések kihelyezését és az altartományon belüli átvétel elkerülését. Az ellenőrző azonosító nélkül korábban konfigurált egyéni tartományok esetében az ellenőrző AZONOSÍTÓnak a DNS-rekordhoz való hozzáadásával ugyanezt a kockázatot kell biztosítania. A gyakori, nagy súlyosságú fenyegetésekkel kapcsolatos további információkért lásd: <a href="/azure/security/fundamentals/subdomain-takeover">altartomány átvétele</a>.
    </details>
    
<a name="info"></a>

3. **(Csak rekord)** Hozzárendelés <abbr title="A DNS-beli címtartomány egy állomásnevet rendel egy IP-címhez.">A-rekord</abbr>, szüksége lesz az alkalmazás külső IP-címére. Az **Egyéni tartományok** lapon másolja ki az **IP-cím** értékét.

   ![Képernyőkép, amely egy Azure-alkalmazás portáljának navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. a DNS-rekordok létrehozása

1. Jelentkezzen be a tartományszolgáltatója webhelyére.

    <details>
        <summary>Kezelhetem a DNS-t a tartományom szolgáltatójából az Azure használatával?</summary>
        Ha szeretné, a Azure DNS segítségével kezelheti a tartomány DNS-rekordjait, és konfigurálhatja a Azure App Service egyéni DNS-nevét. További információ: <a href="/azure/dns/dns-delegate-domain-azure-dns">oktatóanyag: a tartomány üzemeltetése Azure DNS></a>.
    </details>

1. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. 

    <details>
        <summary>Hogyan megkeresni a lapot?</summary>
        <p>Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely <strong>Tartománynév</strong>, <strong>DNS</strong> vagy <strong>Névkiszolgáló kezelése</strong> címkével ellátott területeit keresse.</p>
        <p>A DNS-rekordok oldalon gyakran megtekintheti a fiók adatait, majd egy olyan hivatkozást keres, mint például <strong>a saját tartományok</strong>. Nyissa meg a lapot, majd keresse meg a nevet, például a <strong>zónafájl</strong>, a <strong>DNS-rekordok</strong>vagy a <strong>Speciális konfiguráció</strong>nevű hivatkozást.</p>
    </details>

   A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

   ![A DNS-rekordok egy példáját megjelenítő képernyőkép.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. A rekord létrehozásához válassza a **Hozzáadás** vagy a megfelelő widget elemet. 

1. Válassza ki a létrehozandó rekord típusát, és kövesse az utasításokat. Használhatja a <abbr title="A DNS-beli kanonikus név rekord egy tartománynevet (egy aliast) egy másikra (a kanonikus névre) képez.">CNAME-rekord</abbr> vagy egy <abbr title="A DNS-beli címtartomány egy állomásnevet rendel egy IP-címhez.">A-rekord</abbr> Egyéni DNS-név leképezése App Service. 

    <details>
        <summary>Melyik rekordot válasszam?</summary>
        <div>
            <ul>
            <li>A legfelső szintű tartomány (például) leképezéséhez <code>contoso.com</code> használjon egy rekordot. Ne használja a gyökérszintű rekord CNAME rekordját (További információ: <a href="https://en.wikipedia.org/wiki/CNAME_record">wikipedia bejegyzés</a>).</li>
            <li>Egy altartomány hozzárendeléséhez (például <code>www.contoso.com</code> ) használjon CNAME rekordot.</li>
            <li>Egy altartományt közvetlenül az alkalmazás IP-címére képezhető le egy rekorddal, de lehetséges, <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">hogy az IP-cím változhat</a>. A CNAME leképezése az alkalmazás állomásneve, ami kevésbé hajlamos a módosításra.</li>
            <li><a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">Helyettesítő karakteres tartomány</a> (például) leképezéséhez <code>*.contoso.com</code> használjon CNAME rekordot.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Az `www` `www.contoso.com` alábbi táblázat szerint hozzon létre két rekordot az-ban található altartományhoz:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `<subdomain>` (például: `www` ) | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid.<subdomain>` (például: `asuid.www` ) | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

![Képernyőkép, amely megjeleníti a portál navigációs felületét egy Azure-alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Az `contoso.com` alábbi táblázat alapján hozzon létre két rekordot a gyökértartomány számára:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#3-get-a-domain-verification-id) szakaszból származó IP-cím | Maga a tartomány-hozzárendelés ( `@` általában a legfelső szintű tartományt jelenti). |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. A gyökérszintű tartományhoz használja a következőt: `asuid` . |

![A DNS-rekordok lapot megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Mi a teendő, ha egy altartományt szeretnék leképezni egy rekorddal?</summary>
Ha olyan altartományt szeretne leképezni, mint egy `www.contoso.com` rekord egy javasolt CNAME-rekord helyett, akkor a rekordnak és a txt-rekordnak a következő táblázathoz hasonlóan kell kinéznie:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">3. táblázat</caption>
<thead>
<tr>
<th>Rekordtípus</th>
<th>Gazdagép</th>
<th>Érték</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (például: <code>www</code> )</td>
<td><a href="#info" data-linktype="self-bookmark">Az alkalmazás IP-címének másolása</a> szakaszból származó IP-cím</td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (például: <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">A korábban kapott ellenőrző azonosító</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Helyettesítő karakter (CNAME)](#tab/wildcard)

A (z) rendszerbeli helyettesítő karakterekhez hasonlóan `*` `*.contoso.com` hozzon létre két rekordot az alábbi táblázat szerint:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service hozzáfér a `asuid` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

![Képernyőkép, amely egy Azure-alkalmazás navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>A módosítások törlődnek az oldal elhagyása után.</summary>
<p>Bizonyos szolgáltatók (például a GoDaddy) esetében a DNS-rekordok módosításai csak a külön <strong>Módosítások mentése</strong> hivatkozás kiválasztása után lépnek életbe.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. a leképezés engedélyezése az alkalmazásban

1. A Azure Portal alkalmazás lap bal oldali ablaktábláján válassza az **Egyéni tartományok** elemet.

    ![Az egyéni tartományok menüt megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép az állomásnév hozzáadása elemről.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME (www \. example.com vagy bármely altartomány)** értékre van beállítva. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely az egyéni tartomány hozzáadása gombot jeleníti meg.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Mi a <strong>nem biztonságos</strong> figyelmeztetési címke?</summary>
        Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: <a href="/azure/app-service/configure-ssl-bindings">Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban</a>.
    </details>

    Ha kihagyott egy lépést, vagy korábban elgépelt egy hibát, a lap alján egy ellenőrző hiba jelenik meg.

    ![Az ellenőrzési hibát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`). 

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely a DNS-név hozzáadását mutatja be az alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    ![A rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Mi a <strong>nem biztonságos</strong> figyelmeztetési címke?</summary>
        Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: <a href="/azure/app-service/configure-ssl-bindings">Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban</a>.
    </details>
    
    Ha kihagyott egy lépést, vagy korábban elgépelt egy hibát, a lap alján egy ellenőrző hiba jelenik meg.
    
    ![Az ellenőrzési hibát mutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Helyettesítő karakter (CNAME)](#tab/wildcard)

3. Adjon meg egy teljesen minősített tartománynevet, amely megfelel a helyettesítő karakteres tartománynak. Például a,, `*.contoso.com` `sub1.contoso.com` `sub2.contoso.com` `*.contoso.com` vagy bármely más, a helyettesítő karakterrel egyező karakterláncot használhatja. Ezután válassza az **Érvényesítés** lehetőséget.

    Az **egyéni tartomány hozzáadása** gomb aktiválva van.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME rekord (www \. example.com vagy bármely altartomány)**. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely a DNS-név hozzáadását mutatja az alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    <details>
        <summary>Mi a <strong>nem biztonságos</strong> figyelmeztetési címke?</summary>
        Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: <a href="/azure/app-service/configure-ssl-bindings">Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. tesztelés böngészőben

Tallózással keresse meg a korábban konfigurált DNS-neveket.

![Képernyőkép, amely egy Azure-alkalmazás navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>HTTP 404 (nem található) hibaüzenet jelenik meg.</summary>
<ul>
<li>A konfigurált egyéni tartományból hiányzik egy rekord vagy egy CNAME rekord.</li>
<li>A böngészőügyfél gyorsítótárazta a tartomány régi IP-címét. Törölje a gyorsítótárat, és ismételje meg a DNS-feloldási tesztet. Windows-gépen az <code>ipconfig /flushdns</code> paranccsal törölheti a gyorsítótárat.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

<details>
<summary>Erre van szükségem?</summary>
<p>Ez az alkalmazástól függ. Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem&#39;t a gyökérkönyvtárban indulnak el. A <a href="https://laravel.com/">Laravel</a> például a <code>public</code> alkönyvtárban indul. A <code>contoso.com</code> DNS-példa folytatásához egy ilyen alkalmazás a (z) címen érhető el <code>http://contoso.com/public</code> , de <code>http://contoso.com</code> <code>public</code> ehelyett inkább a címtárhoz szeretne csatlakozni. </p>
</details>

Bár ez egy gyakori forgatókönyv, valójában nem magában foglalja az egyéni tartomány-hozzárendelést, de az alkalmazáson belüli virtuális könyvtár testreszabására van szükség.

1. A webalkalmazás oldal bal oldali ablaktábláján válassza az **Alkalmazásbeállítások** lehetőséget.

1. A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

    ![A virtuális könyvtárak testreszabását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. A művelet befejeződése után az alkalmazás gyökérkönyvtárában navigáljon a böngészőben (például `http://contoso.com` vagy `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/)segítségével automatizálhatja az egyéni tartományok parancsfájlokkal történő kezelését.

#### <a name="azure-cli"></a>Azure CLI

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

További információ: [Egyéni tartomány leképezése egy webalkalmazásra](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

További információ: [Egyéni tartomány hozzárendelése egy webalkalmazáshoz](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan köthető egy egyéni TLS/SSL-tanúsítvány egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)