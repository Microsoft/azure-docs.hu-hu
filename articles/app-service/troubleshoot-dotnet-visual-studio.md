---
title: Hibaelhárítás a Visual Studióval
description: Megtudhatja, hogyan lehet elhárítani egy App Service alkalmazást a Visual Studio 2013-be beépített távoli hibakeresési, nyomkövetési és naplózási eszközök használatával.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 65eb4e8fefb74219c927fe507baceca7c55462be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095721"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Alkalmazások hibakeresése Azure App Service a Visual Studióval
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt mutatja be, hogyan használható a Visual Studio Tools a [app Service](./overview.md)alkalmazás hibakeresési [módban](/visualstudio/debugger/) történő futtatásával, vagy az alkalmazás-naplók és a webkiszolgáló-naplók megtekintésével.

Az oktatóanyagból a következőket sajátíthatja el:

* Az App Management funkciói a Visual Studióban érhetők el.
* A Visual Studio távoli nézetének használata a gyors módosítások végrehajtásához egy távoli alkalmazásban.
* Hibakeresési mód távoli futtatása, miközben egy projekt fut az Azure-ban, mind az alkalmazás, mind a Webjobs esetében.
* Alkalmazás-nyomkövetési naplók létrehozása és megtekintése az alkalmazás létrehozása közben.
* Webkiszolgáló-naplók megtekintése, beleértve a részletes hibaüzeneteket és a sikertelen kérelmek nyomkövetését.
* Diagnosztikai naplók küldése Azure Storage-fiókba, és azok megtekintése.

Ha rendelkezik a Visual Studio Ultimate-vel, a hibakereséshez használhatja a [IntelliTrace](/visualstudio/debugger/intellitrace) is. Ebben az oktatóanyagban nem szerepel a IntelliTrace.

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag a fejlesztői környezettel, a webes projekttel és App Service alkalmazással működik, amelyet a [ASP.NET-alkalmazás létrehozása](quickstart-dotnet-framework.md)című cikkben állított be Azure app Service. A webjobs szakaszban szüksége lesz az [Azure WEBJOBS SDK-val való ismerkedéshez][GetStartedWJ]létrehozott alkalmazásra.

Az oktatóanyagban látható mintakód egy C# MVC-webalkalmazásra mutat, de a hibaelhárítási eljárások ugyanazok Visual Basic és Web Forms alkalmazásokhoz.

Az oktatóanyag feltételezi, hogy a Visual Studio 2019-et használja. 

A folyamatos átviteli naplók funkció csak a .NET-keretrendszer 4-es vagy újabb verzióját használó alkalmazásokhoz használható.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Alkalmazások konfigurálása és kezelése
A Visual Studio hozzáférést biztosít az alkalmazás-felügyeleti függvények és a [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715)elérhető konfigurációs beállítások egy részhalmazához. Ebben a szakaszban láthatja, hogy mi érhető el a **Server Explorer** használatával. A legújabb Azure-integrációs funkciók megtekintéséhez próbálja ki a **Cloud Explorert** is. A **nézet** menüből is megnyithatja a Windowst.

1. Ha még nem jelentkezett be az Azure-ba a Visual Studióban, kattintson a jobb gombbal az **Azure** elemre, és válassza a kapcsolódás **Microsoft Azure előfizetéshez** a **Server Explorerben** lehetőséget.

    Egy másik lehetőség egy olyan felügyeleti tanúsítvány telepítése, amely lehetővé teszi a fiókhoz való hozzáférést. Ha a tanúsítvány telepítését választja, kattintson a jobb gombbal az **Azure** -csomópontra a **Server Explorerben**, majd válassza az **Előfizetések kezelése és szűrése** lehetőséget a helyi menüben. Az **Microsoft Azure-előfizetések kezelése** párbeszédpanelen kattintson a **tanúsítványok** lapra, majd az **Importálás** elemre. Az Azure-fiókhoz tartozó előfizetési fájl (más néven *. publishsettings* fájl) letöltéséhez és importálásához kövesse az utasításokat.

   > [!NOTE]
   > Ha letölt egy előfizetési fájlt, mentse a forráskód-címtárakon kívüli mappába (például a letöltések mappában), majd törölje az importálás befejezése után. Az előfizetési fájl elérését elérő rosszindulatú felhasználó szerkesztheti, létrehozhatja és törölheti az Azure-szolgáltatásokat.
   >
   >

    További információ az Azure-erőforrásokhoz való csatlakozásról a Visual studióból: [Azure-szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).
