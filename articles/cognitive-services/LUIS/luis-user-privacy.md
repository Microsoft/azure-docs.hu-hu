---
title: Az adatok törlését & exportálása
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) őrzi meg a szolgáltatás ügyfél-tartalmat, de a LUIS felhasználó keresztül megtekintése, exportálását és törlését a teljes körű vezérléssel rendelkezik. Ezt megteheti a LUIS webes portálon vagy a LUIS programozható API-k révén.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: de8e8ba2c54a2d355cbff3ab50ab92a907ad67e9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073244"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportálás és az ügyféladatokat, a Language Understanding (LUIS) a Cognitive Services törlése

## <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása
Language Understanding Intelligent Service (LUIS) őrzi meg a szolgáltatás ügyfél-tartalmat, de a LUIS felhasználó keresztül megtekintése, exportálását és törlését a teljes körű vezérléssel rendelkezik. Ezt megteheti a LUIS weben keresztül [portál](luis-reference-regions.md) vagy a [LUIS programozható API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Ügyfél tartalom titkosítása a Microsoft regionális Azure storage-ban tárolt, és tartalmazza:

- Regisztráció során gyűjtött felhasználói fiók tartalom
- Betanítási adatok (azaz szándékot & entitások) a modellek létrehozásához szükséges
- Naplózza a felhasználói modellek javítása érdekében futásidőben felhasználói lekérdezések
  - Bármikor kikapcsolhatják lekérdezések naplózása hozzáfűzésével `&log=false` a kérést, részletesen [Itt](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése
A LUIS felhasználók törli a felhasználó tartalma, legyen az a LUIS webes portálon vagy a LUIS programozható API-k teljes hozzáféréssel rendelkeznek. A következő táblázat mindkét segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portál** | [Hivatkozás](luis-how-to-account-settings.md) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása
A LUIS-felhasználók az adatok megtekintése a portálon, a teljes hozzáféréssel rendelkeznek, azonban a LUIS programozható API-kon keresztül kell exportálni. Az alábbi táblázat a LUIS programozható API-kon keresztül adatexportálást segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS régiók referencia](./luis-reference-regions.md)
