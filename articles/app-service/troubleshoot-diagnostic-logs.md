---
title: Diagnosztikai naplózás engedélyezése
description: Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá rendszerállapotot az alkalmazáshoz, valamint hogyan érheti el az Azure által naplózott információkat.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 44e18be9d66131ad5f4a3ebcc039621ec9e9dbe6
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452254"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnosztikai naplózás engedélyezése a Azure App Serviceban lévő alkalmazásokhoz
## <a name="overview"></a>Áttekintés
Az Azure beépített diagnosztika segítségével segíti az [app Service-alkalmazások](overview.md)hibakeresését. Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá rendszerállapotot az alkalmazáshoz, valamint hogyan érheti el az Azure által naplózott információkat.

Ez a cikk a [Azure Portal](https://portal.azure.com) és az Azure CLI-t használja a diagnosztikai naplókhoz való együttműködéshez. További információ a diagnosztikai naplók használatáról a Visual Studióban: [Az Azure hibaelhárítása a Visual Studióban](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> A jelen cikkben szereplő naplózási utasítások mellett új, integrált naplózási képesség áll rendelkezésre az Azure monitoring szolgáltatással. Erről a képességről a [naplók küldése Azure monitor (előzetes verzió)](#send-logs-to-azure-monitor-preview) szakaszban talál további tájékoztatást. 
>
>

|Típus|Platform|Hely|Leírás|
|-|-|-|-|
| Alkalmazásnaplózás | Windows, Linux | App Service fájlrendszer és/vagy Azure Storage-Blobok | Az alkalmazás kódjában létrehozott üzenetek naplózása. Az üzeneteket a választott webes keretrendszer hozza létre, vagy az alkalmazás kódjából közvetlenül a nyelv szabványos naplózási mintájának használatával. Minden üzenethez a következő kategóriák egyike van rendelve: **kritikus**, **hiba**, **Figyelmeztetés**, **információ**, **hibakeresés** és **nyomkövetés**. Kiválaszthatja, hogy milyen részletességgel szeretné a naplózást beállítani a súlyossági szint beállításával az alkalmazások naplózásának engedélyezésekor.|
| Webkiszolgáló naplózása| Windows | App Service fájlrendszer vagy Azure Storage-Blobok| Nyers HTTP-kérelmekre vonatkozó, a [W3C bővített naplófájl formátuma](/windows/desktop/Http/w3c-logging). Minden naplóbejegyzés olyan adatforrást tartalmaz, mint például a HTTP-metódus, az erőforrás URI-ja, az ügyfél IP-címe, az ügyfél portja, a felhasználói ügynök, a válasz kódja stb. |
| Részletes hibaüzenetek| Windows | App Service fájlrendszer | Az ügyfél böngészőjébe küldendő *. htm* -hibaüzenetek lapjai. Biztonsági okokból a rendszer nem tudja elküldeni a részletes hibaüzeneteket az éles környezetben lévő ügyfeleknek, de App Service a hibaüzenetet minden alkalommal mentheti, amikor olyan alkalmazáshiba történik, amelynek HTTP-kódja 400 vagy nagyobb. A lap tartalmazhat olyan információkat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adja vissza a hibakódot. |
| Sikertelen kérelmek nyomkövetése | Windows | App Service fájlrendszer | Részletes nyomkövetési információk a sikertelen kérelmekről, beleértve a kérés feldolgozásához használt IIS-összetevők nyomkövetését és az egyes összetevőkre vonatkozó időt. Ez akkor hasznos, ha javítani szeretné a hely teljesítményét, vagy egy adott HTTP-hibát szeretne elkülöníteni. A rendszer az összes sikertelen kérelemhez létrehoz egy mappát, amely tartalmazza az XML-naplófájlt, valamint az XSL-stíluslapot, amellyel a naplófájlt megtekintheti. |
| Központi telepítés naplózása | Windows, Linux | App Service fájlrendszer | Naplók, amikor tartalmat tesz közzé egy alkalmazásban. A központi telepítés naplózása automatikusan megtörténik, és nincsenek konfigurálható beállítások a telepítési naplózáshoz. Segít eldönteni, miért nem sikerült üzembe helyezni a központi telepítést. Ha például [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)használ, a központi telepítés naplózásával határozhatja meg, hogy miért sikertelen a parancsfájl. |

> [!NOTE]
> A App Service egy dedikált, interaktív diagnosztikai eszközt biztosít az alkalmazás hibakereséséhez. További információ: [Azure app Service diagnosztika áttekintése](overview-diagnostics.md).
>
> Emellett más Azure-szolgáltatásokkal is javítható az alkalmazás naplózási és figyelési képességei, például a [Azure monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Alkalmazások naplózásának engedélyezése (Windows)

> [!NOTE]
> A blob Storage-beli alkalmazások naplózása csak a App Service azonos régióban található tárolási fiókokat használhatja.

Ha engedélyezni szeretné az alkalmazások naplózását a [Azure Portalban](https://portal.azure.com)lévő Windows-alkalmazásokban, navigáljon az alkalmazáshoz, és válassza a **app Service naplók** lehetőséget.

Válassza **a** be lehetőséget az **Application Logging (fájlrendszer)** vagy az **Application Logging (blob)**, vagy mindkettő esetében. 

A **fájlrendszer** beállítás ideiglenes hibakeresési célokra szolgál, és 12 órán belül kikapcsol. A **blob** beállítás a hosszú távú naplózáshoz szükséges, és blob Storage-tárolóra van szüksége a naplók írásához.  A **blob** lehetőség további információkat is tartalmaz a naplófájlokban, például a NAPLÓÜZENETEK forrás VM-PÉLDÁNYának azonosítóját ( `InstanceId` ), a szál azonosítóját (), `Tid` és egy részletesebb időbélyeg ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Jelenleg csak a .NET-alkalmazások naplói írhatók a blob Storage-ba. A Java, a PHP, a Node.js és a Python-alkalmazás naplófájljai csak a App Service fájlrendszerben tárolhatók (kód módosítása nélkül, hogy a rendszer a naplókat külső tárolóba írja).
>
> Emellett, ha [újragenerálja a Storage-fiók hozzáférési kulcsait](../storage/common/storage-account-create.md), a frissített hozzáférési kulcsok használatához vissza kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A **configure (Konfigurálás** ) lapon állítsa be a megfelelő naplózási funkciót a **kikapcsoláshoz**. Mentse a beállítást.
> 2. Engedélyezze újra a naplózást a Storage-fiók blobján. Mentse a beállítást.
>
>

Válassza ki a **szintet**, vagy a naplózni kívánt részletességi szintet. A következő táblázat az egyes szintekben található naplózási kategóriákat mutatja be:

| Level | Belefoglalt kategóriák |
|-|-|
|**Disabled** | Nincs |
|**Hiba** | Hiba, kritikus |
|**Figyelmeztetés** | Figyelmeztetés, hiba, kritikus|
|**Tájékoztatás** | Információ, figyelmeztetés, hiba, kritikus|
|**Részletes** | Nyomkövetés, hibakeresés, információ, figyelmeztetés, hiba, kritikus (minden kategória) |

Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="enable-application-logging-linuxcontainer"></a>Alkalmazás naplózásának engedélyezése (Linux/tároló)

Ha engedélyezni szeretné az alkalmazások naplózását a Linux-alkalmazásokhoz vagy az egyéni tároló-alkalmazásokhoz a [Azure Portalban](https://portal.azure.com), navigáljon az alkalmazáshoz, és válassza ki **app Service naplókat**.

Az **alkalmazás naplózása** területen válassza a **fájlrendszer** lehetőséget.

A **kvóta (MB)** mezőben határozza meg az alkalmazás naplóihoz tartozó lemezkvóta-kvótát. A **megőrzési időtartam (nap)** beállításnál adja meg, hogy hány nap elteltével kell megőrizni a naplókat.

Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="enable-web-server-logging"></a>Webkiszolgáló naplózásának engedélyezése

Ha engedélyezni szeretné a webkiszolgáló-naplózást a Windows-alkalmazások számára a [Azure Portalban](https://portal.azure.com), navigáljon az alkalmazáshoz, és válassza ki **app Service naplókat**.

A **webkiszolgáló naplózása** lapon válassza a **Storage (tárolás** ) lehetőséget a blob Storage-ban tárolt naplók tárolásához **, illetve a naplófájlok** tárolásához a app Service fájlrendszerben. 

A **megőrzési időtartam (nap)** beállításnál adja meg, hogy hány nap elteltével kell megőrizni a naplókat.

> [!NOTE]
> Ha [újragenerálja a Storage-fiók hozzáférési kulcsait](../storage/common/storage-account-create.md), a frissített kulcsok használatához vissza kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A **configure (Konfigurálás** ) lapon állítsa be a megfelelő naplózási funkciót a **kikapcsoláshoz**. Mentse a beállítást.
> 2. Engedélyezze újra a naplózást a Storage-fiók blobján. Mentse a beállítást.
>
>

Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="log-detailed-errors"></a>Részletes hibák naplózása

Ha menteni szeretné a Windows-alkalmazásokhoz tartozó hiba-vagy sikertelen kérelmek nyomkövetését a [Azure Portalban](https://portal.azure.com), navigáljon az alkalmazáshoz, és válassza ki **app Service naplókat**.

A **részletes hiba naplózása** vagy a **Sikertelen kérelmek nyomkövetése** területen válassza a be lehetőséget, majd válassza **a** **Mentés** lehetőséget.

Mindkét típusú naplót a App Service fájlrendszer tárolja. Akár 50 hibát (fájlok/mappák) tartanak fenn. Ha a HTML-fájlok száma meghaladja az 50-as értéket, a rendszer a legrégebbi 26 hibát automatikusan törli.

## <a name="add-log-messages-in-code"></a>Naplóüzenetek hozzáadása a kódban

Az alkalmazás kódjában a szokásos naplózási létesítményeket használja a naplóüzenetek küldésére az alkalmazás naplófájljaiba. Például:

- A ASP.NET-alkalmazások a [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) osztály használatával naplózzák az adatokat az Application Diagnostics-naplóba. Például:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Alapértelmezés szerint a ASP.NET Core a [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) naplózási szolgáltatót használja. További információ: [ASP.net Core naplózás az Azure-ban](/aspnet/core/fundamentals/logging/). További információ a webjobs SDK naplózásáról: Ismerkedés [a Azure WEBJOBS SDK-val](/azure/app-service/webjobs-sdk-get-started#enable-console-logging)

## <a name="stream-logs"></a>Naplók streamelése

A naplók valós idejű továbbítása előtt engedélyezze a kívánt naplózási típust. Az */LogFiles* könyvtárban (d:/Home/LogFiles) tárolt,. txt,. log vagy. htm fájlban lévő fájlokra írt adatokat app Service továbbítja a rendszer.

> [!NOTE]
> A naplózási puffer bizonyos típusai a naplófájlba írhatók, ami miatt az adatfolyamban megrendelési események is megtekinthetők. Például egy olyan alkalmazási naplóbejegyzés, amely akkor fordul elő, amikor egy felhasználó meglátogat egy oldalt az adatfolyamban, mielőtt megkezdené a megfelelő HTTP-naplóbejegyzés megadását az oldal kéréséhez.
>

### <a name="in-azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com)adatfolyam-naplóihoz navigáljon az alkalmazáshoz, és válassza a **napló stream** lehetőséget. 

### <a name="in-cloud-shell"></a>Cloud Shell

A [Cloud Shell](../cloud-shell/overview.md)élő naplók továbbításához használja a következő parancsot:

> [!IMPORTANT]
> Ez a parancs nem használható a Linux app Service-csomagokban üzemeltetett webalkalmazásokkal.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Bizonyos típusú naplók (például a HTTP) szűréséhez használja a **--Provider** paramétert. Például:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>Helyi terminálon

A helyi konzolon a naplók továbbításához [telepítse az Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be a fiókjába](/cli/azure/authenticate-azure-cli). Miután bejelentkezett, követte a [Cloud Shell vonatkozó utasításokat](#in-cloud-shell)

## <a name="access-log-files"></a>Hozzáférési naplófájlok

Ha az Azure Storage-Blobok lehetőséget konfigurálja a napló típusához, szüksége lesz egy, az Azure Storage szolgáltatással használható ügyfél-eszközre. További információ: [Azure Storage-ügyféleszközök](../storage/common/storage-explorers.md).

A App Service fájlrendszerben tárolt naplók esetében a legegyszerűbb módszer a ZIP-fájl letöltése a böngészőben a következő helyen:

- Linux/Container alkalmazások: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-alkalmazások: `https://<app-name>.scm.azurewebsites.net/api/dump`

A Linux/Container alkalmazások esetében a ZIP-fájl konzol kimeneti naplókat tartalmaz a Docker-gazdagéphez és a Docker-tárolóhoz is. A kibővített alkalmazások esetében a ZIP-fájl az egyes példányok egy készletét tartalmazza. A App Service fájlrendszerben ezek a naplófájlok a */Home/LogFiles* könyvtárának tartalma.

Windows-alkalmazások esetén a ZIP-fájl tartalmazza a *D:\Home\LogFiles* könyvtárának tartalmát a app Service fájlrendszerben. A következő szerkezettel rendelkezik:

| Napló típusa | Címtár | Leírás |
|-|-|-|
| **Alkalmazás-naplók** |*/LogFiles/Application/* | Egy vagy több szövegfájlt tartalmaz. A naplóüzenetek formátuma a használt naplózási szolgáltatótól függ. |
| **Sikertelen kérelmek nyomkövetése** | */LogFiles/W3SVC#########/* | XML-fájlokat és XSL-fájlt tartalmaz. A formázott XML-fájlok megtekinthetők a böngészőben. |
| **Részletes hibák naplói** | */LogFiles/DetailedErrors/* | HTM-hibaüzeneteket tartalmaz. A HTM-fájlok a böngészőben tekinthetők meg.<br/>A sikertelen kérelmek nyomkövetésének másik módja, ha megnyitja az alkalmazás lapját a portálon. A bal oldali menüben válassza a **diagnosztizálás és megoldás problémák** elemet, majd keresse meg a **Sikertelen kérelmek nyomkövetésére vonatkozó naplókat**, majd kattintson a ikonra a kívánt nyomkövetés megkereséséhez és megtekintéséhez. |
| **Webkiszolgáló-naplók** | */LogFiles/http/RawLogs/* | A [W3C bővített naplófájl formátumának](/windows/desktop/Http/w3c-logging)használatával formázott szövegfájlokat tartalmaz. Ezeket az információkat egy szövegszerkesztővel vagy egy olyan segédprogrammal lehet olvasni, mint például a [log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>A App Service nem támogatja a `s-computername` , a `s-ip` vagy a `cs-version` mezőket. |
| **Üzembe helyezési naplók** | */LogFiles/git/* és */Deployments/* | A belső telepítési folyamatok által létrehozott naplókat, valamint a git-példányok naplóit tartalmazza. |

## <a name="send-logs-to-azure-monitor-preview"></a>Naplók elküldése a Azure Monitorba (előzetes verzió)

Az új [Azure monitor integrációval](https://aka.ms/appsvcblog-azmon) [diagnosztikai beállításokat (előzetes verzió) hozhat létre](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) a Storage-fiókokba, a Event Hubsba és a log Analyticsba való küldéshez. 

> [!div class="mx-imgBorder"]
> ![Diagnosztikai beállítások (előzetes verzió)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Támogatott naplózási típusok

A következő táblázat a támogatott naplózási típusokat és leírásokat tartalmazza: 

| Napló típusa | Windows | Windows-tároló | Linux | Linux-tároló | Leírás |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & tomcat | Igen | Igen | Yes | Standard kimenet és standard hiba |
| AppServiceHTTPLogs | Igen | Igen | Igen | Yes | Webkiszolgáló-naplók |
| AppServiceEnvironmentPlatformLogs | Yes | N.A. | Igen | Yes | App Service Environment: skálázás, konfigurációs változások és állapotüzenetek|
| AppServiceAuditLogs | Igen | Igen | Igen | Yes | Bejelentkezési tevékenység FTP-n és kudu |
| AppServiceFileAuditLogs | Igen | Yes | TBA | TBA | A webhely tartalma módosult; **csak a prémium szinthez és a fentiekhez érhető el** |
| AppServiceAppLogs | ASP .NET | ASP .NET | Java SE & tomcat áldott képek <sup>1</sup> | Java SE & tomcat áldott képek <sup>1</sup> | Alkalmazás-naplók |
| AppServiceIPSecAuditLogs  | Igen | Igen | Igen | Yes | IP-szabályoktól érkező kérések |
| AppServicePlatformLogs  | TBA | Igen | Igen | Yes | Tároló műveleti naplói |
| AppServiceAntivirusScanAuditLogs | Igen | Igen | Igen | Yes | Víruskeresési [naplók](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) a Microsoft Defender használatával; **csak a prémium szintű csomag esetében érhető el** | 

<sup>1</sup> a Java SE-alkalmazások esetében adja hozzá a "$website _AZMON_PREVIEW_ENABLED" lehetőséget az alkalmazás beállításaihoz, és állítsa 1 vagy igaz értékre.

## <a name="next-steps"></a><a name="nextsteps"></a> További lépések
* [Naplók lekérdezése Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [A Azure App Service figyelése](web-sites-monitor.md)
* [Hibaelhárítási Azure App Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md)
* [Alkalmazás-naplók elemzése a HDInsight-ben](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
