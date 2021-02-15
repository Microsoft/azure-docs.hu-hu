---
title: Útmutató a Azure Functions fejlesztéséhez
description: Ismerkedjen meg az Azure-ban a függvények fejlesztéséhez szükséges Azure Functions fogalmakkal és technikákkal az összes programozási nyelv és kötés között.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: fdc898c02cfd20ecfdd72dece4fb1e92d803dbb0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386900"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions – fejlesztői útmutató
Azure Functions az egyes függvények a használt nyelvtől vagy kötéstől függetlenül megosztanak néhány alapvető műszaki fogalmat és összetevőt. Mielőtt beolvassa az adott nyelvre vagy kötésre vonatkozó tanulási adatokat, olvassa el ezt az áttekintést, amely az összesre vonatkozik.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions áttekintését](functions-overview.md).

## <a name="function-code"></a>Függvény kódja
A *függvény* a Azure functions elsődleges fogalma. A függvény két fontos darabot tartalmaz – a kódot, amely különböző nyelveken írható, és néhány konfigurációban a fájl function.jsis. A lefordított nyelvek esetében ez a konfigurációs fájl automatikusan létrejön a kódban szereplő jegyzetekből. A programozási nyelvek esetében saját magának kell megadnia a konfigurációs fájlt.

A fájl function.jsa függvény triggerét, kötéseit és egyéb konfigurációs beállításait határozza meg. Minden függvényhez egy, és csakis egy trigger tartozik. A futtatókörnyezet ezt a konfigurációs fájlt használja a figyelni kívánt események meghatározásához, valamint az adatoknak a függvény végrehajtásának és az adatok visszaadásának módját. A következő példában a fájl function.jslátható.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

A `bindings` tulajdonság az eseményindítók és kötések konfigurálására szolgál. Az egyes kötések néhány gyakori beállítást és néhány olyan beállítást is megosztanak, amelyek egy adott típusú kötésre vonatkoznak. Minden kötéshez a következő beállítások szükségesek:

| Tulajdonság    | Értékek | Típus | Megjegyzések|
|---|---|---|---|
| típus  | A kötés neve.<br><br>Például: `queueTrigger`. | sztring | |
| irány | `in`, `out`  | sztring | Azt jelzi, hogy a kötés az adatoknak a függvénybe való fogadására vagy adatok küldésére szolgál. |
| name | Függvény azonosítója.<br><br>Például: `myQueue`. | sztring | A függvényben a kötött adathoz használt név. A C# esetében ez egy argumentum neve; JavaScript esetén a kulcs/érték lista kulcsa. |

## <a name="function-app"></a>Függvényalkalmazás
A Function alkalmazás végrehajtási környezetet biztosít az Azure-ban, amelyben a függvények futnak. Ennek megfelelően a függvények üzembe helyezésének és felügyeletének egysége. A Function alkalmazás egy vagy több olyan önálló függvényből áll, amelyek kezelése, üzembe helyezése és méretezése együtt történik. A functions-alkalmazás összes funkciója ugyanazzal az árképzési csomaggal, telepítési módszerrel és futtatókörnyezet-verzióval rendelkezik. Úgy képzelheti el, hogy egy Function-alkalmazás a függvények rendszerezésére és közös kezelésére szolgál. További információt a [Function app kezelése](functions-how-to-use-azure-function-app-settings.md)című témakörben talál. 

> [!NOTE]
> A Function app összes függvényét ugyanabba a nyelvre kell létrehozni. A Azure Functions futtatókörnyezet [korábbi verzióiban](functions-versions.md) ez nem volt szükséges.

## <a name="folder-structure"></a>Mappa szerkezete
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A fenti az alapértelmezett (és ajánlott) mappa szerkezete egy Function alkalmazáshoz. Ha módosítani szeretné egy függvény kódjának helyét, módosítsa a `scriptFile` _function.js_ fájljának a szakaszát. Javasoljuk továbbá, hogy a [csomag központi](deployment-zip-push.md) telepítését a projekt Azure-beli Function-alkalmazásba történő üzembe helyezéséhez használja. Meglévő eszközöket is használhat, például a [folyamatos integrációt és üzembe helyezést](functions-continuous-deployment.md) , valamint az Azure-DevOps.

