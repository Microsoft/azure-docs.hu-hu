---
title: Application Insights Telemetria a Visual Studio CodeLensben | Microsoft Docs
description: A CodeLens használatával gyorsan elérheti az Application Insights-kérést és a kivételtelemetriát a Visual Studióban.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2cfc4e06dcb5671f34ff98794c9569b6ed5ddae7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94538495"
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Application Insights Telemetria a Visual Studio CodeLensben
A telemetriával megjegyzések fűzhetők a webalkalmazás kódjában szereplő metódusokhoz a futásidejű kivételekkel és a kérések válaszidejével kapcsolatban. Ha telepíti az [Azure Application Insights-t](./app-insights-overview.md) az alkalmazásban, a telemetria megjelenik a Visual Studio [CodeLensben](/visualstudio/ide/find-code-changes-and-other-history-with-codelens?view=vs-2015) – az egyes függvények felett láthatók a jegyzetek, ahol korábban a függvény hivatkozási helyeinek száma, a legutóbbi szerkesztő neve vagy egyéb hasznos információk jelentek meg.

![CodeLens](./media/visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> Az Application Insights a CodeLensben a Visual Studio 2015 Update 3 vagy újabb verzióban, illetve a [Developer Analytics Tools bővítmény](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) legújabb verziójában érhető el. A CodeLens elérhető a Visual Studio Enterprise és Professional kiadásaiban.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Az Application Insights-adatok megkeresése
Az Application Insights Telemetriát a webalkalmazás nyilvános kérési metódusainak CodeLens-jelzőiben találja. A CodeLens-jelzők a C#- és a Visual Basic-kódokban a metódus és egyéb deklarációk fölött jelennek meg. Ha az Application Insights-adatok elérhetők egy metódushoz, akkor a kérések és kivételek jelzőit fogja látni, például „100 kérés, 1% meghiúsult” vagy „10 kivétel”. További információkért kattintson a CodeLens-jelzőre. 

> [!TIP]
> Előfordulhat, hogy az Application Insights kérés- és kivételjelzői néhány másodperccel a CodeLens-jelzők után jelennek meg.
> 
> 

## <a name="exceptions-in-codelens"></a>Kivételek a CodeLensben
![A képernyőképen a Codelensben megjelenített 47-es kivételek láthatók.](./media/visual-studio-codelens/codelens-exceptions.png)

A CodeLens-kivételjelző azt jeleníti meg, hogy az alkalmazás az elmúlt 24 óra alatti 15 leggyakoribb kivétele hányszor fordult elő az adott metódus által kiszolgált kérés feldolgozása közben.

További részletekért kattintson a CodeLens-kivételjelzőre:

* A kivételek számának százalékos változása az elmúlt 24 órából az azt megelőző 24 órához képest
* A **Go to code** (Ugrás a kódra) paranccsal a kivételt kiváltó függvény forráskódjához navigálhat
* A **Search** (Keresés) használatával a kivétel összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Trend** segítségével megtekintheti a kivétel előfordulási trendjének vizualizációját az elmúlt 24 órából
* A **View all exceptions in this app** (Minden kivétel megtekintése az alkalmazásban) paranccsal a kivétel összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Explore exception trends** (Kivételtrendek böngészése) paranccsal megtekintheti az összes kivétel előfordulási trendjeiről készült vizualizációt az elmúlt 24 órából. 

> [!TIP]
> Ha a „0 exceptions” (0 kivétel) üzenetet látja a CodeLensben, de tudja, hogy kell lennie kivételnek, akkor ellenőrizze, hogy a megfelelő Application Insights-erőforrást választotta-e ki a CodeLensben. Másik erőforrás kiválasztásához kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza az **Application Insights > Choose Telemetry Source** (Application Insights > Telemetriaforrás kiválasztása) lehetőséget. A CodeLens csak az alkalmazásban az elmúlt 24 órában leggyakrabban előforduló 15 kivételnél jelenik meg, ezért ha a kivétel nincs a 15 leggyakoribb kivétel között, akkor a „0 exceptions” (0 kivétel) üzenet jelenik meg. Az ASP.NET-nézetek kivételei nem jelennek meg azokon a vezérlőmetódusokon, amelyek az adott nézeteket létrehozták.
> 
> [!TIP]
> Ha a „? exceptions” (? kivétel) üzenet jelenik meg a CodeLensben, akkor Azure-fiókját társítania kell a Visual Studióhoz, vagy lejárt az Azure-fiók hitelesítő adatainak érvényessége. Mindkét esetben kattintson a „? exceptions” (? kivétel) üzenetre, majd válassza az **Add an account...** (Fiók hozzáadása) lehetőséget, és írja be hitelesítő adatait.
> 
> 

## <a name="requests-in-codelens"></a>Kérések a CodeLensben
![A képernyőképen az 684-kérelmek részleteit jeleníti meg, beleértve a 7%-os hibát.](./media/visual-studio-codelens/codelens-requests.png)

A CodeLens-kérésjelző azon HTTP-kérések számát mutatja, amelyeket egy metódus kiszolgált az elmúlt 24 órában, valamint a meghiúsult kérések százalékos arányát.

További részletekért kattintson a CodeLens-kérésjelzőre:

* A kérések és meghiúsult kérések számának változása és a változás százalékos aránya, valamint az átlagos válaszidők az elmúlt 24 óra során az azt megelőző 24 órához képest
* A metódus megbízhatósága az elmúlt 24 órában nem meghiúsult kérések százalékos aránya alapján
* A **Search** (Keresés) használatával a kérések és meghiúsult kérések összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Trend** segítségével megtekintheti a kérések, meghiúsult kérések vagy átlagos válaszidők trendjének vizualizációját az elmúlt 24 órából.
* A CodeLens-adatok forrásának módosításához válassza ki az Application Insights-erőforrás nevét a CodeLens részletes nézetének bal felső sarkában.

## <a name="next-steps"></a><a name="next"></a>Következő lépések
* **[A Application Insights használata a Visual Studióban](./visual-studio.md)**. Telemetriát kereshet, adatokat tekinthet meg a CodeLensben és konfigurálhatja az Application Insights alkalmazást. Mindezt a Visual Studión belül. 
* **[A Application Insights portál használata](./overview-dashboard.md)**. Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. 

