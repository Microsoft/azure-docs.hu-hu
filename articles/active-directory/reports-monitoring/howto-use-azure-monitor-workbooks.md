---
title: Munkafüzetek Azure Monitor jelentésekhez | Microsoft Docs
description: Megtudhatja, hogyan használhatja Azure Monitor munkafüzeteket Azure Active Directory jelentésekhez.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588055"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor munkafüzetek használata Azure Active Directory jelentésekhez

> [!IMPORTANT]
> A munkafüzet alapjául szolgáló lekérdezések optimalizálásához kattintson a "szerkesztés" gombra, kattintson a Beállítások ikonra, és válassza ki azt a munkaterületet, ahol ezeket a lekérdezéseket futtatni kívánja. Alapértelmezés szerint a munkafüzetek minden olyan munkaterületet kijelölnek, ahol az Azure AD-naplókat irányítja. 

Kívánja:

- Megtudhatja, milyen hatással vannak a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) a felhasználói bejelentkezési élményre?

- A bejelentkezési hibák elhárításával jobban áttekintheti a szervezet bejelentkezési állapotát, és gyorsan megoldhatja a problémákat?

- Tudja, kik használják a örökölt hitelesítéseket a környezetbe való bejelentkezéshez? (Az [örökölt hitelesítés blokkolásával](../conditional-access/block-legacy-authentication.md)javíthatja a bérlők védelmét.)

- Ismernie kell a bérlőre vonatkozó feltételes hozzáférési szabályzatok hatását?

- Szeretné áttekinteni a következőt: bejelentkezési naplók, a munkafüzet azt jelenti, hogy hány felhasználó adta meg vagy tagadta meg a hozzáférést, valamint hogy hány felhasználó megkerüli a feltételes hozzáférési szabályzatokat az erőforrásokhoz való hozzáféréskor?

- Az alábbiak mélyebb megismerését szeretnék megismerni: a munkafüzet részleteit/feltételeit, hogy a szabályzatok milyen hatással lehetnek az egyes feltételekre, beleértve az eszköz platformját, az eszköz állapotát, az ügyfélalkalmazás, a bejelentkezési kockázat, a hely és az alkalmazás?

- Mélyebb betekintést nyerhet a bejelentkezési naplókba, a munkafüzet azt jelenti, hogy hány felhasználó adta meg vagy tagadta meg a hozzáférést, valamint hogy hány felhasználó megkerülte a feltételes hozzáférési szabályzatokat az erőforrások elérésekor.

- A kérdések megválaszolásához Azure Active Directory a munkafüzeteket biztosít a figyeléshez. A [Azure monitor munkafüzetek](../../azure-monitor/visualize/workbooks-overview.md) gazdag interaktív jelentésekben egyesítik a szöveges, elemzési és mérőszámokat, valamint a paramétereket.



A cikk tartalma:

- Feltételezi, hogy tisztában van azzal, hogyan [hozhat létre interaktív jelentéseket a figyelő munkafüzetek használatával](../../azure-monitor/visualize/workbooks-overview.md).

- A cikk azt ismerteti, hogyan használhatók a figyelő munkafüzetek a feltételes hozzáférési szabályzatok hatásának megértéséhez, a bejelentkezési hibák elhárításához és a régi hitelesítések azonosításához.
 


## <a name="prerequisites"></a>Előfeltételek

A figyelő munkafüzetek használatához a következőkre lesz szüksége:

- Egy Azure Active Directory bérlő prémium szintű (P1 vagy P2) licenccel. Megtudhatja, hogyan [szerezhet be prémium szintű licencet](../fundamentals/active-directory-get-started-premium.md).

- Egy [Log Analytics-munkaterület](../../azure-monitor/logs/quick-create-workspace.md).

- [Hozzáférés](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) a log Analytics-munkaterülethez
- A következő szerepkörök a Azure Active Directoryban (ha a Log Analytics a Azure Active Directory portálon keresztül éri el)
    - Biztonsági rendszergazda
    - Biztonsági olvasó
    - Jelentés olvasója
    - Globális rendszergazda

