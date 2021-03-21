---
title: Összetett entitás frissítése – LUIS
description: Frissítse az összetett entitást a gépi tanulási entitásra a verziófrissítési folyamattal a LUIS portálon.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025242"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Összetett entitás frissítése gépi tanulási entitásra

Az összetett entitások frissítése a gépi tanulási entitásra olyan entitás létrehozásához, amely átfogóbb előrejelzéseket kap az entitás hibakereséséhez.

## <a name="current-version-model-restrictions"></a>A modell jelenlegi korlátozásai

A frissítési folyamat gépi tanulási entitásokat hoz létre az alkalmazásában található meglévő összetett entitások alapján az alkalmazás egy új verziójába. Ilyenek például az összetett entitások gyermekei és szerepkörei. A folyamat a címkéket például hosszúságú kimondott szöveg is átváltja az új entitás használatára.

## <a name="upgrade-process"></a>Frissítési folyamat

A frissítési folyamat:
* Új gépi tanulási entitást hoz létre minden összetett entitáshoz.
* Alárendelt entitások:
    * Ha a gyermek entitás csak az összetett entitásban használatos, akkor csak a gépi tanulási entitáshoz lesz hozzáadva.
    * Ha a gyermek entitás összetett, _és_ különálló entitásként (például hosszúságú kimondott szöveg) van használatban, akkor a rendszer a verziót entitásként és alentitásként adja hozzá az új gépi tanulási entitáshoz.
    * Ha az alárendelt entitás egy szerepkört használ, a rendszer minden egyes szerepkört egy azonos nevű alentitásba konvertál.
    * Ha az alárendelt entitás nem gépi tanulásra szolgáló entitás (reguláris kifejezés, entitás vagy előre összeállított entitás), egy új alentitás jön létre ugyanazzal a névvel, és az új alentitás rendelkezik a nem gépi tanulást használó entitással, a szükséges funkcióval kiegészítve.
* A nevek megmaradnak, de az alentitás/testvér szintjén egyedinek kell lenniük. Tekintse meg az [egyedi elnevezési korlátokat](./luis-limits.md#name-uniqueness).
* A hosszúságú kimondott szöveg található feliratok az új gépi tanulási entitásra vannak átváltva alentitásokkal.

A modell változásának megismeréséhez használja a következő diagramot:

|Régi objektum|Új objektum|Jegyzetek|
|--|--|--|
|Összetett entitás|gépi tanulási entitás szerkezettel|Mindkét objektum szülő objektum.|
|Az összetett gyermek entitás **egyszerű entitás**|alentitás|Mindkét objektum alárendelt objektum.|
|Az összetett gyermek entitás **előre összeépített entitás** , például szám|az előre összeállított entitások, például a szám és az alentitás neve az előre felépített számú entitás _funkciójának_ értéke _true_ értékre van állítva.|az alentitás az alentitások szintjén megkötéssel rendelkező funkciót tartalmaz.|
|Az összetett gyermek entitás előre összeállított **entitás** , például szám, és az előre elkészített entitás **szerepköre**|a szerepkör nevű alentitás és az alentitás az előre elkészített Number entitás funkcióját a korlátozási beállítás értéke TRUE (igaz) értékre van állítva.|az alentitás az alentitások szintjén megkötéssel rendelkező funkciót tartalmaz.|
|Szerepkör|alentitás|A szerepkör neve az alentitás neve lesz. Az alentitás a gépi tanulási entitás közvetlen leszármazottja.|

## <a name="begin-upgrade-process"></a>Frissítési folyamat megkezdése

A frissítés előtt ügyeljen a következőre:

* Verziók módosítása, ha nem a megfelelő verziót frissíti


1. A frissítési folyamat megkezdése az értesítésből, vagy megvárhatja a következő ütemezett kérést.

    > [!div class="mx-imgBorder"]
    > ![Frissítés megkezdése értesítésekről](./media/update-composite-entity/notification-begin-update.png)

1. Az előugró ablakban válassza a **Frissítés most** lehetőséget.

1. Tekintse át **, hogy mi történik az adatok frissítésekor,** majd válassza a **Folytatás** lehetőséget.

1. Válassza ki az összetett entitásokat a listából a frissítéshez, majd válassza a **Folytatás** lehetőséget.

1. A nem betanított módosításokat a jelenlegi verzióról a frissített verzióra helyezheti át a jelölőnégyzet bejelölésével.

1. A frissítési folyamat elindításához válassza a **Folytatás** lehetőséget.

1. A folyamatjelző sáv a frissítési folyamat állapotát jelzi.

1. A folyamat megkezdése után új, betanított verziót kell megtennie az új gépi tanulási entitásokkal. Válassza az új entitások **kipróbálása** lehetőséget az új entitás megjelenítéséhez.

    Ha az új entitás frissítése vagy betanítása meghiúsult, az értesítés további információkat tartalmaz.

1. Az entitások listája lapon az új entitások a típus neve mellett **új** jelöléssel jelennek meg.

## <a name="next-steps"></a>Következő lépések

* [Szerzők és közreműködők](luis-how-to-collaborate.md)