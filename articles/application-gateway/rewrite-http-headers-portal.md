---
title: HTTP-kérelem és válasz-fejlécek újraírása a portálon – Azure Application Gateway
description: Megtudhatja, hogyan konfigurálhat egy Azure-Application Gateway az Azure Portal használatával úgy, hogy újraírja a HTTP-fejléceket az átjárón áthaladó kérelmekben és válaszokban.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708934"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-kérelem és válasz-fejlécek újraírása az Azure Application Gateway-Azure Portal

Ez a cikk azt ismerteti, hogyan használható a Azure Portal egy [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) -példány konfigurálásához a HTTP-fejlécek újraírásához a kérelmekben és a válaszokban.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához rendelkeznie kell egy Application Gateway v2 SKU-példánnyal. Az Újraírási fejlécek nem támogatottak a v1 SKU-ban. Ha nem rendelkezik a v2 SKU-val, hozzon létre egy [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) -példányt a Kezdés előtt.

## <a name="create-required-objects"></a>Szükséges objektumok létrehozása

A HTTP-fejléc újraírásának konfigurálásához el kell végeznie ezeket a lépéseket.

1. Hozza létre a HTTP-fejléc újraírásához szükséges objektumokat:

   - **Újraírási művelet**: a kérelem és a kérelem fejlécének azon mezőinek megadására szolgál, amelyeket át szeretne írni, valamint a fejlécek új értékét. Egy vagy több Újraírási feltétel is társítható egy Újraírási művelettel.

   - **Újraírási feltétel**: opcionális konfiguráció. Az Újraírási feltételek kiértékelik a HTTP (S) kérelmek és válaszok tartalmát. Az újraírás művelet akkor fordul elő, ha a HTTP (S) kérelem vagy válasz megfelel az Újraírási feltételnek.

     Ha egynél több feltételt társít egy művelethez, a művelet csak akkor lép fel, ha az összes feltétel teljesül. Más szóval a művelet logikai és művelet.

   - **Újraírási szabály**: több Újraírási művelet/Újraírási feltétel kombinációt tartalmaz.

   - **Szabály sorrendje**: segít meghatározni, hogy az Újraírási szabályok hogyan legyenek végrehajtva. Ez a konfiguráció akkor hasznos, ha több Újraírási szabály található egy Újraírási készletben. Az alacsonyabb szabálykészlet-értékkel rendelkező Újraírási szabály először fut. Ha ugyanazokat a szabálygyűjtemény-értékeket rendeli hozzá két Újraírási szabályhoz, a végrehajtás sorrendje nem determinisztikus.

   - **Újraírási készlet**: több Újraírási szabályt tartalmaz, amelyek egy kérelem-útválasztási szabályhoz lesznek társítva.

2. Csatolja az Újraírási készletet egy útválasztási szabályhoz. Az Újraírási konfiguráció a forrás figyelőhöz van csatolva az útválasztási szabály segítségével. Alapszintű útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció egy forrás-figyelőhöz van társítva, és a globális fejléc újraírása. Elérésiút-alapú útválasztási szabály használata esetén a fejléc-Újraírási konfiguráció az URL-cím elérési útja alapján van definiálva. Ebben az esetben csak a hely adott elérési útja területére vonatkozik.

Több, a HTTP-fejléc Újraírási készletét is létrehozhatja, és az egyes újraírásokat több figyelőre alkalmazhatja. De csak egy Újraírási készletet alkalmazhat egy adott figyelőre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="configure-header-rewrite"></a>Fejléc újraírásának konfigurálása

Ebben a példában egy átirányítási URL-címet módosítunk a háttérbeli alkalmazás által küldött HTTP-válasz Location (hely) fejlécének átírásával.

1. Válassza az **összes erőforrás** lehetőséget, majd válassza ki az Application Gateway-t.

2. Válassza az **újraírások** lehetőséget a bal oldali ablaktáblán.

