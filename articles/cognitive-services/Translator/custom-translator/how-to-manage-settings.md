---
title: A beállítások kezelése -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Beállítások kezelése, munkaterület létrehozása, munkaterület megosztása és előfizetési kulcs kezelése egyéni fordítóban.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895781"
---
# <a name="how-to-manage-settings"></a>Beállítások kezelése

Az egyéni fordítói beállítások lapon megoszthatja a munkaterületet, módosíthatja a fordító előfizetési kulcsát, és törölhet munkaterületet.

A beállítások lap elérése:

1. Jelentkezzen be az [Egyéni Translator](https://portal.customtranslator.azure.ai/) portálra.
2. Az egyéni Translator Portalon kattintson a fogaskerék ikonra az oldalsávon.

    ![Hivatkozás beállítása](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Fordítói előfizetés társítása

A modellek betanításához vagy üzembe helyezéséhez a munkaterülethez társított Translator előfizetési kulccsal kell rendelkeznie.

Ha nem rendelkezik előfizetéssel, kövesse az alábbi lépéseket:

1. Előfizetés fordítói erőforrás létrehozásához. A Translator- [regisztrációra](../translator-how-to-signup.md) való feliratkozáshoz és a fordítói kulcs beszerzéséhez kövesse az alábbi lépéseket.
2. Jegyezze fel a fordítói előfizetés kulcsát. A Key1 vagy a Key2 egyike elfogadható.
3. Váltson vissza az egyéni Translator portálra.

## <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

1. Kattintson a "+ Munkaterület létrehozása" gombra a "Custom Translator" oldalsávban.

    ![Új munkaterület létrehozása](media/how-to/create-new-workspace.png)

2. A párbeszédpanelen adja meg az új munkaterület nevét.
3. Kattintson a "tovább" gombra.
4. Válassza az előfizetés típusa lehetőséget.
5. Válassza az előfizetési régió lehetőséget. A régiónak meg kell egyeznie a kiválasztott régióval, amikor a fordítói erőforrás kulcsát létrehozták.
6. Adja meg a fordítói előfizetés kulcsát, majd kattintson a Save (Mentés) gombra.

    ![Új munkaterület létrehozása párbeszédpanel](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Az egyéni fordító nem támogatja munkaterületek létrehozását Translator Text API erőforráshoz (más néven Az [engedélyezett VNET](../../../api-management/api-management-using-with-vnet.md)belül létrehozott Azure-előfizetési kulcs).

### <a name="modify-existing-key"></a>Meglévő kulcs módosítása

1. Navigáljon a munkaterület "beállítások" lapjára.
2. Kattintson a kulcs módosítása elemre.

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key.png)

3. A párbeszédpanelen adja meg a fordítói előfizetés kulcsát, majd kattintson a Save (Mentés) gombra.

    ![Előfizetés-kulcs hozzáadása párbeszédpanel](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Saját munkaterület kezelése

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához használható munkaterület. A munkaterületek több projektet, modellt és dokumentumot is tartalmazhatnak.

Ha a munka különböző részeit különböző személyekkel kell megosztani, akkor lehet, hogy több munkaterület létrehozása is hasznos lehet.

## <a name="share-your-workspace"></a>Munkaterület megosztása

Az egyéni fordítóban megoszthatja a munkaterületet másokkal, ha a munka különböző részeit különböző személyekkel kell megosztani.

1. Navigáljon a munkaterület "beállítások" lapjára.
2. Kattintson a "személyek hozzáadása" gombra a "megosztási beállítások" szakaszban.

    ![Munkaterület megosztása](media/how-to/share-workspace.png)

3. A párbeszédpanelen adja meg a munkaterület által megosztott e-mail-címek vesszővel tagolt listáját. Győződjön meg arról, hogy a személy által az egyéni Fordítóba való bejelentkezéshez használt e-mail-címet használja. Ezután válassza ki a megfelelő szintű megosztási engedélyt, és kattintson a Save (Mentés) gombra.

    ![Munkaterület megosztása párbeszédpanel](media/how-to/share-workspace-dialog.png)

4. Ha a munkaterület még a "saját munkaterület" nevű alapértelmezett névvel rendelkezik, a munkaterület megosztása előtt módosítania kell azt.
5. Kattintson a Save (Mentés) gombra.

## <a name="sharing-permissions"></a>Megosztási engedélyek

1. **Olvasó:** A munkaterület olvasói megtekinthetik a munkaterület összes adatát.

2. **Szerkesztő:** A munkaterületen lévő szerkesztők hozzáadhatnak dokumentumokat, betanítási modelleket és törölhetnek dokumentumokat és projekteket. Hozzáadhatnak egy előfizetési kulcsot, de nem módosíthatják, hogy a munkaterület hogyan legyen megosztva, törölje a munkaterületet, vagy módosítsa a munkaterület nevét.

3. **Tulajdonos:** A tulajdonos teljes hozzáféréssel rendelkezik a munkaterülethez.

## <a name="change-sharing-permission"></a>Megosztási engedély módosítása

Ha egy munkaterület meg van osztva, a "megosztási beállítások" szakasz megjeleníti az összes e-mail-címet, amelyet a munkaterület megosztva használ. Ha tulajdonosi hozzáféréssel rendelkezik a munkaterülethez, módosíthatja az egyes e-mail-címek meglévő megosztási engedélyeit.

1. Az egyes e-mailek "megosztási beállítások" szakaszában a legördülő menü megjeleníti az aktuális jogosultsági szintet.

2. Kattintson a legördülő menüre, és válassza ki az új jogosultsági szintet, amelyet hozzá szeretne rendelni ehhez az e-mail-címéhez.

    ![Megosztási engedély beállításai](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>A munkaterület rögzítése

Az első létrehozott munkaterület alapértelmezés szerint rögzített. Amikor bejelentkezik, a rögzített munkaterület megjelenik a hely betöltésekor. Ha több munkaterületet hozott létre, és azt szeretné, hogy az egyiket a bejelentkezéskor az alapértelmezett értékre kelljen tenni, akkor rögzítenie kell azt.

1. Az oldalsávon kattintson a rögzíteni kívánt munkaterület nevére.
2. Navigáljon a munkaterület "beállítások" lapjára.
3. Kattintson a rögzítés ikonra.

    ![Munkaterület rögzítése](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan hozhat létre munkaterületet és projekteket](workspace-and-project.md)