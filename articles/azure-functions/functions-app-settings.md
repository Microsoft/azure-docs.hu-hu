---
title: Azure Functions – alkalmazásbeállítási referencia
description: A Azure Functions Alkalmazásbeállítások vagy környezeti változók dokumentációja.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: fb00f0fe16342bf603d534c34a860278dc21deac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595976"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions – alkalmazásbeállítási referencia

A Function alkalmazásban az Alkalmazásbeállítások olyan globális konfigurációs beállításokat tartalmaznak, amelyek az adott Function alkalmazás összes funkcióját érintik. Helyileg futtatva ezek a beállítások helyi [környezeti változókként](functions-run-local.md#local-settings-file)érhetők el. Ez a cikk a Function apps alkalmazásban elérhető Alkalmazásbeállítások listáját tartalmazza.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Más globális konfigurációs beállítások is találhatók a fájl [host.jsjában](functions-host-json.md) , a fájl [local.settings.js](functions-run-local.md#local-settings-file) pedig a fájlban.

> [!NOTE]  
> Az Alkalmazásbeállítások segítségével felülbírálhatja host.jsbeállításait, anélkül, hogy módosítania kellene a host.jsa fájlon. Ez olyan esetekben hasznos, amikor egy adott környezet beállításain egy adott host.jskell konfigurálnia vagy módosítania. Ez a beállítás azt is lehetővé teszi, hogy a projekt újbóli közzététele nélkül módosítsa host.jsbeállításait. További tudnivalókat ahost.jscímű [ cikkben](functions-host-json.md#override-hostjson-values)talál. A Function app-beállítások módosítása megköveteli a Function alkalmazás újraindítását.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights rendszerállapot-kulcsa. Csak a vagy a valamelyikét használja `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` . Ha Application Insights egy szuverén felhőben fut, használja a t `APPLICATIONINSIGHTS_CONNECTION_STRING` . További információ: a [Azure functions figyelésének konfigurálása](configure-monitoring.md). 

|Kulcs|Mintaérték|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

A Application Insightshoz tartozó kapcsolatok karakterlánca. `APPLICATIONINSIGHTS_CONNECTION_STRING` `APPINSIGHTS_INSTRUMENTATIONKEY` A következő esetekben használja a parancsot:

+ Ha a Function alkalmazásnak a kapcsolati sztringtel támogatott kiegészítő testreszabásokat kell használnia. 
+ Ha a Application Insights-példány egy szuverén felhőben fut, amelyhez egyéni végpont szükséges.

További információ: a [kapcsolatok karakterláncai](../azure-monitor/app/sdk-connection-string.md). 

|Kulcs|Mintaérték|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [kulcs]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Alapértelmezés szerint a [functions-proxyk](functions-proxies.md) egy parancsikon használatával küldenek API-hívásokat a proxyk az azonos Function alkalmazásban található függvényeknek. Ez a parancsikon az új HTTP-kérelem létrehozása helyett használatos. Ezzel a beállítással letilthatja a parancsikon viselkedését.

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|A helyi function alkalmazásban a függvényre mutató háttér-URL-címmel rendelkező hívások nem lesznek közvetlenül a függvényhez küldve. Ehelyett a rendszer visszairányítja a kérelmeket a functions-alkalmazáshoz tartozó HTTP-felületre.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|hamis|A helyi function alkalmazásban egy függvényre mutató háttérbeli URL-címmel rendelkező hívások közvetlenül a függvénynek továbbítódnak. Ez az alapértelmezett érték. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Ezzel a beállítással szabályozható, hogy a rendszer a karaktereket a `%2F` háttérbeli URL-cím beillesztésekor perjelként dekódolja-e az útválasztási paraméterekben. 

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|A kódolt perjelekkel rendelkező útvonal-paraméterek dekódolásra kerülnek. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|hamis|A rendszer az összes útvonal-paramétert változatlanul adja át, ami az alapértelmezett viselkedés. |

Tegyük fel például, hogy a fájl proxies.jsa tartományban található Function alkalmazáshoz `myfunction.com` .

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Ha a értékre `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` van állítva `true` , az URL-cím a következőre lesz `example.com/api%2ftest` feloldva: `example.com/api/test` . Alapértelmezés szerint az URL-cím változatlan marad a következőként: `example.com/test%2fapi` . További információ: [functions proxys](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

A functions futtatókörnyezet 2. x vagy újabb verziójában a futásidejű környezet alapján konfigurálja az alkalmazások viselkedését. Ez az érték az inicializálás során olvasható, és tetszőleges értékre állítható. `Development` `Staging` A futtatókörnyezetnek csak a, és értékeit kell megadnia `Production` . Ha ez az Alkalmazásbeállítás nem jelenik meg az Azure-ban való futtatáskor, a rendszer a környezetet feltételezi `Production` . Akkor használja ezt a beállítást, `ASPNETCORE_ENVIRONMENT` Ha az Azure-ban módosítania kell a futásidejű környezetet `Production` . A Azure Functions Core Tools `AZURE_FUNCTIONS_ENVIRONMENT` `Development` helyi számítógépen való futásra van beállítva, és ez nem bírálható felül a fájl local.settings.js. További információért lásd: [környezet-alapú indítási osztály és metódusok](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods).

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

A functions futtatókörnyezet 2. x vagy újabb verziójában az Alkalmazásbeállítások felülbírálják [host.js](functions-host-json.md) az aktuális környezet beállításainál. Ezek a felülbírálások az Alkalmazásbeállítások nevű alkalmazás-beállításokban vannak kifejezve `AzureFunctionsJobHost__path__to__setting` . További információ: [host.jsfelülbírálása az értékeken](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcionális Storage-fiókhoz tartozó kapcsolatok karakterlánca a naplók tárolásához és megjelenítéséhez a portál **figyelés** lapján. Ez a beállítás csak olyan alkalmazások esetén érvényes, amelyek az Azure Functions futtatókörnyezet 1. x verzióját célozzák meg. A Storage-fióknak olyan általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. További információ: a [Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = <name> ; AccountKey =<key>|

> [!NOTE]
> A jobb teljesítmény és a használhatóság érdekében a Runtime 2. x vagy újabb verziója a APPINSIGHTS_INSTRUMENTATIONKEY és az alkalmazás-ellenőrzési funkciókat használja a figyeléshez `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` azt jelenti, hogy le kell tiltania az alapértelmezett kezdőlapot, amely megjelenik a Function app gyökér URL-címénél. Az alapértelmezett szint a `false`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Ha ezt az alkalmazást kihagyja vagy beállítja, a `false` következő példához hasonló oldal jelenik meg az URL-címre adott válaszban `<functionappname>.azurewebsites.net` .

![Function app kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` azt jelenti, hogy a kiadási módot használja a .NET-kód fordításakor; `false` hibakeresési módot használ. Az alapértelmezett szint a `true`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Az engedélyezni kívánt béta-funkciók vesszővel tagolt listája. A jelzők által engedélyezett béta-funkciók nem állnak készen a gyártásra, de a kísérleti használatra engedélyezhető a működésük előtt.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Megadja a kulcstárolóhoz használandó tárházat vagy szolgáltatót. Jelenleg a támogatott adattárak a blob Storage (blob) és a helyi fájlrendszer ("fájlok"). Az alapértelmezett érték a blob a 2. és a fájlrendszerben az 1. verzióban.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|Fájlok|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

A Azure Functions futtatókörnyezet ezt a Storage-fiókhoz tartozó kapcsolatok sztringjét használja a normál működéshez. A Storage-fiók egyes felhasználási módjai közé tartoznak a kulcskezelő, az időzítő-triggerek kezelése és a Event Hubs ellenőrzőpontok. A Storage-fióknak olyan általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. Lásd a [Storage-fiókra](functions-infrastructure-as-code.md#storage-account) és a [Storage-fiókra vonatkozó követelményeket](storage-considerations.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [név]; AccountKey = [kulcs]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Az írógéppel használt fordító elérési útja. Lehetővé teszi az alapértelmezett érték felülbírálását, ha szükséges.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNKCIÓ \_ alkalmazás \_ szerkesztési \_ módja

Azt diktálja, hogy engedélyezve van-e a Azure Portal szerkesztése. Az érvényes értékek: "READWRITE" és "ReadOnly".

|Kulcs|Mintaérték|
|---|------------|
|FUNKCIÓ \_ alkalmazás \_ szerkesztési \_ módja|ReadOnly|

## <a name="functions_extension_version"></a>FUNCTIONs \_ bővítmény \_ verziója

A Function alkalmazást futtató functions futtatókörnyezet verziója. A `~` nagyobb verziószámú tilde () a főverzió legújabb verzióját használja (például: "~ 3"). Ha az azonos főverzióhoz tartozó új verziók elérhetők, a rendszer automatikusan telepíti őket a Function alkalmazásba. Az alkalmazás egy adott verzióra való rögzítéséhez használja a teljes verziószámot (például "3.0.12345"). Az alapértelmezett érték a "~ 3". Az alkalmazás a `~1` futtatókörnyezet 1. x verziójára való PIN-kód. További információ: [Azure functions Runtime Versions – áttekintés](functions-versions.md).

|Kulcs|Mintaérték|
|---|------------|
|FUNCTIONs \_ bővítmény \_ verziója|~ 3|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONs \_ v2 \_ kompatibilitási \_ mód

Ez a beállítás lehetővé teszi, hogy a Function alkalmazás 2. x kompatibilis módban fusson a 3. x verziójú futtatókörnyezetben. Ezt a beállítást csak akkor használja, ha problémák merülnek [fel a Function alkalmazás 2. x és 3. x verzióra való frissítésekor](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Ez a beállítás csak rövid távú megkerülő megoldás, ha úgy frissíti az alkalmazást, hogy megfelelően fusson a 3. x verzióban. Ez a beállítás akkor támogatott, ha a [2. x futtatókörnyezet támogatott](functions-versions.md). Ha olyan problémák merülnek fel, amelyek megakadályozzák, hogy az alkalmazás a 3. x verzión fusson anélkül, hogy ezt a beállítást használja, [jelentse a problémát](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Megköveteli, hogy a [functions \_ bővítmény \_ verziója](functions-app-settings.md#functions_extension_version) legyen beállítva `~3` .

|Kulcs|Mintaérték|
|---|------------|
|FUNCTIONs \_ v2 \_ kompatibilitási \_ mód|true|

## <a name="functions_worker_process_count"></a>FUNCTIONs \_ munkavégző \_ folyamatok \_ száma

Meghatározza a nyelv munkavégző folyamatainak maximális számát, alapértelmezett értékkel `1` . A megengedett maximális érték: `10` . A függvények meghívása egyenletesen oszlik meg a nyelvi munkavégző folyamatok között. A nyelv munkavégző folyamatai 10 másodpercenként jönnek létre, amíg el nem éri a FUNCTIONs \_ munkavégző \_ folyamatának \_ száma. Több nyelvi feldolgozó folyamat használata nem ugyanaz, mint a [skálázás](functions-scale.md). Akkor érdemes ezt a beállítást használni, ha a számítási feladathoz CPU-kötésű és I/O-kötésű hívás is tartozik. Ez a beállítás az összes non-.NET nyelvre vonatkozik.

|Kulcs|Mintaérték|
|---|------------|
|FUNCTIONs \_ munkavégző \_ folyamatok \_ száma|2|

## <a name="python_threadpool_thread_count"></a>PYTHON \_ szálkészlet munkaszála belépett \_ szálak \_ száma

Meghatározza, hogy a Python nyelvi feldolgozó hány szálat használjon a függvények meghívásának végrehajtásához, a `1` Python-verzió alapértelmezett értékével és az `3.8` alatta. `3.9`A Python és újabb verziók esetében az érték a következőre van beállítva: `None` . Vegye figyelembe, hogy ez a beállítás nem garantálja, hogy hány szálat kell beállítani a végrehajtás során. A beállítás lehetővé teszi a Python számára a szálak számának a megadott értékre való kiterjesztését. A beállítás csak a Python functions-alkalmazásokra vonatkozik. Emellett a beállítás a szinkron függvények meghívására, és nem a munkarutinokra is vonatkozik.

|Kulcs|Mintaérték|Maximális érték|
|---|------------|---------|
|PYTHON \_ szálkészlet munkaszála belépett \_ szálak \_ száma|2|32|


## <a name="functions_worker_runtime"></a>FUNCTIONs \_ Worker \_ futtatókörnyezet

A Function alkalmazásban betölteni kívánt nyelvi feldolgozó futtatókörnyezet.  Ez megfelel az alkalmazásban használt nyelvnek (például "DotNet"). A függvények több nyelven is közzétehető több alkalmazásban, amelyek mindegyike egy megfelelő munkavégző futásidejű értékkel rendelkezik.  Érvényes értékek: `dotnet` (C#/f #), `node` (JavaScript/írógéppel), `java` (Java), `powershell` (PowerShell) és `python` (Python).

|Kulcs|Mintaérték|
|---|------------|
|FUNCTIONs \_ Worker \_ futtatókörnyezet|dotnet|

## <a name="pip_extra_index_url"></a>PIP \_ extra \_ index \_ URL-címe

A beállítás értéke a Python-alkalmazások egyéni csomag-indexelési URL-címét jelöli. Akkor használja ezt a beállítást, ha távoli buildet szeretne futtatni egy további csomag-indexben található egyéni függőségek használatával.   

|Kulcs|Mintaérték|
|---|------------|
|PIP \_ extra \_ index \_ URL-címe|http://my.custom.package.repo/simple |

További információ: [Egyéni függőségek](functions-reference-python.md#remote-build-with-extra-index-url) a Python fejlesztői referenciában.

## <a name="scale_controller_logging_enabled"></a>SKÁLÁZÁSi \_ vezérlő \_ naplózása \_ engedélyezve

_Ez a beállítás jelenleg előzetes verzióban érhető el._  

Ezzel a beállítással szabályozható a Azure Functions skálázási vezérlő naplózása. További információkért lásd: a [vezérlő naplófájljainak skálázása](functions-monitoring.md#scale-controller-logs).

|Kulcs|Mintaérték|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights: részletes|

A kulcs értéke a következő formátumban van megadva `<DESTINATION>:<VERBOSITY>` :

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBHELY \_ CONTENTAZUREFILECONNECTIONSTRING

A Storage-fiókhoz tartozó kapcsolati karakterlánc, amelyben a Function app-kód és a konfiguráció a Windows rendszeren futó, eseményvezérelt skálázási tervekben tárolódik. További információkért lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#windows).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [név]; AccountKey = [kulcs]|

Csak Prémium csomag telepítésekor vagy Windows rendszeren futó használati tervhez használatos. A Linux rendszerű fogyasztási csomagok esetében nem támogatott. A beállítás módosítása vagy eltávolítása miatt előfordulhat, hogy a Function alkalmazás nem indul el. További információt [ebben a hibaelhárítási cikkben](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)talál. 

## <a name="website_contentovervnet"></a>WEBHELY \_ CONTENTOVERVNET

Csak a prémium csomagokhoz. Az érték `1` lehetővé teszi a Function alkalmazás méretezését, ha a Storage-fiókja egy virtuális hálózatra van korlátozva. Ezt a beállítást akkor érdemes engedélyezni, ha a Storage-fiókot egy virtuális hálózatra korlátozza. További információ: [a Storage-fiók korlátozása egy virtuális hálózatra](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBHELY \_ CONTENTSHARE

A függvény alkalmazás kódjának és konfigurációjának elérési útja egy Windows rendszerű eseményvezérelt skálázási tervben. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING használatával használható. Az alapértelmezett érték egy egyedi karakterlánc, amely a Function alkalmazás nevével kezdődik. Lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#windows).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Csak Prémium csomag telepítésekor vagy Windows rendszeren futó használati tervhez használatos. A Linux rendszerű fogyasztási csomagok esetében nem támogatott. A beállítás módosítása vagy eltávolítása miatt előfordulhat, hogy a Function alkalmazás nem indul el. További információt [ebben a hibaelhárítási cikkben](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)talál.

Amikor az üzembe helyezés során egy Azure Resource Manager használatával hoz létre egy Function-alkalmazást, a sablonban ne szerepeljenek WEBSITE_CONTENTSHARE. Ez az Alkalmazásbeállítás a telepítés során jön létre. További információ: az [erőforrás-telepítés automatizálása a Function alkalmazáshoz](functions-infrastructure-as-code.md#windows).   

## <a name="website_dns_server"></a>WEBHELY \_ DNS- \_ kiszolgálója

Az IP-címek feloldásakor az alkalmazás által használt DNS-kiszolgálót állítja be. Ez a beállítás gyakran szükséges bizonyos hálózati funkciók, például a [Azure DNS privát zónák](functions-networking-options.md#azure-dns-private-zones) és a [magánhálózati végpontok](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)használatakor.   

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY \_ DNS- \_ kiszolgálója|168.63.129.16|

## <a name="website_max_dynamic_application_scale_out"></a>a webhely \_ maximális \_ dinamikus alkalmazás- \_ \_ felskálázása \_

Azon példányok maximális száma, amelyeket az alkalmazás fel tud skálázásra. Az alapértelmezett érték nem korlát.

> [!IMPORTANT]
> Ez a beállítás előzetes verzióban érhető el.  A [maximális felskálázás funkcióhoz tartozó app Property](./event-driven-scaling.md#limit-scale-out) hozzáadása megtörtént, és az ajánlott módszer a felskálázás korlátozására.

|Kulcs|Mintaérték|
|---|------------|
|a webhely \_ maximális \_ dinamikus alkalmazás- \_ \_ felskálázása \_|5|

## <a name="website_node_default_version"></a>WEBHELY- \_ csomópont \_ DEFAULT_VERSION

_Csak Windows._  
A Function alkalmazás Windows rendszeren való futtatásakor használandó Node.js verzióját állítja be. Ha a futásidejű modult használja, használjon egy tilde (~) verziót a célként megadott főverzió legújabb elérhető verziójának használatára. Ha például a értékre `~10` van állítva, a rendszer a Node.js 10 legújabb verzióját használja. Ha egy főverziót egy tilde megcéloz, nem kell manuálisan frissítenie a másodlagos verziót. 

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY- \_ csomópont \_ DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBHELY \_ futtatása \_ \_ csomagból

Lehetővé teszi, hogy a Function alkalmazás egy csatlakoztatott csomagfájl használatával fusson.

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY \_ futtatása \_ \_ csomagból|1|

Az érvényes értékek egy URL-cím, amely feloldja a központi telepítési csomag fájljának helyét, vagy `1` . Ha a értékre `1` van állítva, a csomagnak a mappában kell lennie `d:\home\data\SitePackages` . Ha a zip-telepítést ezzel a beállítással használja, a csomag automatikusan erre a helyre lesz feltöltve. Az előzetes verzióban ez a beállítás neve `WEBSITE_RUN_FROM_ZIP` . További információkért lásd: [függvények futtatása csomagfájl alapján](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>WEBHELY \_ \_ időzónája

Lehetővé teszi a Function alkalmazás időzónájának beállítását. 

|Kulcs|Operációs rendszer|Mintaérték|
|---|--|------------|
|WEBHELY \_ \_ időzónája|Windows|Keleti téli idő|
|WEBHELY \_ \_ időzónája|Linux|Amerika/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>WEBHELY \_ VNET \_ útvonala \_

Azt jelzi, hogy az alkalmazásból érkező összes kimenő forgalom a virtuális hálózaton keresztül van-e irányítva. A Setting érték `1` azt jelzi, hogy az összes forgalmat a virtuális hálózaton keresztül irányítja a rendszer. Ezt a beállítást akkor kell használni, ha a [regionális virtuális hálózati integráció](functions-networking-options.md#regional-virtual-network-integration)funkcióit használja. Ez akkor is használatos, amikor egy [virtuális hálózati NAT-átjárót használ a statikus kimenő IP-cím definiálásához](functions-how-to-use-nat-gateway.md). 

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY \_ VNET \_ útvonala \_|1|

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan frissítheti az alkalmazás beállításait](functions-how-to-use-azure-function-app-settings.md#settings)

[Lásd: a fájl host.jsjának globális beállításai](functions-host-json.md)

[További Alkalmazásbeállítások App Service alkalmazásokhoz](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
