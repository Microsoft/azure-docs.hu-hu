---
title: Azure cache a Redis az Azure Private linkkel (előzetes verzió)
description: Az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure-beli privát kapcsolaton keresztül az Azure cache-hez biztosít privát és biztonságos Redis. Ebből a cikkből megtudhatja, hogyan hozhat létre Azure cache-t, egy Azure-Virtual Networkt és egy privát végpontot a Azure Portal használatával.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 22bdf93e7236ae5220a6bb7c6ead898628bb51a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007585"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure cache a Redis az Azure Private linkkel (nyilvános előzetes verzió)
Ebből a cikkből megtudhatja, hogyan hozhat létre egy virtuális hálózatot és egy Azure cache-t a Redis-példányhoz egy privát végponttal a Azure Portal használatával. Azt is megtudhatja, hogyan adhat hozzá privát végpontot egy meglévő Azure cache-hez a Redis-példányhoz.

Az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure-beli privát kapcsolaton keresztül az Azure cache-hez biztosít privát és biztonságos Redis. 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> A privát végpontok használatához a Redis-példányhoz tartozó Azure cache-t a 2020. július 28-ig kell létrehozni.
> Jelenleg a Geo-replikálás, a tűzfalszabályok, a portál konzol támogatása, a fürtözött gyorsítótárban több végpont, a tűzfal és a VNet Beinjektált gyorsítótárak használata nem támogatott. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Privát végpont létrehozása új Azure cache-sel a Redis-példányhoz 

Ebben a szakaszban létrehoz egy új Azure cache-t a Redis-példányhoz egy privát végponttal.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Válassza az erőforrás létrehozása lehetőséget.":::

2. Az **új** lapon válassza a **hálózatkezelés** , majd a **virtuális hálózat** lehetőséget.

3. A virtuális hálózat létrehozásához válassza a **Hozzáadás** lehetőséget.

4. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amelynek alapján létre szeretné hozni a virtuális hálózatot. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a virtuális hálózatot és más erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Név** | Adja meg a virtuális hálózat nevét. | A névnek betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzással kell végződnie, és csak betűket, számokat, aláhúzást, pontot vagy kötőjelet tartalmazhat. | 
   | **Régió** | Legördülő menüből válassza ki a régiót. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a virtuális hálózatot használó egyéb szolgáltatások közelében. |

5. Válassza az **IP-címek** fület, vagy kattintson a lap alján található **következő: IP-címek** gombra.

6. Az **IP-címek** lapon megadhatja az **IPv4-címtartományt** egy vagy több CIDR-jelöléssel (például 192.168.1.0/24).

7. Az alhálózat **neve** alatt kattintson az **alapértelmezett** elemre az alhálózat tulajdonságainak szerkesztéséhez.

8. Az **alhálózat szerkesztése** ablaktáblán adja meg az **alhálózat nevét** , valamint az **alhálózati címtartományt**. Az alhálózat címtartományének CIDR-jelöléssel kell rendelkeznie (például 192.168.1.0/24). Ennek a virtuális hálózat címterület részét kell tartalmaznia.

9. Kattintson a **Mentés** gombra.

10. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

11. Győződjön meg arról, hogy az összes adat helyes, és kattintson a **Létrehozás** elemre a virtuális hálózat kiépítéséhez.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Azure cache létrehozása Redis-példányhoz privát végponttal
Gyorsítótár-példány létrehozásához kövesse az alábbi lépéseket.

1. Lépjen vissza a Azure Portal kezdőlapjára, vagy nyissa meg a Sidebar menüt, majd válassza az **erőforrás létrehozása** lehetőséget. 
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Válassza ki az Azure cache-t a Redis.":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a *\<DNS name> . Redis.cache.Windows.net* lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Tarifacsomag** | Legördülő menüből válassza ki a [díjszabási szintet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |

1. Válassza a **hálózatkezelés** fület, vagy kattintson a lap alján található **hálózatkezelés** gombra.

1. A **hálózatkezelés** lapon válassza a **magánhálózati végpont** lehetőséget a kapcsolódási módszerhez.

1. A privát végpont létrehozásához kattintson a **Hozzáadás** gombra.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="A hálózatkezelés területen adjon hozzá egy privát végpontot.":::

1. A **magánhálózati végpont létrehozása** lapon konfigurálja a magánhálózati végpont beállításait az utolsó szakaszban létrehozott virtuális hálózattal és alhálózattal, és kattintson az **OK gombra**. 

1. Válassza a **Next (speciális** ) lapot, vagy kattintson a lap alján található **Tovább: speciális** gombra.

1. Az alapszintű vagy standard gyorsítótár-példány **speciális** lapján jelölje be a váltás engedélyezése jelölőnégyzetet, ha engedélyezni szeretné a nem TLS portot.

