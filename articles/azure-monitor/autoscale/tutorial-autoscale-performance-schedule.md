---
title: Azure-erőforrások méretezése az adatmennyiség vagy az ütemterv alapján
description: Automatikus méretezési beállítás létrehozása egy App Service-csomaghoz metrikai adatok és egy ütemezés használatával
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627781"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Automatikus méretezési beállítás Azure-erőforrásokhoz teljesítményadatok vagy ütemezés alapján

Az automatikus méretezés lehetővé teszi szolgáltatáspéldányok előre beállított feltételek szerinti hozzáadását vagy eltávolítását. Ezek a beállítások a portálon keresztül hozhatók létre. Ez a metódus egy böngészőalapú felhasználói felületet biztosít az automatikus méretezési beállítások létrehozásához és konfigurálásához. 

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani: 
> [!div class="checklist"]
> * Webalkalmazás és App Service-csomag létrehozása
> * Automatikus méretezési szabályok egy webalkalmazás fogadott kéréseinek száma alapján történő konfigurálása horizontális le- és felskálázáshoz
> * Horizontális felskálázási művelet kiváltása és a példányszám növekedésének megfigyelése
> * Horizontális leskálázási művelet kiváltása és a példányszám csökkenésének megfigyelése
> * Az erőforrások törlése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Webalkalmazás és App Service-csomag létrehozása
1. Kattintson az **Erőforrás létrehozása** lehetőségre a bal oldali navigációs ablaktáblán.
2. Keresse meg és válassza ki a *Webalkalmazás* elemet, és kattintson a **Létrehozás** elemre.
3. Adja meg az alkalmazás nevét, például: *MyTestScaleWebApp*. Hozzon létre egy új erőforráscsoportot * myResourceGroup, vagy helyezze egy kiválasztott erőforráscsoport-csoportba.

A rendszer néhány percen belül üzembe helyezi az erőforrásokat. Az oktatóanyag fennmaradó részében használja a webalkalmazást és a vonatkozó App Service-csomagot.

   ![Új alkalmazás létrehozása a portálon](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Az automatikus méretezési beállítások megnyitása
1. Kattintson a bal oldali navigációs ablaktáblán a **Figyelés** eleme. Ha az oldal betöltött, kattintson az **Automatikus skálázás** lapra.
2. Itt találja az előfizetéséhez tartozó azon erőforrások listáját, amelyek támogatják az automatikus méretezést. Keresse meg az oktatóanyag korábbi részében létrehozott App Service-csomagot, és kattintson rá.

    ![Képernyőfelvétel: a Azure Portal a figyelővel, majd az autoscale kiválasztva.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Az automatikus skálázáshoz kattintson az **Automatikus skálázás engedélyezése** gombra.

A következő néhány lépés segít kitölteni az automatikus skálázási képernyőt, hogy az a következőképp nézzen ki:

   ![Automatikus méretezési beállítás mentése](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Alapértelmezett profil konfigurálása
1. Adjon egy **nevet** az automatikus skálázási beállításnak.
2. Az alapértelmezett profilban a **Skálázási mód** legyen „Skálázás adott példányszámra”.
3. Állítsa a példányszámot **1**-re. Ez a beállítás biztosítja, hogy ha nincs másik aktív vagy működő profil, akkor az alapértelmezett profil példányszáma visszaáll 1-re.

   ![Képernyőfelvétel: az autoskálázási beállítás lapja, amely a beállításhoz megadott névvel rendelkezik.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Ismétlődési profil létrehozása

1. Kattintson az alapértelmezett profil alatt a **Skálázási feltétel hozzáadása** hivatkozásra.

2. Szerkessze a profil **nevét**, amely legyen: „Hétfőtől péntekig profil”.

3. A **Skálázási mód** legyen „Skálázás metrika alapján”.

4. A **Példányszámkorlátoknál** állítsa a **Minimum** értéket „1”-re, a **Maximum** értéket „2”-re, az **Alapértelmezett** értéket pedig szintén „1”-re. Ez a beállítás biztosítja, hogy a profil nem fogja a szolgáltatási csomagot automatikusan 1 példány alá és 2 példány fölé skálázni. Ha a profilnál nincs elég adat megadva a döntéshozáshoz, akkor a rendszer az alapértelmezett példányszámot használja (ami jelen esetben 1).

5. Az **Ütemezésnél** válassza az „Adott napokon ismétlődik” lehetőséget.

6. Állítsa be a profilt, hogy hétfőtől péntekig, minden nap 09:00 és 18:00 között ismétlődjön. Ez a beállítás biztosítja, hogy a profil csak hétfőtől péntekig reggel 9 és délután 6 között legyen aktív és alkalmazható. Minden ezen kívüli időpontban az automatikus beállítás az „Alapértelmezett” profilt használja.

## <a name="create-a-scale-out-rule"></a>Felskálázási szabály létrehozása

1. A „Hétfőtől péntekig profil” alatt.

2. Kattintson a **Szabály hozzáadása** hivatkozásra.

3. Állítsa be a **Metrikaforrásnál** az „Egyéb erőforrás” lehetőséget. Állítsa be az **Erőforrás típusánál** az „App Services” lehetőséget, az **Erőforrásnál** pedig az oktatóanyag során korábban létrehozott webalkalmazást.

4. Az **Idő összesítése** értéke legyen „Összes”, a **Metrika nevénél** „Kérések”, az **Időfelbontási szint statisztikája** pedig „Összesen”.

5. Az **Operátor** legyen „Nagyobb, mint”, a **Küszöbérték** legyen „10”, az **Időtartam** pedig legyen „5 perc”.

6. A kiválasztott művelet legyen **„Mennyiség növelése a következővel”**, a **Példányszám** legyen „1”, a **Várakozási idő** pedig „5” perc.

7. Kattintson a **Hozzáadás** gombra.

Ez a szabály biztosítja, hogy ha a webalkalmazás 5 perc vagy annál rövidebb idő alatt több mint 10 kérést kap, akkor a rendszer hozzáad egy további példányt az App Service-csomaghoz a terhelés kezelése végett.

   ![Felskálázási szabály létrehozása](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Horizontális leskálázási szabály létrehozása
Javasoljuk, hogy a felskálázási szabályok mellett mindig legyen beállítva egy leskálázási szabály is. Ezzel biztosítható, hogy ne legyen túl sok erőforrás üzembe helyezve. Ez azt jelenti, hogy ne fusson több példány, mint amennyi az aktuális terhelés kezeléséhez szükséges. 

1. A „Hétfőtől péntekig profil” alatt.

2. Kattintson a **Szabály hozzáadása** hivatkozásra.

3. Állítsa be a **Metrikaforrásnál** az „Egyéb erőforrás” lehetőséget. Állítsa be az **Erőforrás típusánál** az „App Services” lehetőséget, az **Erőforrásnál** pedig az oktatóanyag során korábban létrehozott webalkalmazást.

4. Az **Idő összesítése** értéke legyen „Összes”, a **Metrika nevénél** „Kérések”, az **Időfelbontási szint statisztikája** pedig „Átlag”.

5. Az **Operátor** legyen „Kisebb, mint”, a **Küszöbérték** legyen „5”, az **Időtartam** pedig legyen „5 perc”.

6. A kiválasztott művelet legyen **„Mennyiség csökkentése a következővel”**, a **Példányszám** legyen „1”, a **Várakozási idő** pedig „5” perc.

7. Kattintson a **Hozzáadás** gombra.

    ![Horizontális leskálázási szabály létrehozása](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Mentse** az automatikus skálázási beállítást.

    ![Automatikus méretezési beállítás mentése](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Felskálázási művelet kiváltása
Az imént létrehozott automatikus méretezési beállítás felskálázási feltételének aktiválásához az szükséges, hogy a webalkalmazás 5 percen belül több mint 10 kérést kapjon.

1. Nyisson meg egy böngészőablakot a böngészőben, majd lépjen az oktatóanyag során korábban létrehozott webalkalmazáshoz. A webalkalmazás URL-címét az Azure Portalon a webalkalmazás erőforrásához lépve, majd az „Áttekintés” lapon a **Böngészés** gombra kattintva találja meg.

2. Egymás után gyorsan frissítse az oldalt 10-nél többször.

3. Kattintson a bal oldali navigációs ablaktáblán a **Figyelés** eleme. Miután az oldal betöltődik, válassza az **autoscale** (automatikusan skálázás) lapot.

4. Válassza ki a listából az oktatóanyag során használt App Service-csomagot.

5. Az automatikus skálázási beállításnál kattintson a **Futtatási előzmények** lapra.

6. Egy ábrát láthat, amely megjeleníti az App Service-csomag példányszámait az idő elteltével.

7. Néhány percen belül a példányszámnak 1-ről 2-re kell nőnie.

8. Az ábra alatt láthatók az automatikus méretezési beállítás által végrehajtott skálázási műveletek tevékenységnapló-bejegyzései.

## <a name="trigger-scale-in-action"></a>Leskálázási művelet kiváltása
Az automatikus méretezési beállítás leskálázási feltétele akkor aktiválódik, ha a webalkalmazás 10 perc alatt kevesebb mint 5 kérést kap. 

1. Győződjön meg arról, hogy a webalkalmazás nem kap kéréseket.

2. Töltse be az Azure Portalt.

3. Kattintson a bal oldali navigációs ablaktáblán a **Figyelés** eleme. Miután az oldal betöltődik, válassza az **autoscale** (automatikusan skálázás) lapot.

4. Válassza ki a listából az oktatóanyag során használt App Service-csomagot.

5. Az automatikus skálázási beállításnál kattintson a **Futtatási előzmények** lapra.

6. Egy ábrát láthat, amely megjeleníti az App Service-csomag példányszámait az idő elteltével.

7. Néhány percen belül a példányszámnak 2-ről 1-re kell csökkennie. Ez a folyamat legalább 100 percig tart.  

8. Az ábra alatt láthatók az automatikus skálázási beállítás által végrehajtott skálázási műveletek tevékenységnapló-bejegyzései.

    ![Leskálázási műveletek megtekintése](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az oktatóanyag során létrehozott webalkalmazást.

2. Az erőforrás oldalán kattintson a **Törlés** elemre, a törlés megerősítéséhez írja be a **yes** (igen) szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

3. Ezután válassza ki az App Service-csomag erőforrását, és kattintson a **Törlés** gombra.

4. A törlés megerősítéséhez írja be a **yes** (igen) szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbiakat végezte el:  
> [!div class="checklist"]
> * Webalkalmazás és App Service-csomag létrehozása
> * Automatikus méretezési szabályok egy webalkalmazás fogadott kéréseinek száma alapján történő konfigurálása horizontális le- és felskálázáshoz
> * Horizontális felskálázási művelet kiváltása és a példányszám növekedésének megfigyelése
> * Horizontális leskálázási művelet kiváltása és a példányszám csökkenésének megfigyelése
> * Az erőforrások törlése


Az automatikus méretezési beállításokkal kapcsolatos további információért folytassa az [automatikus méretezés áttekintésével](../autoscale/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Monitorozási adatok archiválása](../essentials/platform-logs-overview.md)

