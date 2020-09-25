---
title: Korlátok és kvóták – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti a licencelési kulcsok különböző típusait, valamint a Custom Vision Service korlátozásait és kvótáit.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: b79bf5e8ead16bbdf9c69e8d7faae43fa778ab3e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316579"
---
# <a name="limits-and-quotas"></a>Korlátok és kvóták

A Custom Vision szolgáltatásnak két szintje van. A Azure Portalon keresztül regisztrálhat F0 (ingyenes) vagy S0 (standard) előfizetésre. A díjszabással és a tranzakciókkal kapcsolatos részletekért tekintse meg a megfelelő [Cognitive Services díjszabási oldalt](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .

A betanítási lemezképek száma projektenként és címkék szerint projektenként a S0-projektek esetében várhatóan növekedni fog.

|Szempont|**F0**|**S0**|
|-----|-----|-----|
|Projektek|2|100|
|Képek betanítása projektenként |5000|100.000|
|Előrejelzések/hónap|10,000 |Korlátlan|
|Címkék/projekt|50|500|
|Ismétlések |10|10|
|Képek címkével ellátott minimális száma címkék szerint, besorolás (50 + ajánlott) |5|5|
|Képek címkével ellátott minimális száma címkék szerint, objektumok észlelése (50 + ajánlott)|15|15|
|Mennyi ideig tárolják az előrejelzési képeket|30 nap|30 nap|
|[Előrejelzési](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tárolóval (tranzakció/másodperc)|2|10|
|[Megjóslási](https://go.microsoft.com/fwlink/?linkid=865445) műveletek tárterület nélkül (tranzakció/másodperc)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-hívások másodpercenként)|2|10|
|[Egyéb API-hívások](https://go.microsoft.com/fwlink/?linkid=865446) (tranzakció/másodperc)|10|10|
|Elfogadott rendszerképek típusai|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Minimális képméret/szélesség képpontban|256 (lásd a megjegyzést)|256 (lásd a megjegyzést)|
|Maximális képméret/szélesség képpontban|10 240|10 240|
|Maximális képméret (betanítási képfeltöltés) |6 MB|6 MB|
|Maximális képméret (előrejelzés)|4 MB|4 MB|
|Objektumok maximális száma objektum-észlelési betanítási képen|300|300|
|Címkék maximális száma besorolási képenként|100|100|

> [!NOTE]
> Az 256 képpontnál kisebb méretű képek is el lesznek fogadva, a méretük pedig nagyobb.
> A kép oldalarányának aránya nem lehet nagyobb, mint 25
