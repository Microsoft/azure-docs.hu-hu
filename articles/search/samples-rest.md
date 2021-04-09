---
title: REST-minták
titleSuffix: Azure Cognitive Search
description: Keresse meg az Azure Cognitive Search bemutató REST-kód mintáit, amelyek a keresési vagy a felügyeleti REST API-kat használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98956531"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>REST-kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search megoldás funkcióit és munkafolyamatait bemutató REST API mintákat. Ezek a minták a [**keresési REST API-kat**](/rest/api/searchservice)használják.

A REST az Azure-Cognitive Search végleges programozási felülete, és a programozott módon meghívható összes művelet elérhetővé válik a REST-ben, majd az SDK-ban is. Emiatt a dokumentáció legtöbb példája kihasználja a REST API-kat a fontos fogalmak bemutatására vagy magyarázatára.

A REST-mintákat általában a Poster fejleszti és teszteli, de használhat bármely olyan ügyfelet, amely támogatja a HTTP-hívásokat:

+ Első lépések [: hozzon létre egy Azure Cognitive Search indexet a REST API](search-get-started-rest.md) -k használatával, amely segítséget nyújt a http-hívások kialakításához.
+ Ha Visual Studio Code-ban dolgozik, próbálja ki a [Visual Studio Code-bővítményt az Azure Cognitive Searchhoz](search-get-started-vs-code.md), amely jelenleg előzetes verzióban érhető el.

## <a name="doc-samples"></a>Doc-minták

A Cognitive Search csapata által bemutatott funkciók és munkafolyamatok. Ezen minták közül sokat az oktatóanyagok, a rövid útmutatók és a útmutató cikkek tartalmaznak. Ezeket a mintákat az [**Azure-Samples/Azure-Search-Poster-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) on GitHub webhelyen találja.

| Példák | Cikk |
|---------|---------|
| [Gyors útmutató](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet a REST API](search-get-started-rest.md)-k használatával. Ez a cikk a keresési indexek mintaadatok használatával történő létrehozásához, betöltéséhez és lekérdezéséhez szükséges alapszintű munkafolyamatot ismerteti. |
| [Oktatóanyag](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | [Oktatóanyag: az Azure-Blobok kereshető tartalmának létrehozásához használja a REST és a AI használatát](cognitive-search-tutorial-blob.md). Ebből a cikkből megtudhatja, hogyan hozhat létre olyan készségkészlet, amely az Azure-blobokon keresztül ismétli az információk és a következtetések szerkezetét.|
| [Hibakeresés – munkamenetek](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Az oktatóanyag forráskódja [: a Készségkészlet diagnosztizálása, javítása és érvényesítése](cognitive-search-tutorial-debug-sessions.md). Ez a cikk bemutatja, hogyan használhatja a készségkészlet hibakeresési munkamenetet a Azure Portal. A REST használatával hozhatók létre a hibakeresés során használt objektumok.|
| [Egyéni elemzők](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | [Oktatóanyag: egyéni analizátor létrehozása a telefonszámokhoz](tutorial-create-custom-analyzer.md). Ez a cikk azt ismerteti, hogyan használhatók az elemzők a minták és a speciális karakterek kereshető tartalomban való megőrzéséhez.|
| [Knowledge-Store](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Forrás kód a [Knowledge Store-hoz a REST és a Poster használatával](knowledge-store-create-rest.md). Ez a cikk ismerteti a tudásalapú adatbányászati munkafolyamatokhoz használt Tudásbázis feltöltéséhez szükséges lépéseket. |
| [előrejelzések](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Forráskód a [dúsítások formálásához és exportálásához](knowledge-store-projections-examples.md). Ez a cikk bemutatja, hogyan határozhatja meg a fizikai adatstruktúrákat egy Tudásbázisban.|
| [index – titkosított Blobok](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Forráskód a [titkosított Blobok blob-indexelő és szakértelmével használatával történő indexeléséhez](search-howto-index-encrypted-blobs.md). Ez a cikk bemutatja, hogyan indexelheti a korábban a Azure Key Vault használatával titkosított dokumentumokat az Azure Blob Storage-ban. |

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="other-samples"></a>Egyéb minták

A Cognitive Search csapata a következő mintákat is közzéteszi, de a dokumentációban nem hivatkozik rá. A kapcsolódó readme-fájlok használati utasításokat tartalmaznak.

| Példák | Leírás |
|---------|-------------|
| [Lekérdezés – példák](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | A különböző lekérdezési technikákat bemutató posters-gyűjtemények, például a fuzzy Search, a RegEx és a helyettesítő karakteres keresés, az automatikus kiegészítés és így tovább. |