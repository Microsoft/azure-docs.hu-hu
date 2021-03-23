---
title: Az eszközértesítések kezelése
description: Az értesítések olyan hálózati tevékenységekkel kapcsolatos információkat biztosítanak, amelyek a beavatkozáshoz szükségesek, valamint a tevékenység kezelésére vonatkozó ajánlásokat is tartalmaznak.
ms.date: 12/12/2020
ms.topic: how-to
ms.openlocfilehash: c0c2fc5a4c01a8a31512cd43c340bf3fadc259b1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781330"
---
# <a name="work-with-device-notifications"></a>Az eszközértesítések kezelése

Az értesítések olyan hálózati tevékenységekkel kapcsolatos információkat biztosítanak, amelyek a beavatkozáshoz szükségesek, valamint a tevékenység kezelésére vonatkozó ajánlásokat is tartalmaznak. Előfordulhat például, hogy értesítést kap a következőről:

- Inaktív eszköz. Ha az eszköz már nem része a hálózatnak, törölheti. Ha az eszköz inaktív, például azért, mert véletlenül le van választva a hálózatról, csatlakoztassa újra az eszközt, és zárja be az értesítést.

- A rendszer IP-címet észlelt egy olyan eszközön, amelyet jelenleg csak MAC-cím azonosít. Válaszoljon az eszköz IP-címének engedélyezésével.

Az értesítésekre adott válasz javítja az eszközök és eszközök leltározásával, valamint az adatbányászati lekérdezésekkel és jelentésekkel kapcsolatos információkat. Emellett betekintést nyújt a megbízható hálózati változásokra és a lehetséges hálózati konfigurációs beállításokra.

**Értesítések és riasztások**

A hálózati tevékenységre vonatkozó értesítések fogadása mellett *riasztásokat* is kaphat. Az értesítések információt nyújtanak a hálózati változásokról, vagy megoldatlan eszköz-tulajdonságokat tartalmaznak, amelyek nem jelentenek fenyegetést. A riasztások olyan hálózati eltérésekről és változásokról nyújtanak információt, amelyek fenyegetést jelenthetnek a hálózatra.

Értesítések megtekintése:

1. Válassza ki az **eszközbeállítások** elemet a konzol bal oldali menüjének ablaktábláján.

2. Válassza az **értesítések** ikont. Az ikon fölötti piros szám az értesítések számát jelzi.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Értesítési ikon.":::

   Az **értesítések** ablak megjeleníti az érzékelő által észlelt összes értesítést.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Értesítések.":::

## <a name="filter-the-notifications-window"></a>Az értesítések ablak szűrése

Használjon keresési szűrőket az Önt érdeklő értesítések megjelenítéséhez.

| Szűrés | Leírás |
|--|--|
| Szűrés típus szerint | Egy adott területre kiterjedő értesítések megtekintése. Megtekintheti például az inaktív eszközökre vonatkozó értesítéseket. |
| Szűrés dátumtartomány szerint | Egy adott időtartományra kiterjedő értesítések megjelenítése. Megtekintheti például a csak az elmúlt héten küldött értesítéseket. |
| Konkrét információk keresése | Adott értesítések keresése. |

## <a name="notification-events-and-responses"></a>Értesítési események és válaszok

A következő táblázat ismerteti a megjelenő értesítési eseményeket, valamint a kezelési lehetőségeit. Az eszköz adatait a javasolt értékkel frissítheti, vagy elvetheti az értesítést. Egy értesítés elvetése után az eszköz adatai nem frissülnek a javasolt információkkal. Ha a rendszer újra észleli a forgalmat, az értesítés újra el lesz küldve.

