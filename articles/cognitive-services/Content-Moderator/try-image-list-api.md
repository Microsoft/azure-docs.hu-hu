---
title: Közepes méretű képek egyéni listával és az API-konzollal – Content Moderator
titleSuffix: Azure Content Moderator
description: Az Azure Content Moderator listázási API-ját egyéni képlisták létrehozására használhatja.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85800148"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Mérsékelt és egyéni képlista az API-konzolon

Az Azure Content Moderator [listázási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) -ját egyéni képlisták létrehozására használhatja. Használja a lemezképek egyéni listáját a kép moderálási API-val. A képmoderálási művelet kiértékeli a képet. Ha egyéni listát hoz létre, a művelet összehasonlítja az egyéni listán lévő képekkel is. Az egyéni lista használatával blokkolhatja vagy engedélyezheti a rendszerképet.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

A listázási API-val a következő feladatokat végezheti el:

- Lista létrehozása.
- Képek hozzáadása egy listához.
- Képernyőképek a listában szereplő képekről.
- Képek törlése egy listáról.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

## <a name="use-the-api-console"></a>Az API-konzol használata
Mielőtt tesztelni tudja az API-t az online konzolon, szüksége lesz az előfizetési kulcsra. Ez a **Beállítások** lap **OCP-APIM-előfizetés-Key** mezőjében található. További információkért lásd az [Áttekintést](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Miután módosította a Képlista módosításait, frissítenie kell az indexét, hogy a módosítások szerepeljenek a későbbi vizsgálatokban. Ez a lépés hasonló ahhoz, ahogy a keresőmotor az asztalon (ha engedélyezve van), vagy egy webkeresőmotor folyamatosan frissíti az indexét új fájlok vagy lapok felvételéhez.

1. A [képlista kezelése API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)a bal oldali menüben válassza a **képlisták** lehetőséget, majd kattintson a **keresési index frissítése** elemre.

   Megnyílik a **képek listája – keresési index frissítése** lap.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 
 
    ![Képlista – keresési index frissítése oldal régiójának kiválasztása](images/test-drive-region.png)

    Megnyílik a **Képlisták – a keresési indexelés API-** konzoljának frissítése.

3. A **listId** mezőben adja meg a lista azonosítóját. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

   ![Képlista – a keresési index frissítése a konzol válaszának tartalma mező](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Rendszerkép-lista létrehozása

1. Lépjen a [képlista kezelése API-referenciához](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Megnyílik a **képek listája – létrehozás** lap. 

3. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét.

   ![Képlista – oldal régiójának létrehozása kijelölés](images/test-drive-region.png)

   Megnyílik a **képek listája – API létrehozása** konzol.
 
4. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

5. A **kérelem törzse** mezőben adja meg a **név** (például MyList) és a **Leírás** értékeit.

   ![Képlista – a konzol kérelem törzsének neve és leírása](images/try-terms-list-create-1.png)

6. A kulcs-érték párok helyőrzői segítségével további leíró metaadatokat rendelhet a listához.

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

   Adja hozzá a lista metaadatait kulcs-érték párokként, nem pedig a tényleges képeket.
 
7. Kattintson a **Küldés** gombra. A lista létrejött. Jegyezze fel az új listához társított **azonosító** értéket. Ehhez az AZONOSÍTÓhoz más képlista-felügyeleti függvények szükségesek.

   ![Képlisták – a konzol válaszának létrehozása mező megjeleníti a lista AZONOSÍTÓját](images/try-terms-list-create-2.png)
 
8. Ezután adja hozzá a képeket a MyList. A bal oldali menüben válassza a **rendszerkép** lehetőséget, majd válassza a **rendszerkép hozzáadása** elemet.

   Megnyílik a rendszerkép **hozzáadása** lap. 

9. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét.

   ![Rendszerkép – rendszerkép hozzáadása oldal régiójának kiválasztása](images/test-drive-region.png)

   Megnyílik a rendszerkép **– rendszerkép hozzáadása API-** konzol.
 
10. A **listId** mezőbe írja be a GENERÁLT lista azonosítóját, majd adja meg a hozzáadni kívánt rendszerkép URL-címét. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

11. Annak ellenőrzéséhez, hogy a rendszerkép hozzá lett-e adva a listához, a bal oldali menüben válassza a **rendszerkép** lehetőséget, majd válassza az **összes rendszerkép-azonosító lekérése** lehetőséget.

    Megnyílik a rendszerkép **– az összes Képazonosítós API-konzol beolvasása** .
  
12. A **listId** mezőben adja meg a lista azonosítóját, majd írja be az előfizetési kulcsot. Kattintson a **Küldés** gombra.

    ![Kép – az összes rendszerkép-azonosító konzol válaszának tartalma mező felsorolja a beírt képeket](images/try-image-list-create-11.png)
 
10. Adjon hozzá még néhány képet. Most, hogy létrehozta a lemezképek egyéni listáját, próbálja meg [kiértékelni a lemezképeket](try-image-api.md) az egyéni lemezképek listájának használatával. 

## <a name="delete-images-and-lists"></a>Képek és a menük törlése

Egy rendszerkép vagy lista törlése egyszerű. Az API használatával a következő feladatokat végezheti el:

- Rendszerkép törlése. (**Rendszerkép – törlés**)
- A lista törlése nélkül törölheti a listában szereplő összes lemezképet. (**Kép – az összes rendszerkép törlése**)
- Egy lista törlése annak teljes tartalmával együtt. (**Képlista – törlés**)

Ez a példa egyetlen rendszerképet töröl:

1. A [képlista kezelése API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)a bal oldali menüben válassza a **rendszerkép** lehetőséget, majd válassza a **Törlés** lehetőséget. 

   Megnyílik a **rendszerkép – törlés** lap.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

   ![Rendszerkép – az oldal régiójának kiválasztása](images/test-drive-region.png)
 
   Megnyílik a **rendszerkép-delete API-** konzol.
 
3. A **listId** mezőben adja meg annak a listának az azonosítóját, amelyből a képet törölni szeretné.  Ezt a számot adja vissza a **rendszerképben – a MyList összes rendszerkép-azonosítójának lekérése** konzol. Ezután adja meg a törölni kívánt rendszerkép **ImageId** . 

A példánkban a lista azonosítója **58953**, a **ContentSource** értéke. A rendszerkép azonosítója **59021**, a **ContentIds** értéke.

1. Adja meg az előfizetési kulcsot, majd válassza a **Küldés** lehetőséget.

1. Annak ellenőrzéséhez, hogy a rendszerkép törölve lett-e, használja a rendszerkép **– az összes rendszerkép-azonosító beolvasása** konzolt.
 
## <a name="change-list-information"></a>Lista adatainak módosítása

Szerkesztheti a lista nevét és leírását, és metaadatokat is hozzáadhat.

1. A [képlista kezelése API-referenciában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)a bal oldali menüben válassza a **képlisták** lehetőséget, majd válassza a **részletek frissítése** elemet. 

   Megnyílik a **képek listája – részletek frissítése** oldal.

2. Az **Open API Testing Console** esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét.  

    ![Képlista – részletek frissítése oldal régió kiválasztása](images/test-drive-region.png)

    Megnyílik a **képek listája – részletek frissítése API-** konzol.
 
3. A **listId** mezőben adja meg a lista azonosítóját, majd írja be az előfizetési kulcsot.

4. A **kérelem törzse** mezőbe írja be a módosításokat, majd kattintson a lapon a **Küldés** gombra.

   ![Képlista – a details konzolra vonatkozó kérelem törzsének módosítása](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Következő lépések

Az alkalmazással való integrációhoz használja a kódban szereplő REST API, vagy Kezdje a [képet a .net](image-lists-quickstart-dotnet.md) rövid útmutatóval.
