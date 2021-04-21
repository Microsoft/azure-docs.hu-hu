---
title: 'Oktatóanyag: Meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (egyéni tartományt) egy webalkalmazáshoz, egy mobilalkalmazás háttéralkalmazásához vagy egy API-alkalmazáshoz a Azure App Service.
keywords: app service, azure app service, tartományleképezés, tartománynév, meglévő tartomány, gazdagépnév, hiús tartomány
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1fcf8c681f4fad65209c27663045d4974be633f7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833245"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: Meglévő egyéni DNS-név leképezése Azure App Service

Ez az oktatóanyag bemutatja, hogyan lehet leképezni egy meglévőt <abbr title="Egy tartományregisztrálótól( például GoDaddy) vásárolt tartománynév, vagy a megvásárolt tartomány altartománya.">egyéni DNS-tartománynév</abbr> felhasználóként a(z) <abbr title="HTTP-alapú szolgáltatás webalkalmazások, REST API-k és mobil háttéralkalmazások üzemeltetéséhez.">Azure App Service</abbr>.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Altartomány leképezés a következővel: <abbr title="A DNS-hez szükséges névrekordok az egyik tartománynevet leképezik egy másikra.">CNAME-rekord</abbr>.
> * Gyökértartomány leképezása a következővel: <abbr title="A DNS-ben egy címrekord leképezi az állomásnevet egy IP-címre.">A-rekord</abbr>.
> * Helyettesítő karakteres tartomány leképezés egy CNAME rekord használatával.
> * Irányítsa át az alapértelmezett URL-címet egy egyéni könyvtárba.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Mindenképpen szerkessze az egyéni tartomány DNS-rekordjait. Ha még nem használhatja az egyéni tartományt, vásárolhat egy App Service [tartományt.](manage-custom-dns-buy-domain.md)

    <details>
        <summary>Mire van szükség a DNS-rekordok szerkesztéséhez?</summary>
        Hozzáférést igényel a tartományszolgáltató DNS-beállításjegyzékéhez, például a GoDaddyhez. Például a <code>contoso.com</code> és a <code>www.contoso.com</code> DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a <code>contoso.com</code> gyökértartomány DNS-beállításainak konfigurálására.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. Az alkalmazás előkészítése

