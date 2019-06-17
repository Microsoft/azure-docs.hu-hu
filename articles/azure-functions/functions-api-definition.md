---
title: Az Azure Functions OpenAPI metaadatait |} A Microsoft Docs
description: OpenAPI-támogatás az Azure Functions áttekintése
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 6d11961f06a75341e633c7a8963e6b83ed37cf13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61341701"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2.0 metaadatok támogatása az Azure Functions (előzetes verzió)
OpenAPI 2.0 (korábbi nevén Swagger) metaadatok támogatása az Azure Functions egy előzetes verziójú funkció, amely segítségével írhat egy OpenAPI 2.0-definíció függvényalkalmazás belül. Ezt a fájlt majd a függvényalkalmazás használatával is üzemeltethet.

> [!IMPORTANT]
> Az OpenAPI előzetes verziójú funkció csak ma, az 1.x futtatókörnyezetben érhető el. 1\.x függvényalkalmazás létrehozásáról [itt találhat](./functions-versions.md#creating-1x-apps) további információt.

[OpenAPI metaadatait](https://swagger.io/) lehetővé teszi, hogy a függvény, amely számos egyéb szoftverek által használt REST API-t futtató. Ezek közé tartozik a Microsoft egyéb ajánlatai, mint például a PowerApps és a [Azure App Service API Apps funkcióját](../app-service/overview.md), külső fejlesztői eszközök, például a [Postman](https://www.getpostman.com/docs/importing_swagger), és [számos további csomagok](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Javasoljuk, hogy az a [kezdeti lépéseket ismertető oktatóanyag](./functions-api-definition-getting-started.md) , és ezt a dokumentumot, az adott funkciókról további információ majd visszatérő.

## <a name="enable"></a>OpenAPI definíció támogatásának engedélyezése
Az összes OpenAPI-beállításokat lehet megadni a **API-definíció** a függvényalkalmazás oldalán **platformfunkciók**.

> [!NOTE]
> Függvény API-definíció szolgáltatás jelenleg nem támogatott a bétaverziójú futtatókörnyezetben.

Az üzemeltetett OpenAPI-definíció és a egy rövid definíció generációja engedélyezéséhez állítsa **API-definíció forrása** való **funkció (előzetes verzió)** . **Külső URL-cím** lehetővé teszi, hogy a függvény az OpenAPI-definíció, amely rendelkezik a hosted máshol.

## <a name="generate-definition"></a>A dbfunctionexpression hozzon létre egy Swagger skeleton
A sablon segítségével írhat az első OpenAPI-definíciójában. A definíció-sablon funkció hoz létre egy ritka OpenAPI-definíció használatával minden metaadat a function.json fájlban a HTTP-eseményindító függvények mindegyike esetében. Az API-jával kapcsolatban további információt adhat meg kell a [OpenAPI-specifikáció](https://swagger.io/specification/), például kérés- és sablonokat.

Részletes útmutatásért tekintse meg a [kezdeti lépéseket ismertető oktatóanyag](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Az elérhető sablonok

|Name (Név)| Leírás |
|:-----|:-----|
|A létrehozott definíció|OpenAPI-definíció, amely a függvény meglévő metaadataiból következtetni lehet a maximális méretű.|

### <a name="quickstart-details"></a>A létrehozott definíciót tartalmaz metaadatok

A generált Swagger vázat van leképezve, az alábbi táblázat a Azure-portál beállítások és a hozzájuk tartozó adatokat function.json jelöli.

|Swagger.json|Portál felhasználói Felületét|Function.json|
|:----|:-----|:-----|
|[Gazdagép](https://swagger.io/specification/#fixed-fields-15)|**Alkalmazásbeállítások függvény** > **App Service szolgáltatás beállításainak** > **áttekintése** > **URL-címe**|*Nincs jelen*
|[Elérési utak](https://swagger.io/specification/#paths-object-29)|**Integráció** > **kiválasztott HTTP-metódusok**|Kötések: Útválasztás
|[Elem elérési útja](https://swagger.io/specification/#path-item-object-32)|**Integráció** > **útvonalsablon**|Kötések: Metódusok
|[Biztonság](https://swagger.io/specification/#security-scheme-object-112)|**kulcsok**|*Nincs jelen*|
|operationID*|**Útvonal + engedélyezett műveletek**|Route + Allowed Verbs|

\*A művelet azonosítója csak a PowerApps és Flow integrálása megadása kötelező.
> [!NOTE]
> Az x-ms-summary bővítmény megadja a megjelenítendő nevet a Logic Apps, PowerApps és Flow.
>
> További tudnivalókért lásd: [powerapps Swagger-definíció testreszabása](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Állítsa be az API-definíció a CI/CD használatával

 API-definíció a portál üzemeltetési verziókövetés a forráskezelőből az API-definíció módosítása engedélyezése előtt engedélyeznie kell. Kövesse az alábbi utasításokat:

1. Keresse meg a **API-definíció (előzetes verzió)** a a függvényalkalmazás-beállításokat.
   1. Állítsa be **API-definíció forrása** való **függvény**.
   1. Kattintson a **létrehozása API-definíciósablon** , majd **mentése** később módosítása a Sablondefiníció létrehozásához.
   1. Megjegyzés: az API-definíció URL-címe és a kulcs.
1. [Folyamatos integráció/folyamatos üzembe helyezés (CI/CD) beállítása](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. \Site\wwwroot, verziókövetési rendszerben swagger.json módosítása\.azurefunctions\swagger\swagger.json.

Most a tárházban swagger.json módosításai az API-t, a függvényalkalmazás által üzemeltetett definíció URL-címe és a kulcs részben megadott lépés 1.c.

## <a name="next-steps"></a>További lépések
* [Kezdeti lépéseket ismertető oktatóanyag](functions-api-definition-getting-started.md). Próbálja ki a forgatókönyv egy OpenAPI-definíció működés megtekintéséhez.
* [Az Azure Functions GitHub-adattára](https://github.com/Azure/Azure-Functions/). Tekintse meg a Functions-adattár küldjön visszajelzést az API definition támogatásának előzetes verziójával. Győződjön meg a githubon bármit meg szeretné tekinteni frissített.
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md). Ismerje meg a függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