## <a name="roles"></a>Szerepkörök
A következő szerepkörök egyikének kell lennie, valamint hozzáféréssel kell rendelkeznie az [alapul szolgáló log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) munkaterülethez a munkafüzetek kezeléséhez:
-   Globális rendszergazda
-   Biztonsági rendszergazda
-   Biztonsági olvasó
-   Jelentés olvasója
-   Alkalmazás-rendszergazda

## <a name="workbook-access"></a>Munkafüzet-hozzáférés 

A munkafüzetek eléréséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon **Azure Active Directory**  >  **figyelési**  >  **munkafüzetekbe**. 

1. Válasszon ki egy jelentést vagy sablont, vagy kattintson a **Megnyitás** gombra az eszköztáron. 

![A Azure Monitor-munkafüzetek keresése az Azure AD-ben](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Bejelentkezési elemzés

A bejelentkezési elemzési munkafüzet eléréséhez a **használat** szakaszban válassza a **bejelentkezések** lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Siker

- Függőben lévő felhasználói művelet

- Hiba

Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezési elemzés](./media/howto-use-azure-monitor-workbooks/43.png)


Minden egyes trend esetében a következő kategóriák szerinti részletezést kapja:

- Hely

    ![Bejelentkezések hely szerint](./media/howto-use-azure-monitor-workbooks/45.png)

