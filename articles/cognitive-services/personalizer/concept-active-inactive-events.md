---
title: Aktív és inaktív események – személyre szabás
description: Ez a cikk a személyre szabott szolgáltatáson belüli aktív és inaktív események használatát ismerteti.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253649"
---
# <a name="active-and-inactive-events"></a>Aktív és inaktív események

Az **aktív** események bármelyike a rangsorban, ahol tudni fogja, hogy az eredményt az ügyfélnek kell megjelennie, és meg kell határoznia a jutalom pontszámát. Ez az alapértelmezett viselkedés.

Az **inaktív** esemény olyan rangú hívás, amelyben nem biztos abban, hogy a felhasználó az üzleti logika miatt mindig látni fogja-e a javasolt műveletet. Ez lehetővé teszi az esemény elvetését, hogy a személyre szabás ne legyen betanítva az alapértelmezett jutalommal. Az inaktív események nem hívhatják meg a jutalmazási API-t.

Fontos, hogy a tanulási hurok tudja a tényleges esemény típusát. Az inaktív események nem kapnak jutalmat. Az aktív eseménynek jutalmazási hívással kell rendelkeznie, de ha az API-hívás soha nem történt meg, a rendszer az alapértelmezett jutalom pontszámot alkalmazza. Egy esemény állapotának módosítása inaktívról aktívra, amint tudja, hogy az hatással lesz a felhasználói élményre.

## <a name="typical-active-events-scenario"></a>Tipikus aktív események forgatókönyve

Amikor az alkalmazás meghívja a Rank API-t, megkapja a műveletet, amelyet az alkalmazásnak a **rewardActionId** mezőben kell megjelenítenie.  Ettől az időponttól kezdve a személyre szabott jutalom-hívás egy jutalom pontszámmal, amely ugyanazzal a Napszállta rendelkezik. A jutalmazási pontszám a modell jövőbeli rangsorolási hívásokhoz való betanítására szolgál. Ha a Napszállta nem érkezik jutalmazási hívás, a rendszer az alapértelmezett jutalmat alkalmazza. Az [alapértelmezett jutalmak](how-to-settings.md#configure-rewards-for-the-feedback-loop) a Azure Portal személyre szabott erőforrására vannak beállítva.

## <a name="other-event-type-scenarios"></a>Egyéb eseménytípus-forgatókönyvek

Bizonyos esetekben előfordulhat, hogy az alkalmazásnak meg kell hívnia a rangsort, mielőtt azt is tudja, hogy az eredményt fogja-e használni vagy megjeleníteni a felhasználó számára. Ez olyan helyzetekben fordulhat elő, amikor például a előléptetett tartalom lapjait a marketing-kampány felülírja. Ha a rangsor hívásának eredménye soha nem volt használatban, és a felhasználó soha nem látta, ne küldjön Önnek megfelelő jutalom-hívást.

Ezek a forgatókönyvek jellemzően a következő esetekben történnek:

* Olyan felhasználói felületet jelenít meg, amelyet a felhasználó esetleg nem lát.
* Az alkalmazása prediktív megszemélyesítést végez, amelyben a rangsorolási hívások kis valós idejű kontextusban történnek, és előfordulhat, hogy az alkalmazás nem használja a kimenetet.

Ezekben az esetekben a személyre szabással hívhatja meg a rangot, és az esemény _inaktívvá_ tételét kéri. A személyre szabott szolgáltatás nem vár jutalmat erre az eseményre, és nem alkalmaz alapértelmezett jutalmat.

Az üzleti logikában később, ha az alkalmazás a rangsor hívásában található információkat használja, csak _aktiválja_ az eseményt. Amint az esemény aktív, a személyre szabási esemény jutalmat vár. Ha a jutalmazási API-ra nem készül explicit hívás, a személyre szabott alapértelmezett jutalom érvényes.

## <a name="inactive-events"></a>Inaktív események

Ha le szeretné tiltani egy esemény betanítását, hívja a rangsort a használatával `learningEnabled = False` .

Inaktív esemény esetén a tanulás implicit módon aktiválódik, ha jutalmat küld a Napszállta, vagy meghívja az API-t az `activate` adott Napszállta.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja [, hogyan határozhatja meg a jutalom pontszámát és a megfontolandó információkat](concept-rewards.md).
