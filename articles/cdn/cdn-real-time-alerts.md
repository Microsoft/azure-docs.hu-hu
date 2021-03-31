---
title: Azure CDN valós idejű riasztások | Microsoft Docs
description: Valós idejű riasztások Microsoft Azure CDN-ben. A valós idejű riasztások a CDN-profilban található végpontok teljesítményével kapcsolatos értesítéseket biztosítanak.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887246"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Valós idejű riasztások Microsoft Azure CDN-ben
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum a Microsoft Azure CDN-ben való valós idejű riasztásokat ismerteti. Ez a funkció valós idejű értesítéseket biztosít a CDN-profilban található végpontok teljesítményéről.  Az e-mailek és a HTTP-riasztások az alábbiak alapján állíthatók be:

* Sávszélesség
* Állapotkódok
* Gyorsítótár állapota
* Kapcsolatok

## <a name="creating-a-real-time-alert"></a>Valós idejű riasztás létrehozása
1. A [Azure Portal](https://portal.azure.com)navigáljon a CDN-profilhoz.
   
    ![CDN-profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil kezelése gomb](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
3. Vigye az egérmutatót az **elemzés** lapra, majd vigye a kurzort a **valós idejű statisztika** menü fölé.  Kattintson a **valós idejű riasztások** elemre.
   
    ![CDN felügyeleti portál](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Megjelenik a meglévő riasztási konfigurációk (ha vannak) listája.
4. Kattintson a **riasztás hozzáadása** gombra.
   
    ![Riasztás hozzáadása gomb](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Megjelenik egy űrlap az új riasztás létrehozásához.
   
    ![Új riasztási űrlap](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Ha azt szeretné, hogy a riasztás aktív legyen, amikor a **Mentés** gombra kattint, jelölje be a **riasztás engedélyezve** jelölőnégyzetet.
6. Adjon meg egy leíró nevet a riasztáshoz a **név** mezőben.
7. Az **adathordozó típusa** legördülő menüben válassza a **http nagyméretű objektum** lehetőséget.
   
    ![Adathordozó típusa kiválasztva HTTP nagyméretű objektummal](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Az **adathordozó típusaként** a **nagyméretű http-objektumot** kell kiválasztania.  A **Azure CDN a Verizon** nem használja a többi lehetőséget.  A **http nagyméretű objektum** kiválasztásának sikertelensége miatt a riasztás soha nem aktiválódik.
   > 
   > 
8. Egy **metrika**, **operátor** és **trigger érték** kiválasztásával hozzon létre egy figyelni kívánt **kifejezést** .
   
   * A **metrika** mezőben válassza ki a figyelni kívánt feltétel típusát.  A **sávszélesség Mbps** értéke a megabit/másodpercben megadott sávszélesség-használat mennyisége.  Az **összes kapcsolat** a peremhálózati kiszolgálókkal létesített párhuzamos http-kapcsolatok száma.  A különböző gyorsítótár-állapotok és állapotkódok definícióit lásd: [Azure CDN gyorsítótár-állapotkódok](/previous-versions/azure/mt759237(v=azure.100)) és [Azure CDN HTTP-állapotkódok](/previous-versions/azure/mt759238(v=azure.100))
   * Az **operátor** a matematikai operátor, amely a metrika és az trigger érték közötti kapcsolatot hozza létre.
   * Az **trigger értéke** a küszöbérték, amelynek teljesülnie kell az értesítés elküldése előtt.
     
     A következő példában a létrehozott kifejezés azt jelzi, hogy a rendszer értesítést küld, ha a 404-es állapotkódok száma meghaladja a 25-ös értéket.
     
     ![Valós idejű riasztási minta kifejezés](./media/cdn-real-time-alerts/cdn-expression.png)
9. Az **intervallum** mezőben adja meg, hogy milyen gyakran szeretné kiértékelni a kifejezést.
10. Az **értesítés** legördülő menüben válassza ki, hogy Mikor szeretne értesítést kapni, ha a kifejezés igaz.
    
    * A **feltétel kezdete** azt jelzi, hogy a rendszer értesítést küld a megadott feltétel első észlelése esetén.
    * A **feltétel vége** azt jelzi, hogy a rendszer értesítést küld, ha a megadott feltétel már nem észlelhető. Ez az értesítés csak akkor indítható el, ha a hálózati figyelő rendszer azt észlelte, hogy a megadott feltétel történt.
    * A **folyamatos** érték azt jelzi, hogy a rendszer minden alkalommal küld értesítést, amikor a hálózati figyelőrendszer észleli a megadott feltételt. Ne feledje, hogy a hálózati figyelő rendszer csak egyszer ellenőrzi a megadott feltételt.
    * A **feltétel kezdete és vége** azt jelzi, hogy a rendszer értesítést küld a megadott feltételnek az első alkalommal, amikor a feltétel már nem észlelhető.
1. Ha e-mailben szeretne értesítéseket kapni, jelölje be az **értesítés e-** mailben jelölőnégyzetet.  
    
    ![Értesítés e-mail-űrlap alapján](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    A **címzett** mezőben adja meg azt az e-mail-címet, ahová el szeretné juttatni az értesítéseket. A **tulajdonos** és a **törzs** esetében meghagyhatja az alapértelmezett értéket, vagy testreszabhatja az üzenetet az **elérhető kulcsszavak** listával, hogy a riasztási adatai dinamikusan legyenek beszúrva az üzenet elküldésekor.
    
    > [!NOTE]
    > Az e-mail-értesítés teszteléséhez kattintson az **értesítés tesztelése** gombra, de csak a riasztás konfigurációjának mentése után.
    > 
    > 
12. Ha azt szeretné, hogy az értesítések webkiszolgálón legyenek közzétéve, jelölje be az **értesítés http-post** jelölőnégyzetet.
    
    ![Értesítés HTTP Post űrlap alapján](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Az **URL** mezőbe írja be azt az URL-címet, AHOVÁ a http-üzenetet közzé kívánja adni. A **fejlécek** szövegmezőbe írja be a kérelemben küldendő HTTP-fejléceket.  A **törzs** esetében az üzenet elküldésekor az **elérhető kulcsszavak** listával testreszabhatja az üzenetet.  A **fejlécek** és a **szövegtörzs** alapértelmezett értéke az alábbi példához hasonló XML-tartalom:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > A HTTP Post értesítés teszteléséhez kattintson az **értesítés tesztelése** gombra, de csak a riasztás konfigurációjának mentése után.
    > 
    > 
13. A riasztási konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.  Ha az 5. lépésben beállította a **riasztást** , a riasztás most aktív.

## <a name="next-steps"></a>Következő lépések
* [Valós idejű statisztikák elemzése Azure CDN](cdn-real-time-stats.md)
* Mélyebb kiásás a [speciális http-jelentésekkel](cdn-advanced-http-reports.md)
* [Használati minták](cdn-analyze-usage-patterns.md) elemzése