2. A **Server Explorerben** bontsa ki az **Azure** elemet, majd bontsa ki a **app Service** elemet.
3. Bontsa ki azt az erőforráscsoportot, amely magában foglalja a [ASP.NET-alkalmazás létrehozása Azure app Serviceban](quickstart-dotnet-framework.md)létrehozott alkalmazást, majd kattintson a jobb gombbal az alkalmazás csomópontjára, majd kattintson a **beállítások megtekintése** elemre.

    ![Beállítások megtekintése a Server Explorerben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Megjelenik az **Azure-webalkalmazás** lap, amelyen láthatja, hogy a Visual Studióban milyen alkalmazás-felügyeleti és konfigurációs feladatok érhetők el.

    ![Azure-webalkalmazások ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Ebben az oktatóanyagban a naplózási és nyomkövetési legördülő menüket fogjuk használni. Távoli hibakeresést is használhat, de más módszert is használhat az engedélyezéséhez.

    Az ebben az ablakban található Alkalmazásbeállítások és a kapcsolatok karakterláncait tartalmazó mezőkkel kapcsolatos információkért lásd [: Azure app Service: az alkalmazás-karakterláncok és a kapcsolatok karakterláncok működése](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Ha olyan app Management-feladatot szeretne végrehajtani, amely ebben az ablakban nem hajtható végre, kattintson a **Megnyitás itt felügyeleti portál** lehetőségre egy böngészőablaknak a Azure Portal való megnyitásához.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Alkalmazások fájljainak elérése a Server Explorerben
Általában egy webes projektet helyez üzembe a `customErrors` jelölővel a Web.config fájlban a vagy a értékre `On` `RemoteOnly` , ami azt jelenti, hogy nem kap hasznos hibaüzenetet, ha valami hiba lép fel. Sok hiba esetén az alábbihoz hasonló oldal jelenik meg:

**Kiszolgálóhiba a következő alkalmazásban: "/".**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="A webböngészőben a &quot;/&quot; alkalmazáshiba miatt kiszolgálóhiba jelenik meg.":::

**Hiba történt:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Képernyőkép, amely egy webböngészőben előforduló általános hiba példáját mutatja be.":::

**A webhely nem tudja megjeleníteni a lapot**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Képernyőfelvétel: a webhely nem tudja megjeleníteni az oldal hibáját egy webböngészőben.":::

A hiba okának megállapításához gyakran a legegyszerűbb módszer a részletes hibaüzenetek engedélyezése, amely az előző képernyőképek első lépéseit ismerteti. Ehhez szükség van az üzembe helyezett Web.config fájl módosítására. Szerkesztheti a *Web.config* fájlt a projektben, és újból üzembe helyezheti a projektet, vagy létrehozhat egy [Web.config átalakítót](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) és üzembe helyezhet egy hibakeresési buildet, de gyorsabb módja van: a **Megoldáskezelőben** közvetlenül megtekintheti és szerkesztheti a távoli alkalmazás fájljait a *távoli nézet* funkció használatával.

1. A **Server Explorerben** bontsa ki az **Azure** elemet, bontsa ki a **app Service** elemet, bontsa ki azt az erőforráscsoportot, amelyben az alkalmazás található, majd bontsa ki a csomópontot az alkalmazáshoz.

    Olyan csomópontok jelennek meg, amelyek hozzáférést biztosítanak az alkalmazás tartalmi fájljaihoz és naplófájljaihoz.
2. Bontsa ki a **fájlok** csomópontot, majd kattintson duplán a *Web.config* fájlra.

    ![Web.config megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    A Visual Studio megnyitja a Web.config fájlt a távoli alkalmazásból, és a címsorban a fájlnév mellett a [távoli] ikont jeleníti meg.
3. Adja hozzá a következő sort a `system.web` elemhez:

    `<customErrors mode="Off"></customErrors>`

    ![Web.config szerkesztése](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Frissítse a böngészőt, amely a nem hasznos hibaüzenetet jeleníti meg, és most egy részletes hibaüzenetet kap, például a következő példát:

    ![Részletes hibaüzenet](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (A megjelenő hiba a pirosról *Views\Home\Index.cshtml*-ra mutatott sor hozzáadásával jött létre.)

A Web.config fájl szerkesztése csak egy példa arra, hogy a App Service alkalmazásban található fájlok olvasása és szerkesztése megkönnyítse a hibaelhárítást.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Távoli hibakeresési alkalmazások
Ha a részletes hibaüzenet nem tartalmaz elegendő információt, és nem tudja helyileg létrehozni a hibát, a hibaelhárítás egy másik módja a hibakeresési mód távoli futtatása. Megadhatja a töréspontokat, megváltoztathatja a memóriát közvetlenül, beléphet a kód fölé, és akár módosíthatja is a kód elérési útját.

A távoli hibakeresés nem működik a Visual Studio Express kiadásaiban.

Ez a szakasz bemutatja, hogyan lehet távolról hibakeresést végezni a [ASP.NET-alkalmazás létrehozása Azure app Serviceban](quickstart-dotnet-framework.md)létrehozott projekt használatával.

1. Nyissa meg a [ASP.NET alkalmazás létrehozása Azure app Serviceban](quickstart-dotnet-framework.md)létrehozott webes projektet.

1. Nyissa meg a *Controllers\HomeController.cs*.

1. Törölje a `About()` metódust, és szúrja be a következő kódot a helyére.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Töréspont beállítása](/visualstudio/debugger/) a `ViewBag.Message` sorban.

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, majd kattintson a **Közzététel** elemre.

1. A **profil** legördülő listában válassza ki ugyanazt a profilt, amelyet a [ASP.NET-alkalmazás létrehozásakor használt Azure app Serviceban](quickstart-dotnet-framework.md). Ezután kattintson a beállítások elemre.

1. A **Közzététel** párbeszédpanelen kattintson a **Beállítások** fülre, majd módosítsa a **konfigurációt** **hibakeresésre**, majd kattintson a **Mentés** gombra.

    ![Közzététel hibakeresési módban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Kattintson a **Közzététel** gombra. Miután az üzembe helyezés befejeződött, és a böngésző megnyílik az alkalmazás Azure URL-címével, akkor zárjuk be a böngészőt.

1. A **Server Explorerben** kattintson a jobb gombbal az alkalmazásra, majd kattintson a **hibakereső csatolása** parancsra.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Képernyőfelvétel a Server Explorer ablakáról, amely egy kiválasztott alkalmazást mutat be, majd a hibakereső csatolása lehetőségre kattint.":::

    A böngésző automatikusan megnyílik az Azure-ban futó kezdőlapon. Előfordulhat, hogy várnia kell 20 másodpercet, amíg az Azure beállítja a kiszolgálót a hibakereséshez. Ez a késleltetés csak akkor történik meg, amikor az első alkalommal hibakeresési módban futtatja az alkalmazást egy 48 órás időszakban. Ha ugyanezen az időszakon belül újra elindítja a hibakeresést, nincs késés.

    > [!NOTE] 
    > Ha bármilyen probléma merül fel a hibakereső indításakor, próbálja meg a **Server Explorer** helyett a **Cloud Explorer** használatával elvégezni.
    >

1. A menüben kattintson a **Névjegy** elemre.

    A Visual Studio leáll a törésponton, és a kód az Azure-ban fut, nem pedig a helyi számítógépen.

1. Vigye az egérmutatót a változó fölé a `currentTime` Time érték megjelenítéséhez.

    ![Változó megtekintése az Azure-ban futó hibakeresési módban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    A megjelenő időpont az Azure-kiszolgáló ideje, amely a helyi számítógéptől eltérő időzónában lehet.

1. Adjon meg egy új értéket a `currentTime` változóhoz, például: "most már fut az Azure-ban".

1. Nyomja le az F5 billentyűt a Futtatás folytatásához.

     Az Azure-ban futó About (Névjegy) oldal megjeleníti a currentTime változóban megadott új értéket.

     ![Az új értékkel rendelkező oldal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> Webjobs-feladatok távoli hibakeresése
Ez a szakasz azt mutatja be, hogyan lehet távolról hibakeresést végezni a projekt és az alkalmazás használatával, amelyet a [Azure WEBJOBS SDK használatának első lépéseiben](https://github.com/Azure/azure-webjobs-sdk/wiki)hozott létre.

Az ebben a szakaszban látható funkciók csak a 4. vagy újabb frissítéssel rendelkező Visual Studio 2013-ben érhetők el.

A távoli hibakeresés csak a folyamatos webjobs-feladatok esetében működik. Az ütemezett és igény szerinti webjobs nem támogatja a hibakeresést.

1. Nyissa meg az [Azure WEBJOBS SDK-val való ismerkedéskor][GetStartedWJ]létrehozott webes projektet.

2. A ContosoAdsWebJob projektben nyissa meg a *functions. cs*.

3. [Állítsa be a töréspontot](/visualstudio/debugger/) a metódus első utasítására `GnerateThumbnail` .

    ![Töréspont beállítása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. **Megoldáskezelő** kattintson a jobb gombbal a webes projektre (ne a webjobs projektre), majd kattintson a **Közzététel** elemre.

5. A **profil** legördülő listában válassza ki ugyanazt a profilt, amelyet az első lépések során használt [a Azure WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kattintson a **Beállítások** fülre, és módosítsa a **konfigurációt** **hibakeresésre**, majd kattintson a **Közzététel** elemre.

    A Visual Studio üzembe helyezi a webes és Webjobs projekteket, és megnyílik a böngészője az alkalmazás Azure URL-címére.

7. A **Server Explorerben** bontsa ki az **Azure > app Service > az erőforráscsoport > az alkalmazást > webjobs > folyamatos** elemet, majd kattintson a jobb gombbal a **ContosoAdsWebJob** elemre.

8. Kattintson a **hibakereső csatolása** elemre.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Képernyőkép a ContosoAdsWebJob, amely a legördülő menüben kiválasztotta a hibakeresőt, és csatolja a Debugger elemet.":::

    A böngésző automatikusan megnyílik az Azure-ban futó kezdőlapon. Előfordulhat, hogy várnia kell 20 másodpercet, amíg az Azure beállítja a kiszolgálót a hibakereséshez. Ez a késleltetés csak akkor történik meg, amikor az első alkalommal hibakeresési módban futtatja az alkalmazást egy 48 órás időszakban. Ha ugyanezen az időszakon belül újra elindítja a hibakeresést, nincs késés.

9. Hozzon létre egy új ad-t a contoso ADS kezdőlapján megnyitott böngészőben.

    Az ad létrehozásával létrejön egy üzenetsor-üzenet, amelyet a Webjobs és a feldolgozott. Amikor a webjobs SDK meghívja a függvényt a várólista-üzenet feldolgozására, a kód eléri a töréspontot.

10. Amikor a hibakereső megszakítja a töréspontot, megvizsgálhatja és módosíthatja a változók értékeit, miközben a program futtatja a felhőt. A következő ábrán a hibakereső a metódusnak átadott blobInfo objektum tartalmát jeleníti meg `GenerateThumbnail` .

     ![blobInfo objektum a hibakeresőben](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Nyomja le az F5 billentyűt a Futtatás folytatásához.

     A `GenerateThumbnail` metódus befejezi a miniatűr létrehozását.

12. A böngészőben frissítse az index oldalt, és megtekintheti a miniatűrt.

13. A hibakeresés leállításához nyomja le a SHIFT + F5 billentyűkombinációt a Visual Studióban.

14. A **Server Explorerben** kattintson a jobb gombbal a ContosoAdsWebJob csomópontra, majd kattintson az **irányítópult megtekintése** elemre.

15. Jelentkezzen be az Azure-beli hitelesítő adataival, majd a Webjobs nevére kattintva lépjen a Webjobs oldalára.

     ![Kattintson a ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Az irányítópulton látható, hogy a `GenerateThumbnail` függvény nemrég lett végrehajtva.

     (Amikor legközelebb rákattint az **irányítópult megtekintése** lehetőségre, nem kell bejelentkeznie, és a böngésző közvetlenül a webjobs lapjára lép.)

16. Kattintson a függvény nevére a függvény végrehajtásának részleteinek megtekintéséhez.

     ![Függvény részletei](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Ha a függvény [naplókat írt](https://github.com/Azure/azure-webjobs-sdk/wiki), a **ToggleOutput** gombra kattintva megtekintheti őket.

## <a name="notes-about-remote-debugging"></a>Megjegyzések a távoli hibakereséssel kapcsolatban

* Az éles környezetben való futtatás hibakeresési módban nem ajánlott. Ha az éles alkalmazás nem méretezhető több kiszolgálópéldány számára, a hibakeresés megakadályozza, hogy a webkiszolgáló válaszoljon más kérelmekre. Ha több webkiszolgáló-példánya is van, a hibakeresőhöz való csatoláskor véletlenszerű példányt kap, és nem biztosíthatja, hogy a következő böngésző-kérelmek ugyanarra a példányra lépjenek. Azt is megteheti, hogy a hibakeresési buildet általában nem helyezi üzembe éles környezetben, és a fordítói optimalizálások nem tudták megmutatni, hogy mi történik sorban a forráskódban. Az éles környezetben felmerülő problémák elhárításához a legjobb erőforrás az alkalmazás-nyomkövetés és a webkiszolgáló-naplók.
* A távoli hibakeresés során Kerülje a töréspontok hosszú leállását. Az Azure egy olyan folyamatot kezel, amely egy nem válaszoló folyamatnál hosszabb ideig leállt, és leállítja azt.
* A hibakeresés során a kiszolgáló adatokat küld a Visual Studiónak, ami hatással lehet a sávszélességre. A sávszélesség díjszabásával kapcsolatos információkért lásd: az [Azure díjszabása](https://azure.microsoft.com/pricing/calculator/).
* Győződjön meg arról, hogy a `debug` `compilation` *Web.config* fájlban lévő elem attribútuma True (igaz) értékű. A hibakeresési Build konfigurációjának közzétételekor alapértelmezés szerint igaz értékre van állítva.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Ha úgy találja, hogy a hibakereső nem lép be a hibakereséshez használni kívánt kódra, előfordulhat, hogy módosítania kell a Saját kód beállítást.  További információ: [annak megadása, hogy csak a felhasználói kódokat kell-e hibakeresést végezni a Visual studióban saját kód használatával](/visualstudio/debugger/just-my-code).
* A Távoli hibakeresési funkció engedélyezésekor egy időzítő indul el a kiszolgálón, és 48 óra elteltével a szolgáltatás automatikusan ki lesz kapcsolva. Ez a 48 órás korlát biztonsági és teljesítménybeli okokból történik. A funkció egyszerűen visszakapcsolható, ahányszor csak szeretné. Azt javasoljuk, hogy a rendszer letiltsa a letiltást, ha nem aktívan végzi a hibakeresést.
* Manuálisan is csatlakoztathatja a hibakeresőt bármely folyamathoz, nem csak az alkalmazás folyamatához (w3wp.exe). További információ a hibakeresési mód a Visual Studióban történő használatáról: [hibakeresés a Visual Studióban](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Diagnosztikai naplók – áttekintés
Egy App Service alkalmazásban futó ASP.NET-alkalmazás a következő típusú naplókat tudja létrehozni:

* **Alkalmazás-nyomkövetési naplók**<br/>
  Az alkalmazás létrehozza ezeket a naplókat a [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) osztály metódusának meghívásával.
* **Webkiszolgáló-naplók**<br/>
  A webkiszolgáló minden HTTP-kérelemhez létrehoz egy naplóbejegyzést az alkalmazáshoz.
* **Részletes hibaüzenetek naplói**<br/>
  A webkiszolgáló egy HTML-oldalt hoz létre a sikertelen HTTP-kérelmekre vonatkozó további információkkal (a 400-as vagy újabb állapotkódot eredményező kérelmek).
* **Sikertelen kérelmek nyomkövetési naplói**<br/>
  A webkiszolgáló létrehoz egy XML-fájlt, amely részletes nyomkövetési információkat biztosít a sikertelen HTTP-kérésekhez. A webkiszolgáló egy XSL-fájlt is biztosít az XML-fájl böngészőben való formázásához.

A naplózás hatással van az alkalmazások teljesítményére, így az Azure lehetővé teszi, hogy szükség szerint engedélyezze vagy tiltsa le az egyes típusú naplókat. Az alkalmazás naplói esetében megadhatja, hogy csak bizonyos súlyossági szintű naplók legyenek megírva. Új alkalmazás létrehozásakor alapértelmezés szerint minden naplózás le van tiltva.

A naplók az alkalmazás fájlrendszerében lévő *LogFiles* mappában található fájlokra íródnak, és az FTP-n keresztül érhetők el. A webkiszolgálói naplók és az alkalmazások naplói egy Azure Storage-fiókba is írhatók. A Storage-fiókokban nagyobb mennyiségű naplót is megtarthat, mint amennyit a fájlrendszerben lehet. A fájlrendszer használata esetén legfeljebb 100 megabájt méretű naplót használhat. (A fájlrendszer naplói csak a rövid távú adatmegőrzéshez szükségesek. Az Azure törli a régi naplófájlokat, hogy a korlát elérésekor a rendszer helyet szabadítson fel újak számára.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Alkalmazás-nyomkövetési naplók létrehozása és megtekintése
Ebben a szakaszban a következő feladatokat hajtja végre:

* Vegyen fel nyomkövetési utasításokat ahhoz a webes projekthez, amelyet az [Azure és a ASP.net első lépéseiben](quickstart-dotnet-framework.md)hozott létre.
* A naplókat a projekt helyi futtatásakor tekintheti meg.
* Megtekintheti az Azure-ban futó alkalmazás által generált naplókat.

További információ az alkalmazások naplóinak a webjobs-ben való létrehozásáról: az [Azure üzenetsor-tárolás használata a Webjobs SDK használatával – naplók írása](https://github.com/Azure/azure-webjobs-sdk/wiki). A naplók megtekintésének és az Azure-ban tárolt adatok szabályozásának az alábbi lépései a webjobs által létrehozott alkalmazási naplókra is érvényesek.

### <a name="add-tracing-statements-to-the-application"></a>Nyomkövetési utasítások hozzáadása az alkalmazáshoz
1. Nyissa meg a *Controllers\HomeController.cs*, és cserélje le a `Index` , `About` , és `Contact` metódusokat a következő kódra az `Trace` utasítások és a utasítások hozzáadásához `using` `System.Diagnostics` :

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Adjon hozzá egy `using System.Diagnostics;` utasítást a fájl elejéhez.

### <a name="view-the-tracing-output-locally"></a>A nyomkövetési kimenet helyi megtekintése
1. Az alkalmazás hibakeresési módban való futtatásához nyomja le az F5 billentyűt.

    Az alapértelmezett nyomkövetési figyelő a **kimeneti** ablakba írja az összes nyomkövetési kimenetet, valamint más hibakeresési kimenetet is. A következő ábrán a metódushoz hozzáadott nyomkövetési utasítások kimenete látható `Index` .

    ![Nyomkövetés a hibakeresési ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    A következő lépések bemutatják, hogyan tekintheti meg a nyomkövetési kimenetet egy weblapon a hibakeresési módban való fordítás nélkül.
1. Nyissa meg az alkalmazás Web.config fájlt (amely a projekt mappájában található), és adjon hozzá egy `<system.diagnostics>` elemet a fájl végén közvetlenül a záró `</configuration>` elem előtt:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

A `WebPageTraceListener` lehetővé teszi a nyomkövetési kimenet megtekintését `/trace.axd` .
1. Vegyen fel egy <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">nyomkövetési elemet</a> `<system.web>` a Web.config fájlban, például a következő példában:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
1. A böngészőablak címsorában adja hozzá a *trace. axd* címet az URL-címhez, majd nyomja le az ENTER billentyűt (az URL-cím hasonló `http://localhost:53370/trace.axd` ).
1. Az **alkalmazás-nyomkövetés** lapon kattintson a **részletek megtekintése** elemre az első sorban (ne a BrowserLink sor).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Képernyőkép az alkalmazás-nyomkövetési oldalról egy webböngészőben, amely az első sorban kiválasztott részletek megjelenítését jeleníti meg.":::

    Megjelenik a **kérelem részletei** lap, és a **nyomkövetési adatok** szakaszban a metódushoz hozzáadott nyomkövetési utasítások kimenete látható `Index` .

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="A webböngészőben a kérelem részletei oldal képernyőképe, amely a nyomkövetési adatok szakaszban Kiemelt üzenetet jelenít meg.":::

    Alapértelmezés szerint `trace.axd` csak helyileg érhető el. Ha azt szeretné, hogy elérhető legyen egy távoli alkalmazásból, a `localOnly="false"` `trace` *Web.config* fájlban lévő elemhez hozzáadhatja az elemet a következő példában látható módon:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    Az `trace.axd` éles alkalmazásokban való engedélyezés azonban biztonsági okokból nem ajánlott. A következő részekben könnyebben beolvashatja a nyomkövetési naplókat egy App Service alkalmazásban.

### <a name="view-the-tracing-output-in-azure"></a>A nyomkövetés kimenetének megtekintése az Azure-ban
1. **Megoldáskezelő** kattintson a jobb gombbal a webes projektre, majd kattintson a **Közzététel** elemre.
2. A **webes közzététel** párbeszédpanelen kattintson a **Közzététel** elemre.

    Miután a Visual Studio közzétette a frissítést, megnyílik egy böngészőablak a kezdőlapon (feltéve, hogy nem törölte a **cél URL-címet** a **Kapcsolódás** lapon).
3. A **Server Explorerben** kattintson a jobb gombbal az alkalmazásra, és válassza a **folyamatos átviteli naplók megtekintése** lehetőséget.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Képernyőfelvétel a Server Explorer böngészőről a jobb gombbal az alkalmazásra kattintva, az új ablakban kiválasztott folyamatos átviteli naplók megtekintésével.":::

    A **kimeneti** ablak azt mutatja, hogy csatlakozik a naplózási szolgáltatáshoz, és minden percben olyan értesítési sort ad hozzá, amely a napló megjelenítésének hiányában leáll.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="A kimeneti ablak képernyőképe, amely példát mutat be egy, az értesítési sorokkal rendelkező log streaming szolgáltatással létesített kapcsolódásra.":::

4. Az alkalmazás kezdőlapját megjelenítő böngészőablakban kattintson a **kapcsolat** elemre.

    Néhány másodpercen belül a metódushoz hozzáadott hiba szintű nyomkövetés kimenete megjelenik `Contact` a **kimeneti** ablakban.

    ![Hibák nyomon követése a kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    A Visual Studio csak a hiba szintű nyomkövetést jeleníti meg, mert ez az alapértelmezett beállítás a naplózási szolgáltatás engedélyezésekor. Amikor új App Service alkalmazást hoz létre, a rendszer alapértelmezés szerint letiltja az összes naplózást, ahogy azt a korábbi beállítások lap megnyitásakor látta el:

    ![Alkalmazás kijelentkezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Ha azonban a **folyamatos átviteli naplók megtekintése** lehetőséget választotta, a Visual Studio automatikusan megváltoztatta az **alkalmazás naplózását (fájlrendszerét)** a **hibához**, ami azt jelenti, hogy a hiba szintű naplók beolvasása megtörtént. Ha szeretné megtekinteni az összes nyomkövetési naplót, ezt a beállítást **részletesen** is megváltoztathatja. Ha a hiba alatti súlyossági szintet választja, a magasabb súlyossági szintű naplókat is jelenteni kell. Tehát a részletes beállítás kiválasztásakor az információ, a figyelmeztetés és a hibanapló is megjelenik.  

5. A **Server Explorerben** kattintson a jobb gombbal az alkalmazásra, majd kattintson a **beállítások megtekintése** lehetőségre a korábban leírtak szerint.
6. Módosítsa az **alkalmazás naplózását (fájlrendszerét)** a **részletesre**, majd kattintson a **Mentés** gombra.

    ![Nyomkövetési szint beállítása a részletes értékre](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. A megjelenő böngészőablakban **kattintson a** **Kezdőlap** lehetőségre, majd a **Névjegy elemre**, végül a **kapcsolattartó** elemre.

    Néhány másodpercen belül a **kimeneti** ablak megjeleníti az összes nyomkövetési kimenetet.

    ![Részletes nyomkövetési kimenet](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Ebben a szakaszban engedélyezte és letiltotta a naplózást az Alkalmazásbeállítások használatával. A nyomkövetési figyelőket a Web.config fájl módosításával is engedélyezheti és tilthatja le. A Web.config fájl módosítása azonban az alkalmazás tartományának újrahasznosítását eredményezi, miközben az alkalmazás konfigurációjának használatával nem teszi lehetővé a naplózást. Ha a probléma hosszabb időt vesz igénybe, vagy időszakosan történik, az alkalmazás-tartomány újrahasznosítása a "javítás", és a várakozási idő elteltével megvárja a műveletet. A diagnosztika engedélyezése az Azure-ban lehetővé teszi, hogy azonnal elindítsa a hibák rögzítését az alkalmazás tartományának újrahasznosítása nélkül.

### <a name="output-window-features"></a>A kimeneti ablak funkciói
A **kimeneti** ablak **Microsoft Azure naplók** lapján több gomb és egy szövegmező látható:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Képernyőfelvétel: a kimeneti ablak Microsoft Azure naplók lapjának gombjai és szövegdoboza.":::

Ezek a következő funkciókat hajtják végre:

* Törölje a **kimenet** ablakot.
* A sortörés engedélyezése vagy letiltása.
* A figyelési naplók elindítása vagy leállítása.
* A figyelni kívánt naplók meghatározása.
* Naplók letöltése.
* Naplók szűrése keresési karakterlánc vagy reguláris kifejezés alapján.
* A **kimeneti** ablak bezárásához.

Ha keresési karakterláncot vagy reguláris kifejezést ír be, a Visual Studio szűrői adatokat naplóz az ügyfélen. Ez azt jelenti, hogy megadhatja a feltételeket, miután a naplók megjelennek a **kimenet** ablakban, és a szűrési feltételeket a naplók újbóli létrehozása nélkül is módosíthatja.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Webkiszolgáló-naplók megtekintése
A webkiszolgáló naplói rögzítik az alkalmazás összes HTTP-tevékenységét. Ahhoz, hogy megjelenjenek a **kimeneti** ablakban, engedélyeznie kell azokat az alkalmazáshoz, és a Visual studiót, amelyet figyelni kíván.

1. Az **Azure webalkalmazás-konfiguráció** lapon, amelyet a **Server Explorerben** nyitott meg, módosítsa a webkiszolgáló-naplózás beállítást **a be** értékre, majd kattintson a **Mentés** gombra.

    ![Webkiszolgáló naplózásának engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. A **kimenet** ablakban kattintson a **figyelni kívánt Microsoft Azure naplók meghatározása** gombra.

    ![A figyelni kívánt Azure-naplók meghatározása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. A **Microsoft Azure naplózási beállítások** párbeszédpanelen válassza a **webkiszolgáló-naplók** lehetőséget, majd kattintson **az OK** gombra.

    ![Webkiszolgáló-naplók figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Az alkalmazást megjelenítő böngészőablakban kattintson a **Kezdőlap**, majd a Névjegy elemre **, majd** a **Névjegy** elemre.

    Az alkalmazás naplói általában először, majd a webkiszolgáló naplóiban jelennek meg. Előfordulhat, hogy a naplók megjelenítéséhez várnia kell egy ideig.

    ![Webkiszolgáló-naplók a kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Alapértelmezés szerint, amikor először engedélyezi a webkiszolgálói naplók használatát a Visual Studióval, az Azure a naplófájlokat a fájlrendszerbe írja. Alternatív megoldásként használhatja a Azure Portal annak megadásához, hogy a webkiszolgálói naplókat egy Storage-fiókban lévő blob-tárolóba kell írni.

Ha a portálon engedélyezi a webkiszolgálói naplózást egy Azure Storage-fiókba, majd letiltja a naplózást a Visual Studióban, akkor a rendszer visszaállítja a Storage-fiók beállításait, amikor újra engedélyezi a naplózást a Visual Studióban.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Részletes hibaüzenetek naplófájljainak megtekintése
A részletes naplófájlok további információkat nyújtanak a hibakódot eredményező HTTP-kérésekről (400 vagy újabb). Ahhoz, hogy megjelenjenek a **kimeneti** ablakban, engedélyeznie kell azokat az alkalmazáshoz, és a Visual studiót, amelyet figyelni kíván.

1. A **Server Explorer böngészőből** megnyitott **Azure webalkalmazás-konfiguráció** lapon módosítsa **a részletes hibaüzeneteket** **a be** értékre, majd kattintson a **Mentés** gombra.

    ![Részletes hibaüzenetek engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. A **kimenet** ablakban kattintson a **figyelni kívánt Microsoft Azure naplók meghatározása** gombra.

3. A **Microsoft Azure naplózási beállítások** párbeszédpanelen kattintson a **minden napló** elemre, majd az **OK** gombra.

    ![Az összes napló figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. A böngészőablakban vegyen fel egy további karaktert az URL-címre, hogy 404-as hibát okozzon (például: `http://localhost:53370/Home/Contactx` ), majd nyomja le az ENTER billentyűt.

    Néhány másodperc elteltével a részletes hibanapló a Visual Studio **kimeneti** ablakában jelenik meg.

    ![Részletes hibanapló – kimeneti ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Vezérlő + kattintson a hivatkozásra a naplófájlban a böngészőben formázott kimenet megtekintéséhez:

    ![Részletes hibanapló – böngészőablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Fájlrendszer naplófájljainak letöltése
A **kimeneti** ablakban nyomon követhető naplók is letölthetők *. zip* -fájlként.

1. A **kimeneti** ablakban kattintson a **streaming naplók letöltése** elemre.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Képernyőkép a kimeneti ablakról, amely a streaming naplók letöltése gombot jelölte ki.":::

    A fájlkezelő a kiválasztott letöltött fájllal nyílik meg a *letöltések* mappájába.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Képernyőkép a fájlkezelő letöltések mappájában egy kiválasztott letöltött fájllal.":::

2. Bontsa ki a *. zip* fájlt, és láthatja a következő mappastruktúrát:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Képernyőkép a. zip-fájl mappájának struktúrájáról a fájl kibontása után.":::

   * Az alkalmazás-nyomkövetési naplók a *LogFiles\Application* mappában található *. txt* fájlokban találhatók.
   * A webkiszolgálói naplók a *. log* fájlokban találhatók a *LogFiles\http\RawLogs* mappában. A fájlok megtekintéséhez és módosításához használhatja a [log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22) eszközt is.
   * A részletes hibaüzenetek naplói a *LogFiles\DetailedErrors* mappában található *. html* fájlok.

     (A *központi telepítések* mappa a verziókövetés közzétételekor létrehozott fájlok esetében nem tartalmaz semmit a Visual Studio Publishing szolgáltatással kapcsolatban. A *git* mappa a verziókövetés közzétételével és a naplófájl-adatfolyam-továbbítási szolgáltatással kapcsolatos nyomkövetésekhez használható.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Sikertelen kérelmek nyomkövetési naplóinak megtekintése
A sikertelen kérelmek nyomkövetési naplói akkor hasznosak, ha meg kell ismernie, hogy az IIS hogyan kezeli a HTTP-kéréseket, például URL-Újraírási vagy hitelesítési problémák esetén.

App Service alkalmazások ugyanazt a sikertelen kérelmek nyomkövetési funkciót használják, amely az IIS 7,0-es és újabb verzióiban érhető el. Nincs hozzáférése az IIS-beállításokhoz, amelyekkel konfigurálhatja, hogy mely hibák legyenek naplózva. Ha engedélyezi a sikertelen kérelmek nyomkövetését, az összes hiba rögzítve lesz.

A sikertelen kérelmek nyomkövetését a Visual Studio használatával engedélyezheti, de a Visual Studióban nem tekintheti meg őket. Ezek a naplók XML-fájlok. Az adatfolyam-naplózási szolgáltatás csak egyszerű szöveges módban olvasható fájlokat figyeli:  *. txt*, *. html* és *. log* fájlok.

A sikertelen kérelmek nyomkövetési naplói a böngészőben közvetlenül FTP-n keresztül vagy helyileg is megtekinthetők, miután az FTP-eszköz használatával letölti azokat a helyi számítógépre. Ebben a szakaszban közvetlenül a böngészőben tekintheti meg őket.

1. A **Server Explorerben** megnyitott **Azure Web App** -ablak **konfiguráció** lapján módosítsa a **Sikertelen kérelmek nyomkövetését** **a be** értékre, majd kattintson a **Mentés** gombra.

    ![Sikertelen kérelmek nyomkövetésének engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Az alkalmazást megjelenítő böngészőablak címsorában adjon hozzá egy további karaktert az URL-címhez, majd az ENTER billentyűt lenyomva 404-hibát eredményezhet.

    Ennek hatására létrejön a sikertelen kérelmek nyomkövetési naplója, és a következő lépések bemutatják, hogyan tekintheti meg és töltheti le a naplót.

3. A Visual Studióban, az **Azure-webalkalmazás** **konfiguráció** lapján kattintson a **Megnyitás felügyeleti portál** lehetőségre.

4. Az alkalmazás [Azure Portal](https://portal.azure.com) **Beállítások** lapján kattintson a **központi telepítési hitelesítő adatok** elemre, majd adjon meg egy új felhasználónevet és jelszót.

    ![Új FTP-Felhasználónév és jelszó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Amikor bejelentkezik, a teljes felhasználónevet kell használnia az előtaggal ellátott alkalmazás nevével. Ha például a "MyID" nevet adja meg felhasználónévként, és a hely "myexample", akkor "myexample\myid" néven kell bejelentkeznie.
    >

5. Egy új böngészőablakban keresse meg az **FTP-állomásnév** vagy a **FTPS hostname** alatt látható URL-címet az alkalmazás **Áttekintés** lapján.

6. Jelentkezzen be a korábban létrehozott FTP-hitelesítő adatokkal (beleértve a Felhasználónév előtagját is).

    A böngészőben megjelenik az alkalmazás gyökérkönyvtára.

7. Nyissa meg a *LogFiles* mappát.

    ![Naplófájlok mappa megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Nyissa meg a W3SVC nevű mappát, valamint egy numerikus értéket.

    ![A W3SVC mappa megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A mappa XML-fájlokat tartalmaz a sikertelen kérelmek nyomkövetésének engedélyezése után naplózott hibákhoz, valamint egy XSL-fájlhoz, amelyet a böngésző használhat az XML formázására.

    ![W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kattintson arra a sikertelen kérelemre, amelyre vonatkozóan meg szeretné jeleníteni a nyomkövetési adatokat.

    A következő ábrán egy minta hiba nyomkövetési információinak egy része látható.

    ![Sikertelen kérelmek nyomkövetése a böngészőben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>További lépések
Látta, hogy a Visual Studio megkönnyíti a App Service-alkalmazások által létrehozott naplók megtekintését. A következő szakaszok a kapcsolódó témakörökben található további erőforrásokra mutató hivatkozásokat tartalmaznak:

* Hibaelhárítás App Service
* Hibakeresés a Visual Studióban
* Távoli hibakeresés az Azure-ban
* Nyomkövetés a ASP.NET-alkalmazásokban
* Webkiszolgáló-naplók elemzése
* Sikertelen kérelmek nyomkövetési naplóinak elemzése
* Hibakeresés Cloud Services

### <a name="app-service-troubleshooting"></a>Hibaelhárítás App Service
A Azure App Service alkalmazások hibaelhárításával kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Alkalmazások figyelése](web-sites-monitor.md)
* [A Azure app Service a Visual Studio 2013-es memória-szivárgások kivizsgálása](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Microsoft ALM blogbejegyzés a Visual Studio funkcióiról a felügyelt memória problémáinak elemzéséhez.
* [Azure app Service a szolgáltatással kapcsolatos online eszközöket](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blogbejegyzés az Apple által.

Ha segítségre van egy konkrét hibaelhárítási kérdésben, indítson el egy szálat az alábbi fórumok valamelyikében:

* [Az Azure Forum a ASP.net webhelyen](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Az Azure-fórum a Microsoft Q&a](/answers/topics/azure-webapps.html)-ben.
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Hibakeresés a Visual Studióban
További információ a hibakeresési mód a Visual Studióban történő használatáról: [hibakeresés a Visual Studióban](/visualstudio/debugger/debugging-in-visual-studio) és a [hibakeresési tippek a Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)-ben.

### <a name="remote-debugging-in-azure"></a>Távoli hibakeresés az Azure-ban
A App Service-alkalmazások és webjobs-feladatok távoli hibakeresésével kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Bevezetés a távoli hibakeresés Azure app Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [A távoli hibakeresés bemutatása Azure App Service 2. rész – távoli hibakeresés](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [A távoli hibakeresés bemutatása Azure App Service 3. rész – többpéldányos környezet és GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Webjobs-hibakeresés (videó)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Ha az alkalmazás egy Azure web API-t vagy Mobile Services háttér-használatot használ, és hibakeresést szeretne végezni, tekintse meg a [.net-háttér hibakeresése a Visual Studióban](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio)című témakört.

### <a name="tracing-in-aspnet-applications"></a>Nyomkövetés a ASP.NET-alkalmazásokban
Nincsenek alapos és naprakész bevezetések az interneten elérhető ASP.NET-nyomkövetéshez. A legjobb megoldás, ha a régi bevezető anyagok a Web Formshoz lettek írva, mert az MVC még nem létezett, és kiegészíti az adott problémákra koncentráló újabb blogbejegyzéseket. Néhány jó kezdési hely a következő erőforrások:

* [Monitorozás és telemetria (Real-World felhőalapú alkalmazások fejlesztése az Azure-ban)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-könyv fejezet az Azure Cloud Applications nyomkövetési javaslataival.
* [ASP.NET nyomkövetés](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Régi, de még mindig jó erőforrás a tárgy alapszintű bevezetéséhez.
* [Nyomkövetési figyelők](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Információ a nyomkövetési figyelőkről, de nem említi a [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Útmutató: a ASP.NET-nyomkövetés integrálása a System. Diagnostics nyomkövetésével](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Ez a cikk szintén régi, de tartalmaz néhány további információt, amelyet a bevezető cikk nem fed le.
* [Nyomkövetés a ASP.NET MVC borotva nézeteiben](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  A borotva nézeteinek nyomkövetése mellett a post azt is ismerteti, hogyan hozhat létre egy hibakódot, hogy naplózza az összes kezeletlen kivételt egy MVC-alkalmazásban. További információ a Web Forms alkalmazás összes kezeletlen kivételének naplózásáról: a Global. asax példa az MSDN-beli [hibakódok teljes példájában](/previous-versions/bb397417(v=vs.140)) . Ha az MVC-ben vagy a Web Formsban szeretne bizonyos kivételeket naplózni, de az alapértelmezett keretrendszer kezelése érvénybe lép, az alábbi példához hasonlóan elvégezheti és eldöntheti:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Stream diagnosztika nyomkövetési naplózása az Azure parancssorból (plusz pillantás!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Útmutató az oktatóanyaghoz a Visual Studióban történő végrehajtásához a parancssor használatával. A [pillantás](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) egy eszköz a ASP.NET-alkalmazások hibakereséséhez.
* [Web Apps naplózás és diagnosztika használata – David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) és a [streaming-naplókból a web Appsból – David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videók Scott Jancsi és David Ebbo.

A hibák naplózása érdekében a saját nyomkövetési kód megírásának alternatívája egy nyílt forráskódú naplózási keretrendszer használata, például a [ELMAH](https://nuget.org/packages/elmah/). További információ: [Scott Jancsi blog-bejegyzései a ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Emellett nem kell ASP.NET vagy nyomkövetést használnia `System.Diagnostics` Az Azure-ból származó adatfolyam-naplók beszerzéséhez. A App Service app streaming log szolgáltatás a *naplófájlok* mappában található bármely *. txt*, *. html* vagy *. log* fájlt továbbítja. Ezért létrehozhat egy saját naplózási rendszer, amely az alkalmazás fájlrendszerére ír, és a fájlt a rendszer automatikusan továbbítja és letölti. Mindössze annyit kell tennie, hogy írási programkódot hoz létre, amely létrehozza a fájlokat a *d:\home\logfiles* mappában.

### <a name="analyzing-web-server-logs"></a>Webkiszolgáló-naplók elemzése
A webkiszolgáló-naplók elemzésével kapcsolatos további információkért tekintse meg a következő forrásokat:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Egy eszköz a webkiszolgáló-naplók (*. log* fájlok) adatmegjelenítéséhez.
* [AZ IIS teljesítményével kapcsolatos hibák elhárítása és az LogParser-t használó alkalmazáshiba](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Bevezetés a log Parser eszközbe, amely a webkiszolgáló-naplók elemzésére használható.
* [Blogbejegyzések: Robert McMurray on using LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [A HTTP-állapotkód az IIS 7,0-ben, az IIS 7,5-ben és az IIS 8,0-ban](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Sikertelen kérelmek nyomkövetési naplóinak elemzése
A Microsoft TechNet webhely tartalmaz egy [Sikertelen kérelmek nyomkövetésének használatával](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) foglalkozó szakaszt, amely hasznos lehet a naplók használatának megismeréséhez. A dokumentáció azonban elsősorban a sikertelen kérelmek nyomkövetésének konfigurálására koncentrál az IIS-ben, amelyet nem tehet meg Azure App Serviceban.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki