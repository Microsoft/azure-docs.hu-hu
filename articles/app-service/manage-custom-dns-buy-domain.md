---
title: Egyéni tartománynév vásárlása
description: Megtudhatja, hogyan vásárolhat egy App Service tartományt, és hogyan használhatja egyéni tartományként az alkalmazás Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704854"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Egyéni tartománynév vásárlása az Azure App Service-hez

App Service tartományok olyan egyéni tartományok, amelyek közvetlenül az Azure-ban kezelhetők. Megkönnyítik [Azure app Service](overview.md)egyéni tartományának kezelését. Ez az oktatóanyag bemutatja, hogyan vásárolhat egy App Service tartományt, és hogyan rendelhet hozzá DNS-neveket Azure App Servicehoz.

Azure-beli virtuális gép vagy Azure Storage esetében lásd: [app Service tartomány kiosztása Azure-beli virtuális géphez vagy Azure Storage-hoz](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Cloud Services esetében lásd: [Egyéni tartománynév konfigurálása Azure Cloud Service-hez](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [Hozzon létre egy App Service-alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást. Az alkalmazásnak egy nyilvános Azure-régióban kell lennie. Jelenleg az Azure nemzeti felhők nem támogatottak.
* [Távolítsa el az előfizetés](../cost-management-billing/manage/spending-limit.md#remove)költségkeretét. Ingyenes előfizetési Kredittel rendelkező App Service-tartományok nem vásárolhatók meg.

## <a name="buy-an-app-service-domain"></a>App Service-tartomány vásárlása

App Service tartományokra vonatkozó díjszabási információkért tekintse meg a [app Service díjszabási oldalát](https://azure.microsoft.com/pricing/details/app-service/windows/) , és görgessen le app Service tartományhoz.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. A keresési sávban keresse meg és válassza ki **app Service tartományokat**.

    ![Navigálás a portálon Azure App Service tartományokhoz](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. A **app Service tartományok** nézetben kattintson a **Hozzáadás** gombra.

    ![Kattintson a Hozzáadás lehetőségre App Service tartományokban](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. A **app Service tartományok újabb verziójának kipróbálásához kattintson a click (kattintás) lehetőségre**.

    ![App Service tartomány létrehozása új felhasználói felülettel](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapvető** beállítások lapon konfigurálja a beállításokat a következő táblázat használatával:  

   | Beállítás  | Leírás |
   | -------- | ----------- |
   | **Előfizetés** | A tartomány megvásárlásához használandó előfizetés. |
   | **Erőforráscsoport** | Az az erőforráscsoport, amelybe a tartományt be kell állítani. Például az alkalmazáshoz tartozó erőforráscsoport. |
   | **Tartomány** | Adja meg a kívánt tartományt. Például adja meg a **contoso.com** nevet. Ha a kívánt tartomány nem érhető el, választhat az elérhető tartományok javaslatai közül, vagy másik tartományt is kipróbálhat. |

    > [!NOTE]
    > App Service tartományok a következő [legfelső szintű tartományokat](https://wikipedia.org/wiki/Top-level_domain) támogatják: _com_, _net_, _Co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_ és _Co.in_.
    >
    >
    
2. Ha elkészült, kattintson a **Tovább gombra: kapcsolattartási adatok**.

### <a name="contact-information-tab"></a>Kapcsolattartási adatok lap

1. Adja meg a tartományi regisztrációhoz az [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) által megkövetelt adatokat. 

    Fontos, hogy az összes kötelező mezőt kitöltse a lehető legnagyobb pontossággal. A kapcsolattartási adatok helytelen adatai miatt nem lehet megvásárolni a tartományt.

1. Ha elkészült, kattintson a **Tovább: speciális** lehetőségre.

### <a name="advanced-tab"></a>Speciális lap

1. A **speciális** lapon adja meg a választható beállításokat:  

   | Beállítás  | Leírás |
   | -------- | ----------- |
   | **Automatikus megújítás** | Alapértelmezés szerint engedélyezett. A App Service tartománya egy éves növekményekben van regisztrálva. Az automatikus megújítás biztosítja, hogy a tartomány regisztrációja nem jár le, és hogy megőrizze a tartomány tulajdonjogát. Az Azure-előfizetése automatikusan felszámítja az éves tartományi regisztrációs díjat a megújítás időpontjában. A **letiltáshoz** válassza a Letiltás lehetőséget. Ha az automatikus megújítás le van tiltva, [manuálisan is megújíthatja](#renew-the-domain). |
   | **Adatvédelem** | Alapértelmezés szerint engedélyezett. Az adatvédelmi védelem elrejti a tartomány regisztrációs kapcsolattartási adatait a WHOIS-adatbázisból. Az adatvédelem már része az éves tartományi regisztrációs díj. A **letiltáshoz** válassza a Letiltás lehetőséget. |

2. Ha elkészült, kattintson a **Tovább gombra: címkék**.

### <a name="finish"></a>Befejezés

1. A **címkék** lapon állítsa be a app Service tartományhoz használni kívánt címkéket. A címkézés nem szükséges App Service tartományok használatához, de az Azure egyik [funkciója, amely segít az erőforrások kezelésében](../azure-resource-manager/management/tag-resources.md).

1. Kattintson a **Tovább gombra: felülvizsgálat + létrehozás**.

1. A **felülvizsgálat + létrehozás** lapon tekintse át a tartomány sorrendjét. Ha végzett, kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > App Service tartományok a GoDaddy használatával tartományi regisztrációt használnak, és Azure DNS a tartományok üzemeltetéséhez. Az éves tartományi regisztrációs díj mellett a Azure DNS vonatkozó használati díjak is érvényesek. További információ: [Azure DNS díjszabása](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Ha a tartomány regisztrálása befejeződött, megjelenik az **erőforrás** megnyitása gomb. Válassza ki, hogy megtekintse a felügyeleti lapját.

    ![App Service tartomány létrehozva. Erőforrás megnyitása](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Most már készen áll egy App Service alkalmazás hozzárendelésére ehhez az egyéni tartományhoz.

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Ha egyéni DNS-nevet szeretne hozzárendelni egy webalkalmazáshoz, a webalkalmazás [app Service csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős szintnek kell lennie (közös, alapszintű, standard, prémium vagy Azure functions). Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Az alkalmazás megkeresése az Azure Portalon

1. A felső keresési sávban keresse meg és válassza ki **app Services**.

    ![App Services keresése](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Válassza ki az alkalmazás nevét.

    ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Megjelenik az App Service-alkalmazás felügyeleti oldala.  

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1. Az alkalmazás lapjának bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza ki a **Vertikális felskálázás (App Service-csomag)** elemet.

    ![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás nem az **F1** szinten van. Az egyéni DNS nem támogatott az **F1** szinten. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Képernyőfelvétel: az alkalmazás oldalának bal oldali navigációs menüje, amely felskálázással (App Service terv) van kiválasztva.":::

1. Ha az App Service-csomag nem az **F1** -es szinten található, akkor a vertikális **felskálázás** oldal bezárásával ugorjon [a tartomány megvásárlása](#buy-an-app-service-domain)elemre.

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1. Válassza ki bármelyik nem ingyenes szintet (**D1**, **B1**, **B2**, **B3**, vagy a **Production** kategória bármelyik szintje). További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

1. Kattintson az **Alkalmaz** gombra.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Képernyőfelvétel: az éles kategóriába tartozó egyéni tartomány díjszabási szintjei az üzemi lapon, a B1-terv és az alkalmaz gomb kiemelve.":::

    Amikor megjelenik a következő értesítés, a skálázási művelet befejeződött.

    ![Skálázási művelet megerősítése](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>App Service tartomány hozzárendelése az alkalmazáshoz

Egyszerűen leképezhető egy állomásnév a App Service tartományban egy App Service alkalmazásba, feltéve, hogy ugyanabban az előfizetésben található. A App Service tartományt vagy annak altartományát közvetlenül az alkalmazásában képezi le, az Azure pedig létrehozza a szükséges DNS-rekordokat.

> [!NOTE]
> Ha a tartomány és az alkalmazás különböző előfizetésekben található, akkor a App Service tartományt az alkalmazáshoz társítja, ugyanúgy, mint a [külsőleg megvásárolt tartományok leképezése](app-service-web-tutorial-custom-domain.md). Ebben az esetben Azure DNS a külső tartományi szolgáltató, és [manuálisan kell hozzáadnia a szükséges DNS-rekordokat](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>A tartomány leképezése

1. Az alkalmazás lap bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza az **Egyéni tartományok** elemet.

    ![Az egyéni tartományok menüt megjelenítő képernyőkép.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Válassza az **egyéni tartomány hozzáadása** lehetőséget.

    ![Képernyőkép az állomásnév hozzáadása elemről.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Írja be a App Service tartományt (például **contoso.com**) vagy altartományt (például **www.contoso.com**), és kattintson az **Érvényesítés** elemre.

    > [!NOTE]
    > Ha elgépelte a App Service tartománynevet, a lap alján egy ellenőrző hiba jelenik meg, amelyből megtudhatja, hogy hiányzik néhány DNS-rekord. Ezeket a rekordokat nem kell manuálisan hozzáadnia egy App Service tartományhoz. Győződjön meg arról, hogy helyesen adta meg a tartománynevet, majd kattintson az **Érvényesítés** gombra.
    >
    > ![Az ellenőrzési hibát bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Fogadja el az **állomásnév bejegyzéstípust** , és kattintson az **egyéni tartomány hozzáadása** lehetőségre.

    ![Képernyőkép, amely az egyéni tartomány hozzáadása gombot jeleníti meg.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Eltarthat egy ideig, amíg az új egyéni tartomány megjelennek az alkalmazás **Egyéni tartományok** lapján. Frissítse a böngészőt az adatfrissítéshez.

    ![A CNAME rekord hozzáadását bemutató képernyőkép.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nem kötődik TLS/SSL-tanúsítványhoz. A böngészőtől az egyéni tartományba érkező HTTPS-kérések a böngészőtől függően hibaüzenetet vagy figyelmeztetést kapnak. TLS-kötés hozzáadásával kapcsolatban lásd: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Az egyéni tartomány tesztelése

Az egyéni tartomány teszteléséhez navigáljon a böngészőben.

## <a name="renew-the-domain"></a>A tartomány megújítása

A megvásárolt App Service tartomány a vásárlás időpontjában egy évig érvényes. Alapértelmezés szerint a tartomány úgy van konfigurálva, hogy automatikusan megújítsa a fizetési módot a következő évre. A tartománynevet manuálisan is megújíthatja.

Ha ki szeretné kapcsolni az automatikus megújítást, vagy ha manuálisan szeretné megújítani a tartományt, kövesse az alábbi lépéseket.

1. A keresési sávban keresse meg és válassza ki **app Service tartományokat**.

    ![Navigálás a portálon Azure App Service tartományokhoz](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. A **app Service tartományok** szakaszban válassza ki a konfigurálni kívánt tartományt.

1. A tartomány bal oldali navigációs sávján válassza a **tartomány megújítása** lehetőséget. A tartomány automatikus megújításának leállításához válassza a **ki** lehetőséget. A beállítás azonnal érvénybe lép.

    ![Képernyőfelvétel: a tartomány automatikus megújításának lehetősége.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Ha az oldalról navigál, figyelmen kívül hagyhatja a "nem mentett módosítások elvetése" hibaüzenetet, ha az **OK** gombra kattint.
    >

A tartomány manuális megújításához válassza a **tartomány megújítása** lehetőséget. Ez a gomb azonban [a tartomány lejárata előtt 90 nappal](#when-domain-expires)nem aktív.

Ha a tartomány megújítása sikeres, a rendszer 24 órán belül értesítést küld e-mailben.

## <a name="when-domain-expires"></a>Ha a tartomány lejár

Az Azure a következő módon bánik a lejáró vagy lejárt App Service tartománnyal:

* Ha az automatikus megújítás le van tiltva: 90 nappal a tartomány lejárata előtt, a rendszer elküldi a megújítási értesítő e-mailt, és a **megújítási tartomány** gomb aktiválva lesz a portálon.
* Ha engedélyezve van az automatikus megújítás: a tartomány lejárati dátuma utáni napon az Azure megpróbál kiszámlázni a tartománynév megújítása után.
* Ha hiba történik az automatikus megújítás során (például a kártyán a fájl lejárt), vagy ha az automatikus megújítás le van tiltva, és lehetővé teszi, hogy a tartomány lejárjon, az Azure értesíti a tartomány lejáratáról és a tartománynevet. A tartományt [manuálisan is megújíthatja](#renew-the-domain) .
* Az Azure a lejárat után a 4. és a 12. napon belül további értesítő e-maileket küld. A tartományt [manuálisan is megújíthatja](#renew-the-domain) . A lejáratot követő 5 napon belül a DNS-feloldás leáll a lejárt tartományhoz.
* A lejárat utáni 19. napon a tartomány továbbra is érvényben marad, de a rendszer beváltási díjat számít fel. Az ügyfélszolgálat meghívásával megújíthatja a tartománynevet, a megújítási és beváltási díjakra is érvényes.
* Az Azure a lejárat utáni 25. napon üzembe helyezi az árverést a tartománynév-ágazati aukciós szolgáltatással. Az ügyfélszolgálat meghívásával megújíthatja a tartománynevet, a megújítási és beváltási díjakra is érvényes.
* A lejárat után 30 napon belül már nem tudja beváltani a tartományt.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Egyéni DNS-rekordok kezelése

Az Azure-ban a App Service tartomány DNS-rekordjait [Azure DNS](https://azure.microsoft.com/services/dns/)használatával felügyeli a rendszer. Hozzáadhat, eltávolíthat és frissíthet DNS-rekordokat, ugyanúgy, mint a külsőleg megvásárolt tartományhoz. Egyéni DNS-rekordok kezelése:

1. A keresési sávban keresse meg és válassza ki **app Service tartományokat**.

    ![Navigálás a portálon Azure App Service tartományokhoz](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. A **app Service tartományok** szakaszban válassza ki a konfigurálni kívánt tartományt.

1. Az **Áttekintés** lapon válassza a **DNS-rekordok kezelése** lehetőséget.

    ![Képernyőfelvétel: a DNS-rekordok elérési helyének megjelenítése.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

További információ a DNS-rekordok szerkesztéséről: [DNS-zónák kezelése a Azure Portalban](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Vásárlás megszakítása (tartomány törlése)

Miután megvásárolta a App Service tartományt, öt napja van, hogy megszakítsa a vásárlást teljes visszatérítésre. Öt nap elteltével törölheti a App Service tartományt, de nem kaphat visszatérítést.

1. A keresési sávban keresse meg és válassza ki **app Service tartományokat**.

    ![Navigálás a portálon Azure App Service tartományokhoz](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. A **app Service tartományok** szakaszban válassza ki a konfigurálni kívánt tartományt.

1. A tartomány bal oldali navigációs sávján válassza a **hostname-kötések** lehetőséget. Az összes Azure-szolgáltatás állomásnév-kötéseit itt találja.

    ![A hostname kötések lapot megjelenítő képernyőkép.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Az egyes állomásnév-kötések törléséhez válassza a **...**  >  lehetőséget. **Törlés**. Az összes kötés törlése után válassza a **Mentés** lehetőséget.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. A tartomány bal oldali navigációs sávján válassza az **Áttekintés** lehetőséget. 

1. Ha a megvásárolt tartomány megszakítási időszaka nem telt el, válassza a **vásárlás megszakítása** lehetőséget. Ellenkező esetben a Delete ( **Törlés** ) gomb jelenik meg. Ha a tartományt visszatérítés nélkül szeretné törölni, válassza a **Törlés** lehetőséget.

    ![A megvásárolt tartomány törlésének vagy megszakításának helyét bemutató képernyőkép.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Az **Igen gombra** kattintva erősítse meg a műveletet.

    A művelet befejezése után a tartományt a rendszer az előfizetésből bocsátja ki, és bárki számára elérhetővé válik. 

## <a name="direct-default-url-to-a-custom-directory"></a>Egyéni könyvtár közvetlen alapértelmezett URL-címe

Az App Service alapértelmezés szerint az alkalmazáskód gyökérkönyvtárára irányítja a webes kérelmeket. Ha egy alkönyvtárba szeretné irányítani őket (például `public` :), tekintse át [az átirányítás egyéni könyvtárba](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)című témakört.

## <a name="next-steps"></a>Következő lépések

Útmutató egyéni TLS/SSL-tanúsítvány kötéséhez App Serviceához.

> [!div class="nextstepaction"]
> [Egyéni DNS-név biztonságossá tétele TLS-kötéssel Azure App Service](configure-ssl-bindings.md)
