---
title: Mérsékelt szöveg egyéni kifejezési listával – Content Moderator
titleSuffix: Azure Cognitive Services
description: A List Management API használatával létrehozhat egyéni, a szöveges moderálási API-val használható feltételeket tartalmazó listát.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800040"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Mérsékelt az API-konzol egyéni feltételekkel rendelkező listája

Az Azure Content Moderator alapértelmezett globális kifejezéslistája a legtöbb tartalommoderálási igénynek megfelel. Azonban szükség lehet bizonyos szervezetspecifikus kifejezések szűrésére is. Például elképzelhető, hogy meg szeretné címkézni az üzleti vetélytársak neveit további ellenőrzés céljából. 

A [List Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) használatával létrehozhat egyéni, a szöveges MODERÁLÁSi API-val használható feltételeket tartalmazó listát. A szöveges **képernyős** művelet megkeresi a szöveget a káromkodáshoz, és összehasonlítja az egyéni és a megosztott címek listáit származó szöveget is.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

A következő feladatok elvégzéséhez használhatja a List Management API-t:
- Lista létrehozása.
- Kifejezések hozzáadása egy listához.
- Egy listán szereplő kifejezések szűrése.
- Kifejezések törlése egy listából.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

## <a name="use-the-api-console"></a>Az API-konzol használata

Mielőtt tesztelni tudja az API-t az online konzolon, szüksége lesz az előfizetési kulcsra. Ez a kulcs a **Beállítások** lap **OCP-APIM-előfizetés-Key** mezőjében található. További információkért lásd az [Áttekintést](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Miután módosította a feltételek listáját, frissítenie kell az indexét, hogy a módosítások szerepeljenek a későbbi vizsgálatokban. Ez a lépés hasonló ahhoz, ahogy a keresőmotor az asztalon (ha engedélyezve van), vagy egy webkeresőmotor folyamatosan frissíti az indexét új fájlok vagy lapok felvételéhez.

1. A [kifejezés-felügyeleti API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)a bal oldali menüben válassza ki a **kifejezések listáját**, majd kattintson a **keresési index frissítése** elemre. 

   Megnyílik a **kifejezések listája – keresési index frissítése** oldal.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Feltételek listája – keresési index frissítése oldal régiójának kiválasztása](images/test-drive-region.png)

   Megjelenik a **lists-refresh Search index API-** konzol.

3. A **listId** mezőben adja meg a lista azonosítóját. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

   ![A kifejezés felsorolja az API-t keresési index frissítése konzol válaszának tartalma mező](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Kifejezéslista létrehozása
1. Nyissa meg a [kifejezés lista kezelési API-referenciáját](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Megnyílik a **kifejezések listája – létrehozás** lap.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Lejárati listák – oldal régiójának létrehozása kijelölés](images/test-drive-region.png)

   Megnyílik a **lists-Create API-** konzol.
 
3. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

4. A **kérelem törzse** mezőben adja meg a **név** (például MyList) és a **Leírás** értékeit.

   ![Kifejezések listája – a konzol kérelem törzsének neve és leírása](images/try-terms-list-create-1.png)

5. A kulcs-érték párok helyőrzői segítségével további leíró metaadatokat rendelhet a listához.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Lista metaadatainak hozzáadása kulcs-érték párokként, és nem a tényleges kifejezések.
 
6. Kattintson a **Küldés** gombra. A lista létrejött. Jegyezze fel az új listához társított **azonosító** értéket. Erre az AZONOSÍTÓra szüksége lesz a más feltételek listázása felügyeleti funkcióihoz.

   ![Kifejezések listája – a konzol válaszának létrehozása mező megjeleníti a lista AZONOSÍTÓját](images/try-terms-list-create-2.png)
 
7. Adjon hozzá feltételeket a MyList. A bal oldali menüben a **kifejezés** területen válassza a **feltétel hozzáadása** lehetőséget. 

   Megnyílik a **feltételek hozzáadása** lap. 

8. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezés – feltétel hozzáadása oldal régiójának kiválasztása](images/test-drive-region.png)

   Megnyílik a kifejezés **– feltétel hozzáadása API-** konzol.
 