| Értesítési események típusai | Leírás | Válaszok |
|--|--|--|
| Új IP-cím észlelhető | Új IP-cím van társítva az eszközhöz. Öt forgatókönyv észlelése lehetséges: <br /><br /> További IP-cím lett társítva egy eszközhöz. Ez az eszköz egy meglévő MAC-címnek is társítva van.<br /><br /> A rendszer új IP-címet észlelt egy meglévő MAC-címet használó eszközhöz. Az eszköz jelenleg nem kommunikál IP-cím használatával.<br /> <br /> A rendszer új IP-címet észlelt egy NetBIOS-nevet használó eszközhöz. <br /><br /> A rendszer IP-címet észlelt egy MAC-címhez társított eszköz felügyeleti felülete. <br /><br /> A rendszer új IP-címet észlelt egy virtuális IP-címet használó eszközhöz. | **További IP-cím beállítása eszközre** (eszközök egyesítése) <br /> <br />**Meglévő IP-cím cseréje** <br /> <br /> **Elvetés**<br /> Távolítsa el az értesítést. |
| Inaktív eszközök | Az eszközön a forgalom több mint 60 napig nem észlelhető. | **Törlés** <br /> Ha az eszköz nem része a hálózatnak, távolítsa el. <br /><br />**Elvetés** <br /> Ha az eszköz a hálózat része, távolítsa el az értesítést. Ha az eszköz inaktív (például azért, mert véletlenül le van választva a hálózatról), zárja be az értesítést, és csatlakoztassa újra az eszközt. |
| Új OT-eszközök | Az alhálózat olyan OT-eszközt tartalmaz, amely nincs definiálva az ICS-alhálózatban. <br /><br /> Minden olyan alhálózat, amely legalább egy OT tartalmaz, ICS-alhálózatként is definiálható. Ez segít megkülönböztetni az OT és az IT-eszközöket a térképen. | **Beállítás ICS-alhálózatként** <br /> <br /> **Elvetés** <br />Távolítsa el az értesítést, ha az eszköz nem része az alhálózatnak. |
| Nincsenek konfigurált alhálózatok | Jelenleg nincsenek alhálózatok konfigurálva a hálózaton. <br /><br /> Konfigurálja az alhálózatokat a Térkép jobb megjelenítéséhez, valamint az OT és az IT-eszközök közötti különbségtétel lehetőségét. | **Nyissa meg az alhálózatok konfigurációját** , és konfigurálja az alhálózatokat. <br /><br />**Elvetés** <br /> Távolítsa el az értesítést. |
| Operációs rendszer módosításai | Egy vagy több új operációs rendszer van társítva az eszközhöz. | Válassza ki az eszközhöz hozzárendelni kívánt új operációs rendszer nevét.<br /><br /> **Elvetés** <br /> Távolítsa el az értesítést. |
| Új alhálózatok | Új alhálózatok lettek felderítve. | **Learn**<br />Az alhálózat automatikus hozzáadása.<br />**Alhálózat konfigurációjának megnyitása**<br />Adja hozzá az összes hiányzó alhálózati információt.<br />**Elvetés**<br />Távolítsa el az értesítést. |
| Eszköz típusának módosítása | Az eszközhöz új típusú eszköz van társítva. | **Beállítás {...}**<br />Társítsa az új típust az eszközhöz.<br />**Elvetés**<br />Távolítsa el az értesítést. |

## <a name="respond-to-many-notifications-simultaneously"></a>Válaszoljon egyszerre sok értesítésre

Előfordulhat, hogy egyszerre több értesítést kell kezelnie. Például:

- Ha az operációs rendszer nagy mennyiségű hálózati kiszolgálóra frissült, akkor utasíthatja az érzékelőt, hogy megismerje az összes frissített kiszolgáló új kiszolgálói verzióit. 

- Ha egy adott sorban lévő eszközök egy csoportja fokozatosan elindult, és többé nem aktív, akkor utasíthatja az érzékelőt, hogy távolítsa el ezeket az eszközöket a konzolról.

Az érzékelőt arra utasíthatja, hogy az újonnan észlelt adatokat több eszközre alkalmazza, vagy hagyja figyelmen kívül.   

Értesítések megjelenítése és az értesítések kezelése:

1. Használja a **szűrés típus, dátumtartomány** vagy az **összes kijelölése** lehetőséget. Szükség szerint törölje az értesítések kijelölését.

2. A **tanulás** lehetőség kiválasztásával utasíthatja az érzékelőt, hogy alkalmazza az újonnan észlelt adatokat a kiválasztott eszközökre. Vagy utasítsa az érzékelőt, hogy hagyja figyelmen kívül az újonnan észlelt adatokat az **elutasítás** lehetőség kiválasztásával. A megjelenő értesítések száma, amelyekkel egyidejűleg megtekintheti és elvégezheti az egyenként kezelendő értesítések számát.

Az **új IP** -címek és az **alhálózatok** konfigurált eseményei nem kezelhetők egyszerre. Manuális megerősítést igényelnek.

## <a name="see-also"></a>Lásd még

[Riasztások megtekintése](how-to-view-alerts.md)
