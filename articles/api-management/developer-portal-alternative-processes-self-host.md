---
title: Alternatív megoldások saját üzemeltetésű fejlesztői portálhoz
titleSuffix: Azure API Management
description: Ismerje meg a fejlesztői portál azure-beli üzembe API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741701"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>A saját gazda fejlesztői portál alternatív megközelítései

A fejlesztői portál önálló gazdagépeként többféle alternatív módszer [is elérhető:](developer-portal-self-host.md)

* Használja a tervező és a közzétevő éles buildeket.

* Egy Azure-függvényalkalmazással tegye közzé a portált.

* A lapbetöltési idők csökkentése érdekében a portál fájljait Content Delivery Network (CDN) elé.

Ez a cikk az egyes megközelítésekkel kapcsolatos információkat tartalmaz. 

Ha még nem tette meg, állítson be egy helyi [környezetet](developer-portal-self-host.md#step-1-set-up-local-environment) a fejlesztői portál legújabb kiadásában.

## <a name="build-for-production"></a>Build éles környezetben

Ha a portál fejlesztési környezetét együttműködési célokra online szeretné használni, használja a tervező és a közzétevő éles buildeket. Az éles buildek csomagolják a fájlokat, kizárják a forrástérképeket stb.

Hozzon létre egy csomagot a `./dist/designer` könyvtárban az parancs futtatásával:

```sh
npm run build-designer
```

Az eredmény egy egyoldalas alkalmazás, így továbbra is üzembe helyezheti egy statikus webkiszolgálón, például a statikus Azure Blob Storage számára.

Hasonlóképpen helyezzen el egy lefordított és optimalizált közzétevőt a `./dist/publisher` mappában:

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Függvényalkalmazás használata a portál közzétételéhez

Futtassa a közzétételi lépést a felhőben a helyi végrehajtás alternatívájaként.

Az Azure-függvényalkalmazásokkal való közzététel megvalósításához a következő előfeltételekre van szükség:

- [Hozzon létre egy Azure-függvényt.](../azure-functions/functions-create-first-azure-function.md) A függvénynek JavaScript nyelvi függvénynek kell lennie.
- Telepítse a Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>1. lépés: A kimeneti tároló konfigurálása

A tartalom feltöltése közvetlenül az üzemeltetési webhelyre ("$web" kimeneti tárolóba helyi mappa helyett. Konfigurálja ezt a változást a `./src/config.publish.json` fájlban:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>2. lépés: A függvényalkalmazás összeállítása és üzembe helyezése

A mappában található egy HTTP-eseményindító-függvényminta. `./examples` A buildhez és a helyen való helyére `./dist/function` futtassa a következő parancsot:

```sh
npm run build-function
```

Ezután jelentkezzen be az Azure CLI-be, és telepítse:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Az üzembe helyezés után http-hívással hívhatja meg:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Üzemeltetés és CDN

Saját fejlesztői portálon javasolt egy Azure [Storage-fiók](developer-portal-self-host.md) használata a webhelye tárolásához. A fájlokat azonban bármilyen megoldáson keresztül közzéteheti, beleértve az üzemeltetési szolgáltatók szolgáltatásait is.

A fájlokat el is tudja látni egy Content Delivery Network (CDN) a lapbetöltési idő csökkentése érdekében. Javasoljuk, hogy használja a [Azure CDN.](https://azure.microsoft.com/services/cdn/)

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
