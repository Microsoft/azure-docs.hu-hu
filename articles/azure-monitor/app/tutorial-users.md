---
title: Az ügyfelek megismerése az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogy az Azure Application Insights használatával hogyan tudhatja meg, miként használják az ügyfelek az alkalmazását.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: 4f6eeb5265d99a17ed18ace91fa5f2d2f744dbb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627558"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Az alkalmazás ügyfelek általi használatának megismerése az Azure Application Insights használatával

Az Azure Application Insights használati adatokat gyűjt, amely alapján megismerheti, hogyan használják a felhasználók az alkalmazást.  Az oktatóanyag lépésről lépésre bemutatja a különböző rendelkezésre álló erőforrásokat, amelyekkel ezek az információk elemezhetők.  Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Az alkalmazást használó felhasználókkal kapcsolatos adatok elemzése
> * Az alkalmazás felhasználók általi használatának elemzése munkamenet-információk alapján
> * Tölcsérek definiálása a várt és a tényleges felhasználói tevékenységek összevetéséhez 
> * Vizualizációk és lekérdezések összerendelése egyetlen dokumentumba munkafüzetek létrehozásával
> * Hasonló felhasználók együttes elemzése csoportba rendezéssel
> * Az alkalmazás visszatérő felhasználóinak megismerése
> * A felhasználók alkalmazáson belüli mozgásának vizsgálata


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a következő munkaterhelésekkel:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Töltse le és telepítse a [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) alkalmazást.
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../app/asp-net.md)-t. 
- [Küldjön telemetriát az alkalmazásból](../app/usage-overview.md#send-telemetry-from-your-app) egyéni események/oldalmegtekintések hozzáadásához
- Küldjön [felhasználói környezetet](../app/usage-send-user-context.md) a felhasználók tevékenységeinek időbeli nyomon követéséhez és a használati funkciók teljes körű kiaknázásához.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .

## <a name="get-information-about-your-users"></a>A felhasználókkal kapcsolatos információk lekérése
A **Felhasználók** panelen különböző módokon fontos részleteket tudhat meg a felhasználókról. A panelen megtudhatja például, hogy a felhasználók honnan és milyen ügyfélről csatlakoznak, és hogy az alkalmazás mely területeit tekintik meg. 

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.
2. Kattintson a **Felhasználók** elemre a menüben.
3. Az alapértelmezett nézet az elmúlt 24 órában az alkalmazáshoz csatlakozó egyed felhasználók számát mutatja.  Az időablak módosítható, és az adatok különböző feltételek mentén szűrhetők.

    ![Lekérdezéskészítő](media/tutorial-users/QueryBuilder.png)

6. Kattintson az **Időszak** legördülő menüre, és módosítsa az időablakot 7 napra.  Ez növeli a panelen lévő grafikonokon megjelenített adatok mennyiségét.

    ![Időtartomány módosítása](media/tutorial-users/TimeRange.png)

4. A **Felosztási szempont** legördülő menüben kiválaszthat egy felhasználói tulajdonságot, amely alapján a grafikon le lesz bontva.  Válassza az **Ország vagy régió** lehetőséget.  A gráf ugyanazokat az adatmennyiségeket tartalmazza, de lehetővé teszi, hogy megtekintse az egyes országok/régiók felhasználói számának részletezését.

    ![Ország vagy régió grafikon](media/tutorial-users/CountryorRegion.png)

5. Vigye a kurzort a diagram különböző bárjai fölé, és vegye figyelembe, hogy az egyes országok/régiók száma csak az adott sáv által jelzett időablakot tükrözi.
6. Tekintse meg a jobb oldalon az **Elemzések** oszlopot, amely az adatokon végrehajtott elemzéseket tartalmazza.  Ez az időtartomány során bejelentkezett egyedi munkamenetek számát tartalmazza, valamint a gyakori tulajdonságokkal rendelkező rekordokat, amelyek a felhasználói adatok jelentős hányadában szerepelnek. 

    ![Elemzések oszlop](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Felhasználói munkamenetek elemzése
A **Munkamenetek** panel a **Felhasználók** panelhez hasonló.  Míg a **Felhasználók** panel az alkalmazást használó felhasználókkal kapcsolatos információkat mutatja, a **Munkamenetek** panel azt segít megérteni, hogyan használják a felhasználók az alkalmazást.  

1. Kattintson a **Munkamenetek** elemre a menüben.
2. Láthatja, hogy ezen a grafikonon is ugyanazok a szűrési és bontási lehetőségek érhetők el, mint a **Felhasználók** panelen.

    ![Munkamenetek lekérdezéskészítő](media/tutorial-users/SessionsBuilder.png)

3. A jobb oldalon a **Minta ezekből a munkamenetekből** panel azokat a munkameneteket listázza, amelyek nagy számú eseményt tartalmaznak.  Ezek elemzésre érdemes munkamenetek.

    ![Minta ezekből a munkamenetekből](media/tutorial-users/SessionsSample.png)

4. Az egyes munkamenetekre kattintva tekinthető meg a **Munkamenet idővonala**, amely a munkamenet során végrehajtott műveleteket mutatja.  Így azonosíthatók például a nagy számú kivételt eredményező munkamenetek.

    ![Munkamenetek idővonala](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Hasonló felhasználók csoportosítása
Az **kohorsz** a hasonló jellemzőkkel rendelkező felhasználók csoportja.  A kohorszok segítségével a többi panelen szűrheti az adatokat, így az elemzéseket adott felhasználói csoportokra szűkítheti.  Például elképzelhető, hogy csak az olyan felhasználókat szeretné elemezni, akik vásároltak is.

1.  Kattintson a **Kohorsz** elemre a menüben.
2.  Kattintson az **Új** gombra egy új kohorsz létrehozásához.
3.  Kattintson a **Használók** legördülő menüre, és válasszon egy műveletet.  A jelentés csak azokat a felhasználókat tartalmazza majd, akik az adott műveletet használták az időablakban.

    ![Megadott műveleteket használó kohorsz](media/tutorial-users/CohortsDropdown.png)

4.  Kattintson a **Felhasználók** elemre a menüben.
5.  A **Megjelenítés** legördülő menüben válassza ki az imént létrehozott kohorszt.  A grafikonon csak az ehhez tartozó felhasználók adatai jelennek meg.

    ![Kohorsz a Felhasználók eszközben](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Várt és tényleges tevékenységek összevetése
Míg az eddigi panelek arra összpontosítottak, hogy mit tettek az alkalmazás felhasználói, a **Tölcsérek** arra összpontosít, hogy mit szeretne, ha a felhasználók tennének.  A tölcsér a lépések egy halmazát jelzi az alkalmazásban, valamint hogy a felhasználók hány százaléka követi ezeket a lépéseket.  Például létrehozhat egy tölcsért, amely azt méri, hogy az alkalmazást használó felhasználók hány százaléka keres rá egy termékre.  Ezután megtekintheti, hogy a felhasználók hány százaléka helyezte a terméket a kosarába, valamint hogy hány százalékuk vásárolta is meg a terméket.

1. Kattintson a menüben a **Tölcsérek** elemre, majd kattintson az **Új** gombra. 

    ![Képernyőfelvétel: új tölcsér létrehozása.](media/tutorial-users/funnelsnew.png)

2. Adja meg a **Tölcsér nevét**.
3. Hozzon létre egy tölcsért két lépéssel, mindkét lépéshez kiválasztva egy műveletet.  A műveletek listája az Application Insights által gyűjtött használati adatok alapján áll össze.

    ![A lépések új tölcsérben való létrehozásának módját bemutató képernyőkép.](media/tutorial-users/funnelsedit.png)

4. A **Mentés** gombra kattintva mentheti a tölcsért és tekintheti meg az eredményeket.  A tölcsér jobb oldalán lévő ablakban az első tevékenység előtti és az utolsó tevékenység utáni események láthatók, így könnyebben megértheti az adott sorozat körüli felhasználói trendeket.

    ![Képernyőkép az újonnan létrehozott tölcsér esemény-eredményeiről.](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>A visszatérő ügyfelek megismerése
A **Megtartás** panelen megtudhatja, hogy mely felhasználók térnek vissza az alkalmazásba.  

1. Kattintson a **Megtartás** elemre a menüben.
2. Alapértelmezés szerint az elemzés azokat a felhasználókat tartalmazza, akik végrehajtottak valamilyen műveletet, majd visszatértek, és megint végrehajtottak valamilyen művelet.  Ennek a szűrőnek az átállításával például szűkítheti az elemzést azokra a felhasználókra, akik egy sikeres vásárlás után térnek vissza.

    ![Az adatmegőrzési szűrő beállítását bemutató képernyőkép.](media/tutorial-users/retentionquery.png)

3. A feltételeknek megfelelő visszatérő felhasználók grafikus és táblázatos formában jelennek meg az egyes időintervallumokra vetítve.  A jellegzetes minta szerint a visszatérő felhasználók száma idővel fokozatosan csökken.  Ha a mennyiség egyik időszakról a következőre hirtelen esik, ez okot adhat aggodalomra. 

    ![Képernyőfelvétel: az adatmegőrzési szűrőhöz megadott feltételeknek megfelelő felhasználók gráfját ábrázoló ábra.](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>A felhasználók mozgásának elemzése
A **Felhasználókövetés** megjeleníti, hogy a felhasználók hogyan mozognak az alkalmazás lapjai és funkciói között.  Ennek segítségével választ kaphat például az olyan kérdésekre, hogy a felhasználók általában hová lépnek egy adott oldalról, általában hogyan lépnek ki az alkalmazásból, illetve hogy vannak-e olyan műveletek, amelyek általában ismétlődnek.

1.  Kattintson a **Felhasználókövetés** elemre a menüben.
2.  Az **Új** gombra kattintva hozzon létre egy új felhasználókövetést, majd a **Szerkesztés** gombra kattintva szerkessze a részleteket.
3.  Növelje az **Időtartományt** 7 napra, majd állítson be egy kezdőeseményt.  A követés azokat a felhasználói munkameneteket követi majd, amelyek az adott eseménnyel indulnak.

    ![Képernyőfelvétel: új felhasználói folyamat létrehozása.](media/tutorial-users/flowsedit.png)

4.  Megjelenik a felhasználókövetés, amelyen a különböző felhasználói útvonalak és az azokhoz tartozó munkamenetek száma látható.  A kék vonalak azokat a műveleteket jelzik, amelyeket a felhasználó az aktuális művelet után hajtott végre.  A vörös vonalak a felhasználói munkamenetek végét jelzik.

    ![Képernyőfelvétel: a felhasználói útvonalak és a munkamenetek számának megjelenítése egy felhasználói folyamat esetében.](media/tutorial-users/flows.png)

5.  Ha el szeretne távolítani egy eseményt a követésből, kattintson az **x** ikonra a művelet sarkában, majd kattintson a **Grafikon létrehozása** gombra.  Az újrarajzolt grafikon már nem tartalmazza az eltávolított esemény példányait.  A **Szerkesztés** gombra kattintva láthatja, hogy az esemény felkerült a **Kizárt események** listájára.

    ![Képernyőfelvétel a felhasználói folyamat kizárt eseményeinek listájáról.](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Használati adatok összesítése
A **munkafüzetek** adatvizualizációkat, Analytics-lekérdezéseket és szövegeket egyesítő interaktív dokumentumok.  A munkafüzetek használatával csoportba foglalhatók a gyakori használati információk, összesíthetők egy adott incidensre vonatkozó információk, vagy visszajelzés küldhető a csapat számára az alkalmazás használatáról.

1.  Kattintson a **Munkafüzetek** elemre a menüben.
2.  Kattintson az **Új** gombra egy új munkafüzet létrehozásához.
3.  A rendszer felkínál egy lekérdezést, amely az utolsó napi összes használati adatot sávdiagram formájában jeleníti meg.  Használhatja ezt a lekérdezést, manuálisan szerkesztheti, vagy a **Mintalekérdezések** gombra kattintva további hasznos lekérdezések közül is választhat.

    ![Képernyőfelvétel: a használható példák listáját jeleníti meg.](media/tutorial-users/samplequeries.png)

4.  Kattintson a **Szerkesztés kész** gombra.
5.  A panel tetején a **Szerkesztés** gombra kattintva szerkesztheti a munkafüzet tetején lévő szöveget.  Ez Markdown-szerkesztővel formázható.

    ![A munkafüzet tetején található szöveg szerkesztését bemutató képernyőkép.](media/tutorial-users/markdown.png)

6.  A **Felhasználók hozzáadása** gombra kattintva adhat hozzá egy grafikont a felhasználóinformációkkal.  Igény szerint szerkessze a grafikon részleteit, majd kattintson a **Szerkesztés kész** gombra a mentéséhez.


## <a name="next-steps"></a>Következő lépések
Most, hogy elsajátította, hogyan elemezheti a felhasználókat, térjen át a következő oktatóanyagra, amelyből elsajátíthatja az egyéni irányítópultok létrehozását, amelyek ezeket az információkat az alkalmazással kapcsolatos további hasznos adatokkal kombinálják.

> [!div class="nextstepaction"]
> [Egyéni irányítópultok létrehozása](./tutorial-app-dashboards.md)

