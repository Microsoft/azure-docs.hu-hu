---
title: 'Oktatóanyag: a Microsoft társközi útvonal-szűrőinek konfigurálása – Azure Portal'
description: Ez az oktatóanyag azt ismerteti, hogyan lehet konfigurálni a Microsoft-társak útválasztási szűrőit a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109136"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Oktatóanyag: a Microsoft társközi útvonal-szűrőinek konfigurálása a Azure Portal használatával

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Az olyan szolgáltatások, mint az Exchange Online, a SharePoint Online és a Skype vállalati verzió, a Microsoft-partneri kapcsolaton keresztül érhetők el Microsoft 365. Ha a Microsoft-társat egy ExpressRoute-áramkörben konfigurálja, az ezen szolgáltatásokhoz kapcsolódó összes előtag a létrehozott BGP-munkameneteken keresztül lesz meghirdetve. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Az összes Azure-és Microsoft 365-szolgáltatáshoz való kapcsolódás nagy számú előtagokat okoz a BGP-n keresztül. Az előtagok nagy száma jelentősen megnöveli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

* Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Az útvonal-szűrés szabványos hálózati gyakorlat, és általában számos hálózaton belül használatos.

* Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - BGP-közösségi értékek beolvasása.
> - Hozzon létre egy útválasztási szűrőt és egy szűrési szabályt.
> - Útvonal-szűrő hozzárendelése egy ExpressRoute-áramkörhöz.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

A Microsoft Edge-útválasztók a ExpressRoute-áramkörön konfigurált, a saját peremhálózati útválasztókkal rendelkező BGP-munkameneteket a kapcsolat szolgáltatóján keresztül hozzák létre. Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezett lista az összes BGP közösségi értékről. Ha egy ExpressRoute-áramkörhöz egy útvonal-szűrő erőforrás van meghatározva és csatolva van, akkor a BGP közösségi értékekre leképező összes előtag a hálózatra lesz hirdetve.

Ha Microsoft 365-szolgáltatásokkal szeretne útválasztási szűrőket csatolni, rendelkeznie kell a ExpressRoute-en keresztüli Microsoft 365-szolgáltatások használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy a ExpressRoute-n keresztül használja Microsoft 365-szolgáltatásokat, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 

## <a name="prerequisites"></a>Előfeltételek

- A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

- Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  - A folytatás előtt [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társat](expressroute-howto-routing-portal-resource-manager.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Az előtagok és a BGP közösségi értékek listájának beolvasása

### <a name="get-a-list-of-bgp-community-values"></a>A BGP közösségi értékek listájának beolvasása

A Microsoft-társon keresztül elérhető szolgáltatásokhoz társított BGP-közösségi értékek a [ExpressRoute útválasztási követelmények](expressroute-routing.md) lapján érhetők el.

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>A használni kívánt értékek listájának létrehozása

Készítse el az útválasztási szűrőben használni kívánt [BGP-közösségi értékek](expressroute-routing.md#bgp) listáját. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Útvonal-szűrő és egy szűrési szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját.

1. Válassza az **erőforrás létrehozása** lehetőséget, majd keresse meg az *útvonal szűrőt* az alábbi képen látható módon:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Az útvonal-szűrő oldalt megjelenítő képernyőkép":::

1. Helyezze az útvonal szűrőt egy erőforráscsoporthoz. Győződjön meg arról, hogy a hely megegyezik a ExpressRoute áramkörével. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Az útvonal-szűrő létrehozása oldalt bemutató képernyőkép a megadott értékekkel":::

### <a name="create-a-filter-rule"></a>Szűrő szabály létrehozása

1. A szabályok hozzáadásához és frissítéséhez válassza az útválasztási szűrő szabály kezelése lapját.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Képernyőkép, amely az Áttekintés lapot mutatja, amelyen a szabály kezelése művelet ki van emelve":::

1. Válassza ki azokat a szolgáltatásokat, amelyekhez csatlakozni szeretne a legördülő listából, és mentse a szabályt, ha elkészült.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Képernyőfelvétel: a szabály kezelése ablak az engedélyezett szolgáltatási Közösségek legördülő listában kiválasztott szolgáltatásokkal":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Az útvonal-szűrő csatolása ExpressRoute-áramkörhöz

Csatolja az útvonal-szűrőt egy áramkörhöz a **+ kör hozzáadása** gombra kattintva, és válassza ki a ExpressRoute áramkört a legördülő listából.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Képernyőkép, amely az áttekintő lapot mutatja a kiválasztott kör hozzáadása művelettel":::

Ha a kapcsolati szolgáltató a ExpressRoute-áramkörhöz konfigurálja a társítást, frissítse az áramkört a ExpressRoute-áramkör lapról, mielőtt kiválasztja a **+ kör hozzáadása** gombot.

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Képernyőkép, amely az Áttekintés lapot jeleníti meg a kiválasztott frissítés művelettel.":::

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságait megtekintheti, amikor megnyitja az erőforrást a portálon.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Képernyőkép az Áttekintés lapról":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

1. Az áramkörhöz csatolt BGP-közösségi értékek listáját a **szabály kezelése** gombra kattintva frissítheti.

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Útvonal-szűrők frissítése a szabály kezelése művelettel":::

1. Válassza ki a kívánt szolgáltatási közösségeket, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Képernyőfelvétel a szabály kezelése ablakról a kiválasztott szolgáltatások közül":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Egy áramkör az útvonal-szűrőből való leválasztásához kattintson a jobb gombbal az áramkörre, és válassza a **hozzárendelés** megszüntetése lehetőséget.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Képernyőkép, amely az Áttekintés oldalt mutatja a Kiemelt leválasztási művelettel":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A **delete (Törlés** ) gombra kattintva törölhet egy útvonal-szűrőt. Ezt megelőzően győződjön meg arról, hogy az útvonal szűrője nem kapcsolódik egyetlen áramkörhöz sem.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Útvonal-szűrő törlése":::

## <a name="next-steps"></a>Következő lépések

Az útválasztó-konfigurációs mintákkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md)
