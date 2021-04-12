---
title: 'Oktatóanyag: Cosmos DB hozzáférése a mongúz használatával az Azure statikus Web Apps'
description: Megtudhatja, hogyan férhet hozzá a Cosmos DBban lévő adatokhoz a mongúz használatával egy Azure statikus Web Apps API-függvényből.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260060"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Oktatóanyag: Cosmos DB hozzáférése a mongúz használatával az Azure statikus Web Apps

A [mongúz](https://mongoosejs.com/) a legnépszerűbb ODM-ügyfél a Node.js számára. Az adatstruktúra kialakításának és az ellenőrzés érvényesítésének lehetővé tétele érdekében a mongúz biztosítja az összes olyan eszközt, amely a mongúz API-t támogató adatbázisokkal való kommunikációhoz szükséges. A [Cosmos db](../cosmos-db/mongodb-introduction.md) támogatja a szükséges mongúz API-kat, és háttér-kiszolgálóként elérhető az Azure-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Cosmos DB kiszolgáló nélküli fiók létrehozása
> - Azure statikus Web Apps létrehozása
> - Alkalmazás beállításainak frissítése a kapcsolódási karakterlánc tárolásához

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes próbaverziós fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- [Azure-fiók](https://azure.microsoft.com/free/)
- [GitHub-fiók](https://github.com/join)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Cosmos DB kiszolgáló nélküli adatbázis létrehozása

Először hozzon létre egy [Cosmos db kiszolgáló](https://docs.microsoft.com/azure/cosmos-db/serverless) nélküli fiókot. Kiszolgáló nélküli fiók használata esetén csak a felhasznált erőforrásokért kell fizetnie, és nem kell teljes infrastruktúrát létrehoznia.

1. Navigáljon a [https://portal.azure.com](https://portal.azure.com)
2. Kattintson **az erőforrás létrehozása** elemre.
3. A keresőmezőbe írja be a **Azure Cosmos db**
4. Kattintson **Azure Cosmos db**
5. Kattintson a **Létrehozás** gombra
6. Konfigurálja Azure Cosmos DB-fiókját a következő adatokkal
    - Előfizetés: válassza ki a használni kívánt előfizetést
    - Erőforrás: kattintson az **új létrehozása** elemre, és állítsa a nevet **aswa-mongúz** értékre.
    - Fióknév: egyedi érték szükséges
    - API: **Azure Cosmos db a MONGODB API** -hoz
    - Jegyzetfüzetek (előzetes verzió): **kikapcsolva**
    - Hely: **USA 2. nyugati** régiója
    - Kapacitás mód: **kiszolgáló nélküli (előzetes verzió)**
    - Verzió: **3,6**
    - Availability Zones: az 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="új Cosmos db példány létrehozásának"::: letiltása
7. Kattintson a **felülvizsgálat + létrehozás** gombra.
8. Kattintson a **Létrehozás** gombra

A létrehozási folyamat eltarthat néhány percig. A későbbi lépések a kapcsolódási karakterlánc összegyűjtéséhez visszatérnek az adatbázisba.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

Ez az oktatóanyag egy GitHub-sablon tárházát használja, amely segítséget nyújt az alkalmazás létrehozásában.

1. Navigáljon az [alapszintű sablonhoz](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Válassza ki a **tulajdonost** (ha a fő fióktól eltérő szervezetet használ)
3. A tárház neve **aswa-mongúz-oktatóanyag**
4. Kattintson **a tárház létrehozása sablonból** elemre.
5. Visszatérés a [Azure Portal](https://portal.azure.com)
6. Kattintson **az erőforrás létrehozása** elemre.
7. Írja be a **statikus Web Apps** kifejezést a keresőmezőbe
8. **Statikus webalkalmazás kiválasztása (előzetes verzió)**
9. Kattintson a **Létrehozás** gombra
10. Konfigurálja az Azure-beli statikus webalkalmazást az alábbi adatokkal
    - Előfizetés: válassza ki ugyanazt az előfizetést, mint korábban
    - Erőforráscsoport: Select **aswa – mongúz**
    - Név: **aswa-mongúz-oktatóanyag**
    - Régió: **USA 2. nyugati** régiója
    - Kattintson a **Bejelentkezés a githubon** lehetőségre
    - Kattintson az **Engedélyezés** gombra, ha a rendszer arra kéri, hogy engedélyezze az Azure statikus Web Apps számára a GitHub-művelet létrehozását az üzembe helyezés engedélyezéséhez
    - Szervezet: a fiók neve
    - Adattár: **aswa-mongúz-oktatóanyag**
    - Ág: **Main**
    - Build-beállításkészletek: válassza az **Egyéni** lehetőséget
    - Alkalmazás helye: **/Public**
    - API-hely: **API**
    - Alkalmazás-összetevő helye: *hagyja üresen* az 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="Azure statikus Web Apps űrlapot":::
11. Kattintson **a felülvizsgálat és létrehozás** gombra.
12. Kattintson a **Létrehozás** gombra

A létrehozási folyamat néhány percet vesz igénybe, de az alkalmazás üzembe helyezése után kattintson az **erőforrás ugrás az erőforráshoz** lehetőségre.

## <a name="configure-database-connection-string"></a>Adatbázis-kapcsolatok karakterláncának konfigurálása

Ahhoz, hogy a webalkalmazás kommunikáljon az adatbázissal, az adatbázis-kapcsolati karakterláncot az alkalmazás-beállításként tárolja a rendszer. A beállítás értéke Node.js az objektum használatával érhető el `process.env` .

1. Kattintson a **Home (Kezdőlap** ) gombra a Azure Portal bal felső sarkában (vagy ugorjon vissza [https://portal.azure.com](https://portal.azure.com) )
2. Kattintson az **erőforráscsoportok** elemre.
3. Kattintson a **aswa-mongúz** elemre
4. Kattintson az adatbázis-fiókja nevére – **Azure Cosmos db fiókja** lesz.
5. A **Beállítások** területen kattintson a **kapcsolatok karakterlánca** elemre.
6. Másolja az **elsődleges kapcsolatok karakterlánca** alatt látható kapcsolatok karakterláncát
7. A navigációs menüben kattintson a **aswa-mongúz** elemre.
8. Kattintson a **aswa-mongúz-oktatóanyag** lehetőségre a webhely-példányhoz való visszatéréshez
9. A **Beállítások** területen kattintson a **konfiguráció** elemre.
10. Kattintson a **Hozzáadás** gombra, és hozzon létre egy új Alkalmazásbeállítás értéket a következő értékekkel
    - Név: **CONNECTION_STRING**
    - Érték: illessze be a korábban átmásolt kapcsolatok karakterláncát
11. Kattintson **az OK** gombra
12. Kattintson a **Mentés** gombra.

## <a name="navigate-to-your-site"></a>Navigáljon a webhelyre

Most már felfedezheti a statikus webalkalmazást.

1. Kattintson az **Áttekintés** gombra
1. Kattintson a jobb felső sarokban megjelenő URL-címre
    1. A következőhöz hasonlóan fog kinézni `https://calm-pond-05fcdb.azurestaticapps.net`
1. Új feladat létrehozásához írja be a címet, és kattintson a **feladat hozzáadása** lehetőségre.
1. Ellenőrizze, hogy a feladat megjelenik-e (eltarthat egy pillanatra)
1. A feladat megjelölése készként **a jelölőnégyzetre kattintva**
1. **Az oldal frissítése** , hogy megerősítse egy adatbázis használatát

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

1. Visszatérés a [Azure Portal](https://portal.azure.com)
2. Kattintson az **erőforráscsoportok** elemre.
3. Kattintson a **aswa-mongúz** elemre
4. Kattintson az **erőforráscsoport törlése** elemre.
5. Írja be a **aswa-mongúzt** a szövegmezőbe
6. Kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan konfigurálhatja a helyi fejlesztést...
> [!div class="nextstepaction"]
> [Helyi fejlesztés beállítása](./local-development.md)
 
