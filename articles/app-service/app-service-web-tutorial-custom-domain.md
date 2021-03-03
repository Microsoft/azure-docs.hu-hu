---
title: 'Oktatóanyag: meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (Vanity-tartományt) egy webalkalmazáshoz, egy mobil alkalmazás-háttérhez vagy egy API-alkalmazáshoz Azure App Service.
keywords: app service, azure app service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, gazdagépnév
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: b3ff1b344852d57f0effbd978c06aa617682ea4f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720315"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: meglévő egyéni DNS-név leképezése Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan képezheti le egy meglévő egyéni tartománynévrendszer-(DNS-) nevet a App Service.

![Képernyőkép, amely megjeleníti az Azure-alkalmazások Azure Portal navigációját.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Altartomány hozzárendelése (például `www.contoso.com` ) CNAME-rekord használatával.
> * Legfelső szintű tartomány hozzárendelése (például `contoso.com` ) egy rekord használatával.
> * Helyettesítő karakteres tartomány hozzárendelése (például `*.contoso.com` ) CNAME-rekord használatával.
> * Átirányítja az alapértelmezett URL-címet egy egyéni könyvtárba.
> * A tartomány-hozzárendelés parancsfájlokkal való automatizálása.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Vásároljon egy tartománynevet, és győződjön meg arról, hogy van hozzáférése a tartományi szolgáltató (például a GoDaddy) DNS-beállításjegyzékéhez.

  Például a `contoso.com` és a `www.contoso.com` DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a `contoso.com` gyökértartomány DNS-beállításainak konfigurálására.

  > [!NOTE]
  > Ha nem rendelkezik meglévő tartománynévvel, érdemes lehet [tartományt megvásárolnia a Azure Portal használatával](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Ha egyéni DNS-nevet szeretne hozzárendelni egy webalkalmazáshoz, a webalkalmazás [app Service csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek kell lennie (közös, alapszintű, standard, prémium vagy Azure functions). Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg a [Azure Portal](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.

### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást a Azure Portal

1. Keresse meg és válassza ki a **app Services**.

   ![A App Services kijelölését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

   ![Képernyőfelvétel: a portál navigációs felülete egy Azure-alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Megjelenik az App Service-alkalmazás felügyeleti oldala.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1. Az alkalmazás lap bal oldali paneljén görgessen a **Beállítások** szakaszhoz, és válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

   ![A vertikális felskálázás (App Service terv) menüt bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** -es szinten van. Az egyéni DNS nem támogatott az **F1** -es szinten.

   ![Az ajánlott árképzési csomagokat bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Ha az App Service terv nem az **F1** -es szinten található, akkor a vertikális **felskálázás** lapot zárjuk be, és ugorjon a [CNAME rekord hozzárendelése](#map-a-cname-record)elemre.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1. Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokért válassza a **További beállítások megjelenítése** lehetőséget.

1. Kattintson az **Alkalmaz** gombra.

   ![A díjszabási szintet bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

   ![A skálázási művelet megerősítését bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Tartomány-ellenőrzési azonosító beszerzése

Ha egyéni tartományt szeretne felvenni az alkalmazásba, ellenőriznie kell a tartomány tulajdonjogát úgy, hogy egy ellenőrző azonosítót ad hozzá TXT-rekordként a tartományi szolgáltatóhoz. Az alkalmazás bal oldali ablaktábláján válassza az **Egyéni tartományok** lehetőséget. Másolja az azonosítót az egyéni **tartomány-ellenőrzési azonosító** mezőbe az **Egyéni tartományok** lapon a következő lépéshez.

![Képernyőkép, amely az azonosítót mutatja az egyéni tartomány-ellenőrzési azonosító mezőben.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Ha tartomány-ellenőrzési azonosítókat ad hozzá az egyéni tartományhoz, meggátolhatja a DNS-bejegyzések kihelyezését és az altartományon belüli átvétel elkerülését. Az ellenőrző azonosító nélkül korábban konfigurált egyéni tartományok esetében az ellenőrző AZONOSÍTÓnak a DNS-rekordhoz való hozzáadásával ugyanezt a kockázatot kell biztosítania. A gyakori, nagy súlyosságú fenyegetésekkel kapcsolatos további információkért lásd: [altartomány átvétele](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Saját tartomány leképezése

Az egyéni DNS-neveket CNAME rekordok vagy A rekordok használatával is leképezheti az App Service-re. Hajtsa végre a megfelelő lépéseket:

- [CNAME rekord hozzárendelése](#map-a-cname-record)
- [A rekord hozzárendelése](#map-an-a-record)
- [Helyettesítő karaktert tartalmazó tartomány leképezése (CNAME-rekorddal)](#map-a-wildcard-domain)

> [!NOTE]
> A gyökértartományok (például: `contoso.com`) kivételével minden egyéni DNS-névhez CNAME-rekordot használjon. Gyökértartományokhoz A-rekordokat használjon.

### <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Az oktatóanyag példájában a `www` altartományhoz (például `www.contoso.com`) tartozó CNAME rekordot ad hozzá.

Ha a-től eltérő altartománnyal rendelkezik `www` , cserélje le az `www` altartományra (például `sub` Ha az egyéni tartománya `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományi szolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Altartomány hozzárendelése az alkalmazás alapértelmezett tartománynevéhez (ahol az az `<app-name>.azurewebsites.net` `<app-name>` alkalmazás neve). Ha CNAME leképezést szeretne létrehozni az `www` altartományhoz, hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid.www` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

A CNAME és TXT rekordok hozzáadása után a DNS-rekordok oldal a következő példához hasonlóan néz ki:

![Képernyőkép, amely megjeleníti a portál navigációs felületét egy Azure-alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

1. A Azure Portal alkalmazás lap bal oldali ablaktábláján válassza az **Egyéni tartományok** elemet.

    ![Az egyéni tartományok menüt megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az alkalmazás **Egyéni tartományok** lapján adja hozzá a teljesen minősített egyéni DNS-nevet () a `www.contoso.com` listához.

1. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép az állomásnév hozzáadása elemről.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME (www \. example.com vagy bármely altartomány)** értékre van beállítva. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely az egyéni tartomány hozzáadása gombot jeleníti meg.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).

    Ha kihagyott egy lépést, vagy korábban elgépelt egy hibát, a lap alján egy ellenőrző hiba jelenik meg.

    ![Az ellenőrzési hibát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A rekord hozzárendelése

Az oktatóanyag példájában egy A rekordot ad hozzá a gyökértartományhoz (például `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Az alkalmazás IP-címének másolása

A rekord leképezéséhez az alkalmazás külső IP-címére van szükség. Ezt az IP-címet az alkalmazás **Egyéni tartományok** oldalán találja a Azure Portal.

1. A Azure Portal alkalmazás lap bal oldali ablaktábláján válassza az **Egyéni tartományok** elemet.

   ![Az egyéni tartományok menüt megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Az **Egyéni tartományok** lapon másolja az alkalmazás IP-címét.

   ![Képernyőkép, amely egy Azure-alkalmazás portáljának navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományi szolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Az A rekord létrehozása

Ha egy rekordot egy alkalmazáshoz szeretne hozzárendelni, általában a legfelső szintű tartományhoz, hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím | Maga a tartomány-hozzárendelés ( `@` általában a legfelső szintű tartományt jelenti). |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service hozzáfér a `asuid.<subdomain>` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. A gyökérszintű tartományhoz használja a következőt: `asuid` . |

> [!NOTE]
> Egy altartomány (például) egy `www.contoso.com` , az ajánlott [CNAME-rekord](#map-a-cname-record)helyett egy rekord használatával való hozzáadásához a rekordnak és a txt-rekordnak a következő táblázathoz hasonlóan kell kinéznie:
>
> | Rekordtípus | Gazdagép | Érték |
> | - | - | - |
> | A | `www` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
> | TXT | `asuid.www` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) |
>

A rekordok hozzáadása után a DNS-rekordok oldal a következő példához hasonlóan néz ki:

![A DNS-rekordok lapot megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Az A rekord hozzárendelésének engedélyezése az alkalmazásban

A Azure Portal az alkalmazás **Egyéni tartományok** lapján adja hozzá a teljesen minősített egyéni DNS-nevet (például `contoso.com` ) a listához.

1. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![A gazdagép nevének hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`). 

1. Válassza az **Érvényesítés** lehetőséget. Megjelenik az **egyéni tartomány hozzáadása** lap.

1. Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely a DNS-név hozzáadását mutatja be az alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    ![A rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    
    Ha kihagyott egy lépést, vagy korábban elgépelt egy hibát, a lap alján egy ellenőrző hiba jelenik meg.
    
    ![Az ellenőrzési hibát mutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Az oktatóanyag példájában egy [helyettesítő karaktert tartalmazó DNS-nevet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) képez le az App Service-alkalmazásra egy CNAME rekord hozzáadásával.

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS-rekordok elérése tartományi szolgáltatóval

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

Rendelje hozzá a helyettesítő karaktert `*` az alkalmazás alapértelmezett tartománynevéhez ( `<app-name>.azurewebsites.net` ahol az az `<app-name>` alkalmazás neve). A helyettesítő karakter nevének leképezéséhez hozzon létre két rekordot:

| Rekordtípus | Gazdagép | Érték | Megjegyzések |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Maga a tartomány-hozzárendelés. |
| TXT | `asuid` | [A korábban kapott ellenőrző azonosító](#get-a-domain-verification-id) | App Service hozzáfér a `asuid` txt-rekordhoz az egyéni tartomány tulajdonjogának ellenőrzéséhez. |

A `*.contoso.com` tartomány példájában a CNAME rekord a `*` előtagot a `<app-name>.azurewebsites.net` elemre képezi le.

A CNAME hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Képernyőkép, amely egy Azure-alkalmazás navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>A CNAME rekord hozzárendelésének engedélyezése az alkalmazásban

Mostantól hozzáadhat bármely olyan altartományt, amely megfelel a helyettesítő karakter nevének (például, `sub1.contoso.com` `sub2.contoso.com` és `*.contoso.com` mindkét egyezés `*.contoso.com` ).

1. A Azure Portal alkalmazás lap bal oldali ablaktábláján válassza az **Egyéni tartományok** elemet.

    ![Az egyéni tartományok menüt megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![A gazdagép nevének hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írjon be egy olyan teljes tartománynevet, amely megfelel a helyettesítő karaktert tartalmazó tartománynak (például `sub1.contoso.com`), majd válassza az **Érvényesítés** lehetőséget.

    Az **egyéni tartomány hozzáadása** gomb aktiválva van.

1. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME rekord (www \. example.com vagy bármely altartomány)**. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép, amely a DNS-név hozzáadását mutatja az alkalmazáshoz.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

1. Kattintson **+** ismét az ikonra egy másik olyan egyéni tartomány hozzáadásához, amely megfelel a helyettesítő karakteres tartománynak. Például adja hozzá a következőt: `sub2.contoso.com`.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Az egyéni tartományra vonatkozó figyelmeztetési címke azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Tesztelés böngészőben

Tallózással keresse meg a korábban konfigurált DNS-neveket (például,,, `contoso.com` `www.contoso.com` `sub1.contoso.com` és `sub2.contoso.com` ).

![Képernyőkép, amely egy Azure-alkalmazás navigálását mutatja be.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>A 404 feloldása nem található

Ha HTTP 404 (nem található) hibaüzenet jelenik meg, amikor megkeresi az egyéni tartomány URL-címét, ellenőrizze, hogy a tartomány az alkalmazás IP-címére oldódik-e az <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>használatával. Ha nem, akkor az a következő okok egyike miatt lehet:

- A konfigurált egyéni tartományból hiányzik egy rekord vagy egy CNAME rekord.
- A böngészőügyfél gyorsítótárazta a tartomány régi IP-címét. Törölje a gyorsítótárat, és ismételje meg a DNS-feloldási tesztet. Windows-gépen az `ipconfig /flushdns` paranccsal törölheti a gyorsítótárat.

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem a gyökérkönyvtárban kezdődnek. A [Laravel](https://laravel.com/) például a `public` alkönyvtárban indul. A `contoso.com` DNS-példa folytatásához egy ilyen alkalmazás a (z) címen érhető el `http://contoso.com/public` , de `http://contoso.com` `public` ehelyett inkább a címtárhoz szeretne csatlakozni. Ez a lépés nem jár a DNS-feloldással, hanem a virtuális könyvtár testreszabásával kapcsolatos.

A Windows-alkalmazások virtuális könyvtárainak testreszabásához válassza az **Alkalmazásbeállítások** lehetőséget a webalkalmazás lap bal oldali ablaktábláján. 

> [!NOTE]
> A Linux-alkalmazások nem rendelkeznek ezzel az oldallal. A Linux-alkalmazások hely gyökerének módosításához tekintse meg a nyelvspecifikus konfigurációs útmutatókat (például:[php](configure-language-php.md?pivots=platform-linux#change-site-root)).

A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

![A virtuális könyvtárak testreszabását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

A művelet befejeződése után az alkalmazásnak vissza kell adni a jobb oldalt a gyökér elérési útjában (például: `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/)segítségével automatizálhatja az egyéni tartományok parancsfájlokkal történő kezelését.

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
> * Altartomány hozzárendelése CNAME rekord használatával.
> * Legfelső szintű tartomány hozzárendelése egy rekord használatával.
> * Helyettesítő karakteres tartomány hozzárendelése CNAME rekord használatával.
> * Átirányítja az alapértelmezett URL-címet egy egyéni könyvtárba.
> * A tartomány-hozzárendelés parancsfájlokkal való automatizálása.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan köthető egy egyéni TLS/SSL-tanúsítvány egy webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)
