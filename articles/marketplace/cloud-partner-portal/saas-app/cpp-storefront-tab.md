---
title: Azure SaaS alkalmazás ajánlat kirakat konfigurálása |} Az Azure Marketplace-en
description: SaaS-alkalmazás ajánlatban kirakatban konfigurálása az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 1c6f2553bdff2aed0722bbb37e851b00a16b59d5
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941738"
---
# <a name="saas-application-storefront-details-tab"></a>SaaS-alkalmazás kirakat részletei lapon

Ez a cikk bemutatja, hogyan használja a kirakat Részletek lap be SaaS-alkalmazását, és adja meg a marketing eszközök. Ezen a lapon a következő űrlapok tartalmazza: Áttekintés, Marketing-összetevők, érdeklődői felügyeleti és jogi. 


## <a name="overview"></a>Áttekintés

Az Áttekintés képernyő rendelkezik a szükséges és választható mezőket a következő képernyőfelvételen látható. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

![Storefront áttekintő űrlap](./media/saas-storefront-overview.png)

A következő táblázat ismerteti, amelyek az ajánlat megadhat kirakat részleteit. Kötelező mezőt csillag (*) rendszer feltehetően.

|     Mező               |      Leírás       |
|     ------              |      -----------       |
|  **Ajánlat-összefoglaló\***    | Az ajánlat értékajánlat összefoglalása. Az ajánlat keresés lapon fog megjelenni. Legfeljebb 100 karakterből kell lennie.  |
| **Az ajánlat leírása**  | A leírást, amely megjelenik az alkalmazások részletei lapon. Engedélyezett maximális hossza 1300 karakter lehet. Alapszintű HTML kódtípus címkék használatával formázhatja a tartalmat. Ha például &lt;p&gt;, &lt;h1&gt;, &lt;h2&gt;, és &lt;li&gt;. Szeretné látni, hogyan fog kinézni a formázott leírását, például online valós idejű HTML eszköz használata https://htmledit.squarefree.com    |
|  **Ágazatok**   | Válassza ki a legjobb igazított az ajánlat iparágak. Ha az alkalmazás több iparágban vonatkozik, maximum kettőt kiválaszthatja.   |
| **Javasolt kategóriák (max. 3)\*** | Válassza ki a kategóriákat, amelyek az ajánlat legjobb igazodik. Legfeljebb három kategóriák közül választhat. |
| **Alkalmazásverzió**         | Adja meg az alkalmazás verziószáma. |
| **A keresési kulcsszavak (max. 3)** | Adja meg legfeljebb három keresési kulcsszavakat, amellyel az ügyfelek az alkalmazás keresése a piactéren kirakat webhelyen.  |
|   |   |


## <a name="marketing-artifacts"></a>Marketing-összetevők

Használja a **Marketing összetevők** űrlap azonosításához az Azure Marketplace-en marketing-adategységeket, mint a emblémák, videók, képernyőképek és dokumentumokat.

![Storefront marketing összetevők űrlap](./media/saas-storefront-artifacts.png)

A következő táblázat ismerteti a mezők Digitálismarketing-összetevők.  Kötelező mezőt csillag (*) rendszer feltehetően.

|       Mező       |            Leírás            |
|       ------      |            -----------            |
|    **Emblémát**      |  Amennyiben egy eladási Microsoft SaaS-alkalmazáson keresztül, az összes embléma rendszerkép kell adnia. Ha ez egy lista, mindössze 2 emblémák szükség. Az alábbi emblémák irányelvek a Cloud Partner portálra feltöltött:<br><ul><li>Tartsa számát az elsődleges és másodlagos színt az embléma alacsony. Az Azure arculata egyszerű színpalettát használ. </li><li>Kerülje a fekete vagy fehér, mint az embléma háttérszínének. Az Azure Portal a témák színei fekete-fehér. Ehelyett használja az egyes szín, amely biztosítja, az embléma neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha áttetsző háttérrel használ, akkor ügyeljen arra, hogy az embléma és a szöveg nem lesznek fekete, a fehér vagy a kék. </li><li>Az embléma háttér átmenetének nem használja. </li><li>Szöveg-, akár a vállalat vagy a márkanév, elkerüli az embléma. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.</li><li>A emblémakép nem kell nyújtani.</li></ul>    |
|   **Videók**       | Lehetővé teszi az Ön ajánlatát a videók hivatkozások hozzáadását. YouTube-on és/vagy Vimeo videók, az ügyfelek számára láthatók és az ajánlat mutató hivatkozásokat is használhatja. Emellett adja meg a 1280 x 720 képpont méretű png-kép a videó egy miniatűr képére kell. Legfeljebb négy videót ajánlat / rendelkezhet. |
|  **Dokumentumok**     | Marketing dokumentumok hozzáadása az ajánlat lehetővé teszi. Minden dokumentum PDF formátumban kell megadni, és legfeljebb három dokumentumok ajánlat rendelkezhet.   |
|  **Képernyőképek**   | Az ajánlat pillanatképeiért hozzáadását teszi lehetővé. Nincs legfeljebb öt képernyőképek ajánlat / adható hozzá. A maximális képméret 1280 x 720 képpont.  |
|  **Hasznos hivatkozások**  | Lehetővé teszi, hogy az ajánlat az architektúra-diagramok, mutasson a, vagy egyéb webhely, amely egy ügyfél szeretné tekintse meg a külső URL-címeket. |
|  |  |


### <a name="marketing-examples"></a>Marketing-példák

A következő képernyőfelvétel egy Marketplace keresési eredmény egy példát mutat be.

![Marketplace-en keresési eredmény](./media/saas-marketplace-search-result.png)

Az alábbi képen látható, hogyan az ajánlat jelenjen meg a Marketplace-en, miután egy ügyfél a keresési eredményekben az ajánlat csempére kattint.

![Marketplace-en keresési eredmény részletei](./media/saas-marketplace-search-result-details.png)


## <a name="lead-management"></a>Felügyeleti vezethet

Lead felügyelet konfigurálásához válassza a **érdeklődő cél** a legördülő listából. A következő képernyőfelvételen látható az elérhető célok.

![Storefront részletek lead felügyelet](./media/saas-storefront-lead-destination.png)

>[!TIP] 
>Válassza ki az információ ikonra az üzenet jelenik meg: "Válassza ki az érdeklődőket a rendszer hol tárolja a rendszer. Ismerje meg, hogyan csatlakozhat a CRM-rendszerbe [Itt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "


## <a name="legal"></a>Jogi tudnivalók

A jogi képernyőn adja meg a szükséges minden egyes ajánlathoz jogi dokumentációját.

![Jogi űrlap storefront részletei](./media/saas-storefront-lead-legal.png)

Adja meg az alábbi információkat:

- **Adatvédelmi szabályzat URL-címe\***  – adjon meg egy hivatkozást az alkalmazás adatvédelmi szabályzathoz.
- **Használati feltételek\***  – adja meg a használati feltételeket az alkalmazáshoz. Fogadja el ezeket a feltételeket, mielőtt az alkalmazás próbálkozhatnak ügyfelek szükségesek.


## <a name="next-steps"></a>További lépések

[Névjegyek lap](./cpp-contacts-tab.md)
