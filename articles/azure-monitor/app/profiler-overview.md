---
title: Profil éles alkalmazások az Azure-ban Application Insights Profiler
description: Azonosítsa a webkiszolgáló kódjában található gyors elérési utat egy alacsony helyigényű Profilerrel.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499392"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil éles alkalmazások az Azure-ban Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Application Insights Profiler engedélyezése az alkalmazáshoz

Az Azure Application Insights Profiler teljesítmény-nyomkövetést biztosít az Azure-ban éles környezetben futó alkalmazások számára. A Profiler automatikusan rögzíti az adatmennyiséget, anélkül, hogy negatívan befolyásolná a felhasználókat. A Profiler segítségével azonosíthatja a "forró" kód elérési útját, amely a leghosszabb időt veszi igénybe egy adott webes kérelem kezelése során. 

A Profiler a következő Azure-szolgáltatásokon üzembe helyezett .NET-alkalmazásokkal működik. A Profiler minden szolgáltatástípus engedélyezésére vonatkozó utasítások az alábbi hivatkozásokban találhatók.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Előzetes** verzió ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Ha engedélyezte a Profilert, de nem lát nyomkövetést, tekintse meg a [hibaelhárítási útmutatót](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Profiler-adathalmazok megtekintése

A Profiler a Nyomkövetések feltöltéséhez az alkalmazásnak aktívan kell kezelnie a kérelmeket. Ha kísérletet hajt végre, [Application Insights teljesítményteszt](/vsts/load-test/app-service-web-app-performance-test)használatával hozhatja elő a kérelmeket a webalkalmazáshoz. Ha újonnan engedélyezte a Profilert, rövid betöltési tesztet is futtathat. Amíg a terhelési teszt fut, kattintson a **profil most** gombra a [ **Profiler-beállítások** panelen](profiler-settings.md). Ha a Profiler fut, a rendszer óránként egyszer, és két percen belül véletlenszerűen készíti el a profilt. Ha az alkalmazás a kérelmek állandó streamjét kezeli, a Profiler óránként feltölti a nyomkövetéseket.

Miután az alkalmazás megkapta a forgalmat és a Profiler-nek van ideje a Nyomkövetések feltöltésére, a megtekintéshez nyomkövetést kell használnia. Ez a folyamat 5 – 10 percet is igénybe vehet. A Nyomkövetések megtekintéséhez a **teljesítmény** ablaktáblán válassza a **műveletek elvégzése** lehetőséget, majd válassza a **Profiler Nyomkövetések** gombot.

![Application Insights a teljesítmény ablaktábla előnézet Profiler nyomkövetései][performance-blade]

Válasszon ki egy mintát a kérelem végrehajtásához szükséges idő kód szintű részletezésének megjelenítéséhez.

![Application Insights nyomkövetési tallózó][trace-explorer]

A nyomkövetési tallózó a következő információkat jeleníti meg:

* **Gyors elérési út megjelenítése**: megnyitja a legnagyobb levél csomópontot, vagy legalább egy bezárást. A legtöbb esetben ez a csomópont a teljesítmény szűk keresztmetszete közelében van.
* **Label (címke**): a függvény vagy esemény neve. A fa a kód és a bekövetkezett események, például az SQL-és a HTTP-események kombinációját jeleníti meg. A felső esemény a kérelem teljes időtartamát jelenti.
* **Eltelt** idő: a művelet kezdete és a művelet vége közötti időintervallum.
* **Mikor**: az az idő, amikor a függvény vagy esemény más függvényekhez képest futott.

## <a name="how-to-read-performance-data"></a>Teljesítményadatok beolvasása

A Microsoft Service Profiler a mintavételi módszerek és a rendszerállapotok kombinációját használja az alkalmazás teljesítményének elemzéséhez. Ha a részletes gyűjtemény folyamatban van, a szolgáltatás Profiler minden egyes számítógép-CPU esetében ezredmásodpercben megkeresi az utasítás mutatóját. Mindegyik minta rögzíti a jelenleg végrehajtás alatt álló szál teljes hívási veremét. Részletes információkkal szolgál arról, hogy a szál mit csinált, és magas szinten és alacsony absztrakciós szinten. A szolgáltatás Profiler más eseményeket is gyűjt a tevékenységek korrelációjának és okozati viszonyának nyomon követéséhez, beleértve a környezet váltási eseményeit, a feladatok párhuzamos kódtára (TPL) eseményeit és a szál-készlet eseményeit.

Az idősor nézetben megjelenő hívási verem a mintavételezés és a kivezetés eredménye. Mivel mindegyik minta rögzíti a szál teljes hívási veremét, Microsoft .NET-keretrendszerből származó kódot, valamint az Ön által hivatkozott egyéb keretrendszereket is tartalmazza.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Objektum kiosztása (CLR! JIT \_ új vagy CLR! JIT \_ Newarr1)

**CLR! JIT \_ új** és **CLR! A JIT \_ Newarr1** olyan segítő függvények a .net-keretrendszerben, amelyek memóriát foglalnak le egy felügyelt halomból. **CLR! Egy \_** objektum lefoglalásakor a rendszer meghívja az új JIT-t. **CLR! A \_** rendszer meghívja a JIT Newarr1, amikor egy objektum-tömböt foglal le. Ez a két függvény általában gyors, és viszonylag kis mennyiségű időt vesz igénybe. Ha **CLR! JIT \_ új** vagy **CLR! A JIT- \_ Newarr1** sok időt vesz igénybe az idővonalban, a kód több objektumot is lefoglalhat, és jelentős mennyiségű memóriát is igénybe vehet.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Kód betöltése (CLR! ThePreStub)

**CLR! A ThePreStub** a .NET-keretrendszer egyik segítő funkciója, amely előkészíti a kódot az első alkalommal történő végrehajtásra. Ez a végrehajtás általában magában foglalja az igény szerinti (JIT) fordítást, de nem kizárólagosan. Minden C#-metódushoz **CLR! A ThePreStub** a folyamat során legfeljebb egyszer kell meghívni.

Ha **CLR! A ThePreStub** hosszú időt vesz igénybe, a kérelem az első, amely végrehajtja ezt a metódust. A .NET-keretrendszer futtatókörnyezetének az első módszer betöltéséhez szükséges ideje jelentős. Érdemes lehet olyan bemelegedési folyamatot használni, amely végrehajtja a kód azon részét, mielőtt a felhasználók hozzáférnek hozzá, vagy érdemes lehet natív rendszerkép-generátort (ngen.exe) futtatni a szerelvényeken.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Zárolás feloldása (CLR! JITutil \_ MonContention vagy CLR! JITutil \_ MonEnterWorker)

**CLR! JITutil \_ MonContention** vagy **CLR! A JITutil \_ MonEnterWorker** azt jelzi, hogy az aktuális szál zárolás felszabadítására vár. Ez a szöveg gyakran jelenik meg C# **zárolási** utasítás végrehajtásakor, a figyelő meghívásával **. adja meg** a metódust, vagy hívja meg a metódust a **MethodImplOptions. szinkronizált** attribútummal. A zárolási tartalom általában akkor fordul elő, ha a _szál egy zárolást szerez be_ , és a _B_ szál megpróbálja ugyanazt a zárolást megnyerni, mielőtt elkezdené _a kiadást_ .

### <a name="loading-code-cold"></a><a id="ngencold"></a>Kód betöltése ([hideg])

Ha a metódus neve **[hideg]** nevet tartalmaz, például **Mscorlib.ni! [ HIDEG] System. Reflection. CustomAttribute. IsDefined**, a .NET Framework Runtime olyan kódot hajt végre, amely a [profil által vezérelt optimalizálás](/cpp/build/profile-guided-optimizations)által nem optimalizált első alkalommal fut. Az egyes módszerekhez a folyamat során legfeljebb egyszer kell megjeleníteni.

Ha a betöltési kód nagy mennyiségű időt vesz igénybe, a kérelem az első, amely a metódus nem optimalizált részét hajtja végre. Érdemes lehet egy bemelegedési folyamatot használni, amely a kód azon részét futtatja, mielőtt a felhasználók hozzáférnek hozzá.

### <a name="send-http-request"></a><a id="httpclientsend"></a>HTTP-kérelem küldése

A **HttpClient. Send** metódusok például azt jelzik, hogy a kód a HTTP-kérelem befejeződésére vár.

### <a name="database-operation"></a><a id="sqlcommand"></a>Adatbázis-művelet

Az olyan metódusok, mint például a **SqlCommand.Exearanyosak** , azt jelzik, hogy a kód egy adatbázis-művelet befejezésére vár.

### <a name="waiting-await_time"></a><a id="await"></a>Várakozás (várakozási \_ idő)

**Várakozás \_ Az idő** azt jelzi, hogy a kód egy másik feladat befejeződésére vár. Ez a késleltetés általában a C# **várakozási** utasítással történik. Ha a kód egy C#-t **vár**, a szál felteker, és visszaadja a vezérlést a szál készletnek, és nincs olyan szál, **amely a várakozás befejezésére vár.** Logikusan azonban a **várt** szál "Letiltva", és a művelet befejezésére vár. A várakozási **\_ idő** utasítás azt a letiltott időt jelzi, amíg a feladat befejeződik.

### <a name="blocked-time"></a><a id="block"></a>Letiltott idő

**BLOCKED_TIME** azt jelzi, hogy a kód arra vár, hogy egy másik erőforrás elérhetővé válik. Előfordulhat például, hogy egy szinkronizálási objektumra várakozik egy elérhető szál vagy egy befejezési kérelem.

### <a name="unmanaged-async"></a>Nem felügyelt aszinkron

A .NET-keretrendszer ETW eseményeket bocsát ki, és a szálak között továbbítja a tevékenység-azonosítókat, hogy az aszinkron hívásokat nyomon lehessen követni a szálak között. A nem felügyelt kód (natív kód) és az aszinkron kódok néhány régebbi stílusa hiányzik ezekről az eseményekről és tevékenység-azonosítóról, így a Profiler nem tudja megállapítani, hogy milyen szálat és milyen funkciók futnak a szálon. Ez a hívási verem "nem felügyelt aszinkron" címkéje. Ha letölti a ETW fájlt, lehetséges, hogy a [perfview eszköz](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md)  segítségével részletesebben megismerheti a mi történik.

### <a name="cpu-time"></a><a id="cpu"></a>CPU-idő

A CPU foglalt az utasítások végrehajtásával.

### <a name="disk-time"></a><a id="disk"></a>Lemez ideje

Az alkalmazás lemezes műveleteket végez.

### <a name="network-time"></a><a id="network"></a>Hálózati idő

Az alkalmazás hálózati műveleteket végez.

### <a name="when-column"></a><a id="when"></a>Oszlop

A ha oszlopban látható, hogy a csomópontok számára összegyűjtött minták időbeli **változása** milyen módon változhat. A kérelem teljes tartománya 32 Time-gyűjtőre van osztva. Az adott csomóponthoz tartozó befogadó minták a 32 gyűjtőben vannak összesítve. Az egyes gyűjtők sávként jelennek meg. A sáv magassága a méretezett értéket jelöli. **CPU_TIME** vagy **BLOCKED_TIME** jelölésű csomópontok esetén, vagy ha nyilvánvaló kapcsolat van egy erőforrás (például egy processzor, egy lemez vagy egy szál) felhasználásához, a sáv a gyűjtőben lévő erőforrások egyikét jelöli. Ezekben a mérőszámokban több erőforrás használata esetén lehetséges, hogy 100%-nál nagyobb értéket kapjon. Ha például átlagosan két processzort használ egy intervallumban, akkor 200%-ot kap.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett adatmegőrzési időtartam öt nap. A naponta betöltött maximális mennyiség 10 GB.

A Profiler szolgáltatás használatáért nem számítunk fel díjat. Ahhoz, hogy használhassa, a webalkalmazást legalább a Azure App Service Web Apps funkciójának alapszintű szintjében kell üzemeltetni.

## <a name="overhead-and-sampling-algorithm"></a>Terhelési és mintavételezési algoritmus

A Profiler véletlenszerűen két percen belül fut minden olyan virtuális gépen, amelyen a Profiler engedélyezve van a nyomkövetés rögzítéséhez. Ha a Profiler fut, az 5 – 15 százalékos CPU-terhelést tesz elérhetővé a kiszolgálóra.

## <a name="next-steps"></a>Következő lépések
Application Insights Profiler engedélyezése az Azure-alkalmazáshoz. Lásd még:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
