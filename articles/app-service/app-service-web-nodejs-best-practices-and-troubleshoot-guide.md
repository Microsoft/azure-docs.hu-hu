---
title: Node.js ajánlott eljárások és hibaelhárítás
description: Ismerkedjen meg a Azure App Service-ben futó Node.js alkalmazások ajánlott eljárásaival és hibaelhárítási lépéseivel.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: bfbd93cc3d4e67c8a96a1413221fdd7190c4f0b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572629"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Ajánlott eljárások és hibaelhárítási útmutató a Azure App Service Windows rendszerű csomópont-alkalmazásokhoz

Ebből a cikkből megtudhatja, hogyan tekintheti meg a [Windows Node.js-alkalmazások](quickstart-nodejs.md?pivots=platform-windows) Azure app Service-on ( [iisnode](https://github.com/azure/iisnode)) futtatott, ajánlott eljárásokat és hibaelhárítási lépéseket.

> [!WARNING]
> Körültekintően járjon el, amikor hibaelhárítási lépéseket használ az üzemi webhelyén. Javasoljuk, hogy az alkalmazást nem éles környezetben, például az átmeneti tárolóhelyen és a probléma megoldásakor, az átmeneti tárolóhelyet cserélje le az éles tárolóhelyre.
>

## <a name="iisnode-configuration"></a>IISNODE-konfiguráció

Ez a [sémafájl](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) a iisnode konfigurálható beállításokat jeleníti meg. Az alkalmazáshoz hasznos beállítások némelyike:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Ezzel a beállítással szabályozható az IIS-alkalmazásokban elindított csomópont-folyamatok száma. Az alapértelmezett érték az 1. A virtuális gép vCPU számának megadásával akár több node.exes is elindítható, ha az értéket 0-ra módosítja. A legtöbb alkalmazás esetében a javasolt érték a 0, így a gépen található összes vCPU használhatja. Node.exe egyszálas, így egy node.exe legfeljebb 1 vCPU-t használ fel. A Node-alkalmazás maximális teljesítményének lekéréséhez az összes vCPU használni kívánja.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Ezzel a beállítással szabályozható a node.exe elérési útja. Ezt az értéket beállíthatja úgy, hogy az a node.exe verziójára mutasson.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Ezzel a beállítással szabályozható, hogy a iisnode által küldött egyidejű kérelmek maximális száma az egyes node.exe. Azure App Service esetén az alapértelmezett érték a végtelen. Az értéket beállíthatja attól függően, hogy hány kérelem érkezik az alkalmazáshoz, és hogy az alkalmazás milyen gyorsan dolgozza fel az egyes kérelmeket.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Ezzel a beállítással adható meg, hogy a iisnode hányszor próbálkozzon újra a megnevezett pipe-beli kapcsolódással, hogy elküldje a kérelmeket a node.exenak. Ez a beállítás a namedPipeConnectionRetryDelay együtt határozza meg a iisnode belüli kérelmek teljes időtúllépését. Az alapértelmezett érték 200 Azure App Service. Teljes időkorlát másodpercben = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Ezzel a beállítással szabályozható, hogy a iisnode mennyi ideig várjon (ezredmásodpercben), hogy elküldje a kérést, hogy node.exe a nevesített pipe-on keresztül. Az alapértelmezett érték 250 MS.
Teljes időkorlát másodpercben = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Alapértelmezés szerint a Azure App Service iisnode teljes időtúllépése 200 \* 250 MS = 50 másodperc.

### <a name="logdirectory"></a>logDirectory

Ez a beállítás szabályozza azt a könyvtárat, ahol a iisnode naplózza az stdout/stderr-t. Az alapértelmezett érték a iisnode, amely a fő parancsfájl könyvtárához van viszonyítva (a főserver.js található könyvtár)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Ez a beállítás azt szabályozza, hogy a Node-Inspector iisnode milyen verzióját használja a Node-alkalmazás hibakeresése során. Jelenleg iisnode-inspector-0.7.3.dll és iisnode-inspector.dll a beállításnak csak két érvényes értéke. Az alapértelmezett érték iisnode-inspector-0.7.3.dll. A iisnode-inspector-0.7.3.dll verziója a Node-Inspector-0.7.3-t használja, és webes szoftvercsatornát használ. Engedélyezze a webes szoftvercsatornák használatát az Azure webappban, hogy ezt a verziót használja. <https://ranjithblogs.azurewebsites.net/?p=98>További információ a iisnode konfigurálásáról az új Node-Inspector használatára.

### <a name="flushresponse"></a>flushResponse

Az IIS alapértelmezett viselkedése az, hogy a kiürítés előtt legfeljebb 4 MB-nyi válaszüzenetet, vagy a válasz végéig, attól függően, hogy melyik következik be először. a iisnode olyan konfigurációs beállítást biztosít, amely felülbírálja ezt a viselkedést: a válasz entitás törzsének egy töredékének kiürítéséhez, amint a iisnode megkapja azt a node.exetól, az iisnode/@flushResponse attribútumot web.config "true" értékre kell állítania:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

A válasz entitás törzsének minden töredékének kiürítése lehetővé teszi a teljesítmény terhelését, amely csökkenti a rendszer átviteli sebességét ~ 5%-kal (v 0.1.13). A legjobb, ha ezt a beállítást csak a válaszadási adatfolyamot igénylő végpontokra kívánja használni (például a `<location>` web.config elemének használatával)

Emellett a folyamatos átviteli alkalmazások esetében a iisnode-kezelő responseBufferLimit is 0-ra kell állítania.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

A változások számára figyelt fájlok pontosvesszővel tagolt listája. A fájl bármilyen módosítása az alkalmazás újrahasznosítását okozza. Mindegyik bejegyzés egy opcionális könyvtárnévből és egy szükséges fájlnévből áll, amely a fő alkalmazás belépési pontját tartalmazó könyvtárhoz viszonyítva található. A Wild kártyákat csak a fájlnév részen lehet engedélyezni. Az alapértelmezett érték `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Az alapértelmezett érték a hamis. Ha engedélyezve van, a Node-alkalmazás tud csatlakozni egy nevesített pipe-hoz (környezeti változó IISNODE- \_ vezérlő \_ cső), és "újrahasznosítási" üzenetet küld. Ennek hatására a W3wp szabályosan újrahasznosítható.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Az alapértelmezett érték 0, ami azt jelenti, hogy ez a szolgáltatás le van tiltva. Ha a értéke 0-nál nagyobb értékre van állítva, a iisnode az összes alárendelt folyamatát (ezredmásodpercben) minden "idlePageOutTimePeriod" állapotba kerül. Tekintse meg a [dokumentációt](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) , amelyből megismerheti, hogy mit jelent az oldal. Ez a beállítás olyan alkalmazások esetében hasznos, amelyek nagy mennyiségű memóriát használnak, és a memóriában lévő memóriát időnként a RAM felszabadítása érdekében szeretnék kipróbálni.

> [!WARNING]
> Körültekintően járjon el a következő konfigurációs beállításoknak az üzemi alkalmazásokban való engedélyezésekor. A javaslat célja, hogy ne engedélyezze őket éles üzemi alkalmazásokban.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Az alapértelmezett érték a hamis. Ha igaz értékre van állítva, a iisnode egy HTTP-válasz fejlécét adja hozzá `iisnode-debug` minden HTTP-válaszhoz, amely a `iisnode-debug` fejléc értékét URL-címként küldi el. Az URL-töredékek megkeresésével a rendszer az URL-cím megadásával megnyithatja az egyes diagnosztikai adatokat.

### <a name="loggingenabled"></a>loggingEnabled

Ez a beállítás szabályozza az stdout és a stderr naplózását a iisnode. A Iisnode rögzíti az stdout/stderr csomópontot az általa indított és a "logDirectory" beállításban megadott könyvtárba. Ha ez engedélyezve van, az alkalmazás a naplófájlokat a fájlrendszerbe írja, és az alkalmazás által végzett naplózás mennyiségétől függően lehetséges, hogy teljesítménybeli következményekkel jár.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Az alapértelmezett érték a hamis. Ha igaz értékre van állítva, a iisnode megjeleníti a HTTP-állapotkódot és a Win32-hibakódot a böngészőben. A Win32-kód bizonyos típusú problémák hibakereséséhez hasznos.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (ne engedélyezze az éles környezetben futó webhelyeken)

Ez a beállítás vezérli a hibakeresési funkciót. A Iisnode integrálva van a Node-Inspector szolgáltatással. A beállítás engedélyezésével engedélyezheti a Node-alkalmazás hibakeresését. Ha engedélyezi ezt a beállítást, a iisnode létrehozza a Node-Inspector-fájlokat a Node-alkalmazás első hibakeresési kérelmének "debuggerVirtualDir" könyvtárába. A Node-Inspector betöltéséhez kérelem küldésére van lehetőség `http://yoursite/server.js/debug` . A hibakeresési URL-szegmenst a "debuggerPathSegment" beállítással szabályozhatja. Alapértelmezés szerint a debuggerPathSegment = "debug". Beállíthatja `debuggerPathSegment` egy GUID azonosítóra, például úgy, hogy a többiek nehezebben felderítsék.

A hibakereséssel kapcsolatos további részletekért olvassa el a [Windows rendszeren futó hibakeresési node.js](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) .

## <a name="scenarios-and-recommendationstroubleshooting"></a>Forgatókönyvek és javaslatok/hibaelhárítás

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>A Node-alkalmazásom túlzott kimenő hívásokat tesz

Számos alkalmazás szeretne kimenő kapcsolatokat végezni a normál működésük részeként. Ha például egy kérelem érkezik, a Node-alkalmazás szeretne kapcsolatba lépni egy REST API máshol, és néhány információt kap a kérelem feldolgozásához. Http-vagy https-hívások esetén a Keep Alive ügynököt érdemes használni. A agentkeepalive modult használhatja a Keep Alive-ügynökként a kimenő hívások elkészítésekor.

A agentkeepalive modul biztosítja, hogy a szoftvercsatornák újra felhasználhatók legyenek az Azure WebApp virtuális gépén. Az új szoftvercsatorna létrehozása minden kimenő kérelemhez a terhelést az alkalmazáshoz adja. Ha az alkalmazás a kimenő kérelmekhez szoftvercsatornát használ, biztosítja, hogy az alkalmazás ne lépje túl a virtuális gép által lefoglalt maxsocket. A Azure App Servicera vonatkozó javaslat a agentKeepAlive maxsocket értékének beállítása a \* virtuális gépenként megadott node.exe 32 maxsocket/instance 128) összesen (4 példányra).

Példa [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) -konfigurációra:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 32,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Ez a példa feltételezi, hogy 4 node.exe fut a virtuális gépen. Ha a virtuális gépen eltérő számú node.exe fut, akkor ennek megfelelően kell módosítania a maxsocket beállítást.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>A saját Node alkalmazásom túl sok PROCESSZORt fogyaszt

Előfordulhat, hogy a portálon a nagy CPU-fogyasztással kapcsolatban Azure App Servicera vonatkozó javaslatot kap. Bizonyos [mérőszámok](web-sites-monitor.md)figyeléséhez beállíthatja a figyelőket is. Ha ellenőrzi a CPU-használatot a [Azure Portal irányítópulton](../azure-monitor/essentials/metrics-charts.md), ellenőrizze a processzorok maximális értékeit, hogy ne hagyja ki a csúcsérték-értékeket.
Ha úgy gondolja, hogy az alkalmazása túl sok CPU-t használ, és nem tudja megmagyarázni, hogy miért, a Node-alkalmazás megkereséséhez.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>A Node-alkalmazás profilkészítése Azure App Serviceon V8-Profiler

Tegyük fel például, hogy van egy Hello World-alkalmazás, amelyet a következő módon szeretne profilt használni:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Ugrás a hibakeresési konzol webhelyére `https://yoursite.scm.azurewebsites.net/DebugConsole`

Lépjen be a site/wwwroot könyvtárba. Megjelenik egy parancssor, ahogy az az alábbi példában is látható:

![Képernyőkép, amely megjeleníti a hely/wwwroot könyvtárat és a parancssort.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Futtassa a következő parancsot: `npm install v8-profiler`.

Ez a parancs telepíti a V8-Profilert a Node modules \_ címtárban és annak összes függőségében.
Most szerkessze server.js az alkalmazás profilját.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Az előző kód a WriteConsoleLog függvényt, majd a profil kimenetét írja a "Profile. cpuprofile" fájlba a hely wwwroot. Kérelem küldése az alkalmazásnak. Megjelenik egy "Profile. cpuprofile" fájl, amely a hely wwwroot van létrehozva.

![A profil. cpuprofile fájlt megjelenítő képernyőkép.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Töltse le ezt a fájlt, és nyissa meg a Chrome F12-eszközökkel. Nyomja meg az F12 billentyűt a Chrome-ban, majd válassza a **profilok** fület. Kattintson a **Load (Betöltés** ) gombra. Válassza ki a letöltött Profile. cpuprofile fájlt. Kattintson az imént betöltött profilra.

![Képernyőkép, amely megjeleníti a betöltött profil. cpuprofile fájlt.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Láthatja, hogy az idő 95%-ában használták a WriteConsoleLog függvény. A kimenetben a probléma okozta pontos sorszámokat és forrásfájlokat is megjeleníti.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A saját Node alkalmazásom túl sok memóriát fogyaszt

Ha az alkalmazás túl sok memóriát vesz igénybe, akkor a portálon a nagy memória-használatról szóló értesítés jelenik meg Azure App Service. Beállíthat figyelőket bizonyos [mérőszámok](web-sites-monitor.md)megtekintéséhez. Ha ellenőrzi a memória használatát a [Azure Portal irányítópulton](../azure-monitor/essentials/metrics-charts.md), ügyeljen arra, hogy ellenőrizze a memória maximális értékeit, hogy ne hagyja ki a csúcsérték értékét.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Szivárgás észlelése és heap diff a node.js

A memwatch segítségével azonosíthatja a memóriavesztés azonosítására szolgáló [csomópontot](https://github.com/lloyd/node-memwatch) .
Ugyanúgy telepítheti, `memwatch` mint a V8-Profiler, és szerkesztheti a kódot a rögzítéshez és a diffekhez, hogy azonosítsa a memóriavesztés az alkalmazásban.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A node.exeek véletlenszerűen lettek legyilkolva

Néhány ok, amiért a node.exe véletlenszerűen leáll:

1. Az alkalmazás nem észlelt kivételeket dob – a d: \\ Home \\ LogFiles \\ alkalmazás \\logging-errors.txt fájljában adja meg az eldobott kivétel részleteit. Ennek a fájlnak a verem-nyomkövetése segíti az alkalmazás hibakeresését és javítását.
2. Az alkalmazás túl sok memóriát vesz igénybe, ami az első lépések során más folyamatokat is érint. Ha a virtuális gép teljes memóriája 100%-kal közelebb van, akkor a node.exet a Process Manager leölték. A Process Manager bizonyos folyamatokat tesz lehetővé, hogy más folyamatok is elvégezzenek némi munkát. A probléma megoldásához a memóriabeli szivárgáshoz adja meg az alkalmazás profilját. Ha az alkalmazás nagy mennyiségű memóriát igényel, a vertikális felskálázást egy nagyobb méretű virtuális gépre (ami növeli a virtuális gép számára elérhető RAM-ot).

### <a name="my-node-application-does-not-start"></a>A saját Node-alkalmazás nem indul el

Ha az alkalmazás az indításakor 500 hibát ad vissza, lehetséges, hogy néhány oka lehet:

1. A Node.exe nem szerepel a megfelelő helyen. NodeProcessCommandLine-beállítás keresése
2. A fő parancsfájl nem szerepel a megfelelő helyen. Ellenőrizze web.config, és győződjön meg arról, hogy a kezelők szakaszban található fő parancsfájl neve megegyezik a fő parancsfájl fájljával.
3. Web.config konfiguráció helytelen – a beállítások neveinek és értékeinek a megadásával.
4. Hideg indítás – az alkalmazása túl sokáig tart. Ha az alkalmazás a (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 másodpercnél hosszabb időt vesz igénybe, a iisnode egy 500-es hibát ad vissza. Növelje ezen beállítások értékeit az alkalmazás kezdési idejének megfelelően, hogy megakadályozza a iisnode időtúllépését és a 500-es hiba visszaadását.

### <a name="my-node-application-crashed"></a>A saját Node-alkalmazás összeomlott

Az alkalmazás nem észlelt kivételeket dob – a `d:\\home\\LogFiles\\Application\\logging-errors.txt` fájl részleteit a kidobott kivétel részleteinek megtekintéséhez. Ennek a fájlnak a verem-nyomkövetése segíti az alkalmazás diagnosztizálását és javítását.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A saját Node alkalmazásom túl sok időt vesz igénybe a kezdéshez (hidegindítás)

A hosszú alkalmazások indítási idejének gyakori oka a csomópont-modulok nagy száma \_ . Az alkalmazás az indításkor megpróbálja betölteni a fájlok nagy részét. Alapértelmezés szerint mivel a fájlok a Azure App Service hálózati megosztásán vannak tárolva, sok fájl betöltésével időt vehet igénybe.
Néhány megoldás a folyamat gyorsabb elvégzésére:

1. Próbálja meg a Node-modulok betöltését \_ , és ne töltse be az összes modult az alkalmazás indításakor. A lusta betöltési modulok esetében a kötelező ("modul") hívását kell végrehajtani, amikor ténylegesen szükség van a modulon belül a modul kódjának első végrehajtása előtt.
2. Azure App Service a helyi gyorsítótár nevű szolgáltatást kínálja. A szolgáltatás a hálózati megosztásról másolja a tartalmat a virtuális gép helyi lemezére. Mivel a fájlok helyiek, a csomópont-modulok betöltési ideje \_ sokkal gyorsabb.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http-állapota és alállapota

A `cnodeconstants` [forrásfájl](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) felsorolja az összes lehetséges állapot/részállapot kombinációt, amelyet a iisnode egy hiba miatt vissza tud adni.

Az alkalmazás sikertelen kérelmek ESEMÉNYTÁROLÁSI engedélyezése a Win32-hibakód megjelenítéséhez (Ügyeljen arra, hogy a sikertelen kérelmek ESEMÉNYTÁROLÁSI csak a nem üzemi célú helyeken engedélyezze a teljesítmény szempontjából).

| Http-állapot | Http-alállapot | Lehetséges ok? |
| --- | --- | --- |
| 500 |1000 |Némi probléma merült fel a IISNODE vonatkozó kérelem elküldésekor – ellenőrizze, hogy a node.exe elindult-e. Az indításkor a Node.exe összeomlott. Győződjön meg róla, hogy a web.config konfigurációjában hiba történt. |
| 500 |1001 |-Win32Error 0x2 – az alkalmazás nem válaszol az URL-címre. Ellenőrizze az URL-cím átírási szabályait, vagy ellenőrizze, hogy a megfelelő útvonalak vannak-e megadva az expressz alkalmazáshoz. -Win32Error 0x6d – nevesített cső foglalt – Node.exe nem fogad kérelmeket, mert a cső foglalt. Magas CPU-használat keresése. – Egyéb hibák – ellenőrizze, hogy a node.exe összeomlott-e. |
| 500 |1002 |Node.exe összeomlott – a d: \\ Home \\ logfiles \\logging-errors.txt a verem nyomkövetéséhez. |
| 500 |1003 |Pipe konfigurációs probléma – a nevesített cső konfigurációja helytelen. |
| 500 |1004-1018 |Hiba történt a kérelem elküldésekor vagy node.exere való válasz feldolgozásakor. Ellenőrizze, hogy a node.exe összeomlott-e. a d: \\ Home \\ logfiles \\logging-errors.txtának bejelölése a verem nyomkövetéséhez. |
| 503 |1000 |Nincs elég memória a nevesített pipe-kapcsolatok kiosztásához. Győződjön meg arról, hogy az alkalmazás mennyi memóriát fogyaszt. MaxConcurrentRequestsPerProcess-beállítási értékének megadása. Ha nem végtelen, és sok kérése van, növelje ezt az értéket a hiba elkerüléséhez. |
| 503 |1001 |A kérést nem lehetett elküldeni node.exe, mert az alkalmazás újrahasznosításra kerül. Az alkalmazás újrahasznosítása után a kérelmeket szabályosan kell kézbesíteni. |
| 503 |1002 |A Win32-hibakód tényleges okának ellenőrzése – a kérést nem sikerült elküldeni egy node.exe. |
| 503 |1003 |A nevesített cső túl elfoglalt – ellenőrizze, hogy a node.exe túlzott CPU-e. |

NODE.exe van egy nevű beállítás `NODE_PENDING_PIPE_INSTANCES` . Azure App Service esetén ez az érték 5000-re van állítva. Azt jelenti, hogy a node.exe a nevesített pipe-on egyszerre tud 5000-kérelmeket fogadni. Az értéknek elég jónak lennie a Azure App Serviceon futó legtöbb Node-alkalmazáshoz. A 503,1003-as érték nem látható a Azure App Service a `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>További erőforrások

Az alábbi hivatkozásokat követve további információkat tudhat meg a Azure App Service node.js alkalmazásairól.

* [Ismerkedés a Node.js-webalkalmazásokkal az Azure App Service-ben](quickstart-nodejs.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](/archive/blogs/azureossds/debugging-node-js-apps-on-azure-app-services)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](/archive/blogs/silverlining/windows-azure-websites-node-js)
* [Node.js fejlesztői központ](../nodejs-use-node-modules-azure-apps.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
