---
title: Szabályok konfigurálása és riasztások kezelése
description: Útmutató szabályok konfigurálásához és a riasztások kezeléséhez a FarmBeats-ben
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182927"
---
# <a name="configure-rules-and-manage-alerts"></a>Szabályok konfigurálása és riasztások kezelése

Az Azure FarmBeats lehetővé teszi, hogy az üzleti logikán alapuló szabályokat hozzon létre a farmon üzembe helyezett érzékelőktől és eszközöktől származó érzékelői adatok mellett. A szabályok riasztásokat váltanak ki a rendszeren, amikor az érzékelő értéke túllép egy küszöbértéket. A küszöbértékek után létrehozott riasztások megtekintésével és elemzésével gyorsan elvégezheti a problémák megoldását és a szükséges megoldások beszerzését.

## <a name="create-rule"></a>Szabály létrehozása

1. A kezdőlapon nyissa meg a **szabályok** lapot.
2. Válassza az **új szabály** lehetőséget. Megjelenik az új szabály ablak.

    ![Képernyőfelvétel: az új szabály gomb és az új szabály szakasz.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Adja meg a **szabály nevét** és a **szabály leírását** , majd válasszon ki egy farmot a **Farm kiválasztása** legördülő menüből.
4. Adja meg a farm nevét, hogy kiválassza a farm és a **feltételek** szakaszt, amely ugyanabban az ablakban jelenik meg.  

    ![A feltételek szakaszt kiemelő képernyőkép.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. A **feltételek** mezőben adja meg a **mérték**, az **operátor** és az **érték** értékét.
6. Írja be a mérték nevét a **mérték** legördülő menüben.
7. Válassza a **+ feltétel hozzáadása** lehetőséget a szabályhoz tartozó további feltételek hozzáadásához.
8. Válassza ki a **súlyossági szintet**.
9. Az **e-mail-** riasztások engedélyezéséhez kapcsolja be az **e-mailek engedélyezve** váltógomb gombot.

    ![Képernyőkép, amely megjeleníti az E-mail engedélyezve lehetőséget.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Adja meg azokat az **e-mail-címeket** , amelyekre el szeretné küldeni az e-mail-riasztást, valamint az **e-mail tárgyát** és a **További megjegyzéseket**.  
11. A **szabály állapota** beállításnál a szabály engedélyezéséhez vagy letiltásához váltson az **engedélyezve** váltógomb gombra.
    Megtekintheti a szabály által érintett eszközök számát.
12. A szabály létrehozásához kattintson az **alkalmaz** gombra.

## <a name="view-rule"></a>Szabály megtekintése

A **Farm** lap az elérhető szabályok listáját jeleníti meg. Válassza ki a **szabály nevét**. Egy ablakban a következő, a kiválasztott szabályra vonatkozó részletek jelennek meg:
 - Szabály neve
 - Azon farmra mutató hivatkozás, amelyhez a szabály társítva van
 - Létrehozás dátuma
 - Utolsó frissítés dátuma
 - Súlyossági szint
 - Szabály állapota
 - Feltételek listája  
 - A szabály által érintett eszközök száma

    ![Képernyőkép, amely a szabály részletei képernyőjét jeleníti meg.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Szabály szerkesztése

A szabály szerkesztéséhez kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **szabályok** elemét.
   Megjelenik a szabályok ablak.
2. Válassza ki azt a szabályt, amelynek szerkeszteni kívánja.

    ![A kijelölt szabályt bemutató képernyőkép.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Válassza a **Szerkesztés** lehetőséget a művelet sávján, a **szabály szerkesztése** ablakban.

    ![A szabály szerkesztése képernyőt megjelenítő képernyőkép.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Módosítsa a **szabály nevét** és a **szabály leírását** , majd válasszon ki egy farmot a **Farm kiválasztása** legördülő menüből.
5. Adja meg a farm nevét, hogy kiválassza a farm és a **feltételek** ugyanabban az ablakban.  
6. A **feltételek** területen szerkessze a **mértéket**, az **operátort** és az **értéket**.
7. Írja be a mérték nevét a **mérték** legördülő menüben.
8. Válassza a **+ feltétel hozzáadása** lehetőséget a feltételek a szabályokhoz való hozzáadásához és szerkesztéséhez.

    ![Képernyőfelvétel: a feltétel hozzáadása gomb kiemelése.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Válassza ki a **súlyossági szintet**.  
10. Az **e-mail-** riasztások engedélyezéséhez kapcsolja be az **e-mailek engedélyezve** váltógomb gombot.
11. Szerkessze azokat az **e-mail-címeket** , amelyekre el szeretné küldeni az e-mail-riasztást, valamint az **e-mail tárgyát** és a **További megjegyzéseket**.  
12. A **szabály állapota** beállításnál a szabály engedélyezéséhez vagy letiltásához váltson az **engedélyezve** váltógomb gombra.
Megtekintheti a szabály által érintett eszközök számát.
13. A szabály szerkesztéséhez kattintson az **alkalmaz** gombra.

## <a name="change-rule-status"></a>Szabály állapotának módosítása

Egy szabály állapotának módosításához kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **szabályok** elemét. Megjelenik a szabályok ablak.
2. Válassza ki azt a szabályt, amelynek az állapotát módosítani kívánja.

    ![A Change Status (állapot módosítása) gombot megjelenítő képernyőkép](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Válassza az **Állapot módosítása** lehetőséget a műveleti sávon. Megjelenik az **Állapot módosítása** ablak.

    ![Képernyőkép, amely megjeleníti az állapot módosítása képernyőt.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Módosítsa a szabály állapotát az **Állapot módosítása** váltógomb használatával.
   Megtekintheti a szabály által érintett eszközök számát.
4. Válassza az **alkalmaz** lehetőséget a szabály állapotának módosításához.

## <a name="delete-rule"></a>Szabály törlése

Egy szabály törléséhez kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **szabályok** elemét. Megjelenik a szabályok ablak.
2. Válassza ki azt a szabályt, amelynek törölni kívánja.

    ![Képernyőfelvétel: a törlés gomb kiemelése.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Válassza a **Törlés** lehetőséget a műveleti sávon.

    ![A Project Farm veri](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Megjelenik a **szabály törlése** párbeszédpanel. Válassza a **Törlés** elemet.
