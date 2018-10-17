---
title: ASP.NET-alkalmazás és Azure SQL Database üzembe helyezése az Azure DevOps Projecttel | Azure DevOps Services-oktatóanyag
description: A DevOps Project megkönnyíti az Azure-ral való ismerkedést. Az Azure DevOps Project segítségével néhány gyors lépéssel, könnyen helyezheti üzembe az ASP.NET-alkalmazást az Azure SQL Database-ben.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297333"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Oktatóanyag: ASP.NET-alkalmazás és Azure SQL Database üzembe helyezése az Azure DevOps Projecttel

Az Azure DevOps Projecttel meglévő kódok és a Git-adattár segítségével, vagy valamelyik mintaalkalmazásból egyszerűen hozhat létre egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot az Azure-ban.  A DevOps Project automatikusan létrehozza olyan Azure-erőforrásokat, mint az Azure SQL Database, létrehozza és konfigurálja a CI-hoz való buildfolyamatot tartalmazó kiadási folyamatot az Azure DevOpsban, továbbá beállít egy kiadási folyamatot a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * Azure DevOps Project létrehozása egy ASP.NET-alkalmazáshoz és az Azure SQL Database-hez
> * Az Azure DevOps Services és egy Azure-előfizetés konfigurálása 
> * Az Azure DevOps Services CI-folyamat vizsgálata
> * Az Azure DevOps Services CD-folyamat vizsgálata
> * Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Kapcsolódás az Azure SQL Server Database-hez 
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Azure DevOps Project létrehozása egy ASP.NET-alkalmazáshoz és az Azure SQL Database-hez

Az Azure DevOps Project létrehoz egy CI-/CD-folyamatot az Azure-ban.  Létrehozhat egy **új Azure DevOps Services**-szervezetet, vagy használhat egy **meglévő szervezetet** is.  Az Azure DevOps Project az **Azure-erőforrásokat** is létrehozza, például egy Azure SQL Database-t a választott **Azure-előfizetésben**.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A bal oldali navigációs sávban válassza az **Erőforrás létrehozása** ikont, majd keresse meg a **DevOps Projectet**.  Válassza a **Létrehozás** elemet.

    ![Folyamatos kézbesítés indítása](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza a **.NET**, majd a **Tovább** lehetőséget.

1. Az **Alkalmazás-keretrendszer kiválasztásánál** válassza az **ASP.NET** lehetőséget.

1. Válassza az **Adatbázis hozzáadása** lehetőséget, majd kattintson a **Tovább** gombra.  

1. Az előző lépésekben kiválasztott alkalmazás-keretrendszer meghatározza az Azure-szolgáltatás üzembehelyezési céljának itt elérhető típusát.  Kattintson a **Tovább** gombra.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Az Azure DevOps Services és egy Azure-előfizetés konfigurálása

1. Hozzon létre egy **új** Azure DevOps Services-szervezetet, vagy válasszon egy **meglévő** szervezetet.  Válasszon egy **nevet** az Azure DevOps-projektnek.  

1. Válassza ki az **Azure-előfizetését**.

1. A további Azure-konfigurációs beállítások megtekintéséhez kattintson a **Módosítás** hivatkozásra és határozza meg a **felhasználónevet** az **adatbázis-kiszolgáló bejelentkezési adatainál**.  **Tárolja el** a **felhasználónevet** az oktatóanyag további lépéseihez.
 
1. A fenti lépés elvégzése után lépjen ki az Azure-konfigurációs területről, és válassza a **Kész** lehetőséget.  Ha a fenti lépést nem kellett elvégeznie, csak válassza a **Kész** lehetőséget.

1. A folyamat befejezése néhány percet vesz igénybe.  A befejezést követően az Azure DevOps **Project irányítópultja** betöltődik az Azure Portalon.  Az **Azure DevOps Project irányítópultját** közvetlenül is elérheti az **Azure Portal** **Minden erőforrás** területéről.  Az irányítópult jobb oldalán válassza a **Tallózást** a futó alkalmazás megtekintéséhez.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Az Azure DevOps Services CI-folyamat vizsgálata

Az Azure DevOps Project automatikusan konfigurál egy teljes Azure CI-/CD-folyamatot az Azure DevOps Services-szervezetben.  Megvizsgálhatja és testre szabhatja a folyamatot.  Az Azure DevOps Services buildelési folyamatának megismeréséhez kövesse az alábbi lépéseket.

1. Lépjen az **Azure DevOps Project irányítópultjára**.

1. Az **Azure DevOps Project irányítópultjának** **tetején** válassza a **Folyamatok létrehozása** lehetőséget.  Ez a hivatkozás megnyit egy böngészőlapot, és megnyitja az új projekt Azure DevOps Services buildelési folyamatát.

1. Helyezze az egérmutatót a buildelési folyamat jobb oldalára, az **Állapot** mező mellé. Válassza a megjelenő **három pontot**.  Ez a művelet megnyitja a menüt, ahol több tevékenységet is végrehajthat, például **várakozási sorba helyezhet egy új buildet**, **szüneteltethet egy buildet** vagy **szerkesztheti a buildelési folyamatot**.

1. Válassza a **Szerkesztés** elemet.

1. Ebben a nézetben **vizsgálja meg a buildelési folyamathoz tartozó különböző feladatokat**.  A build különböző feladatokat hajt végre, például erőforrásokat olvas be az Azure DevOps Services Git-adattárából, visszaállítja a függőségeket, és közzéteszi az üzembe helyezésekhez használt kimeneteket.

1. A buildelési folyamat tetején válassza a **buildelési folyamat nevét**.

1. Módosítsa a buildelési folyamat **nevét** egy közérthetőbb névre.  Válassza a **mentésre és várakozási sorba helyezésre** szolgáló elemet, majd a **Mentést**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  Ekkor megjelenik a build legutóbbi módosításainak naplója.  Az Azure DevOps Services nyomon követi a buildelési folyamat módosításait, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  Az Azure DevOps Project automatikusan létrehozott egy CI-eseményindítót, és az adattárban történő minden véglegesítés egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**.  A forgatókönyv alapján megadhat szabályzatokat bizonyos számú build megtartására vagy eltávolítására.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Az Azure DevOps Services CD-folyamat vizsgálata

Az Azure DevOps Project automatikusan létrehozza és konfigurálja az Azure DevOps Services-szervezetből az Azure-előfizetésbe való üzembe helyezéshez szükséges lépéseket.  A lépések közé tartozik az Azure-szolgáltatás kapcsolatának konfigurálása az Azure DevOps Services Azure-előfizetésben történő hitelesítéséhez.  Az automatizálás is létrehoz egy Azure DevOps Services-kiadási definíciót, és a kiadás biztosítja a CD-t az Azure számára.  Az Azure DevOps Services-kiadási definíció megvizsgálásához kövesse az alábbi lépéseket.

1. Válassza ki a **Létrehozás és kiadást**, majd a **Kiadásokat**.  Az Azure DevOps Project létrehozott egy Azure DevOps Services-kiadási folyamatot, amely az Azure-ban történő üzembe helyezéseket kezeli.

1. A böngésző bal oldalán válassza a kiadási folyamat melletti **három pontot**, majd a **Szerkesztés** elemet.

1. A kiadási folyamat tartalmaz egy **folyamatot**, amely meghatározza a kiadási folyamatot.  Az **Összetevők** alatt válassza az **Elvetést**.  Az előző lépésekben megvizsgált buildelési folyamat létrehozza az összetevőhöz használt kimenetet. 

1. Az **Elvetés** ikon jobb oldalán válassza a (villámként megjelenő) **Folyamatos üzembehelyezési eseményindító** **ikont**.  Ez a kiadási folyamat egy engedélyezett CD-eseményindítóval rendelkezik.  Az eseményindító minden egyes alkalommal elindít egy üzembe helyezést, amikor elérhetővé válik egy új buildösszetevő.  Ha szeretné, letilthatja az eseményindítót, így az üzembe helyezéseket manuálisan kell majd végrehajtani. 

1. Az Azure DevOps Project egy véletlenszerű SQL-jelszót állított be, és ezt használta a kiadási folyamathoz.  A böngésző bal oldalán válassza a **Változókat**. 

1. **Ezt a lépést csak akkor hajtsa végre, ha módosította az SQL Server jelszavát.**  Egyetlen **Jelszó** változó érhető el.  Kattintson az **Érték** szövegmezőtől jobbra lévő, **lakat alakú** ikonra.  **Adja meg** az új jelszót, majd válassza a **Mentést**.

1. A böngésző bal oldalán válassza a **Feladatokat**, majd válassza ki a **környezetet**.  

1. A feladatok azok a tevékenységek, amelyeket az üzembehelyezési folyamat hajt végre, és ezek **Fázisok** szerint vannak csoportosítva.  Ehhez a kiadási folyamathoz egyetlen fázis tartozik.  A fázis egy **Azure App Service-üzembehelyezési** és **Azure SQL Database-üzembehelyezési** feladatot tartalmaz.

1. Válassza ki az **Azure SQL-t végrehajtó** feladatot, és vizsgálja meg az SQL üzembe helyezéséhez használt különböző tulajdonságokat.  A **Telepítőcsomag** alatti területen figyelje meg, hogy melyik feladat használ **SQL DACPAC-fájlt**.

1. A böngésző jobb oldalán válassza a **Kiadások megtekintését**.  Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza az egyik kiadás mellett a **három pontot**, majd a **Megnyitást**.  Ebben a nézetben több menüt is megtekinthet, például a **kiadás összegzését**, **a társított munkaelemeket** vagy a **Teszteket**.

1. Válassza a **Véglegesítéseket**.  Ez a nézet az adott üzembe helyezéshez hozzárendelt kódvéglegesítéseket jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez összehasonlíthatja a kiadásokat.

1. Válassza a **Naplókat**.  A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban.  Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban 

 > [!NOTE]
 > Az alábbi lépések a CI/CD-folyamatot egy egyszerű szöveges módosítással tesztelik.  Az SQL üzembe helyezési folyamatának tesztelése céljából azt is megteheti, hogy módosítja az SQL Server-sémát a táblában.

Most már készen áll, hogy egy csapattal közösen dolgozzon az alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.  Az Azure DevOps Services Git-adattárának bármely módosítása egy buildet indít el az Azure DevOps Servicesben, és egy Azure DevOps Services CD-folyamat üzembe helyezést hajt végre az Azure-ban.  Kövesse az alábbi lépéseket, vagy használjon egyéb módszereket az adattár módosításainak véglegesítéséhez.  A kód módosításai elindítják a CI/CD-folyamatot és automatikusan üzembe helyezik az új módosításokat az Azure-ban.

1. Az Azure DevOps Services menüből válassza a **Kód** lehetőséget, és lépjen az adattárához.

1. Lépjen a **SampleWebApplication\Views\Home** könyvtárra, válassza ki az **Index.cshtml** fájl melletti **három pontot**, majd a **Szerkesztést**.

1. Módosítsa a fájlt, például az egyik **div címkén** belüli szöveget.  A jobb felső részen kattintson a **Véglegesítés** elemre.  Ismét válassza a **Véglegesítést** a módosítás leküldéséhez. 

1. Néhány pillanat múlva az **Azure DevOps Servicesben elindul egy build**, majd a rendszer egy kiadást hajt végre a módosítások üzembe helyezéséhez.  Monitorozhatja a **build állapotát** a DevOps Project irányítópultján, vagy a böngészőben, az Azure DevOps Services-szervezet segítségével.

1. Miután a kiadás befejeződött, **frissítse az alkalmazást** a böngészőben, és ellenőrizze, hogy megjelentek-e a módosítások.

## <a name="connect-to-the-azure-sql-server-database"></a>Kapcsolódás az Azure SQL Server Database-hez

Az Azure SQL Database-hez való csatlakozáshoz megfelelő engedélyekre van szüksége.

1. Az Azure DevOps Project irányítópultján válassza ki az **SQL Database-t**, hogy az SQL DB felügyeleti lapjára lépjen.
   
1. Válassza a **Kiszolgálói tűzfal beállítása**, majd az **+ Ügyfél IP-címének hozzáadása** lehetőséget.  

1. Kattintson a **Mentés** gombra.  Az ügyfél IP-címe most már hozzáfér az **SQL Server Azure-erőforráshoz**.

1. Lépjen vissza az **SQL Database** panelre. 

1. A képernyő jobb oldalán válassza a **Kiszolgáló neve** lehetőséget, hogy az **SQL Server** konfigurációs lapjára lépjen.

1. Válassza a **Jelszó visszaállítása** lehetőséget, adja meg az **SQL Server-rendszergazda bejelentkezési** jelszavát, majd válassza a **Mentést**.  **Tárolja el** ezt a jelszót az oktatóanyag további lépéseihez.

1. Ezután az Azure SQL Serverhez és Database-hez való kapcsolódáshoz olyan ügyféleszközöket is használhat, amilyen az **SQL Server Management Studio** vagy a **Visual Studio**.  A kapcsolódáshoz használja a **Kiszolgáló neve** tulajdonságot.

   Ha a DevOps Project kezdeti konfigurálása során nem változtatta meg a DB felhasználónevét, akkor a felhasználónév az e-mail-címe helyi része.  Ha tehát az e-mail-címe johndoe@microsoft.com, akkor a felhasználóneve johndoe lesz.

 > [!NOTE]
 > Ha megváltoztatja az SQL bejelentkezési adatait, akkor módosítania kell a jelszót az Azure DevOps Services-kiadási folyamat változójában is, ahogy azt az **Azure DevOps Services CD-folyamat vizsgálatával** foglalkozó szakasz ismerteti

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 > [!NOTE]
 > Az alábbi lépések véglegesen törlik az erőforrásokat.  Ezt a funkciót csak a megjelenő üzenet alapos átolvasása után használja.

Ha éppen tesztel, eltávolíthatja az erőforrást a költségek elkerülése érdekében.  Ha már nincs rájuk szükség, törölheti az ebben az oktatóanyagban létrehozott Azure SQL Database-t és a kapcsolódó erőforrásokat az Azure DevOps Project irányítópultján található **Törléssel**.  **Legyen óvatos**, mivel a törlési funkció megsemmisíti az Azure DevOps Projecttel létrehozott adatokat az Azure-ban és az Azure DevOps Servicesben is, és később nem lesznek lekérhetők.

1. Az **Azure Portalról** lépjen át az **Azure DevOps Projectre**.
2. Az irányítópult **jobb felső** részén válassza a **Törlést**.  A megjelenő üzenet elolvasása után válassza az **Igen** lehetőséget az erőforrások **végleges törléséhez**.

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb projektek sablonjaként is használhatja.  Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure DevOps Project létrehozása egy ASP.NET-alkalmazáshoz és az Azure SQL Database-hez
> * Az Azure DevOps Services és egy Azure-előfizetés konfigurálása 
> * Az Azure DevOps Services CI-folyamat vizsgálata
> * Az Azure DevOps Services CD-folyamat vizsgálata
> * Az Azure DevOps Services módosításainak véglegesítése és automatikus üzembe helyezés az Azure-ban
> * Kapcsolódás az Azure SQL Server Database-hez 
> * Az erőforrások eltávolítása

Tekintse át az oktatóanyagokat az Azure-folyamattal kapcsolatos további részletekért:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]