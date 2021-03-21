---
title: Azure Monitor Application Insights munkaterület-alapú erőforrás-sémája
description: Ismerkedjen meg a Azure Monitor Application Insights munkaterület-alapú erőforrások új táblázatos struktúrájával és sémájával.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: ef9d22cd2b45679928ee54778b2a521ea9ecab03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575595"
---
# <a name="workspace-based-resource-changes"></a>Munkaterület-alapú erőforrás-változások

A [munkaterület-alapú Application Insights erőforrások](create-workspace-resource.md)bevezetését megelőzően Application Insights az adatok a Azure monitor egyéb naplófájljaitól elkülönítve vannak tárolva. Mindkettő az Azure Adatkezelőon alapul, és ugyanazt a Kusto lekérdezési nyelvet (KQL) használja. Ezt a [Azure monitor naplóiban](../logs/data-platform-logs.md)ismertetjük.

A munkaterületen alapuló Application Insights erőforrás-adathalmazok egy Log Analytics munkaterületen vannak tárolva, amelyek más megfigyelési és alkalmazásadatok. Ez egyszerűbbé teszi a konfigurációt azáltal, hogy könnyebben elemezheti az adatelemzést több megoldás között, és kihasználhatja a munkaterületek képességeit.

## <a name="table-structure"></a>Táblastruktúra

| Örökölt tábla neve | Új táblanév | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  A rendelkezésre állási tesztek összesített adatai.|
| browserTimings | AppBrowserTimings | Az ügyfél teljesítményére vonatkozó adat, például a bejövő adat feldolgozásához szükséges idő.|
| függőségek | AppDependencies | Az alkalmazástól a TrackDependency ()-n keresztül rögzített más összetevőkre (beleértve a külső összetevőket is), például a REST API, az adatbázisra vagy a fájlrendszerre irányuló hívásokat.  |
| customEvents | AppEvents | Az alkalmazás által létrehozott egyéni események. |
| customMetrics | AppMetrics | Az alkalmazás által létrehozott egyéni metrikák. |
| Oldalmegtekintések | AppPageViews| Adatok az egyes webhelyekről a böngésző információi között. |
| performanceCounters | AppPerformanceCounters | Az alkalmazást támogató számítási erőforrások teljesítményének mérései, például a Windows-teljesítményszámlálók. |
| kérelmek | AppRequests | Az alkalmazás által fogadott kérelmek. Például egy külön kérési rekord kerül a naplóba minden olyan HTTP-kérelem esetében, amelyet a webalkalmazás fogad.  |
| kivételek | AppSystemEvents | Az alkalmazás-futtatókörnyezet által kiváltott kivételek rögzítik a kiszolgálóoldali és az ügyféloldali (böngészők) kivételeket is. |
| nyomok | AppTraces | A TrackTrace () segítségével rögzített, az alkalmazás kódjának/naplózási keretrendszerén keresztül kibocsátott részletes naplók (Nyomkövetések). |

## <a name="table-schemas"></a>Tábla sémái

A következő szakaszban a klasszikus tulajdonságok nevei és az új munkaterület-alapú Application Insights tulajdonságok nevei közötti leképezés látható.  Ezeket az információkat az örökölt táblákat használó lekérdezések átalakítására használhatja.

A legtöbb oszlop neve azonos a különböző nagybetűkkel. Mivel a KQL megkülönbözteti a kis-és nagybetűket, meg kell változtatnia az egyes oszlopokat a meglévő lekérdezésekben szereplő táblázatos nevekkel együtt. A rendszer kijelöli a nagybetűket és a kiemelést is tartalmazó oszlopokat. A klasszikus Application Insights-lekérdezéseket továbbra is használhatja a Application Insights erőforrás **naplók** paneljén, még akkor is, ha ez egy munkaterület-alapú erőforrás. Az új tulajdonságnév megadása szükséges ahhoz, hogy az Log Analytics munkaterület környezetében történő lekérdezés során.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Örökölt tábla: rendelkezésre állás

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|duration|valós szám|DurationMs|valós szám|
|`id`|sztring|`Id`|sztring|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|Sztring|
|location|sztring|Hely|sztring|
|message|sztring|Üzenet|sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|performanceBucket|sztring|PerformanceBucket|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|size|valós szám|Méret|valós szám|
|sikeres|sztring|Siker|Logikai|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Örökölt tábla: browserTimings

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|name|sztring|Name|dátum/idő|
|networkDuration|valós szám|NetworkDurationMs|valós szám|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|performanceBucket|sztring|PerformanceBucket|sztring|
|processingDuration|valós szám|ProcessingDurationMs|valós szám|
|receiveDuration|valós szám|ReceiveDurationMs|valós szám|
|sdkVersion|sztring|SdkVersion|sztring|
|sendDuration|valós szám|SendDurationMs|valós szám|
|session_Id|sztring|SessionId|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|totalDuration|valós szám|TotalDurationMs|valós szám|
|url|sztring|URL-cím|sztring|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appdependencies"></a>AppDependencies

