---
title: Egyéni pénzmosás-képesség a szakértelmével-ben
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search szakértelmével képességeinek kiterjesztése Azure Machine Learning modellekkel.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630275"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Az Azure Cognitive Search alkoholtartalom-növelési folyamatának pénzmosás-képessége

> [!IMPORTANT] 
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jelenleg nincs .NET SDK-támogatás.

A **pénzmosás** -képesség lehetővé teszi, hogy az AI-bővítést egy egyéni [Azure Machine learning](../machine-learning/overview-what-is-azure-ml.md) (pénzmosás) modellel bővítse. A pénzmosás-modell betanítása [és üzembe helyezése](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)után a **pénzmosás** -képesség a mesterséges intelligenciát integrálja.

A beépített képességekhez hasonlóan a **pénzmosás** -képesség is bemenetekkel és kimenetekkel rendelkezik. A rendszer JSON-objektumként küldi el a bemeneti adatokat a központilag telepített pénzmosás-szolgáltatásnak, amely egy JSON-adattartalomot ad vissza válaszként a sikeres állapotkód mellett. A válasz várhatóan a **pénzmosás** -képesség által megadott kimeneteket adja meg. Minden más válasz hibát jelez, és nem végezhető el a dúsítás.

> [!NOTE]
> Az indexelő kétszer próbálkozik a pénzmosás szolgáltatás által visszaadott szabványos HTTP-állapotkódok esetében. Ezek a HTTP-állapotkódok a következők:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Előfeltételek