3. Válassza az **Újraírási készlet** elemet:

   ![Újraírási készlet hozzáadása](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Adja meg az Újraírási készlet nevét, és társítsa azt egy útválasztási szabállyal:

   - Adja meg az Újraírási készlet nevét a **név** mezőben.
   - Válasszon ki egyet vagy többet a **társított útválasztási szabályok** listában felsorolt szabályok közül. Csak olyan szabályokat választhat ki, amelyek nincsenek társítva más Újraírási készletekhez. A más Újraírási készletekhez már társított szabályok szürkén jelennek meg.
   - Kattintson a **Tovább** gombra.
   
     ![Név és társítás hozzáadása](media/rewrite-http-headers-portal/name-and-association.png)

5. Újraírási szabály létrehozása:

   - Válassza az **Újraírási szabály hozzáadása** lehetőséget.

     ![Újraírási szabály hozzáadása](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Adja meg az Újraírási szabály nevét az **Újraírási szabály neve** mezőben. Adjon meg egy számot a **szabály sorszáma** mezőben.

     ![Újraírási szabály nevének hozzáadása](media/rewrite-http-headers-portal/rule-name.png)

6. Ebben a példában a Location fejlécet csak akkor írjuk át, ha a azurewebsites.net mutató hivatkozást tartalmaz. Ehhez adjon hozzá egy feltételt annak kiértékeléséhez, hogy a válaszban a azurewebsites.net tartalmazza-e a következőt:

   - Válassza a **feltétel hozzáadása** lehetőséget, majd jelölje be az **IF** utasítást tartalmazó jelölőnégyzetet a kibontásához.

     ![Feltétel hozzáadása](media/rewrite-http-headers-portal/add-condition.png)

   - A megjelenő **változó típusa** mezőben válassza a **http-fejléc** lehetőséget.

   - A **fejléc típusa** listában válassza a **Válasz** lehetőséget.

   - Mivel ebben a példában kiértékeljük a Location fejlécet, amely egy közös fejléc, a **fejléc neve** alatt válassza a **Common header (közös fejléc** ) elemet.

   - A **Common header (közös fejléc** ) listában válassza a **hely** lehetőséget.

   - A **kis-és nagybetűk** megkülönböztetése területen válassza a **nem** lehetőséget.

   - Az **operátor** listában válassza az **EQUAL (=)** elemet.

   - Adjon meg egy reguláris kifejezési mintát. Ebben a példában a mintázatot fogjuk használni `(https?)://.*azurewebsites.net(.*)$` .

   - Válassza az **OK** lehetőséget.

     ![If feltétel konfigurálása](media/rewrite-http-headers-portal/condition.png)

7. Művelet hozzáadása a hely fejlécének újraírásához:

   - A **Művelettípus** listán válassza a **beállítás** lehetőséget.

   - A **fejléc típusa** listában válassza a **Válasz** lehetőséget.

   - A **fejléc neve** területen válassza a **Common header (közös fejléc**) lehetőséget.

   - A **Common header (közös fejléc** ) listában válassza a **hely** lehetőséget.

   - Adja meg a fejléc értékét. Ebben a példában `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékét fogjuk használni. Ez az érték a *azurewebsites.net* és a *contoso.com* értéket fogja lecserélni a Location fejlécben.

   - Válassza az **OK** lehetőséget.

     ![Művelet hozzáadása](media/rewrite-http-headers-portal/action.png)

8. Az Újraírási készlet létrehozásához válassza a **Létrehozás** elemet:

   ![A Létrehozás lehetőség kiválasztása](media/rewrite-http-headers-portal/create.png)

9. Ekkor megnyílik az Újraírási készlet nézet. Ellenőrizze, hogy a létrehozott Újraírási készlet szerepel-e az Újraírási készletek listáján:

   ![Set nézet újraírása](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Következő lépések

A gyakori használati esetek beállításával kapcsolatos további tudnivalókért tekintse meg a [gyakori fejléc-Újraírási forgatókönyvek](./rewrite-http-headers.md)című témakört.