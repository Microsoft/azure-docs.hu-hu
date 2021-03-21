---
title: OpenAPI-metaadatok a Azure Functionsban
description: A Azure Functions OpenAPI-támogatásának áttekintése
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000205"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2,0 metaadat-támogatás a Azure Functionsban (előzetes verzió)
A OpenAPI 2,0 (korábban hencegés) metaadatainak támogatása Azure Functions egy előzetes funkció, amellyel OpenAPI 2,0-definíciót írhat a Function alkalmazásban. Ezt a fájlt ezután a Function alkalmazás használatával futtathatja.

> [!IMPORTANT]
> Az OpenAPI előzetes verziójú funkció csak ma, az 1.x futtatókörnyezetben érhető el. 1.x függvényalkalmazás létrehozásáról [itt találhat](./functions-versions.md#creating-1x-apps) további információt.

A [OpenAPI-metaadatok](https://swagger.io/) lehetővé teszik, hogy egy olyan függvényt, amely a különböző szoftverek széles körének használatát REST API üzemelteti. Ez a szoftver olyan Microsoft-ajánlatokat is tartalmaz, mint a PowerApps, valamint a Azure App Service, harmadik féltől származó fejlesztői eszközök, például a [Poster](https://www.getpostman.com/docs/importing_swagger)és [számos további csomag](https://swagger.io/tools/) [API apps funkcióját](../app-service/overview.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Javasoljuk, hogy kezdje a [kezdeti lépéseket ismertető oktatóanyaggal](./functions-openapi-definition.md) , majd térjen vissza ehhez a dokumentumhoz, és tudjon meg többet az egyes funkciókról.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>OpenAPI-definíció támogatásának engedélyezése
A Function app **platform funkcióinak** API- **definíciós** lapján az összes OpenAPI beállításait konfigurálhatja.

> [!NOTE]
> A függvény API-definíciós funkciója jelenleg nem támogatott a bétaverziós futtatókörnyezet esetében.

Egy üzemeltetett OpenAPI-definíció és egy rövid útmutató meghatározásának engedélyezéséhez állítsa be az **API-definíció forrását** a **Function (előzetes verzió)** értékre. A **külső URL-cím** lehetővé teszi, hogy a függvény egy máshol üzemeltetett OpenAPI-definíciót használjon.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Hencegő csontváz létrehozása a függvény metaadataiból
A sablonok segítségével megkezdheti az első OpenAPI-definíció megírását. A definíciós sablon szolgáltatás egy ritka OpenAPI-definíciót hoz létre az összes HTTP-trigger funkciójának function.jsfájljában található metaadatok használatával. További információkat kell kitöltenie az API-ról a OpenAPI- [specifikációból](https://swagger.io/specification/), például a kérések és a válaszok sablonjairól.

Részletes útmutatásért tekintse meg az [első lépéseket ismertető oktatóanyagot](./functions-openapi-definition.md).

### <a name="available-templates"></a><a name="templates"></a>Elérhető sablonok

|Név| Leírás |
|:-----|:-----|
|Generált definíció|Egy OpenAPI-definíció, amely a függvény meglévő metaadataiból kikövetkeztethető maximális mennyiségű adatot tartalmaz.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Tartalmazott metaadatok a generált definícióban

A következő táblázat a Azure Portal beállításokat és a function.jsmegfelelő, a generált hencegő csontvázra leképezett információit tartalmazza.

|Swagger.jsbekapcsolva|Portál felhasználói felülete|Function.jsbekapcsolva|
|:----|:-----|:-----|
|[Állomás](https://swagger.io/specification/#fixed-fields-15)|**Function alkalmazás beállításai**  >  **App Service beállítások**  >  **Áttekintés**  >  **URL-cím**|*Nincs jelen*
|[Elérési utak](https://swagger.io/specification/#paths-object-29)|**Integrálás**  >  **Kiválasztott http-metódusok**|Kötések: útvonal
|[Elérési út eleme](https://swagger.io/specification/#path-item-object-32)|**Integrálás**  >  **Útvonal sablonja**|Kötések: metódusok
|[Biztonság](https://swagger.io/specification/#security-scheme-object-112)|**Kulcsok**|*Nincs jelen*|
|OperationID|**Route + engedélyezett műveletek**|Route + engedélyezett műveletek|

\*A műveleti azonosító csak a PowerApps és a flow integrálásához szükséges.
> [!NOTE]
> Az x-MS-Summary bővítmény megjelenített nevet biztosít Logic Apps, PowerApps és flow-ban.
>
> További információkért lásd: [a PowerApps-hez készült hencegés definíciójának testreszabása](/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>API-definíció beállítása CI/CD használatával

 Engedélyeznie kell az API-definíció üzemeltetését a portálon, mielőtt engedélyezi a verziókövetés számára az API-definíciók forrás-vezérlőelemből való módosítását. Kövesse az alábbi utasításokat:

1. Tallózással keresse meg az **API-definíciót (előzetes verzió)** a Function app-beállításokban.
   1. Állítsa be az **API-definíció forrását** a **működéshez**.
   1. Kattintson az **API-definíciós sablon létrehozása** lehetőségre, majd a **Mentés** gombra a sablon definíciójának létrehozásához a későbbi módosításhoz.
   1. Jegyezze fel az API-definíció URL-címét és kulcsát.
1. [Folyamatos integráció/folyamatos üzembe helyezés (CI/CD) beállítása](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Módosítsa swagger.jsa forrás vezérlőelemben a \site\wwwroot \.azurefunctions\swagger\swagger.json címen.

A tárházban swagger.jsmódosításait mostantól az API-definíció URL-címe és az 1. c lépésben feljegyzett kulcs alapján hajtja végre a függvény alkalmazás.

## <a name="next-steps"></a>Következő lépések
* [Első lépések oktatóanyag](./functions-openapi-definition.md). Próbálja ki a bemutatót egy OpenAPI-definíció működés közbeni megtekintéséhez.
* [Azure functions GitHub-tárház](https://github.com/Azure/Azure-Functions/). Tekintse meg a functions tárházat, és küldjön visszajelzést az API-definíció támogatásának előzetes verziójáról. Hozzon fel GitHub-problémát minden, amit frissíteni szeretne.
* [Azure functions fejlesztői referenciája](functions-reference.md). A kódolási függvények ismertetése és az eseményindítók és kötések meghatározása.