> [!NOTE]
> Ha manuálisan helyez üzembe egy csomagot, ügyeljen arra, hogy a _host.jsa_ fájl-és a függvény mappáiban közvetlenül a `wwwroot` mappára telepítse. Ne adja meg a `wwwroot` mappát a központi telepítések között. Ellenkező esetben a `wwwroot\wwwroot` mappákkal végződik.

#### <a name="use-local-tools-and-publishing"></a>Helyi eszközök használata és közzététel
A Function apps különböző eszközökkel, például a [Visual Studióval](./functions-develop-vs.md), a [Visual Studio Code](./create-first-function-vs-code-csharp.md), a [IntelliJ](./functions-create-maven-intellij.md), az [Eclipse](./functions-create-maven-eclipse.md)és a [Azure functions Core Tools](./functions-develop-local.md)használatával hozhatók létre és tehetők közzé. További információ: [code and test Azure functions helyileg](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Függvények szerkesztése a Azure Portalban
A Azure Portal beépített functions-szerkesztő lehetővé teszi a kód és a *function.js* közvetlen beágyazott fájlként való frissítését. Ez csak kis-és nagymértékű módosítások esetén ajánlott, a legjobb megoldás az, ha egy helyi fejlesztési eszközt használ, például a VS Code-ot.

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több kiváltó esemény is gyorsabb, mint egy egyszálas függvény futtatókörnyezete, akkor a futtatókörnyezet párhuzamosan többször is meghívhatja a függvényt.  Ha egy Function alkalmazás használja a használati [üzemeltetési csomagot](event-driven-scaling.md), akkor a Function alkalmazás automatikusan felskálázásra kerül.  A Function alkalmazás minden példánya, függetlenül attól, hogy az alkalmazás fut-e a használati üzemeltetési csomagon vagy egy normál [app Service üzemeltetési csomagon](../app-service/overview-hosting-plans.md), az egyidejű függvények párhuzamos használatát több szálon is feldolgozhatja.  Az egyes functions-példányokban az egyidejű függvények maximális száma a használt trigger típusától, valamint a Function alkalmazásban a más függvények által használt erőforrásoktól függően változhat.

## <a name="functions-runtime-versioning"></a>Függvények futtatókörnyezetének verziószámozása

A functions futtatókörnyezet verzióját az Alkalmazásbeállítások használatával állíthatja be `FUNCTIONS_EXTENSION_VERSION` . Például a "~ 3" érték azt jelzi, hogy a Function alkalmazás a 3. x verziót fogja használni főverzióként. A Function apps minden új alverzióra frissül, amint azok megjelentek. További információt, többek között a Function app pontos verziójának megtekintését lásd: [Azure functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md).

## <a name="repositories"></a>Adattárak
A Azure Functions kódja nyílt forráskódú, és a GitHub-adattárakban tárolódik:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions gazdagép](https://github.com/Azure/azure-functions-host/)
* [Azure Functions portál](https://github.com/azure/azure-functions-ux)
* [Azure Functions sablonok](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
Itt látható az összes támogatott kötés táblázata.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Problémák léptek fel a kötésekkel kapcsolatos hibákkal kapcsolatban? Tekintse át a [Azure functions kötési hibakódok](functions-bindings-error-pages.md) dokumentációját.


## <a name="connections"></a>Kapcsolatok

A függvény projektje név alapján hivatkozik a kapcsolati adatokra a saját konfigurációs szolgáltatójától. Nem fogadja el közvetlenül a kapcsolat részleteit, így azok a környezetek között módosíthatók. Előfordulhat például, hogy egy trigger definíciója tartalmaz egy `connection` tulajdonságot. Ez egy kapcsolódási sztringre hivatkozik, de a kapcsolódási karakterlánc nem állítható be közvetlenül a-ben `function.json` . Ehelyett `connection` egy olyan környezeti változó nevét kell megadni, amely tartalmazza a kapcsolódási karakterláncot.

Az alapértelmezett konfigurációs szolgáltató környezeti változókat használ. Ezeket az [Alkalmazásbeállítások](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) a Azure functions szolgáltatásban vagy helyileg történő fejlesztéskor a [helyi beállítások fájlból](functions-run-local.md#local-settings-file) is megadhatják.

### <a name="connection-values"></a>Kapcsolatok értékei

Ha a kapcsolódási név egyetlen pontos értékre oldódik fel, a futtatókörnyezet az értéket a _kapcsolódási karakterláncként_ azonosítja, amely általában titkos kulcsot tartalmaz. A kapcsolati karakterlánc részleteit a szolgáltatás határozza meg, amelyhez csatlakozni szeretne.

A kapcsolatok neve azonban több konfigurációs elem gyűjteményére is hivatkozhat. A környezeti változók úgy kezelhetők gyűjteményként, hogy dupla aláhúzással végződő megosztott előtagot használnak `__` . A csoport ezután hivatkozhat a kapcsolódás nevének ehhez az előtaghoz beállításával.

Előfordulhat például, hogy `connection` egy Azure Blob trigger definíciójának tulajdonsága a következő: `Storage1` . Ha nincs egyetlen, névvel konfigurált karakterlánc-érték, a `Storage1` `Storage1__serviceUri` `serviceUri` kapcsolat tulajdonságához lesz használva. A kapcsolatok tulajdonságai eltérőek az egyes szolgáltatásokhoz. Tekintse meg a kapcsolódást használó bővítmény dokumentációját.

### <a name="configure-an-identity-based-connection"></a>Identitás-alapú kapcsolatok konfigurálása

A Azure Functions egyes kapcsolatai titkos kód helyett identitás használatára vannak konfigurálva. A támogatás a kapcsolatok használatával függ a bővítménytől. Bizonyos esetekben előfordulhat, hogy a függvényekben továbbra is szükség van egy kapcsolati karakterláncra annak ellenére, hogy az a szolgáltatás, amelyhez csatlakozik, identitás-alapú kapcsolatokat támogat.

> [!IMPORTANT]
> Még ha egy kötési bővítmény is támogatja az identitás-alapú kapcsolatokat, akkor a konfiguráció még nem támogatott a felhasználási tervben. Lásd az alábbi támogatási táblázatot.

Az identitás-alapú kapcsolatokat a következő trigger-és kötési bővítmények támogatják:

| Kiterjesztés neve | Bővítmény verziója                                                                                     | A használati terv identitás-alapú kapcsolatainak támogatása |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure-blob     | [5.0.0-béta vagy újabb verzió](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Azure Queue    | [5.0.0-béta vagy újabb verzió](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |

> [!NOTE]
> Az identitás-alapú kapcsolatok támogatása még nem érhető el a functions futtatókörnyezet által a Core Behaviors szolgáltatáshoz használt tárolási kapcsolatokhoz. Ez azt jelenti, hogy a `AzureWebJobsStorage` beállításnak egy kapcsolatok sztringnek kell lennie.

#### <a name="connection-properties"></a>Kapcsolat tulajdonságai

Az Azure-szolgáltatásokhoz tartozó identitás-alapú kapcsolatok a következő tulajdonságokat fogadják el:

| Tulajdonság    | Környezeti változó | Kötelező | Description |
|---|---|---|---|
| Szolgáltatás URI-ja | `<CONNECTION_NAME_PREFIX>__serviceUri` | Yes | Annak a szolgáltatásnak az adatsík URI azonosítója, amelyhez csatlakozik. |

Egy adott kapcsolattípus esetében további beállítások is támogatottak. Tekintse meg a kapcsolódást készítő összetevő dokumentációját.

Az Azure Functions szolgáltatásban üzemeltetett identitás-alapú kapcsolatok [felügyelt identitást](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)használnak. A rendszer által hozzárendelt identitás alapértelmezés szerint használatos. Más környezetekben való futtatáskor, például a helyi fejlesztésnél, a fejlesztői identitást használja a rendszer, bár ez az alternatív kapcsolódási paraméterek használatával testreszabható.

##### <a name="local-development"></a>Helyi fejlesztés

Helyileg futtatva a fenti konfiguráció azt jelzi, hogy a futtatókörnyezet a helyi fejlesztői identitást használja. A kapcsolat a következő helyekről kísérli meg a token lekérését, sorrendben:

- A Microsoft-alkalmazások között megosztott helyi gyorsítótár
- A jelenlegi felhasználói környezet a Visual Studióban
- A jelenlegi felhasználói környezet a Visual Studio Code-ban
- Az aktuális felhasználói környezet az Azure CLI-ben

Ha ezek közül egyik beállítás sem sikeres, hiba történik.

Bizonyos esetekben előfordulhat, hogy egy másik identitás használatát szeretné megadni. Hozzáadhat olyan konfigurációs tulajdonságokat is, amelyek a másodlagos identitásra mutatnak.

> [!NOTE]
> A következő konfigurációs beállítások nem támogatottak a Azure Functions szolgáltatásban.

Az ügyfél-AZONOSÍTÓval és a titkos kulccsal rendelkező Azure Active Directory egyszerű szolgáltatással való kapcsolódáshoz adja meg a kapcsolatot a következő tulajdonságokkal:

| Tulajdonság    | Környezeti változó | Kötelező | Description |
|---|---|---|---|
| Szolgáltatás URI-ja | `<CONNECTION_NAME_PREFIX>__serviceUri` | Yes | Annak a szolgáltatásnak az adatsík URI azonosítója, amelyhez csatlakozik. |
| Bérlőazonosító | `<CONNECTION_NAME_PREFIX>__tenantId` | Yes | A Azure Active Directory bérlő (könyvtár) azonosítója. |
| Ügyfél-azonosító | `<CONNECTION_NAME_PREFIX>__clientId` | Yes |  A bérlőn belüli alkalmazás-regisztráció ügyfél-(alkalmazás-) azonosítója. |
| Titkos ügyfélkulcs | `<CONNECTION_NAME_PREFIX>__clientSecret` | Yes | Az alkalmazás regisztrálásához létrehozott ügyfél-titkos kulcs. |

#### <a name="grant-permission-to-the-identity"></a>Engedély megadása az identitásnak

Bármilyen identitás használatban kell lennie a kívánt műveletek végrehajtásához szükséges engedélyekkel. Ez általában egy szerepkör az Azure RBAC való hozzárendelésével vagy egy hozzáférési házirendben megadott identitás megadásával történik attól függően, hogy melyik szolgáltatáshoz csatlakozik. Tekintse meg az egyes szolgáltatásokhoz szükséges dokumentációt, valamint azt, hogy milyen engedélyekre van szükség.

> [!IMPORTANT]
> Előfordulhat, hogy a szolgáltatás bizonyos engedélyeket tesz elérhetővé, amelyek nem szükségesek az összes kontextushoz. Ha lehetséges, tartsa be a **legalacsonyabb jogosultsági szint elvét**, és csak az identitáshoz szükséges jogosultságokat adja meg. Ha például az alkalmazásnak egy blobból kell beolvasnia, használja a [Storage blob Adatolvasói](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) szerepkört, mivel a [Storage blob-adatok tulajdonosa](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) túlzott engedélyekkel rendelkezik az olvasási művelethez.


## <a name="reporting-issues"></a>Jelentéskészítési problémák
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Következő lépések
További információkat találhat az alábbi forrásokban:

* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [Az Azure Functions helyi kódolása és tesztelése](./functions-develop-local.md)
* [Ajánlott eljárások Azure Functions](functions-best-practices.md)
* [Azure Functions C# – fejlesztői dokumentáció](functions-dotnet-class-library.md)
* [Azure Functions Node.js fejlesztői referenciája](functions-reference-node.md)