9. A **listId** mezőben adja meg a GENERÁLT lista azonosítóját, és válasszon egy értéket a **nyelvhez**. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

   ![Kifejezés – kifejezés hozzáadása konzol lekérdezési paramétereinek](images/try-terms-list-create-3.png)
 
10. Annak ellenőrzéséhez, hogy a kifejezés hozzá lett-e adva a listához, a bal oldali menüben válassza a **kifejezés** lehetőséget, majd kattintson az **összes feltétel beolvasása** lehetőségre. 

    Megnyílik a **kifejezés – az összes kifejezés beolvasása API-** konzol.

11. A **listId** mezőben adja meg a lista azonosítóját, majd írja be az előfizetési kulcsot. Kattintson a **Küldés** gombra.

12. A **válasz tartalma** mezőben ellenőrizze a beírt feltételeket.

    ![Kifejezés – minden kifejezés beolvasása a konzol válaszának tartalma mező felsorolja a beírt feltételeket](images/try-terms-list-create-4.png)
 
13. Vegyen fel néhány további kifejezést. Most, hogy létrehozta a feltételek egyéni listáját, próbáljon meg [néhány szöveget beszkennelni](try-text-api.md) az egyéni kifejezés lista használatával. 

## <a name="delete-terms-and-lists"></a>Kifejezések és listák törlése

Egy kifejezés vagy egy lista törlése nagyon egyszerű. Az API használatával a következő feladatokat hajthatja végre:

- Egy kifejezés törlése. (**Kifejezés – törlés**)
- Egy listán szereplő összes kifejezés törlése a lista törlése nélkül. (**Kifejezés – az összes feltétel törlése**)
- Egy lista törlése annak teljes tartalmával együtt. (**Kifejezések listája – törlés**)

Ez a példa egyetlen kifejezést töröl.

1. A [lejárati lista kezelése API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)a bal oldali menüben válassza a **kifejezés**, majd a **Törlés** lehetőséget. 

   Megnyílik a **törlési feltétel** .

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Kifejezés – az oldal régiója kijelölésének törlése](images/test-drive-region.png)

   Megnyílik az API **-törlési** konzol.
  
3. A **listId** mezőben adja meg annak a listának az azonosítóját, amelyből törölni kívánja a kifejezést. Ez az azonosító a következő szám (a példában, **122**), amelyet a rendszer a MyList **lekérése – részletek** konzoljában ad vissza. Adja meg a kifejezést, és válassza ki a kívánt nyelvet.
 
   ![Kifejezés – a konzol lekérdezési paramétereinek törlése](images/try-terms-list-delete-1.png)

4. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

5. Annak ellenőrzéséhez, hogy a kifejezés törölve lett-e, használja a következő **kifejezést: lists-Get all** Console.

   ![Kifejezések listája – az összes konzol válaszának beolvasása mező mutatja, hogy a rendszer törli a kifejezést](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Lista adatainak módosítása

Szerkesztheti a lista nevét és leírását, és metaadatokat is hozzáadhat.

1. A [lejárati lista kezelése API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)a bal oldali menüben válassza a **kifejezés listák** lehetőséget, majd válassza a **részletek frissítése** elemet. 

   Megnyílik a **feltételek listája – részletek frissítése** oldal.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Lejárati listák – részletek frissítése oldal régió kiválasztása](images/test-drive-region.png)

   Megnyílik a **Details (részletek frissítése** ) API-konzol.

3. A **listId** mezőben adja meg a lista azonosítóját, majd írja be az előfizetési kulcsot.

4. A **kérelem törzse** mezőbe írja be a módosításokat, majd válassza a **Küldés** lehetőséget.

   ![Lejárati listák – részletek frissítése konzol kérelem törzsének szerkesztése](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Következő lépések

Az alkalmazással való integrációhoz használja a kódban szereplő REST API, vagy Kezdje a [kifejezést a .net](term-lists-quickstart-dotnet.md) gyors útmutatóval.
