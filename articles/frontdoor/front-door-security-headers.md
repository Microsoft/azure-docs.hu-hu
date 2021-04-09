---
title: 'Oktatóanyag: biztonsági fejlécek hozzáadása a szabályok motorral – Azure bejárati ajtó'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat biztonsági fejléceket az Azure-beli előtérben található Rules Engine használatával
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1727193faa028a1d681f2a74df950afeb9570ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270049"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Oktatóanyag: biztonsági fejlécek hozzáadása a Rules Engine szolgáltatással

Ez az oktatóanyag bemutatja, hogyan valósítható meg a biztonsági fejlécek a böngészőalapú sebezhetőségek, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy vagy az X-frame-Options használatával. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.

Az alábbi példa bemutatja, hogyan adhat hozzá egy Content-Security-Policy fejlécet az összes olyan bejövő kérelemhez, amely megfelel a szabályok motor-konfigurációjának útvonalán megadott elérési útnak. Itt csak a megbízható webhelyről származó szkripteket engedélyezzük **https://apiphany.portal.azure-api.net** az alkalmazáson való futtatáshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Content-Security-Policy beállítása a szabályok motorján belül.

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információkért lásd a [Rövid útmutató: Front Door létrehozása](quickstart-create-front-door.md) szakaszt.
* Ha első alkalommal használja a Rules Engine szolgáltatást, tekintse [meg a szabályok létrehozása](front-door-tutorial-rules-engine.md)című témakört.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Tartalom-biztonsági házirend fejlécének hozzáadása a Azure Portal

1. Adjon meg új szabályt a **Hozzáadás** lehetőséggel. Adja meg a szabály nevét, majd kattintson a **műveleti**  >  **Válasz fejlécének** hozzáadása lehetőségre.

1. Állítsa be úgy a kezelőt **, hogy ezt** a fejlécet adja hozzá az útvonal összes bejövő kérelmének válaszként.

1. Adja hozzá a fejléc neve: **Content-Security-Policy** , és határozza meg, hogy a fejlécnek milyen értékeket kell elfogadnia. Ebben a forgatókönyvben a *"script-src" saját https://apiphany.portal.azure-api.net* lehetőséget választjuk.

1. Ha hozzáadta az összes szabályt, amelyet szeretne konfigurálni, ne felejtse el, hogy a kívánt útvonalra lépjen, és a szabályok motorjának beállításait az útválasztási szabályhoz rendeli. Ez a lépés a szabály működésének engedélyezéséhez szükséges. 

![portál minta](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Ebben a forgatókönyvben nem vettünk fel [egyezési feltételeket](front-door-rules-engine-match-conditions.md) a szabályhoz. Az útvonal-szabályban definiált elérési útra megegyező összes bejövő kérelem esetében ez a szabály lesz alkalmazva. Ha azt szeretné, hogy csak a kérelmek egy részhalmazára vonatkozzon, ügyeljen arra, hogy a szabályhoz az adott **egyeztetési feltételeket** adja hozzá.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben a biztonsági fejléceket a szabályok motorral konfigurálta. Ha már nem szeretné, hogy a szabály, távolítsa el a szabály törlése lehetőségre kattintva.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Szabály törlése":::

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan konfigurálhat egy webalkalmazási tűzfalat az előtérben, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazási tűzfal a Front Dooron](front-door-waf.md)