Örökölt tábla: függőségek

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|adatok|sztring|Adatok|sztring|
|duration|valós szám|DurationMs|valós szám|
|`id`|sztring|`Id`|sztring|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|Sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|performanceBucket|sztring|PerformanceBucket|sztring|
|resultCode|sztring|ResultCode|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|sikeres|sztring|Siker|Logikai|
|cél|sztring|Cél|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|típus|sztring|DependencyType|sztring|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appevents"></a>AppEvents

Örökölt tábla: customEvents

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appmetrics"></a>AppMetrics

Örökölt tábla: customMetrics

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|
|érték|valós szám|távolítva||
|valueCount|int|ValueCount|int|
|valueMax|valós szám|ValueMax|valós szám|
|valueMin|valós szám|ValueMin|valós szám|
|valueStdDev|valós szám|ValueStdDev|valós szám|
|valueSum|valós szám|ValueSum|valós szám|

### <a name="apppageviews"></a>AppPageViews

Örökölt tábla: oldalmegtekintések

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|duration|valós szám|DurationMs|valós szám|
|`id`|sztring|`Id`|sztring|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|Sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|performanceBucket|sztring|PerformanceBucket|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|url|sztring|URL-cím|sztring|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Örökölt tábla: performanceCounters

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|category|sztring|Kategória|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|számláló|sztring|távolítva||
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|például|sztring|Példány|sztring|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|name|sztring|Name|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|
|érték|valós szám|Érték|valós szám|

### <a name="apprequests"></a>AppRequests

Örökölt tábla: kérelmek

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|Dinamikus|
|customMeasurements|dinamikus|Mérések|Dinamikus|
|duration|valós szám|DurationMs|Valós|
|`id`|sztring|`Id`|Sztring|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|Sztring|
|name|sztring|Name|Sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|performanceBucket|sztring|PerformanceBucket|Sztring|
|resultCode|sztring|ResultCode|Sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|source|sztring|Forrás|Sztring|
|sikeres|sztring|Siker|Logikai|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|url|sztring|URL-cím|Sztring|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="appsystemevents"></a>AppSystemEvents

Örökölt tábla: kivételek

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|szerelvény|sztring|Szerelvény|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|dinamikus|
|customMeasurements|dinamikus|Mérések|dinamikus|
|Részletek|dinamikus|Részletek|dinamikus|
|handledAt|sztring|HandledAt|sztring|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|innermostAssembly|sztring|InnermostAssembly|sztring|
|innermostMessage|sztring|InnermostMessage|sztring|
|innermostMethod|sztring|InnermostMethod|sztring|
|innermostType|sztring|InnermostType|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|message|sztring|Üzenet|sztring|
|method|sztring|Metódus|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|outerAssembly|sztring|OuterAssembly|sztring|
|outerMessage|sztring|OuterMessage|sztring|
|outerMethod|sztring|OuterMethod|sztring|
|outerType|sztring|OuterType|sztring|
|problemId|sztring|ProblemId|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|severityLevel|int|SeverityLevel|int|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|típus|sztring|ExceptionType|sztring|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

### <a name="apptraces"></a>AppTraces

Örökölt tábla: nyomkövetés

|ApplicationInsights|Típus|LogAnalytics|Típus|
|:---|:---|:---|:---|
|appId|sztring|\_Erőforrás GUID azonosítója|sztring|
|application_Version|sztring|AppVersion|sztring|
|appName|sztring|\_ResourceId|sztring|
|client_Browser|sztring|ClientBrowser|sztring|
|client_City|sztring|ClientCity|sztring|
|client_CountryOrRegion|sztring|ClientCountryOrRegion|sztring|
|client_IP|sztring|Ügyfélip|sztring|
|client_Model|sztring|ClientModel|sztring|
|client_OS|sztring|ClientOS|sztring|
|client_StateOrProvince|sztring|ClientStateOrProvince|sztring|
|client_Type|sztring|ClientType|sztring|
|cloud_RoleInstance|sztring|AppRoleInstance|sztring|
|cloud_RoleName|sztring|AppRoleName|sztring|
|customDimensions|dinamikus|Tulajdonságok|dinamikus|
|customMeasurements|dinamikus|Mérések|dinamikus|
|Rendszerállapotkulcsot|sztring|Rendszerállapotkulcsot|sztring|
|itemCount|int|ItemCount|int|
|elemazonosító|sztring|\_Elemazonosító|sztring|
|itemType|sztring|Típus|sztring|
|message|sztring|Üzenet|sztring|
|operation_Id|sztring|OperationId|sztring|
|operation_Name|sztring|OperationName|sztring|
|operation_ParentId|sztring|OperationParentId|sztring|
|operation_SyntheticSource|sztring|OperationSyntheticSource|sztring|
|sdkVersion|sztring|SdkVersion|sztring|
|session_Id|sztring|SessionId|sztring|
|severityLevel|int|SeverityLevel|int|
|időbélyeg|dátum/idő|TimeGenerated|dátum/idő|
|user_AccountId|sztring|UserAccountId|sztring|
|user_AuthenticatedId|sztring|UserAuthenticatedId|sztring|
|user_Id|sztring|UserId (Felhasználóazonosító)|sztring|

## <a name="next-steps"></a>Következő lépések

* [Metrikák böngészése](../essentials/metrics-charts.md)
* [Analytics-lekérdezések](../logs/log-query-overview.md)

