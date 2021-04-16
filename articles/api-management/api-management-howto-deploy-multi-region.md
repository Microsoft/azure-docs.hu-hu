---
title: Azure API Management-szolgáltatások üzembe helyezése több Azure-régióban
titleSuffix: Azure API Management
description: Megtudhatja, hogyan helyezhet üzembe egy Azure API Management-szolgáltatáspéldányt több Azure-régióban.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 9546813173e72b1f264c3668ee889bbeea07ce7f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534082"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban

Az Azure API Management támogatja a többrégió-alapú üzembe helyezést, ami lehetővé teszi, hogy az API-közzétevők egyetlen Azure API Management szolgáltatást osszanak el bármilyen számú támogatott Azure-régió között. A többrétű funkció segít csökkenteni a földrajzilag elosztott API-felhasználók által észlelt kérések késését, és javítja a szolgáltatás rendelkezésre állását, ha egy régió offline állapotba kerül.

Az új Azure API Management szolgáltatás kezdetben [][unit] csak egy egységet tartalmaz egyetlen Azure-régióban, az elsődleges régióban. Az elsődleges és a másodlagos régiókhoz további egységek is hozzáadhatóak. A API Management összes kijelölt elsődleges és másodlagos régióban telepítve lesz egy átjáró-összetevő. A bejövő API-kérések automatikusan a legközelebbi régióhoz vannak irányítva. Ha egy régió offline állapotba kerül, az API-kérések automatikusan a hibás régión keresztül a legközelebbi átjáróhoz lesznek irányítva.

> [!NOTE]
> A rendszer csak a API Management összes régióban telepíti. A szolgáltatáskezelési összetevő és a fejlesztői portál csak az elsődleges régióban üzemel. Ezért az elsődleges régió kimaradása esetén a fejlesztői portálhoz való hozzáférés és a konfiguráció módosítása (például API-k hozzáadása, szabályzatok alkalmazása) hátrányosan befolyásolja, amíg az elsődleges régió újra online állapotba nem kerül. Amíg az elsődleges régió offline állapotban van, az elérhető másodlagos régiók továbbra is a számukra elérhető legújabb konfigurációval fogják kiszolgálni az API-forgalmat. Igény szerint engedélyezheti [a zónaredundanciát](zone-redundancy.md) az elsődleges vagy másodlagos régiók rendelkezésre állásának és rugalmasságának javítása érdekében.

>[!IMPORTANT]
> Az ügyféladatok egyetlen régióban való tárolásának engedélyezésére szolgáló funkció jelenleg csak a Geo délkelet-ázsiai régiójában (Szingapúr) Ázsia és a Csendes-óceáni térség érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>Előfeltételek

* Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg a API Management [szolgáltatáspéldány létrehozása részt.](get-started-create-service-instance.md) Válassza ki a Prémium szolgáltatási szintet.
* Ha a API Management-példánya egy virtuális [](api-management-using-with-vnet.md)hálózaton van üzembe állítva, győződjön meg arról, hogy a hozzáadni tervezi a virtuális hálózatot, az alhálózatot és a nyilvános IP-címet.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>A API Management üzembe helyezése egy további helyen

1. A Azure Portal lépjen a API Management, és válassza a **Helyek** lehetőséget a menüben.
1. A **felső sávon** válassza a + Hozzáadás lehetőséget.
1. Válassza ki a helyet a legördülő listából.
1. Válassza ki a méretezési **[egységek](upgrade-and-scale.md)** számát a helyen.
1. Ha szeretné, engedélyezze [**a rendelkezésre állási zónákat.**](zone-redundancy.md)
1. Ha a API Management-példány egy virtuális hálózaton van [üzembe állítva,](api-management-using-with-vnet.md)konfigurálja a virtuális hálózati beállításokat a helyen. Válasszon ki egy meglévő virtuális hálózatot, alhálózatot és nyilvános IP-címet, amely elérhető a helyen.
1. A **megerősítéshez válassza** a Hozzáadás lehetőséget.
1. Ismételje meg ezt a folyamatot, amíg nem konfigurálja az összes helyet.
1. Az **üzembe helyezési** folyamat elkezdődhet a felső sávon a Mentés gombra.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Szolgáltatás API Management törlése

1. A Azure Portal lépjen a API Management szolgáltatáshoz, és kattintson a **Menü Helyek** bejegyzésre.
2. Az eltávolítani szeretne helyhez nyissa meg a helyi menüt a táblázat jobb végén található **...** gombbal. Válassza a **Törlés** lehetőséget.
3. Erősítse meg a törlést, **majd kattintson a Mentés gombra** a módosítások alkalmazáshoz.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>API-hívások útválasztása regionális háttérszolgáltatásokhoz

Az Azure API Management csak egy háttérszolgáltatás URL-címét tartalmazza. Bár különböző régiókban API Management Azure-beli virtuálispéldányok, az API-átjáró továbbra is ugyanannak a háttérszolgáltatásnak továbbítja a kéréseket, amely csak egy régióban van telepítve. Ebben az esetben a teljesítménybeli nyereség csak a kérésre jellemző régióban, az Azure API Management-ban gyorsítótárazott válaszokból lesz kihozva, de a háttérbeli kapcsolatfelvétel a világ különböző régióiban is nagy késést okozhat.

A rendszer földrajzi elosztásának teljes kihasználása érdekében a háttérszolgáltatásokat ugyanabban a régióban kell üzembe helyezni, mint az Azure API Management példányait. Ezután a szabályzatok és a tulajdonság használatával a háttérbeli helyi példányokhoz irányíthatja a `@(context.Deployment.Region)` forgalmat.

1. Lépjen az Azure API Management-példányra, és kattintson az **API-k elemre** a bal oldali menüben.
2. Válassza ki a kívánt API-t.
3. Kattintson **a Kódszerkesztő** elemre a Bejövő **feldolgozás legördülő menüben.**

    ![API-kódszerkesztő](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. A feltételes szabályzatokkal kombinálva megfelelő útválasztási szabályzatot hoz `set-backend` `choose` létre a fájl `<inbound> </inbound>` szakaszában.

    Az alábbi XML-fájl például az USA nyugati régiójában és a Kelet-Ázsia működik:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> A háttérszolgáltatásokat a következővel is [Azure Traffic Manager:](https://azure.microsoft.com/services/traffic-manager/), irányíthatja az API-hívásokat a Traffic Manager- és az útválasztás automatikus feloldására.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Egyéni útválasztás használata regionális API Management létrehozásához

API Management a kéréseket egy regionális  átjáróra a legkisebb [késés alapján.](../traffic-manager/traffic-manager-routing-methods.md#performance) Bár ez a beállítás nem bírálható felül a API Management, használhatja a saját Traffic Manager egyéni útválasztási szabályokkal.

1. Hozzon létre egy [saját Azure Traffic Manager.](https://azure.microsoft.com/services/traffic-manager/)
1. Ha egyéni tartományt [használ,](../traffic-manager/traffic-manager-point-internet-domain.md) használja a Traffic Manager a API Management helyett.
1. [Konfigurálja a API Management végpontokat a következő Traffic Manager:](../traffic-manager/traffic-manager-manage-endpoints.md). A regionális végpontok a URL-mintáját `https://<service-name>-<region>-01.regional.azure-api.net` követik, `https://contoso-westus2-01.regional.azure-api.net` például: .
1. [Konfigurálja a API Management állapotvégpontokat a](../traffic-manager/traffic-manager-monitoring.md)Traffic Manager. A regionális állapot végpontjai a URL-mintáját `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` követik, `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` például: .
1. Adja [meg az útvonaltervezési](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
