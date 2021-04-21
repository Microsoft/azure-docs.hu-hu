---
title: 'Oktatóanyag: Meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (egyéni tartományt) egy webalkalmazáshoz, egy mobilalkalmazás háttéralkalmazásához vagy egy API-alkalmazáshoz a Azure App Service.
keywords: app service, azure app service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, gazdagépnév
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 8e310cb0507146eb53c7b55c2aaed492baa79521
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833244"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: Meglévő egyéni DNS-név leképezése Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan lehet leképezni egy meglévő egyéni DNS-nevet a App Service.

![Az Azure-Azure Portal navigációját bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Leképezhet egy altartományt (például ) egy `www.contoso.com` CNAME rekord használatával.
> * Leképezhet egy gyökértartományt (például `contoso.com` ) egy A rekord használatával.
> * Leképezhet egy helyettesítő karakteres tartományt (például ) egy `*.contoso.com` CNAME rekord használatával.
> * Irányítsa át az alapértelmezett URL-címet egy egyéni könyvtárba.
> * Tartományleképezés automatizálása szkriptekkel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Vásároljon egy tartománynevet, és győződjön meg arról, hogy rendelkezik hozzáféréssel a tartományszolgáltató (például a GoDaddy) DNS-beállításjegyzékéhez.

  Például a `contoso.com` és a `www.contoso.com` DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a `contoso.com` gyökértartomány DNS-beállításainak konfigurálására.

  > [!NOTE]
  > Ha még nem létezik tartománynév, fontolja meg egy tartomány megvásárlását [a Azure Portal.](manage-custom-dns-buy-domain.md)

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Egy egyéni DNS-név webalkalmazásra való leképezéséhez a webalkalmazás [App Service-csomagnak](https://azure.microsoft.com/pricing/details/app-service/) fizetős csomagnak kell lennie (megosztott, alapszintű, standard, prémium vagy használatú Azure Functions). Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Ha eltávolít vagy hozzáad egy egyéni tartományt a webalkalmazáshoz, a webhely újraindul.
### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg [a Azure Portal,](https://portal.azure.com)és jelentkezzen be Azure-fiókjával.

### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást a Azure Portal

1. Keresse meg és válassza a **App Services.**

   ![Képernyőkép a beállítások App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. A **App Services** válassza ki az Azure-alkalmazás nevét.

   ![Képernyőkép egy Azure-alkalmazás portálon való navigálásról.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Megjelenik az App Service-alkalmazás felügyeleti oldala.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1. Az alkalmazásoldal bal oldali panelen görgessen a **Beállítások** szakaszhoz, és válassza a **Felskálás (App Service csomag) lehetőséget.**

   ![Képernyőkép a Felskála (App Service terv) menüről.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Ellenőrizze, hogy az alkalmazás nem az **F1** szinten van-e. Az egyéni DNS nem támogatott az **F1 szinten.**

   ![Az ajánlott tarifacsomagokat bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Ha a App Service csomag nem az **F1** szinten van, zárja be a Felskálás oldalt, és ugorjon [a CNAME rekord leképezését gombra.](#map-a-cname-record) 

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1. Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokért válassza a **További beállításokat lásd:**.

1. Kattintson az **Alkalmaz** gombra.

   ![Képernyőkép a tarifacsomag ellenőrzésről.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

   ![A méretezési művelet megerősítését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Tartomány-ellenőrzési azonosító leellenőrzése

Ha egyéni tartományt szeretne hozzáadni az alkalmazáshoz, ellenőriznie kell a tartomány tulajdonjogát úgy, hogy hozzáad egy ellenőrző azonosítót TXT-rekordként a tartományszolgáltatónál. Az alkalmazásoldal bal oldali panelen válassza az Egyéni **tartományok lehetőséget.** A következő lépéshez másolja Custom Domain **Egyéni** tartományok  lap Ellenőrző azonosító mezőben található azonosítót.

![Az Azonosító mezőben látható Custom Domain képernyőkép.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Ha tartomány-ellenőrzési címeket ad hozzá az egyéni tartományhoz, az megakadályozhatja a dns-bejegyzések összefűzését, és segít elkerülni az altartományok feladatátvételét. Az ellenőrző azonosító nélkül korábban konfigurált egyéni tartományok esetén az ellenőrző azonosító DNS-rekordhoz való hozzáadásával védheti meg őket ugyanazokkal a kockázatokkal. További információ erről a gyakori, nagy súlyosságú fenyegetésről: [Altartományok átvétele.](../security/fundamentals/subdomain-takeover.md)

## <a name="map-your-domain"></a>Saját tartomány leképezése

Az egyéni DNS-neveket CNAME rekordok vagy A rekordok használatával is leképezheti az App Service-re. Hajtsa végre a megfelelő lépéseket:

- [CNAME rekord hozzárendelése](#map-a-cname-record)
- [A rekord hozzárendelése](#map-an-a-record)
- [Helyettesítő karaktert tartalmazó tartomány leképezése (CNAME-rekorddal)](#map-a-wildcard-domain)

> [!NOTE]
> A gyökértartományok (például: `contoso.com`) kivételével minden egyéni DNS-névhez CNAME-rekordot használjon. Gyökértartományokhoz A-rekordokat használjon.

### <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Az oktatóanyag példájában a `www` altartományhoz (például `www.contoso.com`) tartozó CNAME rekordot ad hozzá.

Ha az altartománya nem , cserélje le a helyére az altartományt (például a helyére, ha az egyéni `www` `www` `sub` tartománya `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományszolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Leképez egy altartományt az alkalmazás alapértelmezett tartománynevére ( , ahol `<app-name>.azurewebsites.net` `<app-name>` az az alkalmazás neve). Az altartomány CNAME-leképezésének `www` létrehozásához hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Magát a tartományleképezést. |
| TXT | `asuid.www` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service a TXT-rekordhoz való hozzáféréssel ellenőrzi az egyéni tartomány `asuid.<subdomain>` tulajdonjogát. |

A CNAME és TXT rekordok hozzáadása után a DNS-rekordok oldala az alábbi példához hasonlít:

![Képernyőkép egy Azure-alkalmazáshoz való navigálásról a portálon.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

1. Az alkalmazás oldalának bal oldali panelen a Azure Portal válassza az **Egyéni tartományok lehetőséget.**

    ![Az egyéni tartományok menüt bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az alkalmazás **Egyéni tartományok** lapján adja hozzá a teljes egyéni DNS-nevet ( ) a `www.contoso.com` listához.

1. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![Az Állomásnév hozzáadása elemet bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik **az Egyéni tartomány** hozzáadása lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípus** beállítása **CNAME (www \. example.com vagy bármely altartomány).** Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![Az Egyéni tartomány hozzáadása gombot bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Az egyéni tartomány figyelmeztető címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához lásd: [Secure a custom DNS name with a TLS/SSL binding in Azure App Service.](configure-ssl-bindings.md)

    Ha kihagyott egy lépést, vagy korábban elírást végzett, ellenőrzési hiba jelenik meg az oldal alján.

    ![Ellenőrzési hibát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A rekord hozzárendelése

Az oktatóanyag példájában egy A rekordot ad hozzá a gyökértartományhoz (például `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Az alkalmazás IP-címének másolása

A rekord leképezéséhez az alkalmazás külső IP-címére van szükség. Ezt az IP-címet az alkalmazás Egyéni tartományok **lapján** találja a Azure Portal.

1. Az alkalmazás oldalának bal oldali panelen válassza Azure Portal Egyéni **tartományok lehetőséget.**

   ![Az egyéni tartományok menüt bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az Egyéni **tartományok lapon** másolja ki az alkalmazás IP-címét.

   ![Képernyőkép egy Azure-alkalmazás portálon való navigálásról.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományszolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Az A rekord létrehozása

Ha egy A rekordot egy alkalmazáshoz ( általában a gyökértartományhoz) kell leképezni, hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím | Maga a tartományleképezés ( `@` általában a gyökértartományt jelöli). |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service TXT-rekordhoz fér hozzá az egyéni tartomány `asuid.<subdomain>` tulajdonjogának ellenőrzéséhez. A gyökértartományhoz használja a következőt: `asuid` . |

> [!NOTE]
> Ha altartományt (például ) szeretne hozzáadni egy ajánlott CNAME rekord helyett egy A rekord használatával, az A rekordnak és a TXT-rekordnak inkább az alábbi táblához kell `www.contoso.com` hasonlítanunk: [](#map-a-cname-record)
>
> | Rekordtípus | Gazdagép | Érték |
> | - | - | - |
> | A | `www` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
> | TXT | `asuid.www` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) |
>

A rekordok hozzáadása után a DNS-rekordok oldala az alábbi példához hasonlít:

![A DNS-rekordok oldalát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Az A rekord hozzárendelésének engedélyezése az alkalmazásban

Az alkalmazás Egyéni  tartományok lapjára visszatérve adja Azure Portal teljes egyéni DNS-nevet (például ) a `contoso.com` listához.

1. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![Gazdagépnév hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`). 

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik **az Egyéni tartomány** hozzáadása lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![A DNS-név alkalmazáshoz való hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

    ![Képernyőkép egy A rekord hozzáadásáról.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Az egyéni tartomány figyelmeztető címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához tekintse meg az egyéni [DNS-név TLS-/SSL-kötéssel](configure-ssl-bindings.md)való biztonságossá Azure App Service.
    
    Ha kihagyott egy lépést, vagy korábban elírást végzett, az oldal alján ellenőrzési hiba jelenik meg.
    
    ![Ellenőrzési hibát mutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Az oktatóanyag példájában egy [helyettesítő karaktert tartalmazó DNS-nevet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) képez le az App Service-alkalmazásra egy CNAME rekord hozzáadásával.

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományszolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Leképez egy helyettesítő karakteres nevet az alkalmazás alapértelmezett tartománynevére ( , ahol `*` `<app-name>.azurewebsites.net` a az alkalmazás `<app-name>` neve). A helyettesítő karakter nevének leképezésében hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Magát a tartományleképezést. |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service a TXT-rekordhoz való hozzáféréssel ellenőrzi az egyéni tartomány `asuid` tulajdonjogát. |

A `*.contoso.com` tartomány példájában a CNAME rekord a `*` előtagot a `<app-name>.azurewebsites.net` elemre képezi le.

A CNAME hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Képernyőkép egy Azure-alkalmazáshoz való navigációról.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>A CNAME rekord hozzárendelésének engedélyezése az alkalmazásban

Most már bármely olyan altartományt hozzáadhat az alkalmazáshoz, amely megfelel a helyettesítő karakter nevének (például , `sub1.contoso.com` és mindkettő megfelel a `sub2.contoso.com` `*.contoso.com` `*.contoso.com` következőnek: ).

1. Az alkalmazás oldalának bal oldali panelen válassza Azure Portal Egyéni **tartományok lehetőséget.**

    ![Az egyéni tartományok menüt bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![Gazdagépnév hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írjon be egy olyan teljes tartománynevet, amely megfelel a helyettesítő karaktert tartalmazó tartománynak (például `sub1.contoso.com`), majd válassza az **Érvényesítés** lehetőséget.

    Az **Egyéni tartomány hozzáadása** gomb aktiválva van.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípus** beállítása CNAME rekord **(www \. example.com vagy bármely altartomány).** Válassza **az Egyéni tartomány hozzáadása lehetőséget.**

    ![A DNS-név alkalmazáshoz való kiegészítését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Az új egyéni tartomány megjelenik az alkalmazás Egyéni tartományok **lapján.** Frissítse a böngészőt az adatok frissítéséhez.

1. Válassza ismét az ikont egy másik, a helyettesítő karakteres tartománynak megfelelő **+** egyéni tartomány hozzáadásához. Például adja hozzá a következőt: `sub2.contoso.com`.

    ![Képernyőkép egy CNAME rekord hozzáadásáról.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Az egyéni tartomány figyelmeztetési címkéje azt jelenti, hogy még nincs TLS-/SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba való HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásához lásd: [Secure a custom DNS name with a TLS/SSL binding in Azure App Service.](configure-ssl-bindings.md)
    
## <a name="test-in-a-browser"></a>Tesztelés böngészőben

Keresse meg a korábban konfigurált DNS-neveket (például `contoso.com` , `www.contoso.com` , és `sub1.contoso.com` `sub2.contoso.com` ).

![Képernyőkép egy Azure-alkalmazáshoz való navigálásról.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>A 404-es "Nem található" hiba elhárítása

Ha AZ egyéni tartomány URL-címének tallózása közben HTTP 404 <a href="https://www.nslookup.io/" target="_blank">(Nem</a>található) hibaüzenetet kap, ellenőrizze, hogy a tartomány feloldja-e az alkalmazás IP-címét a nslookup.io. Ha nem, ellenőrizze, hogy az A és ANAME rekordok megfelelően vannak-e konfigurálva ugyanazon a helyen. Ha az IP-címet helyesen oldja fel, de továbbra is 404-es hiba jelenik meg, akkor előfordulhat, hogy a böngésző gyorsítótárazza a tartomány régi IP-címét. Ürítse ki a gyorsítótárat, és tesztelje újra a DNS-feloldásokat. Windows-gépen az `ipconfig /flushdns` paranccsal törölheti a gyorsítótárat.

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem a gyökérkönyvtárban kezdődnek. A [Laravel](https://laravel.com/) például a `public` alkönyvtárban indul. A DNS-példa folytatásához egy ilyen alkalmazás elérhető a helyen, de ehelyett a `contoso.com` `http://contoso.com/public` `http://contoso.com` könyvtárba `public` szeretne irányítani. Ez a lépés nem DNS-feloldásról szól, hanem a virtuális könyvtár testreszabásáról.

A Windows-alkalmazások virtuális könyvtárának  testreszabásához válassza a webalkalmazás oldalának bal oldali panelének Alkalmazásbeállítások beállítását. 

> [!NOTE]
> A Linux-alkalmazásoknak nincs ilyen oldala. A Linux-alkalmazások helygyökerének módosítása a nyelvspecifikus konfigurációs útmutatókban[(például PHP)](configure-language-php.md?pivots=platform-linux#change-site-root)található.

A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

![A virtuális könyvtár testreszabását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

A művelet befejezése után az alkalmazásnak a megfelelő oldalt kell visszaadni a gyökérútvonalon (például `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az egyéni tartományok kezelését szkriptekkel automatizálhatja az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell.](/powershell/azure/)

### <a name="azure-cli"></a>Azure CLI

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

További információ: [Egyéni tartomány leképezése egy webalkalmazásra](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

További információ: [Egyéni tartomány hozzárendelése egy webalkalmazáshoz](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Leképez egy altartományt egy CNAME rekord használatával.
> * Gyökértartomány leképezása A rekord használatával.
> * Helyettesítő karakteres tartomány leképezés egy CNAME rekord használatával.
> * Irányítsa át az alapértelmezett URL-címet egy egyéni könyvtárba.
> * Tartományleképezés automatizálása szkriptekkel.

Folytassa a következő oktatóanyagtal, amelyből megtudhatja, hogyan köt egy egyéni TLS-/SSL-tanúsítványt egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá teése TLS/SSL-kötéssel a Azure App Service](configure-ssl-bindings.md)