1. A Premium cache-példány **speciális** lapján konfigurálja a nem TLS port, a fürtözés és az adatmegőrzés beállításait.

1. Válassza a **Next: Tags (tovább** ) lapot, vagy kattintson a **Next: Tags (tovább** ) gombra a lap alján.

1. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné. 

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A felülvizsgálat + létrehozás lapon az Azure ellenőrzi a konfigurációt.

1. Ha megjelenik az átadott zöld érvényesítés üzenet, válassza a **Létrehozás** lehetőséget.

Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra. 
    
> [!IMPORTANT]
> 
> `publicNetworkAccess`Alapértelmezés szerint van egy jelző `Disabled` . 
> Ennek a jelzőnek a célja, hogy lehetővé tegye a nyilvános és a privát végpontok hozzáférését a gyorsítótárhoz, ha az értéke `Enabled` . Ha a értékre `Disabled` van állítva, akkor csak a privát végpontok hozzáférését engedélyezi. Az értéket beállíthatja a vagy a értékre `Disabled` `Enabled` a következő patch-kéréssel. Szerkessze az értéket, hogy tükrözze, melyik jelölőt szeretné használni a gyorsítótárban.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Fürtözött gyorsítótárhoz való kapcsolódáshoz be kell `publicNetworkAccess` állítani a-t, `Disabled` és csak egyetlen privát végponti kapcsolat lehet. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Privát végpont létrehozása meglévő Azure cache-sel a Redis-példányhoz 

Ebben a szakaszban egy privát végpontot fog hozzáadni egy meglévő Azure cache-hez a Redis-példányhoz. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása 
Virtuális hálózat létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget.

2. Az **új** lapon válassza a **hálózatkezelés** , majd a **virtuális hálózat** lehetőséget.

3. A virtuális hálózat létrehozásához válassza a **Hozzáadás** lehetőséget.

4. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amelynek alapján létre szeretné hozni a virtuális hálózatot. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a virtuális hálózatot és más erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Név** | Adja meg a virtuális hálózat nevét. | A névnek betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzással kell végződnie, és csak betűket, számokat, aláhúzást, pontot vagy kötőjelet tartalmazhat. | 
   | **Régió** | Legördülő menüből válassza ki a régiót. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a virtuális hálózatot használó egyéb szolgáltatások közelében. |

5. Válassza az **IP-címek** fület, vagy kattintson a lap alján található **következő: IP-címek** gombra.

6. Az **IP-címek** lapon megadhatja az **IPv4-címtartományt** egy vagy több CIDR-jelöléssel (például 192.168.1.0/24).

7. Az alhálózat **neve** alatt kattintson az **alapértelmezett** elemre az alhálózat tulajdonságainak szerkesztéséhez.

8. Az **alhálózat szerkesztése** ablaktáblán adja meg az **alhálózat nevét** , valamint az **alhálózati címtartományt**. Az alhálózat címtartományének CIDR-jelöléssel kell rendelkeznie (például 192.168.1.0/24). Ennek a virtuális hálózat címterület részét kell tartalmaznia.

9. Kattintson a **Mentés** gombra.

10. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

11. Győződjön meg arról, hogy az összes adat helyes, és kattintson a **Létrehozás** elemre a virtuális hálózat kiépítéséhez.

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása 

Privát végpont létrehozásához kövesse az alábbi lépéseket.

1. A Azure Portal keresse meg az **Azure cache-t a Redis** , majd nyomja le az ENTER billentyűt, vagy válassza ki a keresési javaslatok közül.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Keresse meg az Azure cache-t a Redis.":::

2. Válassza ki azt a gyorsítótár-példányt, amelyhez privát végpontot szeretne hozzáadni.

3. A képernyő bal oldalán válassza a **(előzetes verzió) privát végpont** elemet.

4. Kattintson a **privát végpont** gombra a privát végpont létrehozásához.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Adja hozzá a privát végpontot.":::

5. A **magánhálózati végpont létrehozása lapon** konfigurálja a saját végpontjának beállításait.

   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amelynek alapján létre szeretné hozni ezt a privát végpontot. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Az erőforráscsoport neve, amelyben létre szeretné hozni a saját végpontját és egyéb erőforrásait. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Név** | Adja meg a privát végpont nevét. | A névnek betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzással kell végződnie, és csak betűket, számokat, aláhúzást, pontot vagy kötőjelet tartalmazhat. | 
   | **Régió** | Legördülő menüből válassza ki a régiót. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a privát végpontot fogja használni. |

6. Kattintson a **következő: erőforrás** gombra a lap alján.

