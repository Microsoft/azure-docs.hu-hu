---
title: A Windows rendszerű virtuális asztal integrálása az Azure-Azure Advisor
description: A Azure Advisor használata a Windows rendszerű virtuális asztali környezetben.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89147533"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Azure Advisor használata a Windows rendszerű virtuális asztallal

A Azure Advisor segíthetnek a felhasználók közös problémáinak megoldásában anélkül, hogy támogatási eseteket kellene benyújtaniuk. A javaslatok csökkentik a Súgó kérések beküldésének szükségességét, az idő és a költségek megtakarítását.

Ebből a cikkből megtudhatja, hogyan állíthatja be Azure Advisor a Windows rendszerű virtuális asztali környezetben, hogy segítse a felhasználókat.

## <a name="what-is-azure-advisor"></a>Mi az Azure Advisor?

Azure Advisor elemzi a konfigurációkat és a telemetria, hogy személyre szabott javaslatokat nyújtson a gyakori problémák megoldásához. Ezekkel a javaslatokkal optimalizálhatja Azure-erőforrásait a megbízhatóság, a biztonság, a működés kiválósága, a teljesítmény és a költséghatékonyság érdekében. További információt [a Azure Advisor webhelyén talál](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Az Azure Advisor használatának megkezdése

Az első lépésekhez egy Azure-fiókra van szükség a Azure Portal. Először nyissa meg a Azure Portal at <https://portal.azure.com/#home> , majd válassza az **Advisor** elemet az **Azure-szolgáltatások** területen az alábbi ábrán látható módon. A Azure Portal keresési sávjába is beírhatja a "Azure Advisor" értéket.

> [!div class="mx-imgBorder"]
> ![A Azure Portal képernyőképe. A felhasználó a Azure Advisor hivatkozás fölé viszi az egérmutatót, így a legördülő menü jelenik meg.](media/azure-advisor.png)

Azure Advisor megnyitásakor öt kategória jelenik meg:

- Költségek
- Biztonság
- Megbízhatóság
- Működésbeli kiválóság
- Teljesítmény

> [!div class="mx-imgBorder"]
> ![Az egyes kategóriákhoz tartozó öt menüt bemutató Azure Advisor képernyőképe. A saját mezőiben megjelenő öt elem a Cost, a biztonság, a megbízhatóság, a működési kiválóság és a teljesítmény.](media/advisor-categories.png)

Ha kiválaszt egy kategóriát, az aktív javaslatok lapjára lép. Ezen az oldalon megtekintheti, hogy mely javaslatokat Azure Advisor Önnek, ahogy az alábbi képen is látható.

> [!div class="mx-imgBorder"]
> ![Az aktív javaslatok listáját bemutató képernyőkép az operatív kiválósághoz. A lista hét javaslatot mutat be különböző prioritási szintekkel.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>További tippek a Azure Advisor

- Ügyeljen arra, hogy a javaslatok gyakran, legalább hetente egyszer ellenőrizhetők legyenek. Azure Advisor naponta többször frissíti az aktív javaslataikat. Az új javaslatok ellenőrzése lehetővé teszi a nagyobb problémák elkerülését azáltal, hogy megkönnyíti a kisebbek azonnali kiválasztását és megoldását.

- Mindig próbálkozzon a legmagasabb prioritási szintű problémák megoldásával Azure Advisorban. A magas prioritású problémák piros színnel vannak megjelölve. A magas prioritású javaslatok megoldatlan kihagyása problémákat okozhat a sorban.

- Ha egy javaslat kevésbé fontosnak tűnik, elvetheti vagy elhalaszthatja. A javaslatok elvetéséhez vagy elhalasztásához lépjen a **művelet** oszlopra, és módosítsa az elem állapotát.

- Ne utasítsa el az ajánlásokat, amíg nem tudja, miért jelentkeznek, és biztos lehet benne, hogy nem lesz negatív hatással a felhasználóra vagy a felhasználókra. Mindig válassza a **továbbiak** lehetőséget a probléma megtekintéséhez. Ha a Azure Advisor utasításait követve elhárít egy problémát, automatikusan eltűnik a listából. Jobban kikapcsolja a problémák megoldását, mint azok ismételt elhalasztása.

- Ha a Windows rendszerű virtuális asztal egyik hibája merül fel, mindig tekintse meg Azure Advisor először. Azure Advisor útmutatást nyújt a probléma megoldásához, vagy legalább egy olyan erőforrás felé mutat, amely segíthet.

## <a name="next-steps"></a>Következő lépések

A javaslatok megoldásával kapcsolatos további információkért lásd: [Azure Advisor javaslatok megoldása](azure-advisor-recommendations.md).

Ha javaslatai vannak az új javaslatokhoz, tegye közzé a [Azure Advisor felhasználói hangalapú fórumon](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
