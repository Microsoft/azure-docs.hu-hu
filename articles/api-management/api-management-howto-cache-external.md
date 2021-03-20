---
title: Külső gyorsítótár használata az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és használhat külső gyorsítótárat az Azure API Managementban. A külső gyorsítótár használata lehetővé teszi a beépített gyorsítótár bizonyos korlátainak leküzdését.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018222"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Külső Redis-kompatibilis gyorsítótár használata az Azure-ban API Management

A beépített gyorsítótár kihasználása mellett az Azure API Management lehetővé teszi a válaszok gyorsítótárazását egy külső Redis-kompatibilis gyorsítótárban, például az Azure cache for Redis.

A külső gyorsítótár használata lehetővé teszi a beépített gyorsítótár néhány korlátozásának megszüntetését:

* Kerülje a gyorsítótár rendszeres törlését API Management frissítések során
* Nagyobb mértékben szabályozhatja a gyorsítótár konfigurációját
* A API Management-csomaggal több adattal is gyorsítótárazhat, így
* A gyorsítótárazás használata a API Management fogyasztási szintjével
* Gyorsítótárazás engedélyezése a [API Management saját üzemeltetésű átjárókban](self-hosted-gateway-overview.md)

A gyorsítótárazással kapcsolatos részletes információk: [Az API Management gyorsítótárazási házirendjei](api-management-caching-policies.md) és [Egyedi gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

![Saját gyorsítótár használata a APIM](media/api-management-howto-cache-external/overview.png)

Ismertetett témák:

> [!div class="checklist"]
> * Külső gyorsítótár hozzáadása API Management

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ [Az Azure-API Management gyorsítótárazásának](api-management-howto-cache.md) megismerése

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"></a> Azure cache létrehozása a Redis

Ez a szakasz azt ismerteti, hogyan hozható létre Azure cache a Redis az Azure-ban. Ha már rendelkezik Azure-gyorsítótárral az Azure-on belüli vagy kívüli Redis, <a href="#add-external-cache">ugorjon</a> a következő szakaszra.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"></a> A Redis gyorsítótár üzembe helyezése a Kubernetes-ben

A gyorsítótárazáshoz a saját üzemeltetésű átjárók kizárólag külső gyorsítótárra támaszkodnak. Ahhoz, hogy a gyorsítótárazás hatékony legyen a saját üzemeltetésű átjárók számára, és a rájuk támaszkodó gyorsítótárnak egymás közelében kell lennie, a keresés és a tárolás késésének csökkentése érdekében. A Redis gyorsítótár üzembe helyezése ugyanahhoz a Kubernetes-fürthöz vagy egy különálló fürtben a legjobb választás. Ezt a [hivatkozást](https://github.com/kubernetes/examples/tree/master/guestbook) követve megtudhatja, hogyan helyezhet üzembe Redis gyorsítótárat egy Kubernetes-fürtön.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Külső gyorsítótár hozzáadása

Kövesse az alábbi lépéseket egy külső Azure-gyorsítótár hozzáadásához az Azure API Management Redis.

![Képernyőfelvétel: külső Azure cache hozzáadása az Azure API Management Redis.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **használat forrása** beállítással megadhat egy Azure-régiót vagy egy saját üzemeltetésű átjáró helyét, amely a beállított gyorsítótárat fogja használni. Az **alapértelmezettként** konfigurált gyorsítótárak felülbírálása egy adott egyező régióval vagy hellyel rendelkező gyorsítótárak esetében történik.
>
> Ha például API Management az USA keleti régiójában, a Délkelet-Ázsiában és a Nyugat-európai régióban található, és két gyorsítótár van konfigurálva, az egyik az **alapértelmezett** , a másik pedig a **Délkelet-ázsiai**, a **Délkelet-ázsiai** API Management a saját gyorsítótárát fogja használni, míg a másik két régió az **alapértelmezett** gyorsítótár-bejegyzést fogja használni.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Azure cache hozzáadása ugyanahhoz az előfizetéshez tartozó Redis

1. Tallózással keresse meg API Management-példányát a Azure Portal.
2. Válassza a **külső gyorsítótár** fület a bal oldali menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza ki a gyorsítótárat a **gyorsítótár-példány** legördülő mezőjében.
5. Válassza az **alapértelmezett** lehetőséget, vagy adja meg a kívánt régiót a **használat** a legördülő listából mezőben.
6. Kattintson a **Mentés** gombra.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Azure-gyorsítótár hozzáadása az aktuális Azure-előfizetésen vagy az Azure-on kívül üzemeltetett Redis

1. Tallózással keresse meg API Management-példányát a Azure Portal.
2. Válassza a **külső gyorsítótár** fület a bal oldali menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza az **Egyéni** lehetőséget a **gyorsítótár-példány** legördülő mezőjében.
5. Válassza az **alapértelmezett** lehetőséget, vagy adja meg a kívánt régiót a **használat** a legördülő listából mezőben.
6. Adja meg az Azure cache-t a Redis-kapcsolatok karakterláncához a **kapcsolatok karakterlánca** mezőben.
7. Kattintson a **Mentés** gombra.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Redis cache hozzáadása egy saját üzemeltetésű átjáróhoz

1. Tallózással keresse meg API Management-példányát a Azure Portal.
2. Válassza a **külső gyorsítótár** fület a bal oldali menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza az **Egyéni** lehetőséget a **gyorsítótár-példány** legördülő mezőjében.
5. A **használat** legördülő listából válassza ki a kívánt saját üzemeltetésű átjáró helyét vagy **alapértelmezett értékét** .
6. Adja meg a Redis cache-gyorsítótárbeli kapcsolatok karakterláncát a **kapcsolatok karakterlánca** mezőben.
7. Kattintson a **Mentés** gombra.

## <a name="use-the-external-cache"></a>Külső gyorsítótár használata

Ha a külső gyorsítótár konfigurálva van az Azure API Managementban, a gyorsítótárazási házirendek használatával is használható. A részletes lépésekért lásd: [gyorsítótár hozzáadása az Azure-API Management teljesítményének növeléséhez](api-management-howto-cache.md) .

## <a name="next-steps"></a><a name="next-steps"> </a>Következő lépések

* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
