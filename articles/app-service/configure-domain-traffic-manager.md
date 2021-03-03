---
title: DNS-nevek konfigurálása Traffic Manager
description: Megtudhatja, hogyan konfigurálhat egyéni tartományt egy olyan Azure App Service alkalmazáshoz, amely a terheléselosztáshoz Traffic Manager integrálódik.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709104"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Egyéni tartománynév konfigurálása Azure App Serviceban Traffic Manager integrációval

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services esetében lásd: [Egyéni tartománynév konfigurálása Azure Cloud Service-hez](../cloud-services/cloud-services-custom-domain-name-portal.md).

Ha az [Azure Traffic Manager](../traffic-manager/index.yml) használatával szeretné terheléselosztásra osztani a forgalmat [Azure App Service](overview.md), a App Service alkalmazás a **\<traffic-manager-endpoint> . trafficmanager.net** használatával érhető el. Egy egyéni tartománynevet (például a www \. contoso.com) hozzárendelhet a app Service alkalmazással, hogy a felhasználók számára felismerhető tartománynevet adjon meg.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy egyéni tartománynevet egy olyan App Service-alkalmazással, amely integrálva van a [Traffic Managerba](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Csak a [CNAME](https://en.wikipedia.org/wiki/CNAME_record) rekordok támogatottak, ha tartománynevet állít be a Traffic Manager végpont használatával. Mivel A rekordok nem támogatottak, a legfelső szintű tartomány-hozzárendelés, például a contoso.com is nem támogatott.
> 

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Ha egy egyéni DNS-nevet szeretne hozzárendelni egy Azure Traffic Managerba integrált alkalmazáshoz, a webalkalmazás [app Service csomagjának](https://azure.microsoft.com/pricing/details/app-service/) **standard** vagy magasabb szintűnek kell lennie. Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **app Services** lehetőséget.

A **app Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

Az alkalmazás lap bal oldali navigációs sávján válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Győződjön meg arról, hogy az alkalmazás **standard** szintű vagy magasabb szintű (az **éles** vagy **elszigetelt** kategóriába tartozó bármely szinten található). Ha igen, zárjuk be a **felskálázás** lapot, és ugorjon [a CNAME-leképezés létrehozásához](#create-the-cname-mapping).

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Ha vertikális felskálázásra van szüksége az alkalmazáshoz, válassza az **üzemi** kategóriában az árképzési szintek egyikét. További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

## <a name="create-traffic-manager-endpoint"></a>Traffic Manager végpont létrehozása

A [végpontok hozzáadása vagy törlése](../traffic-manager/traffic-manager-manage-endpoints.md)lépéseit követve adja hozzá a app Service alkalmazást végpontként a Traffic Manager profilban.

Miután a App Service alkalmazás támogatott árképzési szinten van, megjelenik az elérhető App Service célok listájában a végpont hozzáadásakor. Ha az alkalmazás nem szerepel a listáján, [ellenőrizze az alkalmazás díjszabási szintjét](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>CNAME-leképezés létrehozása
> [!NOTE]
> Egy [megvásárolt app Service tartomány](manage-custom-dns-buy-domain.md)konfigurálásához ugorja át ezt a szakaszt, és válassza az [egyéni tartomány engedélyezése](#enable-custom-domain)lehetőséget.
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Míg az egyes tartományi szolgáltatók sajátosságai eltérőek, a [nem gyökérszintű egyéni tartománynevet](#what-about-root-domains) (például **www.contoso.com** *) az* alkalmazáshoz integrált Traffic Manager tartománynévre (**contoso.trafficmanager.net** *) képezi le.* 

> [!NOTE]
> Ha egy rekord már használatban van, és megelőző jelleggel kell kötnie az alkalmazásokat, létrehozhat egy további CNAME rekordot is. Ahhoz például, hogy megelőző jelleggel a kötést a **www \. contoso.com** az alkalmazáshoz, hozzon létre egy CNAME rekordot a **awverify. www** és a **contoso.trafficmanager.net** között. Ezután hozzáadhat "www \. contoso.com" az alkalmazáshoz anélkül, hogy módosítania kellene a "www" CNAME rekordot. További információ: [Active DNS-név Áttelepítésének Azure app Service](manage-custom-dns-migrate-domain.md).

Miután befejezte a DNS-rekordok hozzáadását vagy módosítását a tartományi szolgáltatónál, mentse a módosításokat.

### <a name="what-about-root-domains"></a>Mi a legfelső szintű tartomány?

Mivel a Traffic Manager csak a CNAME rekordokkal rendelkező egyéni tartomány-hozzárendeléseket támogatja, és mivel a DNS-szabványok nem támogatják a CNAME-rekordokat a gyökérszintű tartományok leképezéséhez (például **contoso.com**), Traffic Manager nem támogatja a legfelső szintű tartományok leképezését. A probléma megkerüléséhez használjon URL-átirányítást az alkalmazás szintjén. ASP.NET Core például használhatja az [URL-cím újraírását](/aspnet/core/fundamentals/url-rewriting). Ezután használja a Traffic Manager az altartomány (**www.contoso.com**) elosztásához. Egy másik módszer, [hogy létrehoz egy alias rekordot a tartománynév APEX-hoz, amely egy Azure Traffic Manager-profilra hivatkozik](../dns/tutorial-alias-tm.md). Például: contoso.com. Az átirányító szolgáltatás helyett beállíthatja, hogy a Azure DNS egy Traffic Manager-profilra hivatkozzon közvetlenül a zónából. 

A magas rendelkezésre állási forgatókönyvek esetében Traffic Manager nélkül is megvalósíthat terheléselosztási DNS-telepítést, ha több *olyan rekordot* hoz létre, amely a legfelső szintű tartományból az egyes alkalmazás-példányok IP-címére mutat. Ezután [rendelje hozzá ugyanazt a gyökértartomány-példányt az összes alkalmazás-példányhoz](app-service-web-tutorial-custom-domain.md#map-an-a-record). Mivel ugyanaz a tartománynév nem rendelhető hozzá két különböző alkalmazáshoz ugyanabban a régióban, ez a beállítás csak akkor működik, ha az alkalmazás különböző régiókban található.

## <a name="enable-custom-domain"></a>Egyéni tartomány engedélyezése
Miután a tartományneven lévő rekordok propagálása megtörtént, a böngésző segítségével ellenőrizze, hogy az Egyéni tartománynév feloldódik-e a App Service alkalmazásra.

> [!NOTE]
> Eltarthat egy ideig, amíg a CNAME-fájl propagálni tudja a DNS-rendszerét. Használhat olyan szolgáltatást, amellyel <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> ellenőrizheti, hogy a CNAME elérhető-e.
> 
> 

1. Ha a tartomány feloldása sikeres volt, vissza kell térnie az alkalmazás lapjára az [Azure Portalon](https://portal.azure.com)
2. A bal oldali navigációs sávon válassza az **Egyéni tartományok**  >  **Add hostname (állomásnév hozzáadása**) lehetőséget.
4. Írja be a korábban leképezett egyéni tartománynevet, és válassza az **Érvényesítés** lehetőséget.
5. Győződjön meg arról, hogy az **állomásnév bejegyzéstípusa** **CNAME (www \. example.com vagy bármely altartomány)** értékre van beállítva.

6. Mivel a App Service alkalmazás már integrálva van egy Traffic Manager-végponttal, a Traffic Manager tartománynevet a **CNAME-konfiguráció** területen kell látnia. Jelölje ki, majd kattintson az **egyéni tartomány hozzáadása** lehetőségre.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni DNS-név védelme SSL-kötéssel az Azure App Service-ben](configure-ssl-bindings.md)