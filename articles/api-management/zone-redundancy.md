---
title: Rendelkezésre állási zónák támogatása az Azure API Management
description: Megtudhatja, hogyan javíthatja az Azure API Management szolgáltatáspéldány rugalmasságát egy régióban a zónaredundania engedélyezésével.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559165"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Rendelkezésre állási zónák támogatása az Azure API Management 

Ez a cikk bemutatja, hogyan engedélyezheti a zónaredundan API Management a Azure Portal. [A zónaredundania](../availability-zones/az-overview.md#availability-zones) rugalmasságot és magas rendelkezésre állást biztosít egy adott Azure-régióban (helyen) található szolgáltatáspéldány számára. Zónaredundans esetén az API Management-példány átjárója és vezérlősíkja (Alkalmazáskezelés API, fejlesztői portál, Git-konfiguráció) fizikailag elkülönített zónákban található adatközpontok között replikálódik, így ellenáll a zónahibáknak. 

API Management támogatja a többrétű üzemelő példányokat is, ami segít csökkenteni a földrajzilag elosztott [API-felhasználók](api-management-howto-deploy-multi-region.md)által észlelt kérések késését, és javítja az átjáró-összetevő rendelkezésre állását, ha az egyik régió offline állapotba kerül. A régión belüli redundancia rendelkezésre állási zónái és a többrétű üzemelő példányok együttesen javítják az átjáró rendelkezésre állását regionális kimaradás esetén, ami növeli az átjárópéldány megbízhatóságát és API Management teljesítményét.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Támogatott régiók

A API Management-redundanciához való konfigurálás jelenleg a következő Azure-régiókban támogatott.

* Kelet-Ausztrália
* Dél-Brazília
* Közép-Kanada
* Közép-India
* USA keleti régiója
* USA 2. keleti régiója
* Közép-Franciaország
* Kelet-Japán
* USA déli középső régiója
* Délkelet-Ázsia
* Az Egyesült Királyság déli régiója
* USA 2. nyugati régiója
* USA 3. nyugati régiója

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg [a API Management szolgáltatáspéldány létrehozása részt.](get-started-create-service-instance.md) Válassza ki a Prémium szolgáltatási szintet.
* Ha a API Management-példánya egy virtuális hálózaton van üzembe állítva, győződjön meg arról, hogy beállított egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet minden olyan új helyen, ahol engedélyezni szeretné [a](api-management-using-with-vnet.md)zónaredundaniát.

## <a name="enable-zone-redundancy---portal"></a>Zónaredundania engedélyezése – portál

A portálon engedélyezheti a zónaredundaniát, ha helyet ad a API Management szolgáltatáshoz, vagy frissít egy meglévő helyet.

1. A Azure Portal lépjen a API Management, és válassza a **Helyek** lehetőséget a menüben.
1. Válasszon ki egy meglévő helyet, vagy válassza a **+ Hozzáadás** lehetőséget a felső sávon. A helynek [támogatnia kell a rendelkezésre állási zónákat.](#supported-regions)
1. Válassza ki a méretezési **[egységek](upgrade-and-scale.md)** számát a helyen.
1. A **Rendelkezésre állási zónák csoportban** válasszon ki egy vagy több zónát. A kiválasztott egységek számának egyenletesen kell elosztva lennie a rendelkezésre állási zónák között. Ha például 3 egységet választott, válassza ki a 3 zónát, hogy minden zónában egy egység található.
1. Ha a API Management-példány egy virtuális hálózaton van [üzembe állítva,](api-management-using-with-vnet.md)konfigurálja a virtuális hálózati beállításokat a helyen. Válasszon ki egy meglévő virtuális hálózatot, alhálózatot és nyilvános IP-címet, amelyek elérhetők a helyen.
1. Válassza az **Alkalmaz,** majd a **Mentés lehetőséget.**

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Zónaredundancia engedélyezése":::

> [!IMPORTANT]
> Ha engedélyezi, hozzáadja vagy eltávolítja a rendelkezésre állási zónákat, a helyen található nyilvános IP-cím megváltozik. Ha egy régióban hálózati beállításokkal frissíti a rendelkezésre állási zónákat, a korábban beállítottnál eltérő nyilvános IP-címerőforrást kell konfigurálnia.

> [!NOTE]
> A módosítás alkalmazása a saját példányán 15–45 percet API Management percet.

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure API Management-szolgáltatáspéldány több Azure-régióban való üzembe helyezéséről.](api-management-howto-deploy-multi-region.md)
* A zónaredundaniát egy sablonnal [Azure Resource Manager is engedélyezheti.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones)
* További információ a rendelkezésre [állási zónákat támogató Azure-szolgáltatásokról.](../availability-zones/az-region.md)
* További információ a megbízhatóságra [való felépítésről az](/azure/architecture/framework/resiliency/overview) Azure-ban.