---
title: Adatbázis visszaállítása több-bérlős SaaS-alkalmazásokban
description: Megtudhatja, hogyan állíthatja vissza egyetlen bérlő Azure SQL Databaseét az adattörlést követően
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780478"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Egyetlen bérlő visszaállítása adatbázis-bérlői SaaS-alkalmazással
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az adatbázis-bérlői modell segítségével egyszerűen visszaállíthat egy bérlőt egy korábbi időpontra anélkül, hogy ez hatással lenne a többi bérlőre.

Ebben az oktatóanyagban két adathelyreállítási mintát tanul:

> [!div class="checklist"]
> * Adatbázis visszaállítása párhuzamos adatbázisba (egymás mellett).
> * Állítsa vissza az adatbázist helyben, a meglévő adatbázist lecserélve.

| Mintázat | Leírás |
|:--|:--|
| Visszaállítás egy párhuzamos adatbázisba | Ez a minta olyan feladatokhoz használható, mint például a felülvizsgálat, a naplózás és a megfelelőség, hogy a bérlő egy korábbi pontról vizsgálja meg az adataikat. A bérlő aktuális adatbázisa online állapotú és változatlan marad. |
| Visszaállítás helyben | Ez a minta jellemzően egy bérlő korábbi pontra történő helyreállítására szolgál, miután egy bérlő véletlenül törli vagy megsérült az adatokat. Az eredeti adatbázis ki van kapcsolva a sorban, és a visszaállított adatbázisra vált. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip SaaS-alkalmazás üzembe helyezése és megismerése](./saas-dbpertenant-get-started-deploy.md)című témakört.
* Az Azure PowerShell telepítve van. Részletekért lásd: [Azure PowerShell első lépései](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>A SaaS-bérlő visszaállítási mintáinak bemutatása

Két egyszerű minta van az egyes bérlői adathalmazok visszaállítására. Mivel a bérlői adatbázisok el vannak különítve egymástól, az egyik bérlő visszaállítása nem befolyásolja a többi bérlő adatait. A Azure SQL Database időponthoz tartozó visszaállítási (PITR) funkció mindkét mintában használatos. A PITR mindig létrehoz egy új adatbázist.

* **Visszaállítás párhuzamosan**: az első mintában egy új párhuzamos adatbázis jön létre a bérlő aktuális adatbázisa mellett. A bérlő ezután csak olvasási hozzáférést kap a visszaállított adatbázishoz. A visszaállított adatok áttekinthetők és felhasználhatók a jelenlegi adatértékek felülírására. Az alkalmazás tervezője határozza meg, hogy a bérlő hogyan fér hozzá a visszaállított adatbázishoz, és milyen beállításokat biztosít a helyreállításhoz. Egyszerűen lehetővé teheti, hogy a bérlő egy korábbi pontban tekintse át az adataikat, ami bizonyos helyzetekben szükséges.

* **Visszaállítás helyben**: a második minta akkor hasznos, ha az adatvesztés vagy sérült, és a bérlő egy korábbi pontra kíván visszaállítani. A bérlő kikapcsolt állapotban van, miközben az adatbázis helyreáll. Az eredeti adatbázis törölve lett, és a visszaállított adatbázis át lett nevezve. A törlés után az eredeti adatbázis biztonsági mentési lánca továbbra is elérhető marad, így szükség esetén visszaállíthatja az adatbázist egy korábbi időpontra.

Ha az adatbázis [aktív földrajzi replikálást](active-geo-replication-overview.md) használ, és párhuzamosan állítja vissza a visszaállítást, javasoljuk, hogy a visszaállított másolatból másolja ki a szükséges összes adatforrást az eredeti adatbázisba. Ha lecseréli az eredeti adatbázist a visszaállított adatbázisra, újra kell konfigurálnia és szinkronizálnia kell a Geo-replikációt.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlői alkalmazás parancsfájljainak beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis-szkriptek és az alkalmazás forráskódja a [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhető el. Az Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseiért tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Előkészületek

Adatbázis létrehozásakor 10 – 15 percet is igénybe vehet, mielőtt az első teljes biztonsági mentés elérhetővé válik a visszaállításhoz. Ha most telepítette az alkalmazást, előfordulhat, hogy néhány percet várnia kell, mielőtt megpróbálja ezt a forgatókönyvet.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Bérlők véletlen törlésének szimulálása

A helyreállítási forgatókönyvek bemutatásához először "véletlenül" töröl egy eseményt az egyik bérlői adatbázisból. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Az események alkalmazás megnyitása az aktuális események áttekintéséhez

1. Nyissa meg az Events hubot ( http://events.wtp.&lt ; User &gt; . trafficmanager.net), és válassza a **contoso Concert Hall** elemet.

   ![Events hub](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Görgesse az események listáját, és jegyezze fel a lista utolsó eseményét.

   ![Utolsó esemény jelenik meg](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Véletlenül" törli a legutóbbi eseményt

1. A PowerShell ISE-ben nyissa meg a... \\ Tanulási modulok \\ üzletmenet-folytonossági és vész-helyreállítási \\ RestoreTenant \\ *Demo-RestoreTenant.ps1*, és állítsa be a következő értéket:

   * **$DemoScenario**  =  **1**. *törölje az utolsó eseményt (Ticket Sales nélkül)*.
2. Nyomja le az F5 billentyűt a szkript futtatásához és az utolsó esemény törléséhez. A következő megerősítő üzenet jelenik meg:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Megnyílik a contoso Events oldal. Görgessen lefelé, és ellenőrizze, hogy az esemény el lett-e mentve. Ha az esemény még szerepel a listában, válassza a **frissítés** lehetőséget, és ellenőrizze, hogy elment-e.
   ![Legutóbbi esemény eltávolítva](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Bérlői adatbázis visszaállítása az éles adatbázissal párhuzamosan

Ez a gyakorlat visszaállítja a contoso Concert Hall-adatbázist az esemény törlését megelőző időpontra. Ez a forgatókönyv feltételezi, hogy egy párhuzamos adatbázisban szeretné áttekinteni a törölt fájlokat.

 A *Restore-TenantInParallel.ps1* szkript létrehoz egy *ContosoConcertHall \_ Old* nevű párhuzamos bérlői adatbázist egy párhuzamos katalógus-bejegyzéssel. Ez a visszaállítási minta a kisebb adatvesztéssel való helyreállításhoz ideális megoldás. Akkor is használhatja ezt a mintát, ha megfelelőségi vagy naplózási célból át kell tekintenie az adatelemzési célokat. Az [aktív földrajzi replikálás](active-geo-replication-overview.md)használata ajánlott módszer.

1. Fejezze be a [bérlő szimulálása véletlen adattörlési](#simulate-a-tenant-accidentally-deleting-data) szakaszt.
2. A PowerShell ISE-ben nyissa meg a... \\ Tanulási modulok \\ üzletmenet-folytonossági és vész-helyreállítási \\ RestoreTenant \\ _Demo-RestoreTenant.ps1_.
3. **$DemoScenario**  =  **2** beállítása, *a bérlő visszaállítása párhuzamosan*.
4. A szkript futtatásához nyomja le az F5 billentyűt.

A parancsfájl visszaállítja a bérlői adatbázist egy adott időpontra, mielőtt törölné az eseményt. A rendszer visszaállítja az adatbázist egy _ContosoConcertHall \_ régi_ nevű új adatbázisba. A rendszer törli a visszaállított adatbázisban található katalógus-metaadatokat, majd a *ContosoConcertHall \_ régi* neve alapján létrehozott kulccsal felveszi az adatbázist a katalógusba.

A bemutató parancsfájl az új bérlői adatbázis eseményeinek lapját nyitja meg a böngészőben. Figyelje meg, hogy az oldal URL-címe ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` tartalmazza a visszaállított adatbázisból származó olyan adatok adatait, amelyekben a *_old* hozzá van adva a névhez.

A böngészőben megjelenő események görgetésével ellenőrizze, hogy az előző szakaszban törölt esemény vissza lett-e állítva.

A visszaállított bérlő további bérlőként való kimutatása, a saját események alkalmazásával azonban nem valószínű, hogy hogyan biztosítanak bérlői hozzáférést a visszaállított adatokhoz. A visszaállítási minta szemléltetésére szolgál. Általában csak olvasási hozzáférést adhat a régi adathoz, és megtarthatja a visszaállított adatbázist egy meghatározott időtartamra. A mintában törölheti a visszaállított bérlői bejegyzést, miután végzett a _visszaállított bérlő eltávolítása_ forgatókönyv futtatásával.

1. Állítsa be **$DemoScenario**  =  **4**, a *visszaállított bérlő eltávolítása*.
2. A szkript futtatásához nyomja le az F5 billentyűt.
3. A *ContosoConcertHall \_ régi* bejegyzése már törölve lett a katalógusból. A böngészőben nyissa meg a bérlő eseményeinek oldalát.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Bérlő visszaállítása helyben, a meglévő bérlői adatbázis cseréje

Ez a gyakorlat visszaállítja a contoso Concert Hall bérlőjét az esemény törlését megelőző pontra. A *Restore-TenantInPlace* parancsfájl visszaállítja a bérlői adatbázist egy új adatbázisba, és törli az eredetit. Ez a visszaállítási minta leginkább a súlyos adatsérülések helyreállítására alkalmas, és előfordulhat, hogy a bérlőnek jelentős adatvesztéssel kell rendelkeznie.

1. A PowerShell ISE-ben nyissa meg a **Demo-RestoreTenant.ps1** fájlt.
2. Állítsa be **$DemoScenario**  =  **5**, *a bérlő visszaállítása a helyén*.
3. A szkript futtatásához nyomja le az F5 billentyűt.

A parancsfájl visszaállítja a bérlői adatbázist az esemény törlése előtti pontra. Első lépésként a contoso Concert Hall bérlője kikapcsolja a sort a további frissítések megelőzése érdekében. Ezt követően a rendszer a visszaállítási pontról állítja vissza a párhuzamos adatbázist. A visszaállított adatbázis neve egy időbélyegző, amely biztosítja, hogy az adatbázisnév ne legyen ütközik a bérlői adatbázis meglévő nevével. Ezután a régi bérlői adatbázist törli a rendszer, és a visszaállított adatbázist átnevezi az eredeti adatbázis nevére. Végül a contoso Concert Hall online állapotba kerül, és lehetővé teszi az alkalmazás számára a visszaállított adatbázis elérését.

Az adatbázist sikeresen visszaállította az esemény törlése előtti időpontra. Az **események** Lap megnyitásakor ellenőrizze, hogy az utolsó esemény vissza lett-e állítva.

Az adatbázis visszaállítása után újabb 10 – 15 percet vesz igénybe, mielőtt az első teljes biztonsági mentés elérhető legyen a visszaállításhoz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis visszaállítása párhuzamos adatbázisba (egymás mellett).
> * Adatbázis visszaállítása a helyén.

Próbálja ki a [bérlői adatbázis sémájának kezelése](saas-tenancy-schema-management.md) oktatóanyagot.

## <a name="additional-resources"></a>További források

* [A Wingtip SaaS-alkalmazásra épülő további oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Tudnivalók a SQL Database biztonsági mentésekről](automated-backups-overview.md)