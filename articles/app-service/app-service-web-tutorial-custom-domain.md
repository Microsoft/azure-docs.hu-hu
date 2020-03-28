---
title: 'Oktatóanyag: Meglévő egyéni DNS-név leképezése'
description: Megtudhatja, hogyan adhat hozzá meglévő egyéni DNS-tartománynevet (egyéni tartományt) egy webalkalmazáshoz, egy mobilalkalmazás hátérrendszeréhez vagy egy API-alkalmazáshoz az Azure App Service-ben.
keywords: app service, azure app service, tartomány-hozzárendelés, tartománynév, meglévő tartomány, gazdagépnév
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 2e62062fabfbb7c320bbeaf473979ccef96b0ba3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80047212"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Oktatóanyag: Meglévő egyéni DNS-név hozzárendelése az Azure App Service-hez

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan képezheti le a meglévő egyéni DNS-nevet az Azure App Service-hez.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Altartomány hozzárendelése (például `www.contoso.com`) CNAME rekorddal
> * Gyökértartomány hozzárendelése (például `contoso.com`) A rekorddal
> * Helyettesítő karaktert tartalmazó tartomány hozzárendelése (például `*.contoso.com`) CNAME rekorddal
> * Alapértelmezett URL-cím átirányítása egyéni könyvtárra
> * A tartományleképezés automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.
* Vásároljon egy tartománynevet, és győződjön meg arról, hogy hozzáfér a tartományszolgáltatója (például a GoDaddy) DNS-jegyzékéhez.

  Például a `contoso.com` és a `www.contoso.com` DNS-bejegyzéseinek hozzáadásához képesnek kell lennie a `contoso.com` gyökértartomány DNS-beállításainak konfigurálására.

  > [!NOTE]
  > Ha nem rendelkezik meglévő tartománynévvel, az [Azure Portallal vásárolhat egy tartományt](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Egyéni DNS-név leképezése egy webalkalmazáshoz, a webalkalmazás [App Service-csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek kell lennie **(Megosztott**, **Alapszintű,** **Standard**, **Prémium** vagy Azure Functions **felhasználás).** Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="select-the-app-in-the-azure-portal"></a>Válassza ki az alkalmazást az Azure Portalon

Keresse meg és válassza **az App Services**lehetőséget.

![Alkalmazásszolgáltatások kiválasztása](./media/app-service-web-tutorial-custom-domain/app-services.png)

Az **App Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

Megjelenik az App Service-alkalmazás felügyeleti oldala.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

Az alkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza ki a **Vertikális felskálázás (App Service-csomag)** elemet.

![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** szinten van. Az egyéni DNS nem támogatott az **F1** szinten. 

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Ha az App Service-csomag nem az **F1** szinten van, zárja be a **Vertikális felskálázás** oldalt és folytassa a [CNAME rekord leképezése](#cname) lépéssel.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Saját tartomány leképezése

Az egyéni DNS-neveket **CNAME rekordok** vagy **A rekordok** használatával is leképezheti az App Service-re. Hajtsa végre a megfelelő lépéseket:

- [CNAME rekord hozzárendelése](#map-a-cname-record)
- [A rekord hozzárendelése](#map-an-a-record)
- [Helyettesítő karaktert tartalmazó tartomány leképezése (CNAME-rekorddal)](#map-a-wildcard-domain)

> [!NOTE]
> A gyökértartományok (például: `contoso.com`) kivételével minden egyéni DNS-névhez CNAME-rekordot használjon. Gyökértartományokhoz A-rekordokat használjon.

### <a name="map-a-cname-record"></a>CNAME rekord hozzárendelése

Az oktatóanyag példájában a `www` altartományhoz (például `www.contoso.com`) tartozó CNAME rekordot ad hozzá.

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

CNAME rekord hozzáadásával rendeljen hozzá egy altartományt`<app_name>.azurewebsites.net`az `<app_name>` alkalmazás alapértelmezett tartománynevéhez ( , hol van az alkalmazás neve).

A `www.contoso.com` tartomány példája esetében adjon hozzá egy CNAME rekordot, amely a `www` előtagot az `<app_name>.azurewebsites.net` elemre képezi le.

Miután hozzáadta a CNAME rekordot, a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (`www.contoso.com`) a listához.

Jelölje **+** ki az **Egyéni tartomány hozzáadása**ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be a teljes tartománynevet, amelyhez hozzáadta a CNAME rekordot adott (például `www.contoso.com`).

Válassza az **Érvényesítés** lehetőséget.

Megjelenik **az Egyéni tartomány hozzáadása** lap.

Győződjön meg arról, hogy a **Hostname rekordtípus** **\.CNAME (www example.com vagy bármely altartomány)** értékre van állítva.

Válassza **az Egyéni tartomány hozzáadása**lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelenik az alkalmazás **egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve, és a böngészőből az egyéni tartományba érkező HTTPS-kérelmek a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. SSL-kötés hozzáadásához olvassa [el Az egyéni DNS-név biztonságossá tétele SSL-kötéssel az Azure App Service szolgáltatásban című témakört.](configure-ssl-bindings.md)

Ha kihagyott egy lépést, vagy korábban valamit elgépelt, egy ellenőrzési hiba látható a lap alján.

![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A rekord hozzárendelése

Az oktatóanyag példájában egy A rekordot ad hozzá a gyökértartományhoz (például `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Az alkalmazás IP-címének másolása

A rekord leképezéséhez az alkalmazás külső IP-címére van szükség. Ezt az IP-címet az alkalmazás **Egyéni tartományok** oldalán találja az Azure Portalon.

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Az **Egyéni tartományok** oldalon másolja az alkalmazás IP-címét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Az A rekord létrehozása

Ha egy A rekordot egy alkalmazásra kíván leképezni, az App Service-nek **két** DNS-rekordra van szüksége:

- egy **A** rekordra, amelyet leképezhet az alkalmazás IP-címére.
- **TXT** rekord, amelyet az alkalmazás alapértelmezett `<app_name>.azurewebsites.net`tartománynevére kell leképezni. Az App Service ezt a rekordot csak a konfiguráláskor használja annak ellenőrzéséhez, hogy Ön-e az egyéni tartomány tulajdonosa. Miután az App Service-ben érvényesítette és konfigurálta az egyéni tartományt, törölheti ezt a TXT típusú rekordot.

Az `contoso.com` tartomány példájában a következő táblának megfelelően hozza létre az A és a TXT típusú rekordot (a `@` általában a gyökértartományt jelöli).

| Rekordtípus | Gazdagép | Érték |
| - | - | - |
| A | `@` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Ha az ajánlott `www.contoso.com` [CNAME rekord](#map-a-cname-record)helyett A rekordot szeretne hozzáadni altartományhoz (például) az A rekord hoz, az A rekordnak és a TXT rekordnak az alábbi táblázatnak kell lennie:
>
> | Rekordtípus | Gazdagép | Érték |
> | - | - | - |
> | A | `www` | [Az alkalmazás IP-címének másolása](#info) szakaszból származó IP-cím |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

A rekordok hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![DNS-rekordok oldala](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Az A rekord hozzárendelésének engedélyezése az alkalmazásban

Az Azure Portalon az alkalmazás **Egyéni tartományok** oldalán adja hozzá a teljes egyéni DNS-nevet (például `contoso.com`) a listához.

Jelölje **+** ki az **Egyéni tartomány hozzáadása**ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írja be az A rekordhoz konfigurált teljes tartománynevet (például `contoso.com`).

Válassza az **Érvényesítés** lehetőséget.

Megjelenik **az Egyéni tartomány hozzáadása** lap.

Győződjön meg arról, hogy a **Gazdagépnév rekordtípusa** beállítás értéke **A rekord (example.com)**.

Válassza **az Egyéni tartomány hozzáadása**lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelenik az alkalmazás **egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

![A rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve, és a böngészőből az egyéni tartományba érkező HTTPS-kérelmek a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. SSL-kötés hozzáadásához olvassa [el Az egyéni DNS-név biztonságossá tétele SSL-kötéssel az Azure App Service szolgáltatásban című témakört.](configure-ssl-bindings.md)

Ha kihagyott egy lépést, vagy korábban valamit elgépelt, egy ellenőrzési hiba látható a lap alján.

![Ellenőrzési hiba](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Helyettesítő karaktert tartalmazó tartomány hozzárendelése

Az oktatóanyag példájában egy [helyettesítő karaktert tartalmazó DNS-nevet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (például `*.contoso.com`) képez le az App Service-alkalmazásra egy CNAME rekord hozzáadásával.

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása

CNAME rekord hozzáadásával helyettesítő nevet szeretne leképezni az alkalmazás`<app_name>.azurewebsites.net`alapértelmezett tartománynevére ( ).

A `*.contoso.com` tartomány példájában a CNAME rekord a `*` előtagot a `<app_name>.azurewebsites.net` elemre képezi le.

A CNAME hozzáadása után a DNS-rekordok oldala a következő példához hasonlóan jelenik meg:

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>A CNAME rekord hozzárendelésének engedélyezése az alkalmazásban

Most már bármilyen altartományt hozzáadhat az alkalmazáshoz, amely megfelel a helyettesítő karaktert tartalmazó névnek (például a `sub1.contoso.com` és a `sub2.contoso.com` megfelel a `*.contoso.com` névnek).

Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

![Egyéni tartomány menü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Jelölje **+** ki az **Egyéni tartomány hozzáadása**ikont.

![Gazdagépnév hozzáadása](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Írjon be egy olyan teljes tartománynevet, amely megfelel a helyettesítő karaktert tartalmazó tartománynak (például `sub1.contoso.com`), majd válassza az **Érvényesítés** lehetőséget.

Az **Egyéni tartomány hozzáadása** gomb aktiválva van.

Győződjön meg arról, hogy a **Hostname rekordtípus** CNAME rekordra van állítva **\.(www example.com vagy bármely altartományra).**

Válassza **az Egyéni tartomány hozzáadása**lehetőséget.

![DNS-név hozzáadása az alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Eltarthat egy ideig, amíg az új egyéni tartomány megjelenik az alkalmazás **egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.

Az **+** ikon ismét kiválasztva egy másik, a helyettesítő tartománynak megfelelő egyéni tartományt szeretne hozzáadni. Például adja hozzá a következőt: `sub2.contoso.com`.

![CNAME rekord hozzáadva](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Az egyéni tartomány hoz tartozó **Megjegyzés biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve, és a böngészőből az egyéni tartományba érkező HTTPS-kérelmek a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. SSL-kötés hozzáadásához olvassa [el Az egyéni DNS-név biztonságossá tétele SSL-kötéssel az Azure App Service szolgáltatásban című témakört.](configure-ssl-bindings.md)

## <a name="test-in-browser"></a>Tesztelés a böngészőben

Keresse meg a korábban konfigurált DNS-neve(ke)t (például `contoso.com`, `www.contoso.com`, `sub1.contoso.com` és `sub2.contoso.com`).

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404"Nem található" feloldása

Ha HTTP 404-es (nem található) hiba történik az egyéni tartomány URL-címének megnyitásakor, a <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> használatával ellenőrizze, hogy a tartomány az alkalmazás IP-címére van-e feloldva. Ha nem, azt a következők okozhatják:

- A konfigurált egyéni tartományból hiányzik egy A rekord és/vagy egy CNAME rekord.
- A böngészőügyfél gyorsítótárazta a tartomány régi IP-címét. Ürítse ki a gyorsítótárat, és tesztelje ismét a DNS-feloldást. Windows-gépen az `ipconfig /flushdns` paranccsal törölheti a gyorsítótárat.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Aktív tartomány migrálása

Élő webhely és hozzá tartozó DNS-tartománynév migrálása leállás nélkül az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Átirányítás egyéni könyvtárra

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Bizonyos webes keretrendszerek azonban nem a gyökérkönyvtárban indulnak. A [Laravel](https://laravel.com/) például a `public` alkönyvtárban indul. A `contoso.com` DNS példát folytatva, az ilyen alkalmazások elérhetők a `http://contoso.com/public` könyvtárban, de célszerűbb a `http://contoso.com` átirányítása a `public` könyvtárba. Ez a lépés nem DNS-feloldással, hanem a virtuális könyvtár testreszabásával történik.

Ehhez válassza ki a webalkalmazás oldalán a bal oldali navigációs sávban lévő **Alkalmazásbeállítások** elemet. 

A lap alján, a virtuális gyökérkönyvtár (`/`) alapértelmezés szerint a `site\wwwroot` könyvtárra mutat, amely az alkalmazáskód gyökérkönyvtára. Módosítsa úgy, hogy ehelyett a `site\wwwroot\public` könyvtárra mutasson, és mentse a módosításokat.

![A virtuális könyvtár testreszabása](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

A művelet befejezése után az alkalmazásnak a megfelelő oldalt kell `http://contoso.com`visszaadnia a gyökérelérési úton (például ).

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Szkriptekkel automatizálhatja az egyéni tartományok felügyeletét az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/overview) használatával. 

### <a name="azure-cli"></a>Azure CLI 

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

További információ: [Egyéni tartomány leképezése egy webalkalmazásra](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancs konfigurált egyéni DNS-nevet ad hozzá egy App Service-alkalmazáshoz.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

További információ: [Egyéni tartomány hozzárendelése egy webalkalmazáshoz](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Altartomány hozzárendelése CNAME rekorddal
> * Gyökértartomány hozzárendelése A rekorddal
> * Helyettesítő karaktert tartalmazó tartomány hozzárendelése CNAME rekorddal
> * Alapértelmezett URL-cím átirányítása egyéni könyvtárra
> * A tartományleképezés automatizálása szkriptekkel

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan köthet egyéni SSL-tanúsítványt webalkalmazásokhoz.

> [!div class="nextstepaction"]
> [Egyéni DNS-név védelme SSL-kötéssel az Azure App Service-ben](configure-ssl-bindings.md)
