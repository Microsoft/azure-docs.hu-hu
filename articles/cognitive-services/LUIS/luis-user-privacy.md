---
title: Exportálás & adattörlés – LUIS
titleSuffix: Azure Cognitive Services
description: Teljes körűen szabályozhatja az adatmegjelenítését, exportálását és törlését. Az adatvédelem és a megfelelőség biztosítása érdekében törölje a vásárlói adatokat.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680184"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Ügyféladatok exportálása és törlése Language Understanding (LUIS) Cognitive Services

Az adatvédelem és a megfelelőség biztosítása érdekében törölje a vásárlói adatokat.

## <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-kérési funkciók összegzése
Language Understanding Intelligent Service (LUIS) megőrzi az ügyfél tartalmát a szolgáltatás működtetésére, de a LUIS-felhasználó teljes körűen szabályozhatja az adatokat a megtekintéssel, az exportálással és a törléssel. Ezt a LUIS [webportálon](luis-reference-regions.md) vagy a [Luis authoring (más néven programozott) API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)-kon keresztül teheti meg.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Az ügyfél tartalmát a Microsoft regionális Azure Storage tárolja, és a következőket tartalmazza:

- A regisztráció során összegyűjtött felhasználói fiók tartalma
- A modellek létrehozásához szükséges betanítási adatgyűjtés
- Az [aktív tanulás](luis-concept-review-endpoint-utterances.md) által használt naplózott felhasználói lekérdezések segítenek a modell fejlesztésében
  - A felhasználók a kérelemhez fűzött hozzáfűzéssel kapcsolhatják ki a lekérdezési naplózást `&log=false` , részletek [itt](troubleshooting.md#how-can-i-disable-the-logging-of-utterances) találhatók

## <a name="deleting-customer-data"></a>Ügyféladatok törlése
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a felhasználói tartalmak törléséhez, akár a LUIS webportálon, akár a LUIS authoring (más néven programozott) API-kon keresztül. A következő táblázat a mindkettőt segítő hivatkozásokat tartalmazza:

| | **Felhasználói fiók** | **Alkalmazás** | **Példa a Kimondás (ok) ra** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portál** | [Hivatkozás](luis-concept-data-storage.md#delete-an-account) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktív tanulási hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Naplózott hosszúságú kimondott szöveg](luis-concept-data-storage.md#disable-logging-utterances) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Ügyféladatok exportálása
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a portálon tárolt adat megtekintéséhez, azonban azt a LUIS authoring (más néven programozott) API-k használatával kell exportálni. Az alábbi táblázat a LUIS authoring (más néven programozott) API-k használatával folytatott adatexportálási hivatkozásokat tartalmazza:

| | **Felhasználói fiók** | **Alkalmazás** | **Kimondás (ok)** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Az aktív tanulás helye

Az [aktív tanulás](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)engedélyezéséhez a közzétett Luis-végpontokon fogadott felhasználók naplózott hosszúságú kimondott szöveg a következő Azure-régiókban tárolódnak:

* [Európa](#europe)
* [Ausztrália](#australia)
* [Egyesült Államok](#united-states)

Az aktív tanulási adatmennyiség (alább részletezve) kivételével a LUIS a [regionális szolgáltatások adattárolási gyakorlatát](https://azuredatacentermap.azurewebsites.net/)követi.

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Európa

A Europe authoring (más néven programozási API-k) erőforrásai az Azure Europe földrajzában futnak, és támogatják a végpontok a következő Azure-régiókban való üzembe helyezését:

* Európa
* Franciaország
* Egyesült Királyság

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által fogadott hosszúságú kimondott szöveg az Azure Európa-földrajzában az aktív tanuláshoz lesz tárolva.

### <a name="australia"></a>Ausztrália

Az ausztrál authoring (más néven programozási API-k) erőforrásai az Azure Ausztráliában földrajzában futnak, és támogatják a végpontok a következő Azure-régiókban való üzembe helyezését:

* Ausztrália

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által kapott hosszúságú kimondott szöveg az Azure ausztráliai földrajza az aktív tanuláshoz.

### <a name="united-states"></a>Egyesült Államok

Egyesült Államok authoring (más néven programozott API-k) erőforrásait az Azure Egyesült Államok földrajza üzemelteti, és támogatja a végpontok a következő Azure-régiókba való telepítését:

* Az Európai vagy ausztráliai szerzői régiók nem támogatják az Azure földrajzi területeit

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által fogadott hosszúságú kimondott szöveg az Azure Egyesült Államok-földrajzában az aktív tanuláshoz lesz tárolva. 

## <a name="disable-active-learning"></a>Aktív tanulás letiltása

Az aktív tanulás letiltásához tekintse meg az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning)című témakört. A tárolt hosszúságú kimondott szöveg kezeléséhez lásd: a [teljes törlés](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [LUIS-régiók referenciája](./luis-reference-regions.md)
