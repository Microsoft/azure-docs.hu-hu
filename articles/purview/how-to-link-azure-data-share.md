---
title: Kapcsolódás az Azure-beli adatmegosztáshoz
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók az Azure-beli adatmegosztási fiókok az Azure hatáskörébe az adatforrások kereséséhez és az adatok lebontásához.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553358"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Az Azure-beli adatmegosztás és az Azure-beli szolgáltatás összekötése

Ez a cikk azt ismerteti, hogyan csatlakoztatható az Azure-beli [adatmegosztási](../data-share/overview.md) fiók az Azure hatáskörébe, és Hogyan szabályozható a megosztott adatkészletek (kimenő és bejövő) adatközpontban. A különböző adatirányítási personák felderítik és nyomon követhetik az adatvonalat a határokon, például szervezeteknél, részlegekben és akár adatközpontokban is.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Az adatmegosztási vonal célja, hogy részletes információkat nyújtson a kiváltó okok elemzéséhez és a hatás elemzéséhez.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>1:360-es forgatókönyv: a partner szervezet vagy belső részleg számára megosztott/kimenő adatkészletek megtekintése

Az adattisztek megtekinthetik az összes olyan adatkészletet, amelyek a partner szervezeteivel együttesen vannak megosztva. Megkereshetik és felfedezhetik az adatkészleteket a szervezet neve alapján, és megtekinthetik az összes kimenő és bejövő megosztás teljes nézetét.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>2. forgatókönyv: alapvető okok elemzése – a szervezetbe beérkező adatkészletek (a bejövő megosztások fogyasztói nézete) függősége

Egy jelentés nem megfelelő információval rendelkezik, mert a külső adatmegosztási fiókból származó, felsőbb rétegbeli adatokkal kapcsolatos problémák merültek fel. Az adatmérnökök megismerhetik a felsőbb rétegbeli hibákat, tájékoztatják az okokat, és további információt kaphatnak a megosztás tulajdonosáról az adateltérést okozó problémák megoldásához.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>3. forgatókönyv: a szervezeten kívüli adatkészletek elemzésének hatása (a kimenő megosztások szolgáltatói nézete)

Az adattermelők szeretnék tudni, hogy ki lesz hatással az adathalmaz módosítására. A Lineage használatával az adatelőállító könnyedén megismerheti az olyan alsóbb rétegbeli belső vagy külső partnerek hatását, akik az Azure-adatmegosztás használatával használják az adatmennyiséget.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure-adatmegosztás és a hatáskörébe tartozó kapcsolati élmény

Az Azure-beli adatmegosztás és az Azure-beli hatáskörébe tartozó fiók összekapcsolásához tegye a következőket:

1. Hozzon létre egy hatáskörébe tartozó fiókot. Az összes adatmegosztási információt egy hatáskörébe tartozó fiók fogja gyűjteni. Használhat egy meglévőt, vagy létrehozhat egy új hatáskörébe tartozó fiókot.

1. Kapcsolja össze Azure-beli adatmegosztását a hatáskörébe tartozó fiókkal.

    1. A hatáskörébe tartozó portálon lépjen a **felügyeleti központba** , és csatlakoztassa az Azure-beli adatmegosztást a **külső kapcsolatok** szakaszhoz.
    1. Válassza az **+ új** lehetőséget a felső sávon, keresse meg az Azure-beli adatmegosztást az előugró oldali sávban, és adja hozzá az adatmegosztási fiókot. Futtasson egy pillanatkép-feladatot az adatmegosztásnak a hatáskörébe tartozó fiókhoz való csatlakoztatása után, hogy az adatmegosztási eszközök és az információs vonal adatai láthatók legyenek a hatáskörébe.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Felügyeleti központ az Azure-adatmegosztás összekapcsolásához":::

1. Hajtsa végre a pillanatképet az Azure-adatmegosztásban.

    - Miután az Azure-beli adatmegosztási kapcsolatban létrejött az Azure hatáskörébe, pillanatképet futtathat a meglévő megosztásokhoz. 
    - Ha nem rendelkezik meglévő megosztásokkal, lépjen az Azure adatmegosztási portálra, és [ossza meg adatait](../data-share/share-your-data.md) , [és fizessen elő adatmegosztásra](../data-share/subscribe-to-data-share.md).
    - A megosztási pillanatkép befejezését követően megtekintheti a kapcsolódó adatmegosztási eszközöket és a Lineage-t a hatáskörébe.

1. Ismerje meg az adatmegosztási fiókokat, és ossza meg a hatáskörébe tartozó fiók adatait.

    - A hatáskörébe tartozó fiók kezdőlapján válassza a **Tallózás az eszköz típusa alapján** lehetőséget, és válassza ki az **Azure-adatmegosztás** csempét. Megkeresheti a fiók nevét, a megosztás nevét, a pillanatkép megosztását vagy a partner szervezetét. Máskülönben szűrőket alkalmazhat a keresési eredmény lapon a fióknév, a megosztás típusa (elküldött és fogadott megosztások) között.

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Azure-adatmegosztás a keresési eredmények lapon":::

    >[!Important]
    >Ahhoz, hogy az adatmegosztási eszközök a hatáskörébe tartoznak, az adatmegosztásnak a hatáskörébe való kapcsolódása után el kell indítani egy pillanatkép-feladatot.

1. Az Azure-adatmegosztással megosztott adatkészletek nyomon követése.

    - A hatáskörébe tartozó keresési eredmény lapon válassza ki az adatmegosztási pillanatképet (fogadott/elküldve), majd válassza a **Lineage** (kivezetés) fület, hogy megjelenjen egy felsőbb rétegbeli és alsóbb rétegbeli függőségekkel rendelkező Lineage Graph

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Az Azure-adatmegosztás használatával megosztott adatkészletek":::

## <a name="next-steps"></a>Következő lépések

- [A katalógus Lineage felhasználói útmutatója](catalog-lineage-user-guide.md)
- [Hivatkozás Azure Data Factoryra a Lineage](how-to-link-azure-data-factory.md)
