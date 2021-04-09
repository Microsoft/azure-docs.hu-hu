---
title: Feltételes hozzáférési ismeretek és jelentéskészítési munkafüzet – Azure Active Directory
description: Az Azure AD feltételes hozzáférési információinak és jelentéskészítési munkafüzetének használata a házirendek hibakereséséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae802038626a1fbf8d533800a0b8eb43c4565e8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574170"
---
# <a name="conditional-access-insights-and-reporting"></a>Feltételes hozzáférési ismeretek és jelentéskészítés

A feltételes hozzáférési ismeretek és a jelentéskészítési munkafüzetek lehetővé teszik a feltételes hozzáférési házirendek hatásának megismerését a szervezetben az idő múlásával. A bejelentkezés során egy vagy több feltételes hozzáférési szabályzat is érvényes lehet, ha bizonyos engedélyezési vezérlők teljesülnek, vagy ellenkező esetben megtagadják a hozzáférést. Mivel az egyes bejelentkezések során több feltételes hozzáférési szabályzat is kiértékelhető, az elemzések és a jelentéskészítési munkafüzetek lehetővé teszik az egyes házirendek vagy az összes házirend egy részhalmaza hatásának vizsgálatát.  

## <a name="prerequisites"></a>Előfeltételek

Az elemzések és a jelentéskészítési munkafüzetek engedélyezéséhez a bérlőnek Log Analytics munkaterülettel kell rendelkeznie a bejelentkezési naplók adatai megőrzéséhez. A feltételes hozzáférés használatához a felhasználóknak prémium szintű Azure AD P1 vagy P2 licenccel kell rendelkezniük.

A következő szerepkörök hozzáférhetnek az információhoz és a jelentéskészítéshez:  

- Feltételes hozzáférésű rendszergazda 
- Biztonsági olvasó 
- Biztonsági rendszergazda 
- Globális olvasó 
- Globális rendszergazda 

A felhasználóknak az alábbi Log Analytics munkaterület-szerepkörök egyikére is szükségük van:  

- Közreműködő  
- Tulajdonos 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Az Azure AD-ből Azure Monitor naplókba küldött stream-bejelentkezési naplók 

Ha nem integrálta az Azure AD-naplókat Azure Monitor naplókkal, akkor a munkafüzet betöltése előtt a következő lépéseket kell elvégeznie:  

