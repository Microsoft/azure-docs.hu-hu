---
title: Több-bérlős SaaS-oktatóanyag
description: Új bérlők kiépítése és katalogizálása az önálló alkalmazás mintájának használatával
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793245"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Új bérlők kiépítése és katalogizálása az alkalmazással a bérlői SaaS-minták alapján
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk az új bérlők kiépítése és katalogizálása az önálló alkalmazással, bérlői SaaS-mintán keresztül.
Ez a cikk két fő részből áll:
* Az új bérlők üzembe helyezésének és katalogizálása fogalmi vitája
* Egy oktatóanyag, amely kiemeli a kiépítés és a katalogizálás megvalósítására szolgáló minta PowerShell-kódot
    * Az oktatóanyag az Wingtip tickets minta SaaS-alkalmazást használja, amely az önálló alkalmazáshoz van igazítva a bérlői mintában.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás/bérlői minta

A bérlői minták önálló alkalmazása a több-bérlős SaaS-alkalmazások különböző mintáinak egyike.  Ebben a mintában egy önálló alkalmazás van kiépítve az egyes bérlők számára. Az alkalmazás az alkalmazás szintű összetevőket és egy Azure SQL Database tartalmaz.  Minden bérlői alkalmazás üzembe helyezhető a gyártó előfizetésében.  Azt is megteheti, hogy az Azure olyan [felügyelt alkalmazási programot](../../azure-resource-manager/managed-applications/overview.md) kínál, amelyben az alkalmazások a bérlői előfizetésben helyezhetők üzembe, és a szállító a bérlő nevében felügyelhető.

   ![alkalmazás/bérlői minta](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Amikor egy bérlő számára telepít üzembe egy alkalmazást, az alkalmazás és az adatbázis a bérlőhöz létrehozott új erőforráscsoporthoz lesz kiépítve.  A különálló erőforráscsoportok használata elkülöníti az egyes bérlők alkalmazási erőforrásait, és lehetővé teszi, hogy egymástól függetlenül kezeljék őket. Mindegyik erőforráscsoport esetében minden alkalmazás-példány úgy van konfigurálva, hogy közvetlenül hozzáférjen a kapcsolódó adatbázishoz.  Ez a kapcsolati modell ellentétben áll más mintázatokkal, amelyek katalógust használnak az alkalmazás és az adatbázis közötti kapcsolatok közvetítéséhez.  Mivel nincs erőforrás-megosztás, minden bérlői adatbázist elegendő erőforrással kell kiépíteni a maximális terhelés kezeléséhez. Ez a minta általában kevesebb Bérlővel rendelkező SaaS-alkalmazásokhoz használható, ahol nagy hangsúlyt fektetünk a bérlők elkülönítésére, és kevesebb figyelmet fordítanak az erőforrások költségeire.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Bérlői katalógus használata az alkalmazással a bérlői minta alapján

Noha minden bérlő alkalmazása és adatbázisa teljesen elkülönített, különböző felügyeleti és elemzési forgatókönyvek működhetnek a bérlők között.  Például az alkalmazás új kiadásához tartozó séma módosítása az egyes bérlői adatbázisok sémájának módosítását igényli. A jelentéskészítési és elemzési forgatókönyvekhez szükség lehet a bérlői adatbázisokhoz való hozzáférésre is, függetlenül attól, hogy hol vannak üzembe helyezve.

   ![Diagram, amely bemutatja, hogyan használható a bérlői katalógus az alkalmazással a bérlői mintában.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

A bérlői katalógus a bérlői azonosító és a bérlői adatbázis közötti leképezést tartalmaz, amely lehetővé teszi az azonosító feloldását egy kiszolgáló és egy adatbázis neveként.  A Wingtip SaaS-alkalmazásban a bérlő azonosítóját a bérlő nevének kivonatának számítja, bár más sémák is használhatók.  Habár az önálló alkalmazásoknak nincs szükségük a katalógusra a kapcsolatok kezeléséhez, a katalógus felhasználható más műveleteknek a bérlői adatbázisokra való kiterjesztésére is. A rugalmas lekérdezés például a katalógus használatával határozza meg azon adatbázisok készletét, amelyeken keresztül a lekérdezések terjesztése a több-bérlős jelentéskészítéshez.

## <a name="elastic-database-client-library"></a>Elastic Database ügyféloldali kódtár

A Wingtip-minta alkalmazásban a katalógust az [Elastic Database ügyféloldali kódtár](elastic-database-client-library.md) (EDCL) szegmens felügyeleti funkciói valósítják meg.  A függvénytár lehetővé teszi, hogy az alkalmazás egy adatbázisban tárolt szegmensi térképet hozzon létre, kezelje és használja. A Wingtip jegyek mintájában a katalógus a *bérlői katalógus* adatbázisában tárolódik.  A szegmens leképezi a bérlői kulcsot arra a szilánkra (adatbázis), amelyben a bérlői adattároló található.  A EDCL függvények kezelik a *bérlői katalógus* -adatbázis tábláiban tárolt *globális* szegmenseket, valamint az egyes szegmensekben tárolt helyi szegmensek *leképezését* .

Az EDCL függvények az alkalmazásokból vagy a PowerShell-szkriptekről hívhatók a szegmenses Térkép bejegyzéseinek létrehozásához és kezeléséhez. Más EDCL függvények használatával lekérheti a szegmensek készletét, vagy csatlakozhat a megfelelő adatbázishoz az adott bérlői kulcshoz.

> [!IMPORTANT]
> Ne szerkessze a katalógus-adatbázisban lévő vagy a helyi szegmenses hozzárendelési térképet közvetlenül a bérlői adatbázisokban. A közvetlen frissítések nem támogatottak az adatsérülés magas kockázata miatt. Ehelyett csak a EDCL API-k használatával szerkessze a megfeleltetési adataikat.

## <a name="tenant-provisioning"></a>Bérlői kiépítés

Mindegyik bérlő új Azure-erőforráscsoportot igényel, amelyet az erőforrások üzembe helyezése előtt létre kell hozni. Ha az erőforráscsoport létezik, egy Azure Resource Management-sablon használható az alkalmazás-összetevők és az adatbázis központi telepítéséhez, majd az adatbázis-kapcsolatok konfigurálásához. Az adatbázis-séma inicializálásához a sablon importálni tudja a bacpac-fájlt.  Azt is megteheti, hogy az adatbázist a "sablon" adatbázis másolata is létrehozhatja.  Az adatbázis ezután tovább frissül a kezdeti hely adataival, és regisztrálva van a katalógusban.

## <a name="tutorial"></a>Oktatóanyag

Ezen oktatóanyag segítségével megtanulhatja a következőket:

* Katalógus kiépítése
* Regisztrálja a katalógusban korábban üzembe helyezett minta-bérlői adatbázisokat.
* További bérlő kiépítése és regisztrálása a katalógusban

Az alkalmazás üzembe helyezéséhez és konfigurálásához, a bérlői adatbázis létrehozásához, majd egy bacpac-fájl importálásához egy Azure Resource Manager sablon használható. Előfordulhat, hogy az importálási kérelem több percig is várólistára kerül a művelet végrehajtása előtt.

Az oktatóanyag végén önálló bérlői alkalmazásokkal rendelkezik, és minden adatbázis regisztrálva van a katalógusban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)
* A három minta bérlői alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt szeretné telepíteni ezeket az alkalmazásokat, tekintse meg [a Wingtip tickets SaaS önálló alkalmazási minta üzembe helyezése és megismerése](./saas-standaloneapp-get-started-deploy.md)című részt

## <a name="provision-the-catalog"></a>A katalógus kiépítése

Ebben a feladatban megtudhatja, hogyan építheti ki az összes bérlői adatbázis regisztrálásához használt katalógust. Az alábbiakat fogja elvégezni:

* **A katalógus-adatbázis kiépítése** Azure Resource Management-sablonnal. Az adatbázis inicializálása bacpac-fájl importálásával történik.
* **Regisztrálja a korábban telepített minta bérlői alkalmazásokat** .  Minden bérlő egy, a bérlő nevének kivonata alapján létrehozott kulccsal van regisztrálva.  A bérlő nevét a katalógus egy kiterjesztési táblájában is tárolja.

1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\UserConfig.PSM* , és frissítse az **\<user\>** értéket a három minta alkalmazás telepítésekor használt értékre.  **Mentse a fájlt**.
1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* , és állítsa be a **$Scenario = 1**. Telepítse a bérlői katalógust, és regisztrálja az előre meghatározott bérlőket.

1. Adjon hozzá egy töréspontot úgy, hogy a kurzort bárhová helyezi a sorba, `& $PSScriptRoot\New-Catalog.ps1` majd nyomja le az **F9** billentyűt.

    ![Töréspont beállítása nyomkövetéshez](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Futtassa a szkriptet az **F5** billentyű lenyomásával.
1.  Miután a szkript végrehajtása leáll a törésponton, nyomja le az **F11** billentyűt a New-Catalog.ps1 parancsfájlba lépéshez.
1.  A szkript végrehajtásának nyomon követéséhez használja a Debug menüpontot, az F10 és az F11 billentyűt a függvények meghívásához.
    *   A PowerShell-parancsfájlok hibakeresésével kapcsolatos további információkért lásd: [Tippek a PowerShell-parancsfájlok használatához és hibakereséséhez](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Ha a parancsfájl befejeződik, a katalógus már létezik, és a rendszer az összes minta bérlőt regisztrálja.

Most tekintse meg a létrehozott erőforrásokat.

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és tallózással keresse meg az erőforráscsoportot.  Nyissa meg a **Wingtip-SA- \<user\> Catalog-** Resource csoportot, és jegyezze fel a katalógus-kiszolgálót és az adatbázist.
1. Nyissa meg az adatbázist a portálon, és válassza az *adatkezelő* lehetőséget a bal oldali menüben.  Kattintson a login parancsra, majd adja meg a jelszót = **P \@ ssword1**.


1. Fedezze fel a *tenantcatalog* -adatbázis sémáját.
   * A `__ShardManagement` sémában lévő objektumokat a Elastic Database ügyféloldali kódtár is megadja.
   * A `Tenants` táblázat és a `TenantsExtended` nézet a mintában szereplő bővítmények, amelyek bemutatják, hogyan terjesztheti ki a katalógust további érték biztosítására.
1. Futtassa a lekérdezést `SELECT * FROM dbo.TenantsExtended` .

   ![adatkezelő](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    A Adatkezelő használatának alternatívájaként SQL Server Management Studio-adatbázisból is csatlakozhat az adatbázishoz. Ehhez kapcsolódjon a kiszolgáló Wingtip –


    Ne feledje, hogy ne szerkessze közvetlenül a katalógusban az adatszerkesztést – mindig használja a szegmens felügyeleti API-kat.

## <a name="provision-a-new-tenant-application"></a>Új bérlői alkalmazás kiépítése

Ebben a feladatban megtudhatja, hogyan építhet ki egyetlen bérlős alkalmazást. Az alábbiakat fogja elvégezni:

* **Hozzon létre egy új erőforráscsoportot** a bérlőhöz.
* **Az alkalmazás és az adatbázis kiépítése** az új erőforráscsoporthoz egy Azure Resource Management-sablonnal.  Ez a művelet magában foglalja az adatbázis általános sémával és hivatkozási adattal való inicializálását egy bacpac-fájl importálásával.
* **Inicializálja az adatbázist alapszintű bérlői információkkal**. Ez a művelet magában foglalja a helyszín típusának megadását, amely meghatározza a háttérként használt fényképet az események webhelyén.
* **Regisztrálja az adatbázist a katalógus-adatbázisban**.

1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* , és állítsa be a **$Scenario = 2**. A bérlői katalógus üzembe helyezése és az előre definiált bérlők regisztrálása

1. Vegyen fel egy töréspontot a szkriptbe úgy, hogy a kurzort az 49-es sorban helyezi el, amely a következőt adja meg: `& $PSScriptRoot\New-TenantApp.ps1` 
1. Futtassa a szkriptet az **F5** billentyű lenyomásával.
1.  Miután a szkript végrehajtása leáll a törésponton, nyomja le az **F11** billentyűt a New-Catalog.ps1 parancsfájlba lépéshez.
1.  A szkript végrehajtásának nyomon követéséhez használja a Debug menüpontot, az F10 és az F11 billentyűt a függvények meghívásához.

A bérlő üzembe helyezését követően megnyílik az új bérlő eseményeinek webhelye.

   ![Red Maple Racing](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Ezután ellenőrizheti a Azure Portalban létrehozott új erőforrásokat.

   ![Red Maple Racing-erőforrások](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>A számlázás leállításához törölje az erőforráscsoportokat

Ha befejezte a minta vizsgálatát, törölje a létrehozott összes erőforráscsoportot a társított számlázás leállításához.

## <a name="additional-resources"></a>További források

- További információ a több-bérlős SaaS-adatbázis alkalmazásairól: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip tickets SaaS önálló alkalmazás üzembe helyezése.
> * Az alkalmazást alkotó kiszolgálók és adatbázisok ismertetése.
> * Mintavételi erőforrások törlése a kapcsolódó számlázás leállításához.

Megismerheti, hogyan használható a katalógus a különböző, több-bérlős forgatókönyvek támogatására a [Wingtip tickets SaaS-alkalmazás](./saas-dbpertenant-wingtip-app-overview.md)adatbázis-bérlői verziójának használatával.