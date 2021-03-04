---
title: Azure Monitor tervező átalakítása munkafüzetekbe konverzió összegzése és elérése
description: A munkafüzetek eléréséhez szükséges engedélyek a Azure Monitor nézeteiből való áttéréskor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 42cb8db0f67d3a01b2e8443e6cb7c47f0fc44c4b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043390"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Tervező megtekintése a munkafüzetek átalakításának összegzéséhez és eléréséhez
A [tervező](view-designer.md) a Azure monitor egyik funkciója, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti a log Analytics-munkaterületen lévő, diagramokkal, listákkal és időpontokkal kapcsolatos adatait. A rendszer fokozatosan kiváltja és lecseréli azokat a munkafüzetekkel, amelyek további funkciókat biztosítanak. Ez a cikk részletesen ismerteti, hogyan hozhat létre áttekintés-összefoglalást és a munkafüzetek eléréséhez szükséges engedélyeket.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>A munkaterület összefoglalásának létrehozása az Azure irányítópultról
A tervező felhasználók megtekinthetik, hogy az áttekintő csempék nézeteket képviselnek-e. A vizualizációk áttekintéséhez, például a Designer-munkaterület összefoglalásához, a munkafüzetek rögzített lépéseket biztosítanak, amelyek rögzíthető a [Azure Portal irányítópultján](../../azure-portal/azure-portal-dashboards.md). Akárcsak az Áttekintés csempék a munkaterület összefoglalásakor, a rögzített munkafüzet-elemek közvetlenül a munkafüzet nézethez fognak hivatkozni.

Kihasználhatja az Azure-irányítópultok magas szintű testreszabási funkcióinak előnyeit, amelyek lehetővé teszik a rögzített elemek és vizualizációk automatikus frissítését, áthelyezését, méretezését és további szűrését. 

![A képernyőképen egy testreszabott Azure-irányítópult szerepel a munkaterület összefoglalása című ábrán.](media/view-designer-conversion-access/dashboard.png)

Hozzon létre egy új Azure-irányítópultot, vagy válasszon ki egy meglévő irányítópultot a munkafüzet-elemek rögzítésének megkezdéséhez.

Az egyes elemek rögzítéséhez engedélyeznie kell a PIN-kód ikont az adott lépéshez. Ehhez válassza ki a lépéshez tartozó **Szerkesztés** gombot, majd válassza a fogaskerék ikont a **Speciális beállítások** megnyitásához. Győződjön meg arról, hogy a **lépéshez mindig megjelenjen a rögzítés ikon**, és a lépés jobb felső sarkában megjelenik egy PIN-ikon. Ez a PIN-kód lehetővé teszi adott vizualizációk rögzítését az irányítópulton, például az áttekintő csempéket.

![Rögzítési lépés](media/view-designer-conversion-access/pin-step.png)


A munkafüzetből vagy a munkafüzet teljes tartalmából is rögzíthet több vizualizációt az irányítópulton. A teljes munkafüzet rögzítéséhez válassza a **Szerkesztés** lehetőséget a felső eszköztáron a **szerkesztési mód** váltásához. Ekkor megjelenik egy PIN-kód ikon, amely lehetővé teszi, hogy rögzítse a munkafüzet teljes elemét vagy az összes egyéni lépést és vizualizációt.

![Összes rögzítése](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Engedélyek megosztása és megtekintése 
A munkafüzetek előnye, hogy magán-vagy közös dokumentumok. Alapértelmezés szerint a mentett munkafüzetek a **saját jelentések** alatt lesznek mentve, ami azt jelenti, hogy csak a létrehozó tekintheti meg ezt a munkafüzetet.

A munkafüzetek megosztásához válassza a felső eszköztár **megosztás** ikonját **szerkesztési módban**. A rendszer arra kéri, hogy helyezze át a munkafüzetet a **megosztott jelentésekre**, ami egy olyan hivatkozást fog eredményezni, amely közvetlen hozzáférést biztosít a munkafüzethez.

Ahhoz, hogy egy felhasználó megtekinthesse a megosztott munkafüzeteket, hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez és erőforráscsoporthoz, amelybe a munkafüzetet menti.

![Előfizetés-alapú hozzáférés](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Következő lépések

- [Gyakori feladatok](view-designer-conversion-tasks.md)