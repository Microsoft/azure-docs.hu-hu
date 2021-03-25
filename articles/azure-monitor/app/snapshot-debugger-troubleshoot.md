---
title: Az Azure Application Insights hibáinak megoldása Snapshot Debugger
description: Ez a cikk hibaelhárítási lépéseket és információkat nyújt a fejlesztők számára a Application Insights Snapshot Debugger engedélyezéséhez és használatához.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bd83367ae073e03f03188cdf62cb60faaad7ac97
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026453"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> A Application Insights Snapshot Debugger engedélyezésével vagy a pillanatképek megtekintésével kapcsolatos problémák elhárítása
Ha engedélyezte Application Insights Snapshot Debugger az alkalmazáshoz, de nem tekinti meg a kivételekhez tartozó pillanatképeket, a következő utasításokat követve elháríthatja a hibát.

Számos különböző oka lehet annak, hogy a pillanatképek miért nem hozhatók létre. A Snapshot Health-ellenőrzés futtatásával elindíthatja a lehetséges gyakori okok némelyikét.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Győződjön meg arról, hogy a megfelelő Snapshot Debugger végpontot használja

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) és az [Azure China](/azure/china/resources-developer-guide).

A Application Insights SDK-t használó App Service és alkalmazásokhoz a következő módon kell frissítenie a kapcsolódási karakterláncot a Snapshot Debugger támogatott felülbírálásai alapján:

|A kapcsolatok karakterláncának tulajdonsága    | Egyesült államokbeli kormányzati felhő | Kínai felhő |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

További információ az egyéb kapcsolatok felülbírálásáról: [Application Insights dokumentáció](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

Függvényalkalmazás a következő `host.json` támogatott felülbírálások használatával kell frissítenie:

|Tulajdonság    | Egyesült államokbeli kormányzati felhő | Kínai felhő |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Alább látható egy példa az `host.json` Egyesült államokbeli kormányzati Felhőbeli ügynök végpontjának frissítésére:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>A pillanatkép-állapot-ellenőrzési eszköz használata
Számos gyakori probléma miatt az Open debug pillanatkép nem jelenik meg. Elavult Snapshot Collector használata, például:; a napi feltöltési korlát elérése; vagy lehet, hogy a pillanatkép csak hosszú időt vesz fel a feltöltéshez. Az általános problémák elhárításához használja a pillanatkép állapotának ellenőrzését.

A végpontok közötti nyomkövetés nézet kivétel ablaktábláján található egy hivatkozás, amely a pillanatkép állapotának ellenőrzését végzi.

![Adja meg a pillanatkép állapotának ellenőrzését](./media/snapshot-debugger/enter-snapshot-health-check.png)

Az interaktív, csevegéshez hasonló kezelőfelület gyakori problémákat keres, és végigvezeti Önt a hibák elhárításában.

![Állapot-ellenőrzési](./media/snapshot-debugger/healthcheck.png)

Ha ez nem oldja meg a problémát, tekintse meg a következő kézi hibaelhárítási lépéseket.

## <a name="verify-the-instrumentation-key"></a>A kialakítási kulcs ellenőrzése

Győződjön meg arról, hogy a megfelelő kialakítási kulcsot használja a közzétett alkalmazásban. A rendszerállapot-kulcsot általában a ApplicationInsights.config fájlból olvassa be a rendszer. Ellenőrizze, hogy az érték megegyezik-e a portálon megjelenő Application Insights erőforrás rendszerállapot-kulcsával.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>TLS/SSL-ügyfélbeállítások keresése (ASP.NET)

Ha olyan ASP.NET-alkalmazással rendelkezik, amelyet Azure App Service vagy az IIS-ben üzemeltet a virtuális gépen, az alkalmazás nem tud csatlakozni a Snapshot Debugger szolgáltatáshoz, mert hiányzik az SSL biztonsági protokoll.

[A Snapshot Debugger végponthoz a TLS 1,2-es verziója szükséges](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Az SSL biztonsági protokollok készlete a httpRuntime targetFramework értékének egyike, amely a System. Web szakaszban található, web.config. Ha a httpRuntime targetFramework: 4.5.2 vagy alacsonyabb, akkor a TLS 1,2 alapértelmezés szerint nem szerepel.

> [!NOTE]
> A httpRuntime targetFramework értéke független az alkalmazás létrehozásakor használt cél-keretrendszertől.

A beállítás megadásához nyissa meg web.config fájlt, és keresse meg a System. Web szakaszt. Győződjön meg arról, hogy a `targetFramework` for `httpRuntime` értéke 4,6 vagy újabb.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> A httpRuntime targetFramework értékének módosítása megváltoztatja az alkalmazásra alkalmazott futásidejű hirtelen fordulatokat, és más, finom viselkedési változásokat eredményezhet. Ezt a módosítást követően feltétlenül tesztelje alaposan az alkalmazást. A kompatibilitási változások teljes listáját a következő témakörben tekintheti meg: https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Ha a targetFramework értéke 4,7 vagy újabb, akkor a Windows meghatározza az elérhető protokollokat. Azure App Service a TLS 1,2 elérhető. Ha azonban saját virtuális gépet használ, lehetséges, hogy engedélyeznie kell a TLS 1,2-et az operációs rendszerben.

## <a name="preview-versions-of-net-core"></a>A .NET Core előzetes verziói
Ha a .NET Core előzetes verzióját használja, vagy az alkalmazása Application Insights SDK-ra hivatkozik közvetlenül vagy közvetve egy függő szerelvényen keresztül, kövesse az [Snapshot Debugger engedélyezése más környezetekben](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)című témakör utasításait.

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>A diagnosztikai szolgáltatások helyének bővítményének állapota lap
Ha a Snapshot Debugger a portál [Application Insights paneljén](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) engedélyezte, azt a diagnosztikai szolgáltatások helyének bővítménye engedélyezte.

> [!NOTE]
> Application Insights Snapshot Debugger kód nélküli telepítése a .NET Core támogatási szabályzatot követi.
> További információ a támogatott futtatókörnyezetekről: [.net Core támogatási szabályzat](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

A bővítmény állapot lapját a következő URL-címen tekintheti meg: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Az állapot lap hivatkozásának tartománya a felhőtől függően eltérő lesz.
Ez a tartomány a App Service kudu-felügyeleti helyével azonos lesz.

Ez az állapot lap a Profiler és a Snapshot Collector ügynökök telepítési állapotát jeleníti meg. Ha váratlan hiba történt, akkor megjelenik, és bemutatja, hogyan kell kijavítani.

A App Service kudu felügyeleti webhelyének beszerzéséhez használja az állapot lap alap URL-címét:
1. Nyissa meg a App Service alkalmazást a Azure Portalban.
2. Válassza a **speciális eszközök** lehetőséget, vagy keressen rá a **kudu**.
3. Válassza az **Indítás** lehetőséget.
4. Ha a kudu felügyeleti webhelyen van, az URL-címben **fűzze hozzá a következőt, `/DiagnosticServices` majd nyomja le az ENTER** billentyűt.
 Ez a következőképpen fog kinézni: `https://<kudu-url>/DiagnosticServices`

A következőhöz hasonló állapot lapot jelenít meg: ![ diagnosztikai szolgáltatások állapota lap](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Frissítés a NuGet csomag legújabb verziójára
A Snapshot Debugger engedélyezése alapján a következő lehetőségek érhetők el:

* Ha Snapshot Debugger engedélyezte a [portálon a Application Insights ablaktáblán](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), akkor az alkalmazásnak már a legújabb NuGet-csomagot kell futtatnia.

* Ha a Snapshot Debugger a [Microsoft. ApplicationInsights. snapshotcollector nugetcsomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-csomaggal együtt engedélyezte, a Visual Studio NuGet csomagkezelő segítségével ellenőrizze, hogy a Microsoft. ApplicationInsights. snapshotcollector nugetcsomag legújabb verzióját használja-e.

A legújabb frissítések és hibajavítások [olvassa el a kibocsátási megjegyzéseket](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>A feltöltő naplók keresése

A pillanatkép létrehozása után létrejön egy minidump-fájl (. dmp) a lemezen. Egy külön feltöltő folyamat létrehozza a minidump-fájlt, és feltölti azt a kapcsolódó PDBs együtt, hogy Application Insights Snapshot Debugger tárterületet. Miután a minidump sikeresen feltöltötte, a rendszer törli a lemezről. A feltöltő folyamat naplófájljai a lemezen maradnak. App Service-környezetben ezeket a naplókat a alkalmazásban találja `D:\Home\LogFiles` . A naplófájlok megkereséséhez használja a App Service kudu felügyeleti webhelyét.

1. Nyissa meg a App Service alkalmazást a Azure Portalban.
2. Válassza a **speciális eszközök** lehetőséget, vagy keressen rá a **kudu**.
3. Válassza az **Indítás** lehetőséget.
4. A **hibakeresési konzol** legördülő listájában válassza a **cmd** elemet.
5. Válassza ki a **LogFiles** elemet.

Legalább egy olyan fájlt meg kell jelennie, amelynek a neve a `Uploader_` vagy `SnapshotUploader_` a `.log` kiterjesztéssel kezdődik. Válassza ki a megfelelő ikont a naplófájlok letöltéséhez, vagy nyissa meg őket egy böngészőben.
A fájlnév egy egyedi utótagot tartalmaz, amely a App Service példányt azonosítja. Ha a App Service-példány több gépen üzemel, külön naplófájlok vannak az egyes gépekhez. Ha a feltöltő új minidump-fájlt észlel, azt a rendszer rögzíti a naplófájlban. Íme egy példa egy sikeres pillanatképre és feltöltésre:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> A fenti példa a Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag NuGet csomag 1.2.0 verziója. A korábbi verziókban a rendszer meghívja a feltöltő folyamatot, `MinidumpUploader.exe` és a napló kevésbé részletes.

Az előző példában a kialakítási kulcs a `c12a605e73c44346a984e00000000000` . Ennek az értéknek meg kell egyeznie az alkalmazás kialakítási kulcsával.
A minidump az AZONOSÍTÓval rendelkező pillanatképhez van társítva `139e411a23934dc0b9ea08a626db16c5` . Ezt az azonosítót később is használhatja a Application Insights Analytics szolgáltatásban található társított kivételi rekord megkereséséhez.

A feltöltő 15 percenként megkeresi az új PDBs. Bemutatunk egy példát:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

A App Serviceban _nem_ üzemeltetett alkalmazások esetén a feltöltött naplók a minidumps azonos mappában találhatók: `%TEMP%\Dumps\<ikey>` (ahol a kialakítási `<ikey>` kulcs).

## <a name="troubleshooting-cloud-services"></a>Hibaelhárítási Cloud Services
Cloud Services az alapértelmezett ideiglenes mappa túl kicsi lehet a minidump-fájlok tárolására, ami az elveszett pillanatképeket eredményezi.

A szükséges lemezterület az alkalmazás teljes munkakészletén és az egyidejű Pillanatképek számától függ.

A 32 bites ASP.NET webes szerepkör munkakészlete általában 200 MB és 500 MB között van. Legalább két párhuzamos pillanatkép használatát teszi lehetővé.

Ha például az alkalmazás 1 GB-nyi teljes munkakészletet használ, győződjön meg arról, hogy legalább 2 GB lemezterület van a pillanatképek tárolásához.

Az alábbi lépéseket követve konfigurálhatja a Cloud Service-szerepkört egy dedikált helyi erőforrással a pillanatképekhez.

1. Adjon hozzá egy új helyi erőforrást a Cloud Service-hez a Cloud Service Definition (. csdef) fájl szerkesztésével. Az alábbi példa egy 5 GB méretű nevű erőforrást határoz meg `SnapshotStore` .
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Módosítsa a szerepkör indítási kódját egy olyan környezeti változó hozzáadásához, amely a `SnapshotStore` helyi erőforrásra mutat. A feldolgozói szerepkörök esetében a kódot hozzá kell adni a szerepkör `OnStart` metódusához:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Webes szerepkörök (ASP.NET) esetén a kódot hozzá kell adni a webalkalmazás `Application_Start` metódusához:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. A szerepkör ApplicationInsights.config fájljának frissítése a által használt ideiglenes mappa helyének felülbírálásához `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Az árnyékmásolat-mappa felülbírálása

A Snapshot Collector indításakor megpróbál olyan mappát találni a lemezen, amely alkalmas a pillanatkép-feltöltő folyamat futtatására. A kiválasztott mappát árnyékmásolat-mappaként nevezzük.

A Snapshot Collector néhány jól ismert helyet ellenőriz, így biztos lehet benne, hogy rendelkezik a pillanatkép-feltöltő bináris fájljainak másolásához szükséges engedélyekkel. A következő környezeti változók használatosak:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- IDEIGLENES

Ha nem található megfelelő mappa, Snapshot Collector a következő hibaüzenetet küldi: _"nem található megfelelő árnyékmásolat-mappa"._

Ha a másolás sikertelen, Snapshot Collector hibát jelez `ShadowCopyFailed` .

Ha a feltöltőt nem lehet elindítani, Snapshot Collector `UploaderCannotStartFromShadowCopy` hibát jelez. Az üzenet törzse gyakran tartalmaz `System.UnauthorizedAccessException` . Ez a hiba általában azért fordul elő, mert az alkalmazás csökkentett engedélyekkel rendelkező fiók alatt fut. A fióknak van engedélye az árnyékmásolat mappájába való írásra, de nem rendelkezik a kód végrehajtásához szükséges engedéllyel.

Mivel ezek a hibák általában az indítás során történnek, általában a `ExceptionDuringConnect` _"feltöltő nem indult el."_ hibaüzenet jelenik meg.

Ezeknek a hibáknak a megkerülő megoldásához manuálisan megadhatja az árnyékmásolat-mappát a `ShadowCopyFolder` konfigurációs lehetőség használatával. Például ApplicationInsights.config használatával:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Vagy ha a appsettings.jst .NET Core-alkalmazással használja:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Kivételek keresése a pillanatképekkel a Application Insights Search használatával

Pillanatkép létrehozásakor a throwing kivétel egy pillanatkép-AZONOSÍTÓval van megjelölve. Ez a Pillanatkép-azonosító egyéni tulajdonságként szerepel, ha a kivételt Application Insightsre jelentik. A **Keresés** a Application Insightsban lehetőséggel megkeresheti az `ai.snapshot.id` Egyéni tulajdonsággal rendelkező összes rekordot.

1. Tallózással keresse meg Application Insights erőforrását a Azure Portalban.
2. Válassza a **Keresés** lehetőséget.
3. Írja be `ai.snapshot.id` a keresett szöveget a keresőmezőbe, majd nyomja le az ENTER billentyűt.

![Telemetria keresése pillanatkép-AZONOSÍTÓval a portálon](./media/snapshot-debugger/search-snapshot-portal.png)

Ha a keresés nem ad vissza eredményt, a rendszer nem jelentett pillanatképeket a kijelölt időtartományban Application Insights.

Ha meg szeretne keresni egy adott pillanatkép-azonosítót a feltöltő naplókból, írja be ezt az azonosítót a keresőmezőbe. Ha nem találja a már ismert Pillanatképek rekordjait, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy a megfelelő Application Insights erőforrást tekinti-e meg a kialakítási kulcs ellenőrzésével.

2. A feltöltő napló időtartományát használva állítsa be a keresés időtartomány-szűrőjét, hogy az adott időtartományra vonatkozzon.

Ha még mindig nem látja a pillanatkép-AZONOSÍTÓval kapcsolatos kivételt, akkor a rendszer nem jelentett a kivételi rekordot Application Insights. Ez akkor fordulhat elő, ha az alkalmazás a pillanatkép elvégzése után összeomlott, de a kivétel rekordjának bejelentése előtt megtörtént. Ebben az esetben tekintse meg a App Service-naplókat, és ellenőrizze, `Diagnose and solve problems` hogy nem történt-e váratlan újraindítás vagy nem kezelt kivétel.

## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy-vagy tűzfalszabályok szerkesztése

Ha az alkalmazás proxyn vagy tűzfalon keresztül csatlakozik az internethez, előfordulhat, hogy frissítenie kell a szabályokat a Snapshot Debugger szolgáltatással való kommunikációhoz.

A Application Insights Snapshot Debugger által használt IP-címeket a Azure Monitor szolgáltatás címkéje tartalmazza. További információ: szolgáltatás- [címkék dokumentációja](../../virtual-network/service-tags-overview.md).