Ha egyéni DNS-nevet kell leképezni egy alkalmazásra, az alkalmazás <abbr title="Az alkalmazást tartalmazó webkiszolgálófarm helyét, méretét és szolgáltatásait adja meg.">App Service-csomag</abbr> fizetős szintnek kell lennie (nem <abbr title="Egy Azure App Service szint, amelyben az alkalmazás a többi alkalmazással azonos virtuális gépeken fut, beleértve más ügyfelek alkalmazásait is. Ez a szint fejlesztési és tesztelési célokra szolgál.">**Ingyenes (F1)**</abbr>). További információkért lásd a [Azure App Service áttekintését.](overview-hosting-plans.md)

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg [a Azure Portal,](https://portal.azure.com)és jelentkezzen be Azure-fiókjával.

#### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást a Azure Portal

1. Keresse meg és válassza a **App Services.**

   ![Képernyőkép az adatok App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. A **App Services** válassza ki az Azure-alkalmazás nevét.

   ![Képernyőkép egy Azure-alkalmazás portálon való navigálásról.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Megjelenik az App Service-alkalmazás felügyeleti oldala.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1. Az alkalmazásoldal bal oldali panelen görgessen a **Beállítások** szakaszhoz, és válassza a **Felskálás (App Service csomag) lehetőséget.**

   ![Képernyőkép a Felskála (App Service terv) menüről.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Ellenőrizze, hogy az alkalmazás nem az **F1** szinten van-e. Az egyéni DNS nem támogatott az **F1 szinten.**

   ![Az Ajánlott tarifacsomagok képernyőképe.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Ha a App Service csomag nem az **F1 szinten** van, zárja be a Felskálás **oldalt,** és ugorjon [a 3. szintre. Szerezze be a tartomány-ellenőrzési azonosítót.](#3-get-a-domain-verification-id)

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1. Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokért válassza a **További beállításokat lásd:**.

1. Kattintson az **Alkalmaz** gombra.

   ![Képernyőkép a tarifacsomag ellenőrzésről.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

   ![Képernyőkép a méretezési művelet megerősítéséről.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Tartomány-ellenőrzési azonosító leellenőrzése

Ha egyéni tartományt szeretne hozzáadni az alkalmazáshoz, ellenőriznie kell a tartomány tulajdonjogát úgy, hogy hozzáad egy ellenőrző azonosítót TXT-rekordként a tartományszolgáltatónál. 

1. Az alkalmazáslap bal oldali panelen válassza az Egyéni **tartományok lehetőséget.** 
1. A következő lépéshez másolja Custom Domain **Egyéni** tartományok  lap Ellenőrző azonosító mezőben található azonosítót.

    ![Képernyőkép az Azonosító mezőben Custom Domain azonosítóról.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Miért van erre szükség?</summary>
        Ha tartomány-ellenőrzési címeket ad hozzá az egyéni tartományhoz, az megakadályozhatja az összeomó DNS-bejegyzéseket, és segít elkerülni az altartományok feladatátvételét. Az ellenőrző azonosító nélkül konfigurált egyéni tartományok esetén az ellenőrző azonosító DNS-rekordhoz való hozzáadásával védheti meg őket ugyanazokkal a kockázatokkal. További információ erről a gyakori, nagy súlyosságú fenyegetésről: <a href="/azure/security/fundamentals/subdomain-takeover">Altartományok átvétele.</a>
    </details>
    
<a name="info"></a>

3. **(Csak egy rekord)** Leképezés <abbr title="A DNS-ben egy címrekord leképezi az állomásnevet egy IP-címre.">A-rekord</abbr>, szüksége lesz az alkalmazás külső IP-címére. Az Egyéni **tartományok lapon** másolja ki az **IP-cím értékét.**

   ![Képernyőkép egy Azure-alkalmazás portálon való navigálásról.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. A DNS-rekordok létrehozása

1. Jelentkezzen be a tartományszolgáltatója webhelyére.

    <details>
        <summary>Kezelhetek DNS-t a tartományszolgáltatómtól az Azure-ral?</summary>
        Ha szeretné, a Azure DNS kezelheti a tartomány DNS-rekordjait, és egyéni DNS-nevet konfigurálhat a Azure App Service. További információ: <a href="/azure/dns/dns-delegate-domain-azure-dns">Oktatóanyag: A tartomány gazdagépként való Azure DNS>. </a>
    </details>

1. Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. 

    <details>
        <summary>Hogyan az oldalt?</summary>
        <p>Minden tartományszolgáltató saját felülettel rendelkezik a DNS-rekordok kezelésére, ezért tekintse meg a szolgáltatói dokumentációt. A webhely <strong>Tartománynév</strong>, <strong>DNS</strong> vagy <strong>Névkiszolgáló kezelése</strong> címkével ellátott területeit keresse.</p>
        <p>Gyakran előfordul, hogy megkeresi a DNS-rekordok oldalát a fiókinformációk megtekintésével, majd egy olyan hivatkozás megkeresése után, mint a <strong>Saját tartományok.</strong> Keresse meg az oldalt, és keresse meg a <strong>zónafájl,</strong> <strong>a DNS-rekordok</strong>vagy a Speciális konfiguráció nevű <strong>hivatkozást.</strong></p>
    </details>

   A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

   ![A példa DNS-rekordok oldalát bemutató képernyőkép.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Rekord **létrehozásához** válassza a Hozzáadás lehetőséget vagy a megfelelő widgetet. 

1. Válassza ki a létrehozni kívánt rekord típusát, és kövesse az utasításokat. Használhatja a <abbr title="A DNS-ben a Canonical Name rekord leképezi az egyik tartománynevet (aliast) egy másikra (a canonical névre).">CNAME-rekord</abbr> vagy egy <abbr title="A DNS-ben egy címrekord leképez egy állomásnevet egy IP-címre.">A-rekord</abbr> egyéni DNS-név leképezése a App Service. 

    <details>
        <summary>Melyik rekordot válasszam?</summary>
        <div>
            <ul>
            <li>A gyökértartomány (például ) leképezéséhez <code>contoso.com</code> használjon egy A rekordot. Ne használja a CNAME rekordot a gyökérrekordhoz (további információért lásd a <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipedia bejegyzését).</a></li>
            <li>Altartomány (például ) leképezéshez <code>www.contoso.com</code> használjon egy CNAME rekordot.</li>
            <li>Az altartományokat közvetlenül egy A rekordhoz is leképezheti az alkalmazás IP-címére, de az <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP-cím megváltoztatható.</a> A CNAME ehelyett az alkalmazás gazdanevére van leképezve, így kevésbé lesz kitéve a változásnak.</li>
            <li>Helyettesítő karakteres tartomány (például ) <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">leképezéséhez</a> <code>*.contoso.com</code> használjon egy CNAME rekordot.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Egy olyan altartományhoz, mint a `www` `www.contoso.com` ,, hozzon létre két rekordot az alábbi táblázat alapján:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `<subdomain>` (például: `www` ) | `<app-name>.azurewebsites.net` | Magát a tartományleképezést. |
| TXT | `asuid.<subdomain>` (például: `asuid.www` ) | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service a TXT-rekordhoz való hozzáféréssel ellenőrzi az egyéni tartomány `asuid.<subdomain>` tulajdonjogát. |

![Képernyőkép egy Azure-alkalmazáshoz való navigálásról a portálon.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

A gyökértartományok (például a) `contoso.com` számára hozzon létre két rekordot az alábbi táblázat alapján:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#3-get-a-domain-verification-id) szakaszból származó IP-cím | Maga a tartományleképezés ( `@` általában a gyökértartományt jelöli). |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service a TXT-rekordhoz való hozzáféréssel ellenőrzi az egyéni tartomány `asuid.<subdomain>` tulajdonjogát. A gyökértartományhoz használja a `asuid` következőt: . |

![A DNS-rekordok oldalát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Mi történik, ha le szeretnék leképezni egy altartományt egy A rekordra?</summary>
Ha altartományt szeretne leképezni egy A rekordra az ajánlott CNAME-rekord helyett, az A rekordnak és a TXT-rekordnak inkább az alábbi táblához kell `www.contoso.com` hasonlítanunk:

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

A helyettesítő karakter nevéhez (például a fájlhoz) `*` `*.contoso.com` hozzon létre két rekordot az alábbi táblázat alapján:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Magát a tartományleképezést. |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#3-get-a-domain-verification-id) | App Service TXT-rekordhoz fér hozzá az egyéni tartomány `asuid` tulajdonjogának ellenőrzéséhez. |

![Képernyőkép egy Azure-alkalmazáshoz való navigációról.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>A rendszer törli a módosításokat, miután elhagyom az oldalt.</summary>
<p>Bizonyos szolgáltatók (például a GoDaddy) esetében a DNS-rekordok módosításai csak a külön <strong>Módosítások mentése</strong> hivatkozás kiválasztása után lépnek életbe.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. A leképezés engedélyezése az alkalmazásban

1. Az alkalmazás oldalának bal oldali panelen válassza Azure Portal Egyéni **tartományok lehetőséget.**

    ![Képernyőkép az Egyéni tartományok menüről.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![A Gazdagépnév hozzáadása elemet bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik **az Egyéni tartomány** hozzáadása lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípus** **beállítása CNAME (www \. example.com vagy bármely altartomány).** Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![Az Egyéni tartomány hozzáadása gombot bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Mit jelent a <strong>Nem biztonságos figyelmeztető</strong> címke?</summary>
        Az egyéni tartomány figyelmeztetési címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához lásd: <a href="/azure/app-service/configure-ssl-bindings">Secure a custom DNS name with a TLS/SSL binding in Azure App Service.</a>
    </details>

    Ha kihagyott egy lépést, vagy korábban elírást végzett, az oldal alján ellenőrzési hiba jelenik meg.

    ![Ellenőrzési hibát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`). 

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik **az Egyéni tartomány** hozzáadása lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![A DNS-név alkalmazáshoz való hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

    ![Képernyőkép egy A rekord hozzáadásáról.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Mit jelent a <strong>Nem biztonságos figyelmeztető</strong> címke?</summary>
        Az egyéni tartomány figyelmeztető címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához tekintse meg az egyéni <a href="/azure/app-service/configure-ssl-bindings">DNS-név TLS-/SSL-kötéssel</a>való biztonságossá Azure App Service.
    </details>
    
    Ha kihagyott egy lépést, vagy korábban elírást végzett, az oldal alján ellenőrzési hiba jelenik meg.
    
    ![Ellenőrzési hibát mutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Helyettesítő karakter (CNAME)](#tab/wildcard)

3. Írjon be egy teljes tartománynevet, amely megfelel a helyettesítő karakteres tartománynak. A példában használhatja például a , , vagy bármely más sztringet, amely megfelel a `*.contoso.com` `sub1.contoso.com` helyettesítő karakter `sub2.contoso.com` `*.contoso.com` mintának. Ezután válassza az **Érvényesítés lehetőséget.**

    Az **Egyéni tartomány hozzáadása** gomb aktiválva van.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípus** beállítása CNAME rekord **(www \. example.com vagy bármely altartomány).**. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![A DNS-név alkalmazáshoz való kiegészítését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

    <details>
        <summary>Mit jelent a <strong>Nem biztonságos figyelmeztető</strong> címke?</summary>
        Az egyéni tartomány figyelmeztetési címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához lásd: <a href="/azure/app-service/configure-ssl-bindings">Secure a custom DNS name with a TLS/SSL binding in Azure App Service.</a>
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. Tesztelés böngészőben

Keresse meg a korábban konfigurált DNS-neveket.

![Képernyőkép egy Azure-alkalmazáshoz való navigálásról.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>HTTP 404 (Nem található) hibaüzenetet kapok.</summary>
<ul>
<li>A konfigurált egyéni tartományból hiányzik egy A vagy egy CNAME rekord.</li>
<li>A böngészőügyfél gyorsítótárazta a tartomány régi IP-címét. Ürítse ki a gyorsítótárat, és tesztelje újra a DNS-feloldásokat. Windows-gépen az <code>ipconfig /flushdns</code> paranccsal törölheti a gyorsítótárat.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

<details>
<summary>Szükségem van erre?</summary>
<p>Ez az alkalmazástól függ. Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem&#39;a gyökérkönyvtárban. A <a href="https://laravel.com/">Laravel</a> például a <code>public</code> alkönyvtárban indul. A DNS-példa folytatásához az ilyen alkalmazások a helyen érhetők el, de ehelyett a <code>contoso.com</code> <code>http://contoso.com/public</code> <code>http://contoso.com</code> könyvtárba szeretnénk <code>public</code> irányítani. </p>
</details>

Bár ez egy gyakori forgatókönyv, valójában nem tartalmaz egyéni tartományleképezést, hanem az alkalmazáson belüli virtuális könyvtár testreszabásáról szól.

1. Válassza **az Alkalmazásbeállítások** lehetőséget a webalkalmazás oldalának bal oldali paneljére.

1. A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

    ![A virtuális könyvtár testreszabását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. A művelet befejezése után ellenőrizze, hogy megnyitja az alkalmazás gyökérútvonalát a böngészőben (például vagy `http://contoso.com` `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Szkriptekkel automatizálhatja az egyéni tartományok kezelését az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell.](/powershell/azure/)

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

Folytassa a következő oktatóanyagtal, amelyből megtudhatja, hogyan köt egy egyéni TLS-/SSL-tanúsítványt egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztosítása TLS/SSL-kötéssel a Azure App Service](configure-ssl-bindings.md)
