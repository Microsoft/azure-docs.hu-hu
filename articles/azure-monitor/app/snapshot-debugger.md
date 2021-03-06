---
title: Azure Application Insights Snapshot Debugger .NET-alkalmazásokhoz
description: A hibakeresési Pillanatképek automatikusan bekerülnek, ha a kivételek az éles .NET-alkalmazásokban vannak felvetve
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
author: cweining
ms.author: cweining
ms.reviewer: cweining
ms.openlocfilehash: bd196e60fed8a18064bfa59e343e51a878a11237
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217397"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazások kivételeinek hibakeresési pillanatképei
Ha kivétel történik, automatikusan gyűjthet egy hibakeresési pillanatképet az élő webalkalmazásból. A pillanatkép a forráskód és a változók állapotát mutatja a kivétel eldobásának pillanatában. Az [Azure Application Insights](./app-insights-overview.md) Snapshot Debugger figyeli a webalkalmazás kivétel-telemetria. Pillanatképeket gyűjt a legfelső szintű kivételekről, így az éles környezetben felmerülő problémák diagnosztizálásához szükséges információkkal rendelkezik. Adja meg a [Pillanatkép-gyűjtő NuGet csomagot](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és szükség esetén konfigurálja a gyűjtemény paramétereit [ApplicationInsights.configban ](./configuration-with-applicationinsights-config.md). A pillanatképek a Application Insights portál [kivételei](./asp-net-exceptions.md) között jelennek meg.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ha hatékonyabb hibakeresési élményt szeretne kapni a forráskódtal, nyissa meg a pillanatképeket a Visual Studio 2019 Enterprise használatával. A Visual Studióban úgy is beállíthatja a Snappoints, hogy a rendszer kivétel nélkül is [interaktívan készítsen pillanatképeket](/visualstudio/debugger/debug-live-azure-applications) .

A hibakeresési Pillanatképek tárolása 15 napig tart. Ez az adatmegőrzési szabályzat az alkalmazáson belüli alapon van beállítva. Ha ezt az értéket kell megnövelni, akkor a Azure Portal támogatási esetének megnyitásával növelheti a növekedést.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Application Insights Snapshot Debugger engedélyezése az alkalmazáshoz
A pillanatkép-gyűjtemény a következőhöz érhető el:
* .NET-keretrendszer és a .NET-keretrendszer 4,5-es vagy újabb verzióját futtató ASP.NET-alkalmazások.
* A .NET Core 2,0 és a ASP.NET Core 2,0 rendszerű alkalmazások Windows rendszeren futnak.

A következő környezetek támogatottak:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-függvény](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* Az operációsrendszer-család 4-es vagy újabb verzióját futtató [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 vagy újabb rendszerű [Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Azure-Virtual Machines és Windows Server 2012 R2 vagy újabb rendszert futtató [virtuálisgép-méretezési készletek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 vagy újabb, vagy Windows 8,1 vagy újabb rendszert futtató helyszíni [virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Az ügyfélalkalmazások (például WPF, Windows Forms vagy UWP) nem támogatottak.

Ha engedélyezte Snapshot Debugger de nem lát pillanatképeket, tekintse meg a [hibaelhárítási útmutatót](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Engedélyek megadása

A pillanatképek elérését az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) védi. A pillanatképek vizsgálatához egy előfizetés-tulajdonosnak először fel kell vennie Önt a szükséges szerepkörbe.

> [!NOTE]
> A tulajdonosok és a közreműködők nem rendelkeznek automatikusan ehhez a szerepkörhöz. Ha szeretné megtekinteni a pillanatképeket, fel kell venni magukat a szerepkörbe.

Az előfizetés tulajdonosának hozzá kell rendelnie a `Application Insights Snapshot Debugger` szerepkört azokhoz a felhasználókhoz, akik megvizsgálja a pillanatképeket. Ez a szerepkör egyéni felhasználókhoz vagy csoportokhoz rendelhető hozzá az előfizetések tulajdonosai számára a cél Application Insights erőforráshoz, illetve annak erőforráscsoporthoz vagy előfizetéséhez.

1. Navigáljon a Azure Portal Application Insights erőforráshoz.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. Válassza ki **Application Insights Snapshot Debugger** a **szerepkörök** legördülő listából.
1. Keresse meg a nevet, és adja meg a hozzáadni kívánt felhasználó nevét.
1. Kattintson a **Save (Mentés** ) gombra, és adja hozzá a felhasználót a szerepkörhöz.


> [!IMPORTANT]
> A pillanatképek tartalmazhatnak személyes és más bizalmas adatokat is a változó és a paraméterek értékeiben.

## <a name="view-snapshots-in-the-portal"></a>Pillanatképek megtekintése a portálon

Ha kivétel történt az alkalmazásban, és egy pillanatkép lett létrehozva, pillanatképeket kell megtekintenie. Akár 5 – 10 percet is igénybe vehet, ha egy pillanatkép készen áll, és a portálról megtekinthető. A pillanatképek megtekintéséhez a **hiba** ablaktáblán kattintson a műveletek **gombra a** **műveletek** lap megtekintésekor, vagy válassza a **kivételek** gombot a **kivételek** lap megtekintésekor:

![Hibák lapja](./media/snapshot-debugger/failures-page.png)

Válasszon ki egy műveletet vagy kivételt a jobb oldali ablaktáblán a **végpontok közötti tranzakció részletei** panel megnyitásához, majd válassza ki a kivétel eseményt. Ha a megadott kivételhez pillanatkép áll rendelkezésre, a jobb oldali ablaktáblán megjelenik a [kivétel](./asp-net-exceptions.md)részleteit tartalmazó **nyitott hibakeresési pillanatkép** gomb.

![A hibakeresési pillanatkép gombjának megnyitása kivétel esetén](./media/snapshot-debugger/e2e-transaction-page.png)

A hibakeresési pillanatkép nézetben a hívási verem és a változók ablaktábla látható. Amikor kiválasztja a hívási verem kereteit a hívási verem ablaktáblán, megtekintheti a függvény hívásához tartozó helyi változókat és paramétereket a változók ablaktáblán.

![Hibakeresési pillanatkép megtekintése a portálon](./media/snapshot-debugger/open-snapshot-portal.png)

A pillanatképek bizalmas információkat tartalmazhatnak, és alapértelmezés szerint nem láthatók. A pillanatképek megtekintéséhez `Application Insights Snapshot Debugger` hozzá kell rendelnie a szerepkört.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Pillanatképek megtekintése a Visual Studio 2017 Enterprise vagy újabb verzióban
1. Kattintson a **Pillanatkép letöltése** gombra egy fájl letöltéséhez `.diagsession` , amelyet a Visual Studio Enterprise is megnyithat.

2. A fájl megnyitásához `.diagsession` telepítenie kell a Snapshot Debugger Visual Studio összetevőt. A Snapshot Debugger összetevő a Visual Studióban a ASP.net számítási feladatának szükséges összetevője, és a Visual Studio telepítő egyes összetevők listájában is kiválasztható. Ha a Visual Studio 2017-es 15,5 verziójának korábbi verzióját használja, akkor telepítenie kell a bővítményt a [Visual Studio piactérről](https://aka.ms/snapshotdebugger).

3. A pillanatkép-fájl megnyitása után megjelenik a minidump hibakeresési lapja a Visual Studióban. A pillanatkép hibakeresésének megkezdéséhez kattintson a **felügyelt kód hibakeresése** elemre. A pillanatkép megnyílik a kód azon sorában, ahol a kivételt eldobta a rendszer, hogy a folyamat aktuális állapotát tudja-e debug.

    ![Hibakeresési pillanatkép megtekintése a Visual Studióban](./media/snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatkép a webalkalmazás-kiszolgálón található összes szimbólum-fájlt tartalmazza. Ezek a szimbólumok szükségesek a pillanatkép-adatok forráskódhoz való hozzárendeléséhez. App Service alkalmazások esetében ügyeljen arra, hogy a webalkalmazások közzétételekor engedélyezze a szimbólum központi telepítését.

## <a name="how-snapshots-work"></a>A pillanatképek működése

A Snapshot Collector [Application Insights telemetria processzorként](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)van megvalósítva. Az alkalmazás futtatásakor a rendszer hozzáadja a Snapshot Collector telemetria processzort az alkalmazás telemetria-folyamatához.
Minden alkalommal, amikor az alkalmazás meghívja a [TrackException](./asp-net-exceptions.md#exceptions)-t, a Snapshot Collector kiszámítja a probléma azonosítóját a dobott kivétel típusától és a throwing metódustól.
Minden alkalommal, amikor az alkalmazás meghívja a TrackException-t, a megfelelő problémás azonosító számlálója növekszik. Ha a számláló eléri az `ThresholdForSnapshotting` értéket, a probléma azonosítója bekerül egy gyűjteményi tervbe.

A Snapshot Collector a kivételeket is figyeli a [alkalmazástartomány. CurrentDomain. FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) eseményre való feliratkozással. Az esemény bekövetkezésekor a rendszer kiszámítja a kivétel problémájának AZONOSÍTÓját, és összehasonlítja a gyűjtési tervben szereplő problémás azonosítókkal.
Ha egyezés van, a rendszer létrehozza a futó folyamat pillanatképét. A pillanatképhez egyedi azonosító van rendelve, a kivétel pedig az adott azonosítóval van lepecsételve. A FirstChanceException-kezelő visszaadása után a rendszer a feldobott kivételt a szokásos módon dolgozza fel. Végül a kivétel eléri a TrackException metódust, ahol a pillanatkép-azonosítóval együtt Application Insights.

A fő folyamat továbbra is fut, és a forgalmat a kis megszakítással rendelkező felhasználók számára is kiszolgálja. Eközben a pillanatkép bekerül a pillanatkép-feltöltő folyamatba. A pillanatkép-feltöltő létrehoz egy minidump, és feltölti azt Application Insights a kapcsolódó szimbólumokkal (. PDB) együtt.

> [!TIP]
> - A folyamat pillanatképe a futó folyamat felfüggesztett klónozása.
> - A pillanatkép létrehozása körülbelül 10 – 20 ezredmásodpercet vesz igénybe.
> - Az alapértelmezett értéke `ThresholdForSnapshotting` 1. Ez a minimális érték is. Ezért az alkalmazásnak ugyanezt a kivételt **kétszer** kell elindítania a pillanatkép létrehozása előtt.
> - Állítsa `IsEnabledInDeveloperMode` az igaz értékre, ha pillanatképeket kíván létrehozni a Visual Studióban történő hibakeresés során.
> - A pillanatkép-létrehozási sebességet a `SnapshotsPerTenMinutesLimit` beállítás korlátozza. Alapértelmezés szerint a korlát 10 percenként egy pillanatkép.
> - Naponta legfeljebb 50 pillanatképet lehet feltölteni.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett adatmegőrzési időszak 15 nap. Minden Application Insights-példány esetében naponta legfeljebb 50 pillanatkép engedélyezett.

### <a name="publish-symbols"></a>Közzétételi szimbólumok
A Snapshot Debugger a változók dekódolásához és a Visual Studióban a hibakeresési élmény biztosításához az üzemi kiszolgálón található szimbólum-fájlok szükségesek.
A Visual Studio 2017 15,2-es vagy újabb verziója alapértelmezés szerint közzéteszi a kiadási buildek szimbólumait, amikor közzéteszi a App Service. A korábbi verziókban a következő sort kell felvennie a közzétételi profil `.pubxml` fájljába, hogy a szimbólumok közzé legyenek téve a kiadási módban:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Az Azure-beli számítási és egyéb típusok esetében győződjön meg arról, hogy a szimbólumok a Main Application. dll (általában,) és az `wwwroot/bin` aktuális elérési úton elérhető mappában találhatók.

> [!NOTE]
> Az elérhető különböző szimbólumokkal kapcsolatos további információkért tekintse meg a [Visual Studio dokumentációját](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). A legjobb eredmény érdekében javasoljuk a "teljes", a "hordozható" vagy a "Embedded" használatát.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben a helyi változók nem tekinthetők meg a kiadási buildekben a JIT-fordító által alkalmazott optimalizálások miatt.
Az Azure App Services azonban a Snapshot Collector a begyűjtési terv részét képező metódusok deoptimalizálását is elvégezheti.

> [!TIP]
> A deoptimization támogatásának beszerzéséhez telepítse a App Service Application Insights site bővítményét.

## <a name="next-steps"></a>Következő lépések
Application Insights Snapshot Debugger engedélyezése az alkalmazáshoz:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-függvény](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Snapshot Debuggeron túl:
 
* [Állítsa be a snappoints a kódban](/visualstudio/debugger/debug-live-azure-applications) a pillanatképek lekéréséhez, ha nem vár kivételt.
* [A webalkalmazásokban a kivételek diagnosztizálásakor](./asp-net-exceptions.md) megtudhatja, hogyan teheti elérhetővé a Application Insights további kivételeket.
* Az [intelligens észlelés](./proactive-diagnostics.md) automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket.