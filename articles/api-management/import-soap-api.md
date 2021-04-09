---
title: SOAP API importálása az Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan importálhat egy SOAP API szabványos XML-ábrázolását, majd tesztelheti az API-t az Azure-ban és a fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011064"
---
# <a name="import-soap-api"></a>SOAP API importálása

A cikk bemutatja, hogyan importálhatja egy SOAP API szabványos XML-ábrázolásását. A cikk azt is bemutatja, hogyan tesztelheti a API Management API-t.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * SOAP API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttérbeli API importálása és közzététele

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. Az **Új API hozzáadása** listában válassza a **WSDL** elemet.

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. A **WSDL-specifikáció** mezőben adja meg azt az URL-címet, ahol a SOAP API található.
4. Alapértelmezés szerint a **SOAP típusú továbbítás** választógomb van kijelölve. Így az API SOAP-ként lesz elérhető. A fogyasztónak a SOAP-szabályokat kell használnia. Ha REST API-t szeretne használni, kövesse a [SOAP API importálását és REST-konvertálását](restify-soap-api.md) ismertető szakasz lépéseit.

    ![Képernyőfelvétel: a létrehozás a W S D L-ből párbeszédpanel, ahol megadható a W S D L specifikáció.](./media/import-soap-api/pass-through.png)
5. Nyomja le a Tab billentyűt.

    A rendszer kitölti a következő mezőket a SOAP API-ból származó adatokkal: Megjelenített név, Név, Leírás.
6. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az API Management-példányban. Ebben az API Management-példányban egyedinek kell lennie.
7. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „*Unlimited*” terméket használjuk.  Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha létrehozta a API Management példányt, akkor Ön már rendszergazda, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
8. Adja meg az egyéb API-beállításokat. Megadhatja az értékeket a létrehozás során, vagy később konfigurálhatja őket a **Beállítások** lapon. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
9. Válassza a **Létrehozás** lehetőséget.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Az új API tesztelése a felügyeleti portálon

A műveleteket meg lehet hívni közvetlenül a felügyeleti portálról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.  

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy műveletet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 
1. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)