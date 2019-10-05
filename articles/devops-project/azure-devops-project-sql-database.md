---
title: 'Oktatóanyag: ASP.NET-alkalmazás üzembe helyezése és Azure SQL Database kód használata Azure DevOps Projects'
description: DevOps Projects megkönnyíti az Azure megkezdését. A DevOps Projects segítségével néhány gyors lépésben telepítheti a ASP.NET alkalmazást, és Azure SQL Database kódot.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971504"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Oktatóanyag: ASP.NET-alkalmazás üzembe helyezése és Azure SQL Database kód használata Azure DevOps Projects

Azure DevOps Projects egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást, amellyel folyamatos integrációs (CI) és folyamatos átviteli (CD) folyamatokat hozhat létre az Azure-ba. 

DevOps Projects is:
* Automatikusan létrehoz Azure-erőforrásokat, például egy Azure SQL Database-adatbázist.
* Létrehoz és konfigurál egy kiadási folyamatot az Azure-folyamatokban, amely tartalmazza a CI-hez készült Build folyamatát.
* Egy kiadási folyamat beállítása a CD-hez. 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A ASP.NET-alkalmazás és a Azure SQL Database-kód üzembe helyezése a Azure DevOps Projects használatával
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban
> * Kapcsolódás az Azure SQL Database-hez 
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Projekt létrehozása DevOps Projectsban egy ASP.NET-alkalmazáshoz és egy Azure SQL Database-adatbázishoz

A DevOps Projects egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat, például egy Azure SQL Database-adatbázist is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen válassza az **erőforrás létrehozása**lehetőséget.

1. A keresőmezőbe írja be a **DevOps projects**kifejezést, majd válassza a **Létrehozás**lehetőséget.

    ![A DevOps Projects irányítópult](_img/azure-devops-project-github/fullbrowser.png)

1. Válassza a **.net**lehetőséget, majd kattintson a **tovább**gombra.

1. Az **alkalmazás-keretrendszer kiválasztása**területen válassza a **ASP.net**lehetőséget.

1. Válassza **az adatbázis hozzáadása**lehetőséget, majd kattintson a **tovább**gombra.  
    Az alkalmazás-keretrendszer, amelyet az előző lépésben választott, az itt elérhető Azure-szolgáltatás telepítési célját diktálja be. 
    
1. Kattintson a **Tovább** gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetési szolgáltatásokat.  
    Ha szeretné megtekinteni a további Azure-konfigurációs beállításokat, és azonosítani a felhasználónevet az **adatbázis-kiszolgáló bejelentkezési adatai** szakaszban, akkor válassza a **módosítás**lehetőséget. Az oktatóanyag későbbi lépéseihez tárolja a felhasználónevet. Ha ezt a választható lépést hajtja végre, lépjen ki az Azure konfigurációs területén a **kész**gombra kattintva.
 
1. Válassza a **Done** (Kész) lehetőséget.  
    Néhány perc elteltével a folyamat befejeződik, és megnyílik a DevOps Projects irányítópult a Azure Portal. Közvetlenül az irányítópulton is megnyithatja a Azure Portal **összes erőforrását** . A jobb oldalon kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

DevOps Projects automatikusan konfigurál egy teljes CI/CD-folyamatot az Azure Reposben. Megvizsgálhatja és testre szabhatja a folyamatot. Az Azure DevOps Build folyamatának megismeréséhez tegye a következőket:

1. A DevOps Projects irányítópult tetején válassza a **folyamatok létrehozása**lehetőséget.  
    Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  
    A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán keresse meg a különböző feladatok a a buildelési folyamat.  
    A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A létrehozási folyamat elején jelölje ki a buildelési folyamat neve.

1. Módosítsa a buildelési folyamat nevét egy leíró, jelölje be **várólistára & mentése**, majd válassza ki **mentése**.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.  
    Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

1. Válassza az **Eseményindítókat**.  
    A DevOps Projects automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**.  
    A forgatókönyvtől függően a szabályzatokat, hogy megtartja vagy eltávolítja a buildek bizonyos számú is megadhat.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

DevOps Projects automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezet Azure-előfizetéséhez való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy CD-folyamatot is létrehoz, amely biztosítja a CD-t az Azure-beli virtuális géphez. Ha többet szeretne megtudni az Azure DevOps CD-folyamatáról, tegye a következőket:

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások**lehetőséget.  
    DevOps Projects létrehoz egy kiadási folyamatot az Azure-ba történő üzembe helyezések kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget.  
    A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**.  
    Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon jobb oldalán válassza a **folyamatos üzembe helyezés trigger**lehetőséget.  
    Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Szükség esetén letilthatja az eseményindítót, hogy a központi telepítések igénylik manuális végrehajtását. 

    DevOps Projects beállítja a véletlenszerű SQL-jelszót, és a kiadási folyamathoz használja azt.
    
1. A bal oldalon válassza a **változók**lehetőséget. 

   > [!NOTE]
   > Csak akkor hajtsa végre a következő lépést, ha módosította a SQL Server jelszavát. Egyetlen jelszó-változó van.
  
