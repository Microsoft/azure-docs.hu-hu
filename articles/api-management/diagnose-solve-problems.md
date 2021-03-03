---
title: Azure API Management problémák diagnosztizálása és megoldása
description: Megtudhatja, hogyan oldhatja fel az API-val kapcsolatos problémákat az Azure API Management a Azure Portal diagnosztizálása és megoldása eszközzel.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652402"
---
# <a name="azure-api-management-diagnostics-overview"></a>Az Azure API Management Diagnostics áttekintése

Ha az Azure API Management-ban hoz létre és felügyel egy API-t, elő kell készítenie az esetlegesen felmerülő problémákra, a 404 nem talált hibát a 502 hibás átjáró hibája miatt. A API Management Diagnostics egy intelligens és interaktív megoldás, amely segítséget nyújt a APIM-ben közzétett API-k hibakereséséhez, és nincs szükség konfigurációra. Ha a közzétett API-kkal kapcsolatos problémákba ütközik, API Management a diagnosztika kimutatja, hogy mi a probléma, és végigvezeti Önt a megfelelő információkkal a probléma gyors elhárítása és megoldása érdekében.

Bár ez a felhasználói élmény akkor hasznos, ha az elmúlt 24 órában az API-val kapcsolatos problémákat tapasztalja, az összes diagnosztikai gráf mindig elérhető az elemzéshez.

## <a name="open-api-management-diagnostics"></a>API Management diagnosztika megnyitása

API Management diagnosztika eléréséhez navigáljon a [Azure Portal](https://portal.azure.com)API Management Service-példányához. A bal oldali navigációs sávon válassza a **problémák diagnosztizálása és megoldása** lehetőséget.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="A képernyőképen megtudhatja, hogyan navigálhat a diagnosztika szolgáltatáshoz.":::



## <a name="intelligent-search"></a>Intelligens keresés

Az oldal tetején található keresési sávban megkeresheti a hibákat és problémákat. A keresés segítségével megtalálhatja azokat az eszközöket, amelyek segíthetnek a problémák elhárításában vagy megoldásában. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="képernyőkép az intelligens keresésről.":::


## <a name="troubleshooting-categories"></a>Hibaelhárítási kategóriák

A kategóriák alatt hibaelhárítási problémák jelentkezhetnek. Az API-teljesítményre, az átjáróra, az API-politikákra és a szolgáltatási szintjére vonatkozó gyakori problémák mindegyike elemezhető az egyes kategóriákon belül. Az egyes kategóriák a diagnosztikai ellenőrzéseket is biztosítanak. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="képernyőkép a kategória áttekintéséről.":::


### <a name="availability-and-performance"></a>Rendelkezésre állás és teljesítmény

Tekintse meg az API rendelkezésre állását és a teljesítménnyel kapcsolatos problémákat ebben a kategóriában. Ha kiválasztja ezt a kategóriát, az interaktív felületen néhány gyakori ellenőrzés is látható. Kattintson az egyes ellenőrzések elemre az egyes problémák részleteinek megtekintéséhez. Az ellenőrzési szolgáltatás egy gráfot is tartalmaz, amely az API teljesítményét és a teljesítménnyel kapcsolatos problémák összegzését jeleníti meg. Előfordulhat például, hogy az API-szolgáltatás 5xx hibával és időtúllépéssel rendelkezett az elmúlt órában a háttérben. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Az interaktív felület-ellenőrzések 1. képernyőképe.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Az interaktív interfész-ellenőrzések 2. képernyőképe.":::

### <a name="api-policies"></a>API-házirendek

Ez a kategória hibákat észlel, és értesíti a szabályzattal kapcsolatos problémákról. 

A hasonló interaktív interfész végigvezeti az adatmetrikákkal, és segít az API-házirendek konfigurációjának megoldásában.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="képernyőkép az API-szabályzatok kategória csempéről.":::

### <a name="gateway-performance"></a>Átjárók teljesítménye 

Az átjáróval kapcsolatos kérelmek vagy válaszok, illetve az átjárón 4xx vagy 5xx hibák esetén ezt a kategóriát kell használni a figyeléshez és a hibaelhárításhoz. Hasonlóképpen kihasználhatja az interaktív felületet az API-átjáró teljesítményének megkereséséhez szükséges konkrét területen. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="képernyőkép az átjáró teljesítmény kategória csempéről.":::

### <a name="service-upgrade"></a>Szolgáltatás frissítése

Ez a kategória ellenőrzi, hogy a jelenleg használt szolgáltatási szintet (SKU) használja-e, és emlékezteti a frissítésre, hogy elkerülje az adott szintre esetleg kapcsolódó problémákat. Ugyanez az interaktív felület segíti a mélyebb grafikát és az összegző ellenőrzés eredményét. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="képernyőfelvétel a szolgáltatás verziófrissítési kategóriájának csempéről.":::

## <a name="search-documentation"></a>Keresés a dokumentációban

A problémák diagnosztizálására és megoldására szolgáló eszközök további részében kereshet a problémával kapcsolatos hibaelhárítási dokumentációban. Miután futtatta a diagnosztikát a szolgáltatáson, válassza a **Keresés a dokumentációban** lehetőséget az interaktív felületen. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="a Search dokumentációs függvény használatáról 1. képernyőkép.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="2. képernyőkép a keresési dokumentáció használatáról.":::


## <a name="next-steps"></a>Következő lépések

* Az [API Analytics](howto-use-analytics.md) használatával is elemezheti az API-k használatát és teljesítményét. 
* Szeretné elhárítani a diagnosztika Web Apps hibáit? Olvassa el [itt](../app-service/overview-diagnostics.md)
* Diagnosztika kihasználása az Azure Kubernetes Services problémáinak vizsgálatához. Lásd: [diagnosztika az AK](../aks/concepts-diagnostics.md) -ban
* Tegye fel kérdéseit vagy visszajelzéseit a [UserVoice](https://feedback.azure.com/forums/248703-api-management) címen a "[diag]" cím hozzáadásával.