- Eszköz

    ![Bejelentkezések eszköz szerint](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Korábbi hitelesítéssel történő bejelentkezések 


Ha a munkafüzetet [örökölt hitelesítést](../conditional-access/block-legacy-authentication.md)használó bejelentkezésekhez szeretné elérni, a **használat** szakaszban válassza a **Bejelentkezés örökölt hitelesítés használatával** lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Siker


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

- Protokollok

![Bejelentkezések örökölt hitelesítéssel](./media/howto-use-azure-monitor-workbooks/47.png)


Az egyes trendek esetében az alkalmazás és a protokoll részletezést kap.

![Örökölt hitelesítési bejelentkezések alkalmazás és protokoll szerint](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Bejelentkezések feltételes hozzáféréssel 


Ha a munkafüzetet a [feltételes hozzáférési házirendek](../conditional-access/overview.md)alapján szeretné elérni a bejelentkezésekhez, a **feltételes hozzáférés** szakaszban válassza a **bejelentkezések feltételes hozzáférés alapján** lehetőséget. 

Ez a munkafüzet a letiltott bejelentkezések trendjét jeleníti meg. Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezés feltételes hozzáféréssel](./media/howto-use-azure-monitor-workbooks/49.png)


A letiltott bejelentkezések esetében a feltételes hozzáférési állapot szerinti részletezést kap.

![A képernyőképen a feltételes hozzáférés állapota és a legutóbbi bejelentkezések láthatók.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Feltételes hozzáférés – Insights

### <a name="overview"></a>Áttekintés

A munkafüzetek olyan bejelentkezési naplózási lekérdezéseket tartalmaznak, amelyek segítségével a rendszergazdák megfigyelheti a feltételes hozzáférési házirendek hatását a bérlőn. Azt is megteheti, hogy hány felhasználó adta meg vagy tagadta meg a hozzáférést. A munkafüzet azt mutatja be, hogy hány felhasználónál lehet megkerülni a feltételes hozzáférési szabályzatokat a bejelentkezés időpontjában a felhasználók attribútumai alapján. A szolgáltatás minden feltétel részleteit tartalmazza, hogy a szabályzatok milyen hatással lehetnek a feltételekre, beleértve az eszköz platformját, az eszköz állapotát, az ügyfélalkalmazás, a bejelentkezési kockázat, a hely és az alkalmazás állapotát.

### <a name="instructions"></a>Utasítások 
Ha a munkafüzetet a feltételes hozzáférési információhoz szeretné elérni, válassza a feltételes hozzáférésről szóló szakaszban található **feltételes hozzáférési információ** munkafüzetet. Ez a munkafüzet az egyes feltételes hozzáférési házirendek várható hatását mutatja be a bérlőben. Válasszon ki egy vagy több feltételes hozzáférési szabályzatot a legördülő listából, és szűkítse a munkafüzet hatókörét a következő szűrők alkalmazásával: 

- **Időtartomány**

- **Felhasználó**

- **Alkalmazások**

- **Adatnézet**

![Képernyőfelvétel: a feltételes hozzáférés panel, ahol kiválaszthat egy feltételes hozzáférési szabályzatot.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


A hatás összegzése azon felhasználók vagy bejelentkezések számát jeleníti meg, amelyekhez a kiválasztott szabályzatok adott eredménnyel rendelkeztek. Az összeg azon felhasználók vagy bejelentkezések száma, amelyekhez a kiválasztott szabályzatokat kiértékelték a kiválasztott időtartományban. Kattintson egy csempére a munkafüzetben lévő adatértékek szűréséhez. 

![A képernyőképen az eredmények, például a teljes, a sikeres és a sikertelen műveletek szűréséhez használható csempék láthatók.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Ez a munkafüzet azt is mutatja, hogy a kiválasztott házirendek milyen hatással vannak az egyes hat feltételek szerinti bontásra: 
- **Eszköz állapota**
- **Eszköz platformja**
- **Ügyfélalkalmazások**
- **Bejelentkezési kockázat**
- **Hely**
- **Alkalmazások**

![A képernyőkép a teljes bejelentkezési szűrő részleteit jeleníti meg.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Egyéni bejelentkezéseket is megvizsgálhat, a munkafüzetben kiválasztott paraméterek szerint szűrve. Kereshet egyéni felhasználókat, a bejelentkezési gyakoriság szerint rendezve, és megtekintheti a hozzájuk tartozó bejelentkezési eseményeket. 

![Képernyőfelvétel: az egyéni bejelentkezések áttekinthetők.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Bejelentkezések engedélyezési vezérlőkkel

Ha [vezérlőket ad](../conditional-access/controls.md)meg a bejelentkezéshez, a **feltételes hozzáférés** szakaszban válassza a **bejelentkezések engedélyezése vezérlők alapján** lehetőséget. 

Ez a munkafüzet a következő letiltott bejelentkezési trendeket mutatja:

- MFA megkövetelése
 
- Használati feltételek megkövetelése

- Adatvédelmi nyilatkozat szükséges

- Egyéb


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések engedélyezési vezérlőkkel](./media/howto-use-azure-monitor-workbooks/50.png)


Az egyes trendek esetében az alkalmazás és a protokoll részletezést kap.

![Legutóbbi bejelentkezések részletezése](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Sikertelen bejelentkezések elemzése

A **bejelentkezések sikertelen elemzése** munkafüzettel a következő hibákkal lehet elhárítani a hibákat:

- Bejelentkezések
- Feltételes hozzáférési szabályzatok
- Örökölt hitelesítés 


A bejelentkezés feltételes hozzáférési adatai alapján való eléréséhez a **hibakeresés** szakaszban válassza az **örökölt hitelesítés használatával történő bejelentkezések** lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Siker

- Függőben lévő művelet

- Hiba


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések hibaelhárítása](./media/howto-use-azure-monitor-workbooks/52.png)


Ha segítségre van szüksége a bejelentkezések hibakereséséhez, Azure Monitor a következő kategóriák szerinti bontást biztosít:

- Leggyakoribb hibák

    ![Leggyakoribb hibák összefoglalása](./media/howto-use-azure-monitor-workbooks/53.png)

- Felhasználói műveletre váró bejelentkezések

    ![Felhasználói műveletre váró bejelentkezések összefoglalása](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Következő lépések

[Interaktív jelentéseket hozhat létre a munkafüzetek figyelése használatával](../../azure-monitor/visualize/workbooks-overview.md).