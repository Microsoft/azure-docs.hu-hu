---
title: Bejelentkezés a LUIS portálra
description: Ha Ön egy új felhasználó, aki bejelentkezik a LUIS portálra, a bejelentkezési élmény kis mértékben eltérhet az aktuális felhasználói fióktól.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706775"
---
# <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Ebből a cikkből megtudhatja, hogyan kezdheti el a LUIS-portált, és hogyan hozhat létre szerzői erőforrásokat. A cikkben ismertetett lépések elvégzése után LUIS-alkalmazásokat hozhat létre és tehet közzé.

## <a name="access-the-portal"></a>Hozzáférés a portálhoz


1. A LUIS megkezdéséhez nyissa meg a [Luis portált](https://www.luis.ai). Ha még nem rendelkezik előfizetéssel, a rendszer kérni fogja, hogy hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com//free/cognitive-services/) , és térjen vissza a portálra.
2. Frissítse a lapot az újonnan létrehozott előfizetéssel való frissítéshez
3. Válassza ki az előfizetését a legördülő listából

    > [!div class="mx-imgBorder"]
    > ![előfizetések kiválasztása](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Ha az előfizetése egy másik bérlőn belül lakik, nem fogja tudni váltani a bérlőket a meglévő ablakból. A bérlők átváltásához zárja be ezt az ablakot, és válassza a felső sávon a monogramját tartalmazó jobb oldali ikont. Kattintson a **másik szerzői erőforrás kiválasztása** elemre az ablak újbóli megnyitásához.

    > [!div class="mx-imgBorder"]
    > ![könyvtárak váltása](./media/migrate-authoring-key/switch-directories.png)

5. Ha az előfizetéshez tartozó meglévő LUIS authoring-erőforrással rendelkezik, válassza ki a legördülő listából. Megtekintheti az ebben a szerzői erőforrásban létrehozott összes alkalmazást.
6. Ha nem, akkor kattintson az **új authoring-erőforrás létrehozása** elemre a modális alján.
7.  Új authoring-erőforrás létrehozásakor adja meg a következő információkat:

    > [!div class="mx-imgBorder"]
    > ![Új erőforrás létrehozása](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Bérlő neve** – az a bérlő, amelyhez az Azure-előfizetés társítva van. A bérlőket nem lehet átváltani a meglévő ablakból. A bérlőket úgy válthat, hogy bezárja ezt az ablakot, és kiválasztja az ikont a képernyő jobb felső sarkában, amely tartalmazza a monogramját. Válassza a felül **egy másik szerzői erőforrás kiválasztása** lehetőséget az ablak újbóli megnyitásához.
    * **Azure-erőforráscsoport neve** – az előfizetésében kiválasztott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez. Ha jelenleg nincs erőforráscsoport az előfizetésben, nem lehet létrehozni egyet a LUIS portálon. A bejelentkezési folyamat folytatásához lépjen a [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , és hozzon létre egyet a Luis-ben.
    * **Azure-Erőforrás neve** – az Ön által választott egyéni név, amelyet a szerzői műveletek URL-címének részeként használ a rendszer. Az erőforrás neve csak alfanumerikus karaktereket tartalmazhat, `-` és nem kezdődhet vagy végződhet `-` . Ha bármilyen más szimbólum szerepel a névben, az erőforrás létrehozása sikertelen lesz.
    * **Hely** – válassza ki, hogy a Luis által jelenleg támogatott [három szerzői hely](./luis-reference-regions.md) egyikében adja meg az alkalmazásait, beleértve a következőket: USA nyugati régiója, Nyugat-Európa és Kelet-Ausztrália
    * **Díjszabási** csomag – alapértelmezés szerint a F0 authoring díjszabási szintje van kiválasztva, mivel az ajánlott. Hozzon létre egy [ügyfél által felügyelt kulcsot](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) a Azure Portal, ha további biztonsági réteget keres.
8. Most sikeresen bejelentkezett a LUIS-ba. Most már megkezdheti az alkalmazások létrehozását.

## <a name="troubleshooting"></a>Hibaelhárítás

* Új erőforrás létrehozásakor győződjön meg arról, hogy az erőforrás neve csak alfanumerikus karaktereket, "-" karaktert tartalmaz, és nem kezdődhet vagy végződhet "-" karakterrel. Ellenkező esetben a művelet sikertelen lesz.
* Győződjön meg arról, hogy rendelkezik a [megfelelő engedélyekkel az előfizetéséhez egy Azure-erőforrás létrehozásához](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba az előfizetés rendszergazdájával, és adja meg a megfelelő engedélyeket.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [indíthat el új alkalmazást](luis-how-to-start-new-app.md)