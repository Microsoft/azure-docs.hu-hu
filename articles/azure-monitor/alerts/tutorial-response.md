---
title: Eseményekre való válaszadás Azure Log Analytics-riasztásokkal | Microsoft Docs
description: Az oktatóanyag segítségével megismerheti, hogyan lehet elérni, hogy a Log Analytics-riasztások azonosítsák a munkaterület fontos információit, és proaktívan értesítsék Önt a problémákról, vagy műveleteket indítsanak el ezek megoldására.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: e790486758d6a5e3181df01f85e1213cca3fa12d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627768"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Eseményekre való válaszadás Azure Monitor-riasztásokkal
Az Azure Monitor-riasztások azonosítják a Log Analytics-adattárban található fontos információkat. A riasztásokat riasztási szabályok hozzák létre, amelyek rendszeres időközönként automatikusan naplókereséseket futtatnak. Ha a naplókeresés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztásbejegyzés, amelyet konfigurálni lehet egy automatikus válasz végrehajtására.  Ez az oktatóanyag a [Log Analytics-adatokat tartalmazó irányítópultok létrehozása és megosztása](../learn/tutorial-logs-dashboards.md) oktatóanyag folytatása.   

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Riasztási szabály létrehozása
> * Műveletcsoport konfigurálása e-mail-értesítés küldésére

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](../vm/quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-alerts"></a>Riasztások létrehozása
A riasztásokat riasztási szabályok hozzák létre az Azure Monitorban, és rendszeres időközönként automatikusan mentett lekérdezéseket vagy egyéni naplókereséseket futtathatnak.  Riasztásokat megadott teljesítménymetrikák alapján, illetve bizonyos események létrehozásakor vagy hiányakor hozhat létre, illetve akkor, ha egy adott időtartományon belül több esemény jön létre.  A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket, ha hiányzik egy frissítés, vagy ha létrejön egy esemény, amikor egy adott Windows-szolgáltatás vagy Linux-démon nem fut.  Ha a naplókeresés eredménye megfelel bizonyos feltételeknek, létrejön egy riasztás. A szabály ekkor automatikusan futtathat egy vagy több műveletet, például értesíti Önt a riasztásról, vagy meghív egy másik folyamatot. 

A következő példában létrehoz egy metrikamérési riasztási szabályt az [adatok vizualizációját ismertető oktatóanyagban](../learn/tutorial-logs-dashboards.md) mentett *Azure-beli virtuális gépek – Processzorhasználat* lekérdezés alapján.  Létrejön egy riasztás minden egyes, a 90%-os küszöbértéket meghaladó virtuális géphez.  

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Új riasztás létrehozásához a bal oldali panelen válassza a **Riasztások** elemet, majd kattintson az oldal tetején található **Új riasztási szabály** elemre.<br><br> ![Új riasztási szabály létrehozása](./media/tutorial-response/alert-rule-02.png)<br>
3. Az első lépésben a **Riasztás létrehozása** szakaszban ki fogja választani a Log Analytics-munkaterületet erőforrásként, mivel ez egy naplóalapú riasztási jelzés.  Az eredmények szűréséhez válassza ki az adott **előfizetést** a legördülő listából, ha több előfizetés is van, amely tartalmazza a korábban létrehozott virtuális gépet és Log Analytics-munkaterületet.  Szűrje az **erőforrástípusokat** a **Log Analytics** a legördülő menüből való kiválasztásával.  Végül válassza az **Erőforrás** **DefaultLAWorkspace** elemet, majd kattintson a **Kész** gombra.<br><br> ![Riasztás létrehozása, 1. lépés](./media/tutorial-response/alert-rule-03.png)<br>
4. A **Riasztási feltételek** szakaszban kattintson a **Feltétel hozzáadása** elemre a mentett lekérdezés kiválasztásához, majd adja meg a logikát, amelyet a riasztási szabály követ.  A **Jellogika konfigurálása** panelen válassza az *Azure-beli virtuális gépek – Processzorhasználat* elemet a listából.  A panel frissül, és a riasztás konfigurációs beállításait mutatja.  Felül a kiválasztott jel és a keresési lekérdezés eredményeit mutatja az elmúlt 30 percre vonatkozóan.  
5. Konfigurálja a riasztást az alábbi információkkal:  
   a. A legördülő lista **alapján** válassza a **metrika mértékét**.  A metrikamérés egy riasztást hoz létre a lekérdezés minden egyes objektumához, amelynek értéke meghaladja a megadott küszöbértéket.  
   b. A **Feltétel** értékeként válassza a **Nagyobb, mint** lehetőséget, majd adja meg a **Küszöbérték** mezőben a **90** értéket.  
   c. A Riasztás aktiválásának alapja részben válassza az **Egymás utáni incidensek** elemet, a legördülő listából válassza a **Nagyobb, mint** lehetőséget, és adja meg a 3 értéket.  
   d. Az Értékelés szakasz alapján részben módosítsa az **Időtartam** értékét **30** percre. A szabály öt percenként fog futni, és visszaadja azokat a rekordokat, amelyek az aktuális időhöz képest harminc percen belül jöttek létre.  Ha az időtartamot szélesebb időközre állítja, figyelembe veheti az esetleges adatkésést, és biztosíthatja, hogy a lekérdezés adatokat ad vissza, így nem kap téves negatív választ, ha a figyelmeztetés soha nem aktiválódik.  
6. Kattintson a **Kész** gombra a riasztási szabály létrehozásának befejezéséhez.<br><br> ![Riasztási jel konfigurálása](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. A második lépésben adja meg a riasztás nevét a **Riasztási szabály neve** mezőben, például **Percentage CPU greater than 90 percent**.  Adjon meg egy **Leírást**, ahol részletezi a riasztás tulajdonságait, majd a **Súlyosság** értéknél a megadott lehetőségek közül válassza a **Kritikus (0. súlyosság)** lehetőséget.<br><br> ![Riasztás részleteinek konfigurálása](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Ha a létrehozásakor azonnal aktiválni kívánja a riasztási szabályt, fogadja el a **Szabály engedélyezése a létrehozásakor** alapértelmezett értékét.
9. A harmadik és egyben végső lépésben adjon meg egy **műveletcsoportot**, amely biztosítja, hogy minden alkalommal, amikor egy riasztás aktiválódik ugyanazok a műveletek lesznek végrehajtva, és amely mindegyik meghatározott szabályra használható.  Hozzon létre egy új műveletcsoportot a következő információkkal:  
   a. Válassza az **Új műveletcsoport** lehetőséget, és megnyílik a **Műveletcsoport hozzáadása** panel.  
   b. A **Műveletcsoport neve** részben adjon meg egy nevet, például **IT Operations - Notify** és egy **Rövid nevet** például **itops-n**.  
   c. Ellenőrizze, hogy az alapértelmezett értékek helyesek-e az **Előfizetés** és az **Erőforráscsoport** mezőben. Ha nem, válassza ki a megfelelőt a legördülő listából.   
   d. A Műveletek részben adja meg a művelet nevét, például **E-mail küldése**, és a **Művelettípus** alatti legördülő menüben válassza az **E-mail/SMS/Leküldéses/Hang** lehetőséget. A jobb oldalon megnyílik az **E-mail/SMS/Leküldéses/Hang** tulajdonságait tartalmazó panel, amely további információkat biztosít.  
   e. Az **E-mail/SMS/Leküldéses/Hang** panelen engedélyezze az **e-mailt**, és adjon meg egy érvényes levelezési SMTP-címet az üzenet kézbesítéséhez.  
   f. Kattintson az **OK** gombra a módosítások mentéséhez.<br><br> 

    ![Új műveletcsoport létrehozása](./media/tutorial-response/action-group-properties-01.png)

10. A műveletcsoport elkészítéséhez kattintson az **OK** gombra. 
11. Kattintson a **Riasztási szabály létrehozás** gombra a riasztási szabály létrehozásának befejezéséhez. Azonnal el fog indulni.<br><br> ![Új riasztási szabály létrehozásának befejezése](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Riasztások megtekintése az Azure Portalon
Most, hogy létrehozott egy riasztást, egyetlen panelen tekintheti meg az Azure-riasztásokat és kezelheti az Azure-előfizetésekben lévő összes riasztást. Felsorolja az összes riasztási szabályt (engedélyezett vagy letiltott) és a cél erőforrások, erőforráscsoportok, szabálynév vagy állapot alapján rendezhető. Tartalmazza az összes aktivált riasztás, valamint az összes konfigurált/engedélyezett riasztási szabály összegzését.<br><br> ![Az Azure riasztások állapotát mutató oldala](./media/tutorial-response/azure-alerts-02.png)  

Amikor egy riasztás aktiválódik, a táblázatban megjelenik az állapot, és hogy a választott időtartományon belül (alapértelmezés szerint az elmúlt hat óra) hányszor aktiválódott.  A beérkezett üzenetek között megjelenik egy, a következő példához hasonló e-mail, amely a küszöbértéket megsértő virtuális gépet mutatja és a keresési lekérdezésnek megfelelő első néhány eredményt.<br><br> ![Minta riasztási e-mail művelet](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan képesek a riasztási szabályok proaktívan azonosítani a hibákat, valamint válaszolni rájuk, amikor az ütemezett naplókeresések futtatásakor az eredmények megfelelnek bizonyos feltételeknek.

Kövesse ezt a hivatkozást az előre felépített Log Analytics-szkriptminták megtekintéséhez.  

> [!div class="nextstepaction"]
> [Log Analytics-szkriptminták](../samples/powershell-samples.md)
