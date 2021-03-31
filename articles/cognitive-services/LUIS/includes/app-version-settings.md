---
title: fájl belefoglalása
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "83653198"
---
Ismerje meg a normalizálás [fogalmait](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) , valamint a [verzió](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API-k használatával frissítse ezeket a beállításokat, vagy használja a Luis-portál **felügyelet** szakaszát, a **Beállítások** lapot.


|Felhasználói felület beállításai|API-beállítás|Tájékoztatás|
|--|--|--|
|Nem determinisztikus képzés használata|`UseAllTrainingData`|A képzések a negatív mintavételezés kis hányadát használják. Ha a kis negatív mintavételezés helyett az összes adatát szeretné használni, állítsa a következőre: `true` . |
|Mellékjelek normalizálása|`NormalizeDiacritics`|A Mellékjelek normalizálása a hosszúságú kimondott szöveg lévő mellékjeleket normál karakterekkel helyettesíti. Ez a beállítás csak a mellékjeleket támogató [nyelveken](../luis-reference-application-settings.md#diacritics-normalization) érhető el.|
|Központozás normalizálása|`NormalizePunctuation`|A központozás normalizálása azt jelenti, hogy a modellek betanítása előtt és a végponti lekérdezések megkezdése előtt az írásjelek el lesznek távolítva a hosszúságú kimondott szöveg.|
|Word-űrlapok normalizálása|`NormalizeWordForm`|A gyökéren túli Word-űrlapok figyelmen kívül hagyása.|
