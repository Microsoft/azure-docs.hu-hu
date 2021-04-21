---
title: Diagnosztikai naplózás engedélyezése
description: Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, hogyan adhat hozzá rendszereket az alkalmazásához, és hogyan férhet hozzá az Azure által naplózott információkhoz.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833849"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnosztikai naplózás engedélyezése a Azure App Service
## <a name="overview"></a>Áttekintés
Az Azure beépített diagnosztikát biztosít, amely segítséget nyújt a App Service [hibakereséséhez.](overview.md) Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, hogyan adhat hozzá rendszereket az alkalmazásához, valamint hogyan férhet hozzá az Azure által naplózott információkhoz.

Ez a cikk a [Azure Portal](https://portal.azure.com) és az Azure CLI-t használja a diagnosztikai naplókhoz. További információ a diagnosztikai naplóknak a Visual Studio való használatával kapcsolatban: Az Azure hibaelhárítása a [Visual Studio.](troubleshoot-dotnet-visual-studio.md)

> [!NOTE]
> A cikkben található naplózási utasítások mellett az Azure Monitoring új, integrált naplózási funkcióval is szolgál. Erről a képességről további információt a Naplók küldése Azure Monitor [(előzetes verzió) szakaszban](#send-logs-to-azure-monitor-preview) talál. 
>
>

|Típus|Platform|Hely|Description|
|-|-|-|-|
| Alkalmazásnaplózás | Windows, Linux | App Service fájlrendszer és/vagy Azure Storage-blobok létrehozása | Naplózza az alkalmazáskód által létrehozott üzeneteket. Az üzeneteket a választott webes keretrendszer, vagy közvetlenül az alkalmazás kódját használva, az Ön által választott szabványos naplózási mintával generálhatja. Minden üzenethez a következő kategóriák valamelyike van hozzárendelve: **Kritikus,** **Hiba,** **Figyelmeztetés,** **Információ,** **Hibakeresés** és **Nyomkövetés.** Ha engedélyezi az alkalmazásnaplózást, a súlyossági szint beállításával kiválaszthatja, hogy milyen részletes naplózást szeretne.|
| Webkiszolgáló naplózása| Windows | App Service fájlrendszer vagy Azure Storage-blobok| Nyers HTTP-kérelemadatok [W3C kibővített naplófájlformátumban.](/windows/desktop/Http/w3c-logging) Minden naplóüzenet olyan adatokat tartalmaz, mint a HTTP-metódus, az erőforrás URI-címe, az ügyfél IP-címe, az ügyfélport, a felhasználói ügynök, a válaszkód stb. |
| Részletes hibaüzenetek| Windows | App Service fájlrendszer | Az ügyfélböngészőnek elküldött *.htm* hibalapok másolatai. Biztonsági okokból nem szabad részletes hibalapokat küldeni az éles környezetben található ügyfeleknek, de a App Service minden alkalommal mentheti a hibaoldalt, amikor 400-as vagy annál nagyobb HTTP-kóddal kapcsolatos alkalmazáshiba történik. A lap olyan információkat tartalmazhat, amelyek alapján meghatározható, hogy a kiszolgáló miért adja vissza a hibakódot. |
| Sikertelen kérelmek nyomkövetése | Windows | App Service fájlrendszer | Részletes nyomkövetési információk a sikertelen kérelmekről, beleértve a kérés feldolgozásához használt IIS-összetevők nyomkövetését és az egyes összetevőkben szükséges időt. Akkor hasznos, ha javítani szeretné a webhely teljesítményét, vagy el szeretne különíteni egy adott HTTP-hibát. Minden sikertelen kérelemhez létrejön egy mappa, amely tartalmazza az XML-naplófájlt, valamint az XSL-stíluslap, amellyel a naplófájlt megtekintheti. |
| Üzembe helyezés naplózása | Windows, Linux | App Service fájlrendszer | Naplózza, hogy mikor tesz közzé tartalmat egy alkalmazásban. Az üzembe helyezés naplózása automatikusan történik, és nincsenek konfigurálható beállítások az üzembe helyezés naplózása számára. Segít meghatározni, hogy miért volt sikertelen az üzembe helyezés. Ha például egyéni üzembe helyezési parancsfájlt [használ,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)akkor az üzembe helyezés naplózása segítségével megállapíthatja, hogy miért hibás a parancsfájl. |

> [!NOTE]
> App Service dedikált, interaktív diagnosztikai eszközt biztosít az alkalmazás hibaelhárításához. További információ: A [Azure App Service áttekintése.](overview-diagnostics.md)
>
> Emellett más Azure-szolgáltatásokat is használhat az alkalmazás naplózási és monitorozási képességeinek javítására, például a [Azure Monitor.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Alkalmazásnaplózás engedélyezése (Windows)

> [!NOTE]
> A Blob Storage alkalmazásnaplózása csak a blobtárolóval azonos régióban lévő tárfiókokat App Service

Ha engedélyezni szeretné az alkalmazásnaplózást a windowsos alkalmazásokhoz a [Azure Portal,](https://portal.azure.com)lépjen az alkalmazáshoz, és válassza a **App Service lehetőséget.**

Válassza **a Be** Application Logging **(fájlrendszer)** vagy **Application Logging (Blob)** lehetőséget, vagy mindkettőt. 

A **Fájlrendszer beállítás** ideiglenes hibakeresési célokra szolgál, és 12 órán belül kikapcsolja magát. A **Blob** beállítás hosszú távú naplózáshoz való, és szüksége van egy Blob Storage-tárolóra, amelybe naplókat írhat.  A **Blob** beállítás további információkat is tartalmaz a naplóüzenetek között, például a naplóüzenet (), a szálazonosító ( ) forrás virtuálisgép-példányának azonosítóját, valamint egy részletesebb `InstanceId` időbélyeget ( `Tid` [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Jelenleg csak .NET-alkalmazásnaplók írhatóak a blobtárolóba. A Java, a PHP Node.js és a Python-alkalmazásnaplók csak a App Service fájlrendszerben tárolhatók (kódmódosítások nélkül, amelyek naplókat írnak külső tárolóba).
>
> Ha újra [](../storage/common/storage-account-create.md)létrehozza a tárfiók hozzáférési kulcsait, a frissített hozzáférési kulcsok használatára alaphelyzetbe kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A Configure **(Konfigurálás)** lapon állítsa a megfelelő naplózási funkciót Off **(Ki) beállításra.** Mentse a beállítást.
> 2. Engedélyezze ismét a tárfiókblobba való naplózást. Mentse a beállítást.
>
>

Válassza ki **a naplózni** kívánt szint vagy részletességi szintet. Az alábbi táblázat az egyes szinten található naplókategóriákat mutatja be:

| Level | Tartalmazott kategóriák |
|-|-|
|**Disabled** | Nincsenek |
|**Hiba** | Hiba, Kritikus |
|**Figyelmeztetés** | Figyelmeztetés, hiba, kritikus|
|**Tájékoztatás** | Információ, Figyelmeztetés, Hiba, Kritikus|
|**Részletes** | Nyomkövetés, hibakeresés, információ, figyelmeztetés, hiba, kritikus (minden kategória) |

Ha elkészült, válassza a **Mentés lehetőséget.**

## <a name="enable-application-logging-linuxcontainer"></a>Alkalmazásnaplózás engedélyezése (Linux/Tároló)

Ha engedélyezni szeretné az alkalmazásnaplózást Linux-alkalmazások vagy egyéni tárolóalkalmazások számára a [Azure Portal,](https://portal.azure.com)lépjen az alkalmazáshoz, és válassza a App Service **lehetőséget.**

Az **Alkalmazásnaplózás beállításban** válassza a **Fájlrendszer lehetőséget.**

A **Kvóta (MB) beállításban** adja meg az alkalmazásnaplók lemezkvótáját. A **Megőrzési időtartam (nap) beállításban** adja meg, hogy a rendszer hány napig őrizze meg a naplókat.

Ha elkészült, válassza a **Mentés lehetőséget.**

## <a name="enable-web-server-logging"></a>Webkiszolgáló-naplózás engedélyezése

Ha engedélyezni szeretné a webkiszolgáló-naplózást a windowsos alkalmazásokhoz a [Azure Portal,](https://portal.azure.com)lépjen az alkalmazáshoz, és válassza a **App Service lehetőséget.**

**Webkiszolgáló-naplózás esetén válassza** a **Storage** lehetőséget  a blobtároló naplóinak tárolásához, vagy a fájlrendszert a App Service tárolásához. 

A **Megőrzési időtartam (nap) beállításban** adja meg, hogy a rendszer hány napig őrizze meg a naplókat.

> [!NOTE]
> Ha újra létrehozza a tárfiók hozzáférési [kulcsait,](../storage/common/storage-account-create.md)a frissített kulcsok használatára alaphelyzetbe kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A Configure **(Konfigurálás)** lapon állítsa a megfelelő naplózási funkciót Off **(Ki) beállításra.** Mentse a beállítást.
> 2. Engedélyezze újra a naplózást a tárfiókblobba. Mentse a beállítást.
>
>

Ha elkészült, válassza a **Mentés lehetőséget.**

## <a name="log-detailed-errors"></a>Részletes hibák naplózása

A hibalap vagy a Windows-alkalmazások sikertelen kérelem-nyomkövetésének mentéséhez a Azure Portal [lépjen](https://portal.azure.com)az alkalmazásra, és válassza a **naplók App Service lehetőséget.**

A **Részletes hibanaplózás vagy** Sikertelen kérelmek **nyomkövetése alatt válassza** a **Be** lehetőséget, majd kattintson a **Mentés gombra.**

Mindkét naplótípust a rendszer a App Service tárolja. A rendszer legfeljebb 50 hibát (fájlokat/mappákat) őriz meg. Ha a HTML-fájlok száma meghaladja az 50-et, a rendszer automatikusan törli a legrégebbi 26 hibát.

## <a name="add-log-messages-in-code"></a>Naplóüzenetek hozzáadása a kódban

Az alkalmazás kódjában a szokásos naplózási rendszerekkel naplóüzeneteket küld az alkalmazásnaplókba. Például:

- ASP.NET alkalmazások a [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) osztály használatával naplózhatja az adatokat az alkalmazás diagnosztikai naplójában. Például:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Alapértelmezés szerint a ASP.NET Core a [Microsoft.Extensions.Logging.AzureAppServices naplózási](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) szolgáltatót használja. További információ: Alapvető [naplózás ASP.NET Az Azure-ban.](/aspnet/core/fundamentals/logging/) További információ a WebJobs SDK naplózásával kapcsolatban: [Az Azure WebJobs SDK első lépései](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Naplók streamelése

A naplók valós idejű streamelése előtt engedélyezze a kívánt naplótípust. A .txt, .log vagy .htm végződésű fájlokba írt, *a /LogFiles* könyvtárban (d:/home/logfiles) tárolt adatokat az App Service.

> [!NOTE]
> Bizonyos típusú naplózási puffer ír a naplófájlba, ami a streamben nem sorrendben történt eseményeket eredményezhet. Például egy alkalmazásnapló-bejegyzés, amely akkor fordul elő, amikor egy felhasználó felkeres egy oldalt, a streamben a lapkérés megfelelő HTTP-naplóbejegyzése előtt lehet megjeleníteni.
>

### <a name="in-azure-portal"></a>A Azure Portal

Ha naplókat szeretne streamelni a [Azure Portal,](https://portal.azure.com)lépjen az alkalmazáshoz, és válassza a **Naplóstream lehetőséget.** 

### <a name="in-cloud-shell"></a>A Cloud Shell

A naplók élő streamelésekor [Cloud Shell](../cloud-shell/overview.md)a következő parancsot:

> [!IMPORTANT]
> Előfordulhat, hogy ez a parancs nem működik a Linux App Service-csomagban üzemeltetett webalkalmazásokkal.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Adott naplótípusok, például a HTTP szűréséhez használja a **--Provider paramétert.** Például:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>A helyi terminálon

A naplók helyi konzolon való streamelésekor telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be a fiókjába.](/cli/azure/authenticate-azure-cli) Miután bejelentkezett, kövesse a [Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Naplófájlok elérése

Ha az Azure Storage-blobok beállítást naplótípushoz konfigurálja, szüksége lesz egy, az Azure Storage-ral használható ügyféleszközre. További információ: [Azure Storage-ügyféleszközök.](../storage/common/storage-explorers.md)

Az App Service fájlrendszerben tárolt naplóknál a legegyszerűbb, ha letölti a ZIP-fájlt a böngészőbe a következő címről:

- Linux-/tárolóalkalmazások: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-alkalmazások: `https://<app-name>.scm.azurewebsites.net/api/dump`

Linux-/tárolóalkalmazások esetén a ZIP-fájl a Docker-gazdagép és a Docker-tároló konzolkimeneti naplóit is tartalmazza. A tömörített alkalmazásokban a ZIP-fájl minden példányhoz egy naplókészletet tartalmaz. A App Service fájlrendszerben ezek a naplófájlok a */home/LogFiles könyvtár tartalmai.*

Windows-alkalmazások esetén a ZIP-fájl a *D:\Home\LogFiles* könyvtár tartalmát tartalmazza a App Service fájlrendszerben. A struktúrája a következő:

| Napló típusa | Címtár | Description |
|-|-|-|
| **Alkalmazásnaplók** |*/LogFiles/Application/* | Egy vagy több szövegfájlt tartalmaz. A naplóüzenetek formátuma a használt naplózási szolgáltatótól függ. |
| **Sikertelen kérelmek nyomkövetései** | */LogFiles/W3SVC##########/* | XML-fájlokat és egy XSL-fájlt tartalmaz. A formázott XML-fájlokat a böngészőben tudja megtekinteni. |
| **Részletes hibanaplók** | */LogFiles/DetailedErrors/* | HTM-hibafájlokat tartalmaz. A HTM-fájlokat megtekintheti a böngészőben.<br/>A sikertelen kérelmek nyomkövetésének egy másik módja, ha a portálon az alkalmazás oldalára navigál. A bal oldali menüben válassza a Problémák diagnosztizálása és megoldása **lehetőséget,** majd keressen rá a Sikertelen kérelmek **nyomkövetési** naplói kifejezésre, majd kattintson az ikonra a nyomkövetési adatok tallózásához és megtekintéséhez. |
| **Webkiszolgáló-naplók** | */LogFiles/http/RawLogs/* | A W3C kibővített naplófájlformátummal formázott [szövegfájlokat tartalmaz.](/windows/desktop/Http/w3c-logging) Ezek az információk egy szövegszerkesztővel vagy egy olyan segédprogrammal olvashatók, mint a [Log Parser.](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>App Service nem támogatja a `s-computername` , `s-ip` vagy `cs-version` mezőket. |
| **Üzembe helyezési naplók** | */LogFiles/Git/* és */deployments/* | A belső üzembe helyezési folyamatok által létrehozott naplókat, valamint a Git üzemelő példányainak naplóit tartalmazza. |

## <a name="send-logs-to-azure-monitor-preview"></a>Naplók küldése Azure Monitor (előzetes verzió)

Az új [Azure Monitor](https://aka.ms/appsvcblog-azmon)segítségével diagnosztikai [beállításokat (előzetes verzió)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) hozhat létre, amelyek naplókat küldenek a tárfiókok, a Event Hubs a Log Analytics számára. 

> [!div class="mx-imgBorder"]
> ![Diagnosztikai beállítások (előzetes verzió)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Támogatott naplótípusok

Az alábbi táblázat a támogatott naplótípusokat és -leírásokat tartalmazza: 

| Napló típusa | Windows | Windows-tároló | Linux | Linux-tároló | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Igen | Igen | Yes | Standard kimenet és standard hiba |
| AppServiceHTTPLogs (AppServiceHTTP-naplók) | Igen | Igen | Igen | Yes | Webkiszolgáló-naplók |
| AppServiceEnvironmentPlatformLogs | Yes | N.A. | Igen | Yes | App Service Environment: méretezés, konfigurációváltozások és állapotnaplók|
| AppServiceAuditLogs | Igen | Igen | Igen | Yes | Bejelentkezési tevékenység FTP-n és Kudun keresztül |
| AppServiceFileAuditLogs | Igen | Yes | Tba | Tba | A webhely tartalmán végrehajtott fájlváltozások; **csak prémium szintű és magasabb szintű csomagokhoz érhető el** |
| AppServiceAppLogs | ASP .NET & Java Tomcat <sup>1</sup> | ASP .NET & Java Tomcat <sup>1</sup> | Java SE & Tomcat Images <sup>2</sup> | Java SE & Tomcat Images <sup>2</sup> | Alkalmazásnaplók |
| AppServiceIPSecAuditLogs  | Igen | Igen | Igen | Yes | IP-szabályokból származó kérések |
| AppServicePlatformLogs  | Tba | Igen | Igen | Yes | Tárolóművelet-naplók |
| AppServiceAntivirusScanAuditLogs | Igen | Igen | Igen | Yes | [Víruskeresési naplók a](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) Microsoft Defender használatával; **csak a Prémium szinthez érhető el** | 

<sup>1</sup> Java Tomcat-alkalmazások esetén adja hozzá a "TOMCAT_USE_STARTUP_BAT" értéket az alkalmazásbeállításokhoz, és állítsa false (hamis) vagy 0 (0) értékre. A Legújabb *Tomcat-verziónak* kell lennie, és a *java.util.logging segédprogramot kell használnia.*

<sup>2</sup> Java SE-alkalmazások esetén adja hozzá az "$WEBSITE_AZMON_PREVIEW_ENABLED" gombra az alkalmazásbeállításokat, és állítsa true (igaz) vagy (1) értékre.

## <a name="next-steps"></a><a name="nextsteps"></a> Következő lépések
* [Naplók lekérdezése Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Az Azure App Service](web-sites-monitor.md)
* [Hibaelhárítási Azure App Service a Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Alkalmazásnaplók elemzése a HDInsightban](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
