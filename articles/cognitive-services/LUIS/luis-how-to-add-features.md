---
title: Szolgáltatások használata a LUIS-szavak listájának fejlesztéséhez
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) használatával olyan alkalmazás-funkciókat adhat hozzá, amelyek javítják a kategóriákat és a mintákat tartalmazó szándékok és entitások észlelését vagy előrejelzését
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95999926"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Szolgáltatások használata a szólisták jelzésének növeléséhez

A LUIS-alkalmazáshoz további funkciókat adhat hozzá, hogy javítsa a pontosságot. A-funkciók segítséget nyújtanak a LUIS számára olyan javaslatok biztosításához, amelyekkel bizonyos szavak és kifejezések egy alkalmazás-tartománybeli szókincs részét képezik.

Tekintse át a [fogalmakat](luis-concept-feature.md) , hogy megértse, mikor és miért érdemes használni a szolgáltatást.

## <a name="add-phrase-list-as-a-feature"></a>Kifejezések listájának hozzáadása szolgáltatásként

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Válassza a **Létrehozás**, majd az alkalmazás bal oldali paneljének **szolgáltatások** elemét.

1. A **szolgáltatások** lapon válassza a **+ Létrehozás** elemet.

1. Az **új kifejezés lista funkció létrehozása** párbeszédpanelen adjon meg egy nevet, például: `Cities` . Az **érték** mezőbe írja be a városok példáit, például: `Seattle` . Egyszerre csak egy értéket lehet beírni, vagy vesszővel elválasztva az értékek halmazát, majd nyomja le az **ENTER** billentyűt.

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás (kifejezések listája) városainak hozzáadása képernyőkép](./media/luis-add-features/add-phrase-list-cities.png)

    Ha a LUIS számára elég értéket adott meg, a javaslatok megjelennek. Az összes javasolt értéket **+ felveheti** , vagy kijelölhet egyéni kifejezéseket is.

1. **Ezeket az értékeket** megtarthatja, ha a kifejezések felcserélhetők.

1. A kifejezések listája a teljes alkalmazásra vonatkozik a **globális** beállítással, vagy egy adott modellre (cél vagy entitás). Ha a kifejezés listát a szándék vagy az entitás _funkciói_ alapján hozza létre, a váltógomb nem globális értékre van állítva. Ebben az esetben a váltás jelentése az, hogy a szolgáltatás csak a modellhez van helyi, ezért _nem globális_ az alkalmazáshoz.

1. Válassza a **Kész** lehetőséget. Az új funkció a **ml-szolgáltatások** lapra kerül.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> A kifejezések listáját törölheti vagy inaktiválhatja a **ml-szolgáltatások** oldal környezetfüggő eszköztárán.

## <a name="global-phrase-list-applies-to-entire-app"></a>A globális kifejezések listája a teljes alkalmazásra vonatkozik

A kifejezések listáját arra a szándékra vagy entitásra kell alkalmazni, amelyet segíteni kíván, de előfordulhat, hogy a kifejezéseket a teljes alkalmazásra **globális** szolgáltatásként kell alkalmazni.

A ML-szolgáltatások lapon válassza ki a kifejezés listát, majd válassza a **globális** lehetőséget a felső kontextus eszköztáron.

## <a name="model-as-a-feature"></a>Modell szolgáltatásként

Az entitás lehet egy [szándék vagy entitás szolgáltatás](luis-concept-feature.md).

Ha egy entitást szolgáltatásként szeretne hozzáadni egy szándékhoz, válassza ki a szándékot a szándékok lapról, majd válassza a **+ szolgáltatás hozzáadása** lehetőséget a környezetfüggő eszköztáron. A lista tartalmazza az összes olyan kifejezés-listát és entitást, amely szolgáltatásként alkalmazható.

Ha egy entitást szolgáltatásként szeretne hozzáadni egy másik entitáshoz, hozzáadhatja a szolgáltatást a leképezés részletei lapon az [Entity paletta](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) használatával, vagy [hozzáadhatja a szolgáltatást](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) az entitás részletei lapon.

## <a name="next-steps"></a>Következő lépések

Egy szolgáltatás hozzáadása, szerkesztése, törlése vagy inaktiválása után ismét [betaníthatja és tesztelheti az alkalmazást, és](luis-interactive-test.md) ellenőrizheti, hogy javul-e a teljesítmény.