1. Az **érték** mező mellett válassza a lakat ikont, adja meg az új jelszót, majd kattintson a **Mentés**gombra.

1. A bal oldalon válassza a **feladatok**lehetőséget, majd válassza ki a környezetet.  
    A tevékenységek a telepítési folyamat által végrehajtandó tevékenységek, és fázisokban vannak csoportosítva. Ez a kiadási folyamat egyetlen fázissal rendelkezik, amely egy *Azure app Service üzembe helyezési* és *Azure SQL Database üzembe helyezési* feladatot tartalmaz.

1. Válassza az *Azure SQL* -feladat végrehajtása lehetőséget, és vizsgálja meg az SQL-telepítéshez használt különböző tulajdonságokat.  
    A **központi telepítési csomag**területen a feladat egy *SQL-DACPAC* fájlt használ.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás**gombra.  
     Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**.  
     Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **Naplókat**.  
     A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban 

 > [!NOTE]
 > Az alábbi eljárással tesztelheti a CI/CD folyamatot egy egyszerű szöveges módosítással. Az SQL telepítési folyamatának teszteléséhez beállíthatja, hogy SQL Server séma módosítható legyen a táblára.

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A git-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. A kód módosításai kezdeményezik a CI/CD folyamatot, és automatikusan telepítik a módosításokat az Azure-ba.

1. A bal oldali panelen válassza a **kód**lehetőséget, majd nyissa meg a tárházat.

1. Nyissa meg a *SampleWebApplication\Views\Home* könyvtárat, válassza az *index. cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés**lehetőséget. 

1. Végezze el a fájl módosítását, például a div-címkék egyikén belüli szöveg hozzáadását. 

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.**  
    Néhány pillanat elteltével a buildek az Azure DevOps-ben kezdődnek, és a kiadásokat a rendszer a módosítások üzembe helyezésével hajtja végre. Figyelje a Build állapotát a DevOps Projects irányítópulton vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="connect-to-the-azure-sql-database"></a>Kapcsolódás az Azure SQL Database-hez

Megfelelő engedélyekkel kell rendelkeznie az Azure SQL Database-adatbázishoz való kapcsolódáshoz.

1. Az DevOps Projects irányítópulton válassza a **SQL Database** lehetőséget az SQL Database felügyeleti lapjára való ugráshoz.
   
1. Válassza a **kiszolgáló tűzfal beállítása**lehetőséget, majd kattintson az **ügyfél IP-** címének hozzáadása elemre. 

1. Kattintson a **Mentés** gombra.  
    Az ügyfél IP-címe mostantól hozzáfér a SQL Server Azure-erőforráshoz.

1. Lépjen vissza a **SQL Database** ablaktáblára. 

1. A jobb oldalon válassza ki a kiszolgáló nevét, és navigáljon a **SQL Server**konfigurációs lapjára.

1. Válassza a **jelszó alaphelyzetbe állítása**lehetőséget, adjon meg egy jelszót a SQL Server rendszergazdai bejelentkezéshez, majd kattintson a **Mentés**gombra.  
    Ügyeljen rá, hogy ezt a jelszót ne használja az oktatóanyag későbbi részében.

    A SQL Serverhoz és az Azure SQL Database-hez való kapcsolódáshoz igény szerint használhatja a SQL Server Management Studio vagy a Visual Studio eszközt is. A kapcsolódáshoz használja a **Kiszolgáló neve** tulajdonságot.

    Ha nem módosította az adatbázis-felhasználónevet, amikor először konfigurálta a projektet DevOps Projectsban, a Felhasználónév az e-mail cím helyi része. Ha például az e-mail-címe *johndoe\@microsoft.com*, a Felhasználónév a *johndoe*.

   > [!NOTE]
   > Ha megváltoztatja a jelszavát az SQL-bejelentkezéshez, módosítania kell a kiadási folyamat változóban található jelszót a "CD-folyamat vizsgálata" című szakaszban leírtak szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a számlázási díjak felmerülését az erőforrások tisztításával. Ha már nincs rájuk szükség, törölheti az oktatóanyagban létrehozott Azure SQL Database-adatbázist és kapcsolódó erőforrásokat. Ehhez használja a **Törlés** funkciót a DevOps projects irányítópulton.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrásokat. A *delete* funkció megsemmisíti a projekt által az Azure-ban és az Azure-DevOps található DevOps projects által létrehozott összes adatát, és nem fogja tudni beolvasni. Ezt az eljárást csak akkor használja, ha alaposan elolvasta a kérdéseit.

1. A Azure Portal nyissa meg a DevOps Projects irányítópultot.
2. A jobb felső sarokban válassza a **Törlés**lehetőséget. 
3. A parancssorban válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez* .

## <a name="next-steps"></a>További lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A ASP.NET-alkalmazás és a Azure SQL Database-kód üzembe helyezése a Azure DevOps Projects használatával
> * Az Azure DevOps és az Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban
> * Kapcsolódás az Azure SQL Database-hez 
> * Az erőforrások eltávolítása

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