1. [Hozzon létre egy log Analytics munkaterületet a Azure monitorban](../../azure-monitor/logs/quick-create-workspace.md).
1. [Azure ad-naplók integrálása Azure monitor naplókkal](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Működés 

Az adatfelismerések és a jelentéskészítési munkafüzet elérése:  

1. Jelentkezzen be az **Azure Portalra**.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférési**  >  **ismereteket és jelentéskészítést**.

### <a name="get-started-select-parameters"></a>Első lépések: paraméterek kiválasztása 

Az információk és jelentések irányítópultja lehetővé teszi egy vagy több feltételes hozzáférési szabályzat hatásának megadását egy adott időszakra vonatkozóan. Először állítsa be a munkafüzet felső részén található paramétereket. 

![A feltételes hozzáférési ismeretek és jelentések irányítópultja a Azure Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Feltételes hozzáférési házirend**: válasszon ki egy vagy több feltételes hozzáférési szabályzatot az együttes hatás megtekintéséhez. A szabályzatok két csoportba vannak elkülönítve: engedélyezve és csak jelentési szabályzatok. Alapértelmezés szerint az összes engedélyezett házirend van kiválasztva. Ezek az engedélyezett házirendek a bérlőben jelenleg érvényben lévő szabályzatok.  

**Időtartomány**: válasszon ki egy időtartományt 4 óra és 90 nap közötti időtartamra. Ha az Azure AD-naplók Azure Monitor-val való integrálásakor az időtartomány továbbra is fennáll, akkor csak az integráció időpontja után jelentkeznek be bejelentkezések.  

**Felhasználó**: alapértelmezés szerint az irányítópulton látható, hogy milyen hatással van a kiválasztott szabályzatok az összes felhasználóra. Ha egyéni felhasználó alapján szeretne szűrni, írja be a felhasználó nevét a szövegmezőbe. Az összes felhasználó szűréséhez írja be a "minden felhasználó" szöveget a szövegmezőbe, vagy hagyja üresen a paramétert. 

**Alkalmazás**: alapértelmezés szerint az irányítópulton látható, hogy milyen hatással van a kiválasztott szabályzatok az összes alkalmazásra. Ha egy adott alkalmazás alapján szeretne szűrni, írja be az alkalmazás nevét a szövegmezőbe. Az összes alkalmazás szűréséhez írja be a "minden alkalmazás" szöveget a szövegmezőbe, vagy hagyja üresen a paramétert. 

**Adatnézet**: válassza ki, hogy szeretné-e, hogy az irányítópulton megjelenjenek az eredmények a felhasználók vagy a bejelentkezések száma alapján. Előfordulhat, hogy az egyes felhasználók több száz bejelentkezéssel rendelkeznek számos különböző eredményű alkalmazáshoz egy adott időtartományban. Ha a felhasználók számára az adatnézetet választja, akkor a sikeres és a sikertelen műveletek is megadhatók (például ha 10 felhasználó van, 8 közülük a sikeres volt az elmúlt 30 napban, és 9 közülük az elmúlt 30 napban sikertelen volt).

## <a name="impact-summary"></a>Hatás összegzése 

A paraméterek beállítása után a hatás összegzése betöltődik. Az összefoglalás azt mutatja, hogy az időtartomány hány felhasználót vagy bejelentkezést eredményezett "sikeres", "hiba", "felhasználói beavatkozás szükséges" vagy "nincs alkalmazva" értékre a kiválasztott szabályzatok kiértékelése során.  

![Összefoglalás a feltételes hozzáférés munkafüzetében](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Összesen**: azon felhasználók vagy bejelentkezések száma az időszak során, amikor a kiválasztott szabályzatok közül legalább az egyik ki lett értékelve.

**Sikeres**: a felhasználók vagy bejelentkezések száma az időszak alatt, ahol a kiválasztott szabályzatok együttes eredménye "sikeres" vagy "csak jelentés: sikeres".

**Hiba**: az időszak során a felhasználók vagy a bejelentkezések száma az adott időszakban, amikor a kiválasztott házirendek közül legalább az egyik eredménye "hiba" vagy "csak jelentés: hiba".

**Szükséges felhasználói beavatkozás**: azon felhasználók vagy bejelentkezések száma az adott időszakban, ahol a kiválasztott házirendek összesített eredménye "csak jelentés: felhasználói beavatkozás szükséges". A felhasználói beavatkozásra akkor van szükség, ha egy interaktív engedélyezési vezérlő, például a többtényezős hitelesítés szükséges egy jelentésre vonatkozó feltételes hozzáférési szabályzathoz. Mivel az interaktív engedélyezési vezérlőket nem kényszeríti ki a csak jelentési házirendek, a sikeres vagy sikertelen művelet nem határozható meg.  

**Nincs alkalmazva**: a felhasználók vagy bejelentkezések száma az időszak alatt, ahol a kiválasztott szabályzatok egyike sincs alkalmazva.

### <a name="understanding-the-impact"></a>A hatás megértése 

![Munkafüzet bontása feltétel és állapot szerint](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Megtekintheti az egyes feltételekhez tartozó felhasználók vagy bejelentkezések részletezését. Egy adott eredmény (például sikeres vagy sikertelen) bejelentkezési adatainak szűréséhez válassza ki a munkafüzet tetején található összefoglaló csempéket. A bejelentkezések részletezését az egyes feltételes hozzáférési feltételeknél tekintheti meg: eszköz állapot, eszköz platform, ügyfélalkalmazás, hely, alkalmazás és bejelentkezési kockázat.  

## <a name="sign-in-details"></a>Bejelentkezési részletek 

![Munkafüzet-bejelentkezés részletei](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Egy adott felhasználó bejelentkezéseit is megvizsgálhatja az irányítópult alján található bejelentkezések keresésével. A bal oldali lekérdezés a leggyakoribb felhasználókat jeleníti meg. A felhasználó kiválasztásával a jobb oldalon szűrheti a lekérdezést.  

> [!NOTE]
> A bejelentkezési naplók letöltésekor válassza a JSON-formátum lehetőséget a feltételes hozzáférés jelentési eredményeinek befoglalásához.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Feltételes hozzáférési szabályzat konfigurálása csak jelentési módban

Feltételes hozzáférési szabályzat konfigurálása csak jelentési módban:

1. Jelentkezzen be a **Azure Portalba** feltételes hozzáférési rendszergazdaként, biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válasszon ki egy meglévő szabályzatot, vagy hozzon létre egy új szabályzatot.
1. A **házirend engedélyezése** beállításnál állítsa be a váltás **csak jelentés** módba beállítást.
1. Válassza a **Mentés** lehetőséget

> [!TIP]
> A meglévő szabályzatok engedélyezési **házirendjét** **a** (z) rendszerből a **jelentésbe** való szerkesztésével letilthatja a meglévő szabályzatok betartatását. 

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Miért sikertelenek a lekérdezések az engedélyek hibája miatt?

A munkafüzet eléréséhez szükség van a megfelelő Azure AD-engedélyekre, valamint Log Analytics munkaterület engedélyeire. Egy minta log Analytics-lekérdezés futtatásával tesztelheti, hogy rendelkezik-e a megfelelő munkaterület-engedélyekkel:

1. Jelentkezzen be az **Azure Portalra**.
1. Tallózással keresse meg **Azure Active Directory**  >  **naplókat**.
1. Írja be `SigninLogs` a lekérdezés mezőbe, és válassza a **Futtatás** lehetőséget.
1. Ha a lekérdezés nem ad vissza semmilyen eredményt, előfordulhat, hogy a munkaterülete helytelenül van konfigurálva. 

![Sikertelen lekérdezések hibáinak megoldása](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Az Azure AD bejelentkezési naplófájljainak Log Analytics munkaterületre való továbbításáról további információt az [Azure ad-naplók integrálása Azure monitor naplókkal](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)című cikkben talál.

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Miért nem sikerül a lekérdezés a munkafüzetben?

Az ügyfelek észrevették, hogy a lekérdezések néha meghiúsulnak, ha a hibás vagy több munkaterület van társítva a munkafüzethez. A probléma megoldásához kattintson a munkafüzet tetején található **Szerkesztés** gombra, majd a beállítások fogaskerékre. Válassza ki, majd távolítsa el a munkafüzethez nem társított munkaterületeket. Az egyes munkafüzetekhez csak egy munkaterület társítható.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Miért üres a feltételes hozzáférési házirendek paraméter?

A szabályzatok listáját a legújabb bejelentkezési eseményre kiértékelt szabályzatok alapján hozza létre a rendszer. Ha a bérlő nem rendelkezik újabb bejelentkezésekkel, előfordulhat, hogy néhány percet várnia kell, amíg a munkafüzet betölti a feltételes hozzáférési szabályzatok listáját. Ez közvetlenül a Log Analytics konfigurálása után fordulhat elő, vagy ha a bérlő nem rendelkezik közelmúltbeli bejelentkezési tevékenységgel.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Miért hosszú időt vesz a munkafüzet betöltése?  

A kiválasztott időtartománytól és a bérlő méretétől függően előfordulhat, hogy a munkafüzet rendkívül nagy számú bejelentkezési eseményt értékel ki. A nagyméretű bérlők esetében a bejelentkezések mennyisége túllépheti Log Analytics lekérdezési kapacitását. Próbálja meg lerövidíteni az időtartományt 4 óráig, hogy ellenőrizze, hogy a munkafüzet betöltődik-e.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Néhány perc betöltését követően a munkafüzet nulla eredményt ad vissza? 

Ha a bejelentkezések mennyisége meghaladja Log Analytics lekérdezési kapacitását, a munkafüzet nulla eredményt ad vissza. Próbálja meg lerövidíteni az időtartományt 4 óráig, hogy ellenőrizze, hogy a munkafüzet betöltődik-e.  

### <a name="can-i-save-my-parameter-selections"></a>Menthetem a paraméter-beállításokat?  

A paraméterek kijelölését a munkafüzet tetején is mentheti, ha a   >  **munkafüzetek**  >  **feltételes hozzáférési elemzéseket és jelentéskészítést** Azure Active Directory. Itt megtalálhatja a munkafüzet sablonját, ahol szerkesztheti a munkafüzetet, és menthet egy másolatot a munkaterületre, beleértve a paraméterek kiválasztását is a **saját jelentések** vagy a **megosztott jelentések** területen. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Szerkeszthetem és testre is szabhatom a munkafüzetet további lekérdezésekkel? 

A munkafüzetet szerkesztheti és testreszabhatja úgy, hogy **Azure Active Directory**  >  **munkafüzetek**  >  **feltételes hozzáférési információit és jelentéseit**. Itt megtalálhatja a munkafüzet sablonját, ahol szerkesztheti a munkafüzetet, és menthet egy másolatot a munkaterületre, beleértve a paraméterek kiválasztását is a **saját jelentések** vagy a **megosztott jelentések** területen. A lekérdezések szerkesztésének megkezdéséhez kattintson a **Szerkesztés** gombra a munkafüzet tetején.  
 
## <a name="next-steps"></a>Következő lépések

- [Csak feltételes hozzáférésű jelentés üzemmódja](concept-conditional-access-report-only.md)

- További információ az Azure AD-munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)