* Egy [pénzmosás-munkaterület](../machine-learning/concept-workspace.md)
* Egy [Azure Kubernetes szolgáltatáshoz tartozó számítási cél](../machine-learning/concept-compute-target.md) a munkaterületen egy [üzembe helyezett modellel](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * A [számítási célnak engedélyezve kell](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service)lennie az SSL-nek. Az Azure Cognitive Search csak a **https** -végpontokhoz való hozzáférést engedélyezi
  * Az önaláírt tanúsítványokat nem lehet használni.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Custom. AmlSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk. A használni kívánt paraméterek attól függnek, hogy milyen [hitelesítésre van szükség a pénzmosás szolgáltatásához](#WhatSkillParametersToUse)

| Paraméter neve | Leírás |
|--------------------|-------------|
| `uri` | (Nincs szükség [hitelesítésre vagy kulcsos hitelesítésre](#WhatSkillParametersToUse)) Annak a [pénzmosás-szolgáltatásnak a pontozási URI-ja](../machine-learning/how-to-consume-web-service.md) , amelybe a _JSON_ -tartalom el lesz küldve. Csak a **https** URI-séma engedélyezett. |
| `key` | (A [kulcs hitelesítéséhez](#WhatSkillParametersToUse)szükséges) A [pénzmosás-szolgáltatás kulcsa](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Jogkivonat- [hitelesítéshez](#WhatSkillParametersToUse)szükséges). A pénzmosás szolgáltatás Azure Resource Manager erőforrás-azonosítója. Ennek az előfizetések/{GUID}/resourceGroups/{Resource-Group-Name}/Microsoft. MachineLearningServices/workspaces/{munkaterület-Name}/Services/{service_name} formátumúnak kell lennie. |
| `region` | (Nem kötelező [jogkivonat-hitelesítéshez](#WhatSkillParametersToUse)). Az a [régió](https://azure.microsoft.com/global-infrastructure/regions/) , amelyre a pénzmosás szolgáltatást telepítették. |
| `timeout` | Választható Ha meg van adva, az API-hívást készítő http-ügyfél időtúllépését jelzi. A fájlnak XSD "dayTimeDuration" értéknek kell lennie (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) értékének korlátozott részhalmaza). Például `PT60S` 60 másodpercig. Ha nincs beállítva, a rendszer egy alapértelmezett 30 másodperces értéket választ. Az időtúllépés legfeljebb 230 másodpercig és legalább 1 másodpercig állítható be. |
| `degreeOfParallelism` | Választható Ha meg van adva, az indexelő által az Ön által megadott végponttal párhuzamosan kezdeményezett hívások számát jelzi. Csökkentheti ezt az értéket, ha a végpont nem a kérelem terhelése alatt túl magas, vagy ha a végpont több kérést is képes fogadni, és az indexelő teljesítményének növelését szeretné.  Ha nincs beállítva, a rendszer az alapértelmezett 5 értéket használja. A Analyticsunits legfeljebb 10 és legalább 1 értékre állítható be.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Milyen képzettségi paramétereket kell használni

A szükséges pénzmosás-paraméterek attól függnek, hogy a pénzmosás szolgáltatás milyen hitelesítést használ, ha van ilyen. A pénzmosás-szolgáltatások három hitelesítési lehetőséget biztosítanak:

* [Kulcs alapú hitelesítés](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). A rendszer statikus kulcsot biztosít a pénzmosás-készségekből származó pontozási kérelmek hitelesítéséhez
  * _URI_ és _kulcs_ paraméterek használata
* [Jogkivonat-alapú hitelesítés](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). A pénzmosás szolgáltatás a [jogkivonat-alapú hitelesítés használatával van üzembe helyezve](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Az Azure Cognitive Search szolgáltatás [felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md) megkapja az [olvasó szerepkört](../machine-learning/how-to-assign-roles.md) a pénzmosás szolgáltatás munkaterületén. A pénzmosás-képesség ezután az Azure Cognitive Search szolgáltatás felügyelt identitásával hitelesíti a pénzmosás szolgáltatást, és nincs szükség statikus kulcsokra.
  * Használja a _resourceId_ paramétert.
  * Ha az Azure Cognitive Search szolgáltatás egy másik régióban található a pénzmosás munkaterületen, akkor a _régió_ paraméterrel állíthatja be azt a régiót, amelyet a pénzmosás szolgáltatás üzembe helyezett
* Nincs hitelesítés. A pénzmosás szolgáltatás használatához nincs szükség hitelesítésre
  * Az _URI_ paraméter használata

## <a name="skill-inputs"></a>Szaktudás bemenetei

Ehhez a képzettséghez nincsenek "előre definiált" bemenetek. Kiválaszthat egy vagy több olyan mezőt, amely a szaktudás végrehajtásának időpontjában már elérhető lesz, és a pénzmosás szolgáltatásnak továbbított _JSON_ -adattartalom különböző mezőket tartalmaz.

## <a name="skill-outputs"></a>Szaktudás kimenetei

Ehhez a szakértelemhöz nem tartoznak "előre definiált" kimenetek. Attól függően, hogy milyen választ ad vissza a pénzmosás szolgáltatás, adja hozzá a kimeneti mezőket, hogy azok a _JSON_ -válaszból is felvehetők legyenek.

## <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Minta bemeneti JSON-struktúra

Ez a _JSON_ -struktúra a pénzmosás szolgáltatásnak küldendő hasznos adatokat mutatja. A struktúra legfelső szintű mezői megfelelnek a szaktudás definíciójának szakaszában megadott "nevek" értéknek `inputs` . Ezeknek a mezőknek az értéke az `source` adott mezőkből származik (amelyek a dokumentum egy mezőjéből származnak, vagy esetleg egy másik képességből)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Minta kimenet JSON-szerkezete

A kimenet megfelel a pénzmosás szolgáltatás által visszaadott válasznak. A pénzmosás szolgáltatásnak csak _JSON_ -adattartalmat kell visszaadnia (a `Content-Type` Válasz fejlécének ellenőrzésével ellenőrizve), és olyan objektumnak kell lennie, amelyben a mezők olyan dúsítások, amelyek megfelelnek a "nevek" `output` értéknek, és amelynek értéke a dúsításnak minősül.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Beágyazott kialakítási minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Beágyazott beviteli JSON-struktúra

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Beágyazott példa a kimeneti JSON-struktúra kialakítására

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Hibák esetei
Amellett, hogy a pénzmosás nem érhető el, vagy nem sikeres állapotkódot küld, a következők hibás eseteknek minősülnek:

* Ha a pénzmosás-szolgáltatás sikeres állapotkódot ad vissza, de a válasz azt jelzi, hogy nem `application/json` , akkor a válasz érvénytelennek minősül, és a rendszer nem hajtja végre a dúsítást.
* Ha a pénzmosás-szolgáltatás érvénytelen JSON-értéket ad vissza

Azokban az esetekben, amikor a pénzmosás szolgáltatás nem érhető el, vagy HTTP-hibát ad vissza, a HTTP-hibával kapcsolatos összes rendelkezésre álló adathoz tartozó rövid hiba lesz hozzáadva az indexelő végrehajtási előzményeihez.

## <a name="see-also"></a>Lásd még

+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [A pénzmosás szolgáltatással kapcsolatos hibaelhárítás](../machine-learning/how-to-troubleshoot-deployment.md)