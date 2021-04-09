---
title: Azure Automation runbookok figyelése metrikus riasztásokkal
description: Ez a cikk a metrikai riasztások runbook befejezési állapot alapján történő beállítását ismerteti.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593946"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Runbookok monitorozása metrikariasztásokkal

Ebből a cikkből megtudhatja, hogyan hozhat létre [metrikus riasztást](../azure-monitor/alerts/alerts-metric-overview.md) a runbook befejezési állapota alapján.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="create-alert"></a>Riasztás létrehozása

A riasztások lehetővé teszik, hogy megadjon egy feltételt a figyeléshez, valamint egy műveletet, amely a feltétel teljesülése esetén végrehajtandó.

1. Indítsa el a Azure Automation szolgáltatást a Azure Portal kattintson a **minden szolgáltatás** elemre, majd keresse meg és válassza ki az **Automation-fiókokat**.

2. Az Automation-fiókok listájában válassza ki azt a fiókot, amelyhez riasztást szeretne létrehozni. 

3. A **figyelés** területen válassza a **riasztások** , majd az **+ új riasztási szabály** lehetőséget. A cél hatóköre már definiálva van, és társítva van az Automation-fiókjához.

### <a name="configure-alert-criteria"></a>Riasztási feltételek konfigurálása

1. Kattintson a **feltétel kiválasztása** elemre. Válassza a **mérőszámok** lehetőséget a **jel típusához**, és válassza a **teljes feladatok** elemet a listából.

2. A **jel logikai beállítása** oldalon megadhatja a riasztást kiváltó logikát. A korábbi gráfban két dimenziót, a **Runbook nevét** és **állapotát** mutatjuk be. A méretek az eredmények szűrésére szolgáló metrika különböző tulajdonságai. A **Runbook neve** mezőben válassza ki azt a Runbook, amelyről riasztást szeretne kapni, vagy hagyja üresen a riasztást az összes runbookok. Az **állapot** beállításnál válasszon ki egy állapotot a figyelni kívánt legördülő listából. A legördülő listában megjelenő runbook neve és állapota csak az elmúlt héten futtatott feladatokhoz használható.

   Ha olyan állapotra vagy runbook szeretne riasztást kapni, amely nem jelenik meg a legördülő listában, kattintson az **Egyéni érték hozzáadása** lehetőségre a dimenzió mellett. Ez a művelet egy olyan párbeszédpanelt nyit meg, amely lehetővé teszi egyéni érték megadását, amely az adott dimenzióhoz még nem lett kibocsátva a közelmúltban. Ha olyan értéket ad meg, amely nem létezik olyan tulajdonsághoz, amelyet a riasztás nem indít el.

   > [!NOTE]
   > Ha nem adja meg a **Runbook** neve dimenzió nevét, ha vannak olyan runbookok, amelyek megfelelnek az állapot feltételeinek, beleértve a rejtett rendszer runbookok is, riasztást küld.

    Ha például riasztást szeretne kapni, ha egy runbook _sikertelen_ állapotot ad vissza, a runbook nevének megadása mellett az **állapot** dimenzióhoz **nem sikerült** hozzáadni az egyéni dimenzió értékét.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Egyéni dimenzió értékének megadása" border="false":::

3. A **riasztási logika** szakaszban adja meg a riasztás feltételét és küszöbértékét. A feltétel megadott előnézete az alábbi ábrán látható.

4. A **kiértékelés** alapján területen válassza ki a lekérdezés TimeSpan, és azt, hogy milyen gyakran szeretné futtatni a lekérdezést. Ha például **az elmúlt 5 percben** az **időszakot** választja, és **1 percenként** a **gyakoriságot**, a riasztás az elmúlt 5 percben a feltételnek megfelelő runbookok számát keresi. A lekérdezés percenként fut, és ha a megadott riasztási feltételek már nem találhatók meg egy 5 perces ablakban, a riasztás feloldja magát. Ha elkészült, kattintson a **kész** gombra.

   ![Válasszon ki egy erőforrást a riasztáshoz](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>A végrehajtandó művelet megadása

1. A **műveleti csoport** területen válassza a **műveleti csoport megadása** lehetőséget. A műveleti csoport olyan műveletek csoportja, amelyek több riasztáson is használhatók. Ezek lehetnek például a következők: e-mail-értesítések, runbookok, webhookok és sok más. Ha többet szeretne megtudni a műveleti csoportokról és az e-mailes értesítéseket küldő lépésekről, tekintse meg a [műveleti csoportok létrehozása és kezelése](../azure-monitor/alerts/action-groups.md)című témakört.

### <a name="define-alert-details"></a>Riasztás részleteinek megadása

1. A **riasztási szabály részletei** területen adjon meg egy rövid nevet és egy leírást. Állítsa be a **súlyosságot** a riasztási feltételnek megfelelően. A 0 és 5 közötti értékben öt megszakítás áll fenn. A riasztásokat a súlyosságtól függetlenül kezeli a rendszer, így a súlyossága megegyezik az üzleti logikával.

1. A létrehozáskor az alapértelmezett szabályok engedélyezve vannak, hacsak **nem a nem** lehetőséget választja a **riasztási szabály engedélyezése a létrehozáskor** lehetőségre. A letiltott állapotú riasztások esetén a későbbiekben is engedélyezheti őket, ha készen áll. A módosítások mentéséhez válassza a **riasztási szabály létrehozása** lehetőséget.

## <a name="receive-notification"></a>Értesítés fogadása

Ha a riasztási feltételek teljesülnek, a műveleti csoport futtatja a megadott műveletet. Ebben a cikkben egy e-mailt küldünk. Az alábbi képen egy példa látható a riasztás elindítása után kapott e-mailekre:

![E-mail riasztás](./media/automation-alert-activity-log/alert-email.png)

Ha a metrika már nem a meghatározott küszöbértéken kívül esik, a rendszer inaktiválja a riasztást, és a műveleti csoport futtatja a definiált műveletet. Ha egy e-mail-Művelettípus van kiválasztva, a rendszer elküld egy feloldási e-mailt, amely megoldódott.

## <a name="next-steps"></a>Következő lépések

* További információ: [riasztások használata Azure Automation runbook elindításához](automation-create-alert-triggered-runbook.md).
