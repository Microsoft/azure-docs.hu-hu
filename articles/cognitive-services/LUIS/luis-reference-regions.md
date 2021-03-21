---
title: Közzétételi régiók & végpontok – LUIS
description: A Azure Portalban megadott régió ugyanaz, ahol a LUIS-alkalmazást közzé fogja tenni, és egy végponti URL-cím jön létre ugyanahhoz a régióhoz.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681607"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Régiók és a hozzájuk tartozó kulcsok létrehozása és közzététele

A Luis-szerkesztő régiókat a Luis-portál támogatja. Ahhoz, hogy egy LUIS-alkalmazást több mint egy régióban tegyen közzé, ahhoz régiónként legalább egy kulcsra van szüksége.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS authoring-régiók

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS egy portált használhat, függetlenül a régiótól, a [www.Luis.ai](https://www.luis.ai). Továbbra is ugyanazt a régiót kell létrehoznia és közzétennie.

A szerzői régiók [párosított feladatátvételi régiókkal](../../best-practices-availability-paired-regions.md) rendelkeznek

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Közzétételi régiók és Azure-erőforrások

Az alkalmazás közzé lesz téve a LUIS-portálon hozzáadott LUIS-erőforrásokhoz társított összes régióban. Ha például egy [www.Luis.ai][www.luis.ai]-on létrehozott alkalmazáshoz létrehoz egy Luis vagy kognitív szolgáltatási erőforrást a **westus** -ben, és [hozzáadja az alkalmazáshoz erőforrásként](luis-how-to-azure-subscription.md), akkor az alkalmazás az adott régióban lesz közzétéve.

## <a name="public-apps"></a>Nyilvános alkalmazások
A nyilvános alkalmazások minden régióban közzé vannak téve, hogy a régió-alapú LUIS-erőforrás kulcsával rendelkező felhasználók hozzáférhessenek az alkalmazáshoz, amely bármelyik régióban hozzá van rendelve az erőforrás-kulcshoz.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>A közzétételi régiók a létrehozási régiókhoz vannak kötve

Az authoring region alkalmazást csak a megfelelő közzétételi régióban lehet közzétenni. Amennyiben az alkalmazása rossz szerzői régióban van, exportálja az alkalmazást, majd importálja a közzétételi régiójának megfelelő szerzői régióba.

> [!NOTE]
> A on létrehozott LUIS https://www.luis.ai -alkalmazások mostantól az összes végponton közzétehetők, beleértve az [Európai](#publishing-to-europe) és [ausztráliai](#publishing-to-australia) régiókat is.

## <a name="publishing-to-europe"></a>Közzététel Európába

 Globális régió | API-régió készítése | & lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| Európa | `westeurope`| Közép-Franciaország<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Európa | `westeurope`| Észak-Európa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Európa | `westeurope`| Nyugat-Európa<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Európa | `westeurope`| Az Egyesült Királyság déli régiója<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Közzététel Ausztráliában

 Globális régió | API-régió készítése | & lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| Ausztrália | `australiaeast` | Kelet-Ausztrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Egyéb közzétételi régiók

 Globális régió | API-régió készítése | & lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Dél-Afrika északi régiója<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Közép-India<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Ázsia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Japán<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Nyugat-Japán<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Dél-Korea középső régiója<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Délkelet-Ázsia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Észak-UAE<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Közép-Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Az USA középső régiója<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA keleti régiója<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA 2. keleti régiója<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA északi középső régiója<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA déli középső régiója<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati középső régiója<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA 2. nyugati régiója<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Dél-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Dél-Brazília<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Végpontok

További információ a [szerzői műveletek és előrejelzési végpontokról](developer-reference-resource.md).

## <a name="failover-regions"></a>Feladatátvételi régiók

Minden régió rendelkezik egy másodlagos régióval, amely feladatátvételt hajt végre. Európa az Egyesült Államokban és Ausztráliában is feladatátvételt hajt végre az Ausztráliában belül.

A szerzői régiók [párosított feladatátvételi régiókkal](../../best-practices-availability-paired-regions.md)rendelkeznek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Előre elkészített entitások referenciája](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai