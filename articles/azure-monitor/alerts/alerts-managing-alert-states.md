---
title: Riasztási és intelligenscsoport-állapotok kezelése
description: A riasztási és az intelligens csoportos példányok állapotának kezelése
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: cac926c3c56c7f58e5237b820e0328999ffe2c52
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033571"
---
# <a name="manage-alert-and-smart-group-states"></a>Riasztási és intelligenscsoport-állapotok kezelése

A Azure Monitor riasztásai mostantól [riasztási állapottal és figyelési feltétellel](./alerts-overview.md) rendelkeznek, és Hasonlóképpen az intelligens csoportok [intelligens csoporttal](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)rendelkeznek. Az állapot változásai mostantól a megfelelő riasztáshoz vagy intelligens csoporthoz tartozó előzményekben vannak rögzítve. Ez a cikk végigvezeti az állapot módosításának folyamatán a riasztások és az intelligens csoportok esetében is.

## <a name="change-the-state-of-an-alert"></a>Riasztás állapotának módosítása

1. A riasztások állapotát a következő különböző módokon módosíthatja: 
    * A minden riasztás lapon jelölje be azon riasztások melletti jelölőnégyzetet, amelyeknek módosítani szeretné az állapotát, majd kattintson az állapot módosítása gombra.   
    ![Képernyőfelvétel: az összes riasztás lap, amelynek a változási állapota be van jelölve.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Egy adott riasztási példány riasztás részletei lapon kattintson az állapot módosítása lehetőségre.   
    ![Képernyőfelvétel: a riasztás részleteit tartalmazó lap, amelyen a változás riasztási állapota van kiválasztva.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Egy adott riasztási példány riasztás részletei lapon az intelligens csoport ablaktáblán kattintson a kívánt riasztások melletti jelölőnégyzetre.    
    ![Képernyőfelvétel: a szívverési riasztások oldalát jeleníti meg, és néhány olyan példánnyal, amelyeken pipa jelek szerepelnek.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Az intelligens csoport részletei lapon a tag riasztások listájában kattintson a riasztások elemre a kívánt állapot módosításához, és kattintson a Change intézménye ahová (állapot módosítása) elemre, majd kattintson az állapot módosítása gombra.   
    ![Képernyőfelvétel: az intelligens csoport részletei oldal, ahol kiválaszthatja azokat a riasztásokat, amelyeknek az állapotát módosítani szeretné.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ha az állapot módosítása gombra kattint, megjelenik egy előugró ablak, amely lehetővé teszi, hogy kiválassza az állapotot (új/visszaigazolt/lezárt), és szükség esetén adjon meg egy megjegyzést.   
![Képernyőfelvétel: a részletek változásának riasztása párbeszédpanel.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Ha ez megtörtént, az állapot változását a rendszer a megfelelő riasztás előzményeiben rögzíti. A megtekintéshez nyissa meg a megfelelő részletek lapot, és ellenőrizze az előzmények szakaszt.    
![A képernyőképen az állapot változásainak előzményei láthatók.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Intelligens csoport állapotának módosítása
1. Az intelligens csoportok állapotát a következő módokon módosíthatja:
    1. Az intelligens csoport listája lapon kattintson a jelölőnégyzetre azon intelligens csoportok mellett, amelyeken módosítani kívánja az állapotát, és kattintson az állapot módosítása gombra.  
    ![A képernyőképen az intelligens csoportok változási állapota oldal jelenik meg.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Az intelligens csoport részletei lapon kattintson az állapot módosítása elemre.        
    ![Képernyőfelvétel: a Smart Group Details (intelligens csoport kiválasztásának módosítása) oldalát jeleníti meg.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ha az állapot módosítása gombra kattint, megjelenik egy előugró ablak, amely lehetővé teszi, hogy kiválassza az állapotot (új/visszaigazolt/lezárt), és szükség esetén adjon meg egy megjegyzést. 
![Képernyőfelvétel: az intelligens csoport változási állapotának párbeszédpanelje.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Az intelligens csoportok állapotának módosítása nem változtatja meg az egyes tagokra vonatkozó riasztások állapotát.

1. Ha ez megtörtént, az állapot változása a megfelelő intelligens csoport előzményeiben lesz rögzítve. A megtekintéshez nyissa meg a megfelelő részletek lapot, és ellenőrizze az előzmények szakaszt.     
![A képernyőképen az intelligens csoport változásainak előzményei láthatók.](./media/alerts-managing-alert-states/state-sg-history.jpg)