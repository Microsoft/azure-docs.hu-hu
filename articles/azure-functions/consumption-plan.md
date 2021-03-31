---
title: Azure Functions használati terv üzemeltetése
description: Ismerje meg, hogyan futtathatja a kódot olyan környezetben, amely dinamikusan méretezhető, de csak a végrehajtás során használt erőforrásokért kell fizetnie.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760540"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions használati terv üzemeltetése

A használati terv használatakor a rendszer a Azure Functions gazdagép példányait dinamikusan hozzáadja és eltávolítja a bejövő események száma alapján. A használati terv a Azure Functions teljes <em>kiszolgáló</em> nélküli üzemeltetési lehetősége.

## <a name="benefits"></a>Előnyök

A fogyasztási terv automatikusan, akár nagy terhelésű időszakok esetén is méretezhető. A függvények használati tervben való futtatásakor a számítási erőforrásokért csak akkor kell fizetnie, ha a függvények futnak. Használatalapú csomag esetében egy függvény végrehajtása túllépi a konfigurált időkorlátot.

A használati terv és az üzemeltetési típusok összehasonlításával kapcsolatban lásd: a [függvények méretezési és üzemeltetési lehetőségei](functions-scale.md).

## <a name="billing"></a>Számlázás

A számlázás a végrehajtások száma, a végrehajtási idő és a felhasznált memória alapján történik. A használat a Function alkalmazás összes függvényében összesíthető. További információkért tekintse meg a [Azure functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

Ha többet szeretne megtudni arról, hogyan becsülheti fel a költségeket a használati tervekben, tekintse meg a [felhasználási terv költségeinek megismerése](functions-consumption-costs.md)című témakört.

## <a name="create-a-consumption-plan-function-app"></a>Felhasználási terv Function-alkalmazás létrehozása

Ha a Azure Portalban hoz létre egy Function alkalmazást, a használati terv az alapértelmezett. Ha API-kat használ a Function app létrehozásához, nem kell először létrehoznia App Service tervet, mint a prémium és a dedikált csomagok esetében.

Az alábbi hivatkozásokat követve megtudhatja, hogyan hozhat létre kiszolgáló nélküli Function alkalmazást a használati tervben, vagy programozott módon, vagy a Azure Portal:

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure Portal](./functions-get-started.md)
+ [Azure Resource Manager-sablon](functions-create-first-function-resource-manager.md)

A functions-alkalmazásokat a [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) vagy a [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure)alkalmazásból származó functions-projektek közzétételekor is létrehozhatja egy felhasználási tervben.

## <a name="multiple-apps-in-the-same-plan"></a>Több alkalmazás ugyanabban a csomagban

Az azonos régióban található Function apps ugyanahhoz a használati tervhez is hozzárendelhető. Nincs hátránya, hogy több alkalmazás is fut ugyanabban a használati csomagban. Ha több alkalmazást rendel ugyanahhoz a használati tervhez, az egyes alkalmazások rugalmasságát, méretezhetőségét és megbízhatóságát nem befolyásolja.

## <a name="next-steps"></a>Következő lépések

+ [Üzemeltetési lehetőségek Azure Functions](functions-scale.md)
+ [Eseményvezérelt skálázás Azure Functions](event-driven-scaling.md)
