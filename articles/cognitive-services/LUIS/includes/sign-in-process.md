---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97371311"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), válassza ki az országot vagy régiót, és fogadja el a használati feltételeket. Ha ehelyett a **saját alkalmazások** jelennek meg, a Luis-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre. Két lehetőség regisztrálása:

    * Azure-erőforrás használata (ajánlott) – lehetővé teszi a LUIS-fiók összekapcsolását egy új vagy meglévő Azure authoring-erőforrással. Ez egyenértékű a már áttelepített regisztrációval. Később nem kell átlépnie az [áttelepítési folyamaton](../luis-migration-authoring.md#what-is-migration) .

    * Próbaverziós kulcs használata. Ez lehetővé teszi, hogy bejelentkezzen a LUIS-ba olyan próbaverziós erőforrással, amelyet nem kell beállítania. Ha ezt a lehetőséget választja, a [fiók áttelepítésére](../luis-migration-authoring.md#migration-steps) és az alkalmazások egy szerzői erőforráshoz való csatolására lesz szükség.

1. A megjelenő **authoring (szerzői műveletek** ) ablakban keresse meg az Azure-előfizetését és a Luis authoring Resource elemet. Ha nem rendelkezik erőforrással, létrehozhat egy újat.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Válasszon egy Language Understanding authoring Resource típust.":::
    
    Új authoring-erőforrás létrehozásakor adja meg a következő információkat:
    * **Bérlő neve** – az a bérlő, amelyhez az Azure-előfizetés társítva van.
    * **Azure-előfizetés neve** – az erőforrásért fizetendő előfizetés.
    * **Azure-erőforráscsoport neve** – válasszon ki vagy hozzon létre egy egyéni erőforráscsoport-nevet. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
    * **Azure-Erőforrás neve** – az Ön által választott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.


