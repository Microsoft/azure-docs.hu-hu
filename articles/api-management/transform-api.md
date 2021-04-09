---
title: Oktatóanyag – API átalakítása és biztosítása az Azure API Managementban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan védhető meg az API-k a API Management átalakítási és szabályozási (díjszabási) szabályzatokkal.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010231"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Oktatóanyag: az API átalakítása és biztosítása

Az oktatóanyag bemutatja, hogyan alakíthatja át az API-t, hogy az ne fedje fel az adatokat a privát háttérrendszer számára. Előfordulhat például, hogy el szeretné rejteni a háttéren futó technológiai verem információit. Előfordulhat, hogy az API HTTP-válasz törzsében megjelenő eredeti URL-címeket is el szeretné rejteni, hanem átirányítja őket a APIM-átjáróra.

Ez az oktatóanyag azt is bemutatja, hogy milyen egyszerű a háttérrendszer-API védelme, ha az Azure API Management-vel korlátozza a díjszabást. Előfordulhat például, hogy korlátozni szeretné az API-hívások sebességét, hogy az API-t ne használja a fejlesztők. További információ: [API Management szabályzatok](api-management-policies.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> -   Az API átalakítása a válaszfejlécek eltávolításához
> -   Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire
> -   API-k elleni védelem díjszabási szabályzat hozzáadásával (szabályozás)
> -   Az átalakítások tesztelése

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Szabályzatok a portálon":::

## <a name="prerequisites"></a>Előfeltételek

-   Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
-   Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
-   Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
-   Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Az API átalakítása a válaszfejlécek eltávolításához

Ez a szakasz bemutatja, hogyan rejtheti el azokat a HTTP-fejléceket, amelyeket nem kíván megjeleníteni a felhasználók számára. Ez a példa bemutatja, hogyan törölheti a következő fejléceket a HTTP-válaszban:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintése:

1. Az API Management Service-példányban válassza az **API**-k elemet.
1. Válassza ki a **bemutató konferencia API** -t az API-listából.
1. Válassza a **teszt** fület a képernyő tetején.
1. Válassza ki a **GetSpeakers** műveletet, és válassza a **Küldés** lehetőséget.

Az eredeti válasznak a következőhöz hasonlóan kell kinéznie:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Eredeti API-válasz":::

Amint láthatja, a válasz tartalmazza az **x-AspNet-Version** és az **x-powered-by** fejléceket.

### <a name="set-the-transformation-policy"></a>Az átalakítási szabályzat beállítása

1. Válassza a **bemutató konferencia API**-  >  **tervezés**  >  **minden művelet** lehetőséget.
4. A **kimenő feldolgozás** szakaszban válassza a Kódszerkesztő ( **</>** ) ikont.

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Navigáljon a kimenő szabályzathoz" border="false":::

1. Vigye a kurzort a **&lt; kimenő &gt;** elembe, és válassza a jobb felső sarokban található **kódrészletek megjelenítése** lehetőséget.
1. A jobb oldali ablak **átalakítási házirendek** területén válassza a **http-fejléc beállítása** kétszer (két szabályzat beszúrásához) lehetőséget.

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="HTTP-fejléc házirend beállítása":::

1. Módosítsa a **\<outbound>** kódot úgy, hogy az alábbihoz hasonló legyen:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Set HTTP header"::: (HTTP-fejléc beállítása)

1. Kattintson a **Mentés** gombra.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire

Ez a szakasz bemutatja, hogyan rejtheti el az API HTTP-válasz törzsében megjelenő eredeti URL-címeket, és átirányíthatja őket az APIM-átjáróra.

### <a name="test-the-original-response"></a>Az eredeti válasz tesztelése

Az eredeti válasz megtekintése:

1. Válassza a **bemutató konferencia API**-  >  **teszt** lehetőséget.
1. Válassza ki a **GetSpeakers** műveletet, és válassza a **Küldés** lehetőséget.

    Amint láthatja, a válasz tartalmazza az eredeti háttérbeli URL-címeket:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Válaszként szolgáló eredeti URL-címek":::


### <a name="set-the-transformation-policy"></a>Az átalakítási szabályzat beállítása

1.  Válassza a **bemutató konferencia API**  >  **minden művelet**  >  **kialakítás** elemet.
1.  A **kimenő feldolgozás** szakaszban válassza a Kódszerkesztő ( **</>** ) ikont.
1.  Vigye a kurzort a **&lt; kimenő &gt;** elembe, és válassza a jobb felső sarokban található **kódrészletek megjelenítése** lehetőséget.
1.  A jobb oldali ablak **átalakítási házirendek** területén válassza a **tartalom maszk URL-címek** elemet. 
1.  Kattintson a **Mentés** gombra.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>API-k védelme hívásszám-korlátozási szabályzat (szabályozás) hozzáadásával

Ez a szakasz bemutatja, hogyan lehet védelmet biztosítani a háttérbeli API-k számára a hívásszám korlátjának konfigurálásával. Előfordulhat például, hogy korlátozni szeretné az API-hívások sebességét, hogy az API-t ne használja a fejlesztők. Ebben a példában a korlátot a rendszer 15 másodpercenként 3 hívásra állítja az egyes előfizetés-AZONOSÍTÓk esetében. 15 másodperc elteltével a fejlesztő újrapróbálhatja az API hívását.

1.  Válassza a **bemutató konferencia API**  >  **minden művelet**  >  **kialakítás** elemet.
1.  A **bejövő feldolgozás** szakaszban válassza a Kódszerkesztő ( **</>** ) ikont.
1.  Vigye a kurzort a **&lt; bejövő &gt;** elembe, és válassza a jobb felső sarokban található **kódrészletek megjelenítése** lehetőséget.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Bejövő szabályzat beállítása" border="false":::

1.  A jobb oldali ablakban a **hozzáférés-korlátozási szabályzatok** területen válassza a **maximális hívási sebesség/kulcs** lehetőséget.
1.  Módosítsa a **ráta-limit-by-Key** kódot (a **\<inbound\>** elemben) a következő kódra:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Az átalakítások tesztelése

Ezen a ponton, ha megtekinti a kódot a kódszerkesztőban, a szabályzatok így néznek ki:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

A szakasz további részében teszteljük a szabályzatátalakításokat, amelyeket beállítottunk.

### <a name="test-the-stripped-response-headers"></a>Az eltávolított válaszfejlécek tesztelése

1. Válassza a **bemutató konferencia API**-  >  **teszt** lehetőséget.
1. Válassza ki a **GetSpeakers** műveletet, és válassza a **Küldés** lehetőséget.

    Amint láthatja, a fejlécek el lettek tiltva:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Megfosztott válaszok fejlécei":::

### <a name="test-the-replaced-url"></a>A lecserélt URL-cím tesztelése

1. Válassza a **bemutató konferencia API**-  >  **teszt** lehetőséget.
1. Válassza ki a **GetSpeakers** műveletet, és válassza a **Küldés** lehetőséget.

    Amint látható, az URL-cím le lett cserélve.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Lecserélt URL-cím":::

### <a name="test-the-rate-limit-throttling"></a>Hívásszám-korlát (szabályozás) tesztelése

1. Válassza a **bemutató konferencia API**-  >  **teszt** lehetőséget.
1. Válassza a **GetSpeakers** műveletet. Válassza a három alkalommal a **Küldés** lehetőséget egy sorban.

    A kérelem 3 alkalommal történő elküldése után a **429 túl sok kérést** kap.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Túl sok kérelem":::

1. Várjon 15 másodpercet, és válassza a **Küldés** újra lehetőséget. Ezúttal a **200 OK** választ kapja.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> -   Az API átalakítása a válaszfejlécek eltávolításához
> -   Az API-válasz szövegtörzsében szereplő eredeti URL-címek lecserélése az APIM-átjáró URL-címeire
> -   API-k védelme hívásszám-korlátozási szabályzat (szabályozás) hozzáadásával
> -   Az átalakítások tesztelése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az API monitorozása](api-management-howto-use-azure-monitor.md)
