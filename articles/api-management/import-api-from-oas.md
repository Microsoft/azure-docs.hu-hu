---
title: OpenAPI-specifikáció importálása az Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan importálhat egy OpenAPI-specifikációt API Management, majd tesztelheti az API-t az Azure-ban és a fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994696"
---
# <a name="import-an-openapi-specification"></a>OpenAPI-specifikáció importálása

Ez a cikk bemutatja, hogyan importálható egy „OpenAPI-specifikációjú” háttérrendszeri API, amely a https://conferenceapi.azurewebsites.net?format=json helyen található. Ezt a háttérrendszeri API-t a Microsoft biztosítja, és az Azure-ban üzemel. A cikk az APIM API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * „OpenAPI-specifikációjú” háttérrendszeri API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttérbeli API importálása és közzététele

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. Az **Új API hozzáadása** listáról válassza az **OpenAPI-specifikáció** lehetőséget.

    ![OpenAPI-specifikáció](./media/import-api-from-oas/oas-api.png)
3. Adja meg az API-beállításokat. Megadhatja az értékeket a létrehozás során, vagy később konfigurálhatja őket a **Beállítások** lapon. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
4. Válassza a **Létrehozás** lehetőséget.

> [!NOTE]
> Az API-importálási korlátozások [egy másik cikkben](api-management-api-import-restrictions.md)vannak dokumentálva.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