7. Az **erőforrás** lapon válassza ki az előfizetést, válassza ki az erőforrás típusát `Microsoft.Cache/Redis` , majd válassza ki azt a gyorsítótárat, amelyhez a magánhálózati végpontot szeretné kapcsolni.

8. Kattintson a **következő: konfiguráció** gombra a lap alján.

9. A **konfiguráció** lapon válassza ki az előző szakaszban létrehozott virtuális hálózatot és alhálózatot.

10. Kattintson a **következő: címkék** gombra a lap alján.

11. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné.

12. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** lapon az Azure ellenőrzi a konfigurációt.

13. Ha megjelenik az **átadott zöld érvényesítés** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="faq"></a>GYIK

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Miért nem tudok csatlakozni egy privát végponthoz?
Ha a gyorsítótár már VNet befecskendezett gyorsítótár, a magánhálózati végpontok nem használhatók a gyorsítótár-példánnyal. Ha a gyorsítótár-példány nem támogatott funkciót használ (alább látható), nem fog tudni csatlakozni a saját végpont-példányához. A gyorsítótár-példányokat továbbá július 27. után is létre kell hozni privát végpontok használatára.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Milyen funkciók nem támogatottak a privát végpontok esetében?
Geo-replikáció, tűzfalszabályok, portál-konzol támogatása, fürtözött gyorsítótárban több végpont, a tűzfalszabályok megőrzése és a zóna redundancia. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Hogyan változtathatom meg a privát végpontot, hogy le legyen tiltva vagy engedélyezve legyen a nyilvános hálózati hozzáférés?
`publicNetworkAccess`Alapértelmezés szerint van egy jelző `Disabled` . Ennek a jelzőnek a célja, hogy lehetővé tegye a nyilvános és a privát végpontok hozzáférését a gyorsítótárhoz, ha az értéke `Enabled` . Ha a értékre `Disabled` van állítva, akkor csak a privát végpontok hozzáférését engedélyezi. Az értéket beállíthatja a vagy a értékre `Disabled` `Enabled` a következő patch-kéréssel. Szerkessze az értéket, hogy tükrözze, melyik jelölőt szeretné használni a gyorsítótárban.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Engedélyezve vannak-e a hálózati biztonsági csoportok (NSG) a privát végpontokhoz?
Nem, a magánhálózati végpontok esetében le vannak tiltva. Míg a privát végpontot tartalmazó alhálózatokhoz NSG társítható, a szabályok nem lesznek érvényesek a privát végpont által feldolgozott forgalomra. A privát végpontok alhálózaton való üzembe helyezéséhez [le kell tiltani a hálózati házirendek kényszerítését](../private-link/disable-private-endpoint-network-policy.md) . A NSG továbbra is érvényben van az ugyanazon alhálózaton futó egyéb munkaterheléseken. Az összes ügyfél-alhálózat útvonala egy/32 előtagot használ, és az alapértelmezett útválasztási viselkedés megváltoztatásához hasonló UDR van szükség. 

A forgalmat a forrás-ügyfeleken a kimenő forgalomra vonatkozó NSG szabályok használatával szabályozhatja. Egyéni útvonalak üzembe helyezése a/32 előtaggal a privát végponti útvonalak felülbírálásához. A kimenő kapcsolatok NSG és figyelési információi továbbra is támogatottak, és használhatók

### <a name="can-i-use-firewall-rules-with-private-endpoints"></a>Használhatok tűzfalszabályok privát végpontokkal?
Nem, ez a privát végpontok jelenlegi korlátozása. A magánhálózati végpont nem fog megfelelően működni, ha tűzfalszabályok vannak konfigurálva a gyorsítótárban.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Hogyan lehet csatlakozni egy fürtözött gyorsítótárhoz?
`publicNetworkAccess` be kell állítani, hogy `Disabled` csak egy privát végponti kapcsolatok legyenek.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Mivel a saját végpontom nem a saját VNet, hogyan van társítva a VNet?
Csak a VNet van csatolva. Mivel nem szerepel a VNet, a NSG-szabályokat nem szükséges módosítani a függő végpontok esetében.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Hogyan telepíthetem át a VNet befecskendezett gyorsítótárát egy privát végponti gyorsítótárba?
Törölnie kell a VNet Beinjektált gyorsítótárát, és létre kell hoznia egy új, privát végponttal rendelkező gyorsítótár-példányt.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni az Azure Private linkről, tekintse meg az [Azure Private link dokumentációját](../private-link/private-link-overview.md).
* A gyorsítótár-példány különböző hálózati elkülönítési lehetőségeinek összehasonlításához tekintse meg az [Azure cache Redis hálózati elkülönítési lehetőségek dokumentációját](cache-network-isolation.md).