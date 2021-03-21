---
title: Azure Security Center fő irányítópultja vagy az "áttekintés" oldal
description: További információ a Security Center áttekintése oldal funkcióiról
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099777"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center áttekintése lap

A Azure Security Center megnyitásakor az Áttekintés oldalon megjelenik az első lap. 

Ez az interaktív irányítópult egységes nézetet biztosít a hibrid Felhőbeli számítási feladatok biztonsági állapotának kialakításához. Emellett a biztonsági riasztásokat, a lefedettségi adatokat és egyebeket is megjeleníti.

A lapon található bármely elem kiválasztásával részletesebb információkat kaphat.

:::image type="content" source="media/overview-page/overview.png" alt-text="A Security Center áttekintő lapja":::

## <a name="features-of-the-overview-page"></a>Az Áttekintés oldal funkciói

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Security Center Áttekintés oldalának felső sávjá":::

A **felső menüsor** a következőket kínálja:
- **Előfizetések** – a gombra kattintva megtekintheti és szűrheti az előfizetések listáját. A Security Center úgy állítja be a kijelzőt, hogy tükrözze a kiválasztott előfizetések biztonsági állapotát.
- **Újdonságok –** megnyithatja a [kibocsátási megjegyzéseket](release-notes.md) , így naprakészen tarthatja az új funkciókat, hibajavításokat és elavult funkciókat.
- A csatlakoztatott felhőalapú fiókok **magas szintű száma** az alábbi fő csempén található információk kontextusának megjelenítéséhez. Valamint az értékelendő erőforrások száma, az aktív javaslatok és a biztonsági riasztások. Válassza ki az értékelendő erőforrások számát az [eszközök leltárának](asset-inventory.md)eléréséhez. További információ az AWS- [fiókok](quickstart-onboard-aws.md) és a [GCP-projektek](quickstart-onboard-gcp.md)csatlakoztatásáról.


A lap közepén **négy központi csempe** található, melyek mindegyike egy dedikált irányítópultra mutat, további részletekért:
- **Biztonságos pontszám** – a Security Center folyamatosan értékeli az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákhoz. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint. [További információ](secure-score-security-controls.md).
- **Szabályozási megfelelőség** – a Security Center az Azure-környezet folyamatos felmérése alapján betekintést nyújt a megfelelőségi helyzetbe. A Security Center az ajánlott biztonsági eljárásoknak megfelelően elemzi a hibrid felhőalapú környezet kockázati tényezőit. Ezek az értékelések a megfelelőségi vezérlőkre vannak leképezve a szabványok támogatott készletében. [További információ](security-center-compliance-dashboard.md).
- **Azure Defender** – ez a felhőalapú munkaterhelés-védelmi platform (CWPP), amely az Azure-beli és a hibrid számítási feladatok speciális, intelligens védelméhez Security Centeron belül integrált. A csempén látható a csatlakoztatott erőforrások (a jelenleg kiválasztott előfizetések esetében) és a legutóbbi riasztások lefedettsége, súlyosság szerint kódolva. [További információ](azure-defender.md).
- **Firewall Manager** – ez a csempe a [Azure Firewall managerből](../firewall-manager/overview.md)származó hubok és hálózatok állapotát jeleníti meg. 


Az **Áttekintés** panel testreszabott elemeket kínál a környezethez, többek között a következőket:
- A legtöbbet megtámadott erőforrás
- A biztonsági pontszámok, amelyek a legnagyobb potenciállal rendelkeznek a biztonságos pontszám növeléséhez
- A legtöbb erőforrást érintő aktív javaslatok
- Legutóbbi blogbejegyzések Azure Security Center szakértők által

## <a name="next-steps"></a>Következő lépések

Ez az oldal a Security Center áttekintése lapot mutatta be. A kapcsolódó információk a következő témakörben találhatók:

- [Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel](asset-inventory.md)
- [Biztonsági pontszám az Azure Security Centerben](secure-score-security-controls.md)