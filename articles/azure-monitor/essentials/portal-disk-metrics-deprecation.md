---
title: A lemez metrikáinak elavulása a Azure Portalban | Microsoft Docs
description: Megtudhatja, hogy mely lemez-metrikák elavultak, és hogyan frissítheti metrikus riasztásait új metrikák használatára.
services: azure-monitor
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 607ccacbfff86decaecaad0a869c8c0fe7500680
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033401"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Lemez metrikáinak elavulása a Azure Portalban

Az elavult lemezekkel kapcsolatos metrikák hamarosan el lesznek távolítva a Azure Portalból. Az összes elavult metrika új verziója használható. Ebből a cikkből megtudhatja, hogy mely mérőszámok újak, és hogyan frissítheti a metrikai riasztásokat a használatára.

## <a name="list-of-new-metrics"></a>Új metrikák listája

Ez a táblázat minden elavult mérőszámot leképez a hozzá tartozó új metrikára. 

|Elavult metrika|Új (csere) metrika|
|----|----|
|Adatlemez QD (elavult)|Adatlemez-várólista mélysége (előzetes verzió)|
|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|
|Adatlemez olvasási műveletei (művelet/mp) (elavult)|Adatlemez olvasási művelete/mp (előzetes verzió)|
|Adatlemez-írási sebesség (bájt/s) (elavult)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|
|Adatlemez írási műveletei (művelet/mp) (elavult)|Adatlemez írási műveletei/mp (előzetes verzió)|
|Operációs rendszer QD (elavult)|Operációs rendszer várólistájának mélysége (előzetes verzió)|
|Operációs rendszer olvasási sebessége (bájt/s) (elavult)|Operációs rendszer olvasási sebessége (bájt/s) (előzetes verzió)|
|Operációs rendszer olvasási műveletei/mp (elavult)|Operációs rendszer olvasási műveletei/mp (előzetes verzió)|
|Operációs rendszer írási sebessége (bájt/s) (elavult)|Operációs rendszer írási sebessége (bájt/s) (előzetes verzió)|
|Operációs rendszer írási műveletei/mp (elavult)|Operációs rendszer írási műveletei/mp (előzetes verzió)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Metrikák áttelepíthetők a metrikus riasztásokban

A metrikai riasztások frissítése új metrikák használatára.

1. A Azure Portalban keressen **riasztásokat**. Ezután a **szolgáltatások** szakaszban válassza a **riasztások** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Riasztási szolgáltatás](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. A **riasztások** lapon kattintson a **riasztási szabályok kezelése** gombra. 

   > [!div class="mx-imgBorder"]
   > ![Riasztási szabályok kezelése](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Az **erőforráscsoport** legördülő listában jelölje be a **Virtual Machines** jelölőnégyzetet, majd a **jel típusa** legördülő listában válassza a **metrikák** jelölőnégyzetet. 

   > [!div class="mx-imgBorder"]
   > ![Riasztások szűrése](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. A metrikák listájában azonosítsa a lemezekhez kapcsolódó feltételeket. Kattintson a szabály nevére. 

   A név hiperhivatkozásként jelenik meg a tábla **Name (név** ) oszlopában.

   > [!div class="mx-imgBorder"]
   > ![Lemezterület-feltételek keresése](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. A **szabályok kezelése** lap **feltételek** szakaszában kattintson a riasztás feltételére. 

   A feltétel hiperhivatkozásként jelenik meg.  

   > [!div class="mx-imgBorder"]
   > ![Feltételek igazítása](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Megjelenik a **jel logikai beállítása** lap, és a feltétel beállításai megjelennek az oldal **riasztás logikája** szakaszában.

6. Jegyezze fel ezeket a beállításokat, mert eltűnik az elavult metrika eltávolításakor.

   > [!div class="mx-imgBorder"]
   > ![Feltétel szabályai](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Vegye figyelembe ezeket a beállításokat egy képernyőfelvételen vagy egy szövegfájlban. 

7. Kattintson a **vissza a jelzésre kiválasztási** hivatkozásra.

   > [!div class="mx-imgBorder"]
   > ![Vissza a jel kijelöléséhez](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. A **jel logikai beállítása** lapon válassza ki a megfelelő helyettesítő metrikát (új metrika). A cikk korábbi részében megjelenő [táblázat](#update-metrics) segítségével azonosíthatja az új metrika nevét.

   > [!TIP] 
   > Kezdjen el gépelni a keresési sávban, hogy szűkítse a metrikák neveinek listáját. 

   > [!div class="mx-imgBorder"]
   > ![Új metrika kiválasztása](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Válassza a **kész** gombot. 

   > [!div class="mx-imgBorder"]
   > ![Új metrika beállítása](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Véglegesítse a módosításokat a **Save (Mentés** ) gomb kiválasztásával. 

    > [!div class="mx-imgBorder"]
    > ![Új metrika mentése](./media/portal-disk-metrics-deprecation/save-new-metric.png)






