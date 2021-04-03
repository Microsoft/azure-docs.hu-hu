---
title: SOAP API importálása, majd konvertálása REST-té az Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan importálhat egy SOAP API-t, hogyan alakíthatja át a API Managementba, majd tesztelheti az API-t az Azure-ban és a fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a87f4d0748d14ab995f75279b6a192f350165d6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87843827"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>SOAP API importálása és konvertálása REST-té

A cikk bemutatja, hogyan importálhat SOAP API-t, és konvertálhatja REST-té. A cikk az APIM API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * SOAP API importálása és konvertálása REST-té
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttérbeli API importálása és közzététele

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Az **Új API hozzáadása** listában válassza a **WSDL** elemet.

    ![SOAP API](./media/restify-soap-api/wsdl-api.png)
3. A **WSDL-specifikáció** mezőben adja meg azt az URL-címet, ahol a SOAP API található.
4. Kattintson a **SOAP–REST** választógombra. Amint rákattint erre a lehetőségre, az APIM automatikusan megpróbál elvégezni egy XML–JSON átalakítást. Ebben az esetben a fogyasztónak restful API-ként érdemes hívnia az API-t, amely JSON formátumban adja vissza az eredményeket. Az APIM az összes kérést SOAP-hívássá konvertálja.

    ![SOAP–REST](./media/restify-soap-api/soap-to-rest.png)

5. Nyomja le a Tab billentyűt.

    A rendszer kitölti a következő mezőket a SOAP API-ból származó adatokkal: Megjelenített név, Név, Leírás.
6. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az APIM-példányban. Egyedinek kell lennie az APIM-példányon belül.
9. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „*Unlimited*” terméket használjuk.  Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
10. Válassza a **Létrehozás** lehetőséget.

## <a name="test-the-new-api-in-the-azure-portal"></a>Az új API tesztelése a Azure Portal

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.  

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy műveletet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 
1. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)