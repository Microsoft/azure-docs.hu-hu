---
title: Adattárolás
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: df8af9dc86e26a073b76bff33f35bad2ba32b5e8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105750"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Az adattárolás és eltávolítása a Cognitive Services Language Understanding (LUIS)
A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja. Ezek az adatok 30 napig tárolja. 

## <a name="export-and-delete-app"></a>Exportálás és az alkalmazás törlése
Felhasználók teljes hozzáféréssel rendelkeznek a keresztül [exportálása](luis-how-to-start-new-app.md#export-app) és [törlése](luis-how-to-start-new-app.md#delete-app) az alkalmazást. 

## <a name="utterances-in-an-intent"></a>A megjelölésű kimondott szöveg
Példa utterances képzéshez használt törlése [LUIS](luis-reference-regions.md). Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.

## <a name="utterances-in-review"></a>Tekintse át a kimondott szöveg
Beszédmódok törölheti, hogy a LUIS javasol a felhasználó megcímkézzen listájából a  **[áttekintés végpont kimondott szöveg lap](luis-how-to-review-endoint-utt.md)**. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.

## <a name="accounts"></a>Fiókok
Ha töröl egy fiókot, az összes alkalmazás törlődnek, azokhoz példa kimondott szöveg és a naplókat. Az adatok a fiók előtt 60 napig megőrződnek, és adatok véglegesen törlődnek.

Fiók törlése folyamatban van a érhető el a **beállítások** lapot. Válassza ki a fiók nevét a jobb felső navigációs sávon való a **beállítások** lapot.

## <a name="data-inactivity-as-an-expired-subscription"></a>Lejárt az előfizetésem, adatok inaktív
Adatok megőrzése vagy törlése céljából, inaktív LUIS alkalmazás bármikor _a Microsoft saját belátása szerint értékeli_ lejárt az előfizetésem kell kezelni. Egy alkalmazás akkor számít inaktívnak, ha az utolsó 90 napra vonatkozó megfelel a következő feltételeknek: 

* Volt-e **nincs** intézett hívások.
* Nem lett módosítva.
* Nem nem a jelenlegi kulcs rendelt hozzá.
* Nem volt olyan felhasználó bejelentkezni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg exportálása és a egy alkalmazás törlése](luis-how-to-start-new-app.md)