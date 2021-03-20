---
title: HTTP-végpont testreszabása Azure Functions
description: Megtudhatja, hogyan szabhatja testre a HTTP-trigger végpontját Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683742"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>HTTP-végpont testreszabása Azure Functions

Ebből a cikkből megtudhatja, hogyan hozhat létre a Azure Functions a jól méretezhető API-kat. A Azure Functions beépített HTTP-eseményindítók és-kötések gyűjteményét kínálja, amelyek megkönnyítik a végpontok különböző nyelveken történő készítését, többek között a Node.js, a C# és egyebeket. Ebben a cikkben egy HTTP-trigger testreszabásával kezelheti az API-kialakításban meghatározott műveleteket. Emellett előkészítheti az API-t azáltal, hogy integrálja Azure Functions-proxyk és a modell API-k beállításával. Ezek a feladatok a kiszolgáló nélküli számítási környezeten felül érhetők el, így nem kell aggódnia az erőforrások méretezésével kapcsolatban – csak az API-logikára koncentrálhat.

## <a name="prerequisites"></a>Előfeltételek 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Az eredményül kapott függvény a cikk további részében lesz felhasználva.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="customize-your-http-function"></a>A HTTP-függvény testreszabása

Alapértelmezés szerint a HTTP-trigger függvény a HTTP-metódusok fogadására van konfigurálva. Használhatja az alapértelmezett URL-címet is `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . Ebben a szakaszban úgy módosítja a függvényt, hogy csak a kérések fogadására válaszoljon `/api/hello` . 

1. Az Azure Portalon keresse meg a függvényt. Válassza az **integráció** lehetőséget a bal oldali menüben, majd válassza a **http (REQ)** lehetőséget az **trigger** alatt.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="HTTP-függvény testreszabása":::

1. Használja az alábbi táblázatban megadott HTTP-trigger beállításait.

    | Mező | Mintaérték | Description |
    |---|---|---|
    | Útvonalsablon | /hello | Meghatározza, hogy melyik útvonalat használja a függvény meghívására |
    | Engedélyszint | Névtelen | Opcionális: A függvényt API-kulcs nélkül is elérhetővé teszi |
    | Kiválasztott HTTP-metódusok | GET | Csak bizonyos HTTP-metódusoknak engedélyezi a függvény meghívását |

    Nem tartalmazza az `/api` alapútvonal előtagját az útválasztási sablonban, mert egy globális beállítás kezeli.

1. Kattintson a **Mentés** gombra.

További információ a HTTP-függvények testreszabásáról: [Azure FUNCTIONS http-kötések](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Az API tesztelése

Ezt követően tesztelje a függvényt, és tekintse meg, hogyan működik együtt az új API-felülettel:
1. A függvény lapon válassza a **kód + tesztelés** lehetőséget a bal oldali menüben.

1. A felső menüben válassza a **függvény URL-címének lekérése** lehetőséget, és másolja ki az URL-címet. Győződjön meg arról, hogy most az `/api/hello` elérési utat használja.
 
1. Nyissa meg az URL-címet egy új böngészőlapon vagy a választott REST-ügyfélben. 

   A böngészők alapértelmezés szerint használják a GET értéket.
 
1. Adja hozzá a paramétereket a lekérdezési karakterlánchoz az URL-címben. 

   Például: `/api/hello/?name=John`.
 
1. Az ENTER billentyű lenyomásával erősítse meg, hogy működik-e. Meg kell jelennie a válasznak: "*Hello John*".

1. Azt is megteheti, hogy a végpontot más HTTP-metódussal hívja meg, hogy a függvény ne legyen végrehajtva. Ehhez használjon REST-ügyfelet, például a cURL, a Poster vagy a Hegedűs.

## <a name="proxies-overview"></a>Proxyk áttekintése

A következő szakaszban az API-t egy proxyn keresztül fogja felsurface. Az Azure Functions-proxyk lehetővé teszik a kérések továbbítását más erőforrásokhoz. Egy HTTP-végpontot ugyanúgy definiálhat, mint a HTTP-triggert. Azonban a végpont meghívásakor végrehajtandó kód írása helyett egy távoli implementáció URL-címét kell megadnia. Így több API-forrást is létrehozhat egyetlen API-felületre, amely megkönnyíti az ügyfelek számára a használatot, ami akkor hasznos, ha az API-t a Service szolgáltatásként szeretné felépíteni.

A proxyk bármilyen HTTP-erőforrásra mutathatnak, például:
- Azure Functions 
- API Apps alkalmazások az [Azure App Service-ben](../app-service/overview.md)
- Docker-tárolók a [Linuxon futó App Service-ben](../app-service/overview.md#app-service-on-linux)
- Bármely más üzemeltetett API

További információ a proxykról: [Azure Functions-proxyk használata].

## <a name="create-your-first-proxy"></a>Első proxy létrehozása

Ebben a szakaszban egy új proxyt hoz létre, amely a teljes API-hoz elérhetővé teszi a felületet. 

### <a name="setting-up-the-frontend-environment"></a>Az előtérrendszer beállítása

Ismételje meg a [Függvényalkalmazás létrehozása](./functions-get-started.md) szakasz lépéseit egy új függvényalkalmazás létrehozásához, amelyben létrehozhatja a proxyt. Ez az új alkalmazás URL-címe az API felülete, és a korábban szerkesztés alatt álló Function alkalmazás háttérként szolgál.

1. Keresse meg az új előtérbeli függvényalkalmazást a portálon.
1. Válassza a **Platformfunkciók**, majd az **Alkalmazásbeállítások** lehetőséget.
1. Görgessen le az **Alkalmazásbeállítások** között, ahol a kulcs/érték párok vannak tárolva, és hozzon létre egy új beállítást a kulccsal `HELLO_HOST` . Állítsa az értékét háttérbeli függvényalkalmazás gazdagépére, például `<YourBackendApp>.azurewebsites.net`. Ez az érték a HTTP-függvény teszteléséhez korábban átmásolt URL-cím része. Erre a beállításra később hivatkozni fog a konfigurációban.

    > [!NOTE] 
    > Azért érdemes alkalmazásbeállításokat használni a gazdagép konfigurálásához, hogy a proxynak ne legyen nem módosítható környezeti függősége. Az alkalmazásbeállítások használatakor lehetősége van áthelyezni a proxykonfigurációt más környezetekbe, és ekkor a környezetspecifikus alkalmazásbeállítások fognak vonatkozni rá.

1. Kattintson a **Mentés** gombra.

### <a name="creating-a-proxy-on-the-frontend"></a>Proxy létrehozása az előtérben

1. Váltson vissza az előtér-függvény alkalmazására a portálon.

1. A bal oldali menüben válassza a **proxyk** lehetőséget, majd válassza a **Hozzáadás** lehetőséget. 

1. Az **új proxy** lapon használja az alábbi táblázatban szereplő beállításokat, majd válassza a **Létrehozás** lehetőséget.

    | Mező | Mintaérték | Leírás |
    |---|---|---|
    | Név | HelloProxy | Felhasználóbarát név, kizárólag kezelési célra |
    | Útvonalsablon | /api/remotehello | Meghatározza, hogy melyik útvonalat használja a proxy meghívására |
    | Háttér-URL | https://%HELLO_HOST%/api/hello | Meghatározza a végpontot, ahova továbbítja a kéréseket a proxyn keresztül |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Proxy létrehozása":::

    A Azure Functions-proxyk nem adja `/api` meg az alap elérési út előtagját, amelynek szerepelnie kell az útválasztási sablonban. A `%HELLO_HOST%` szintaxis a korábban létrehozott alkalmazás-beállításra hivatkozik. A feloldott URL az eredeti függvényre fog mutatni.

1. Próbálja ki az új proxyt úgy, hogy átmásolja a proxy URL-címét, és teszteli azt a böngészőben vagy a kedvenc HTTP-ügyfelével:
    - Névtelen függvények esetén használja a következőt:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Engedélyezési funkciót használó függvények esetén:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>API-utánzat létrehozása

Ezután egy proxy használatával hozzon létre egy modell-API-t a megoldásához. Ez a proxy lehetővé teszi az ügyfelek fejlesztését, anélkül, hogy a háttérrendszer teljes mértékben implementálva lenne. Később a fejlesztés során létrehozhat egy új Function-alkalmazást, amely támogatja ezt a logikát, és átirányítja a proxyt.

A modell-API létrehozásához új proxyt hozunk létre, ezúttal a [app Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)használatával. Először is keresse meg a függvényalkalmazást a portálon. Válassza a **platform szolgáltatásai** lehetőséget, és a **fejlesztői eszközök** területen keresse meg **app Service Editor**. A App Service Editor egy új lapon nyílik meg.

Válassza ki a `proxies.json` elemet a bal oldali navigációs sávon. Ez a fájl tárolja az összes proxy konfigurációját. Ha a [függvények központi telepítési módszereit](./functions-continuous-deployment.md)használja, ezt a fájlt a forrás vezérlőelemben tartja karban. A fájlról itt olvashat részletesen: [Proxyk speciális konfigurációja](./functions-proxies.md#advanced-configuration).

Ha követte az eddigi lépéseket, a proxies.jsa következőhöz hasonlóan kell kinéznie:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Ezután adja hozzá a modell API-ját. Cserélje le a proxies.jst a fájlra a következő kóddal:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Ez a kód egy új proxyt hoz létre, `GetUserByName` a `backendUri` tulajdonság nélkül. Egy másik erőforrás meghívása helyett módosítja a proxyk alapértelmezett válaszát, felülírva azt. A kérések és a válaszfelülírások egy háttér-URL-lel együtt is használhatók. Ez a technika különösen akkor hasznos, ha egy örökölt rendszerre való proxyt használ, ahol előfordulhat, hogy módosítania kell a fejléceket, a lekérdezési paramétereket és így tovább. További információk a kérésekről és a válaszfelülírásokról: [Kérések és válaszok módosítása a proxykban](./functions-proxies.md).

Az API-utánzat teszteléséhez hívja meg a `<YourProxyApp>.azurewebsites.net/api/users/{username}` végpontot a böngésző vagy a választott REST-ügyfél használatával. A _{username}_ értéket cserélje a felhasználónevet képviselő sztringre.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan hozhat létre és szabhat testre API-t Azure Functionson. Ezenkívül elsajátította, hogyan lehet több API-t, köztük utánzatokat egyesíteni egyetlen API-felületen. Ezekkel a technikákkal bármilyen összetettségű API-t kiépíthet az Azure Functions kiszolgáló nélküli számítási modelljének használatával.

A következő referenciák hasznára lehetnek az API továbbfejlesztése során:

- [Azure Functions HTTP-kötések](./functions-bindings-http-webhook.md)
- [Azure Functions-proxyk használata]
- [Azure Functions API dokumentálása (előzetes verzió)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Azure Functions-proxyk használata]: ./functions-proxies.md
