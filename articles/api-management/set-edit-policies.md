---
title: Azure API Management-házirendek beállítása vagy szerkesztése | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be vagy szerkesztheti az Azure API Management szabályzatait. Ezek a házirendek olyan XML-dokumentumok, amelyek a bejövő és kimenő utasítások sorát írják le.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078323"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management-szabályzatok beállítása és szerkesztése

A házirend-definíció egy XML-dokumentum, amely a bejövő és kimenő utasítások sorát ismerteti. Az XML szerkeszthető közvetlenül a definíciós ablakban. Kiválaszthat egy előre meghatározott szabályzatot is a házirend ablak jobb oldalán található listából. Az aktuális hatókörre vonatkozó utasítások engedélyezve és kiemelve. Az engedélyezett utasításokra kattintva hozzáadja a megfelelő XML-t a kurzor helyén a definíció nézetben. 

A szabályzatokkal kapcsolatos részletes információkért lásd: [szabályzatok az Azure API Managementban](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Házirend beállítása vagy szerkesztése

A szabályzatok beállításához vagy szerkesztéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Tallózzon az APIM-példányra.
3. Kattintson az **API-k** lapfülre.

    ![A szabályzat szerkesztése](./media/set-edit-policies/code-editor.png)

4. Válassza ki az egyik korábban importált API-t.
5. Válassza ki a **Tervezés** fület.
6. Válasszon ki egy műveletet, amelyre alkalmazni kívánja a házirendet. Ha az összes műveletre alkalmazni szeretné a szabályzatot, válassza a **minden művelet** lehetőséget.
7. Válassza a **</>** (Kódszerkesztő) ikont a **bejövő feldolgozás** vagy a **kimenő feldolgozás** szakaszban.
8. Illessze be a kívánt szabályzat kódját az egyik megfelelő blokkba.

    ```xml
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Hatókör konfigurálása

A szabályzatok globálisan konfigurálhatók, illetve egy termék, API vagy művelet hatókörén keresztül. A szabályzat konfigurálásának megkezdéséhez először ki kell választania azt a hatókört, amelyre a szabályzatot alkalmazni kívánja.

A házirend-hatókörök kiértékelése a következő sorrendben történik:

1. Globális hatókör
2. Termék hatóköre
3. API-hatókör
4. Művelet hatóköre

A szabályzatokon belüli utasítások kiértékelése az elem elhelyezése szerint történik `base` , ha van ilyen. A globális házirend nem rendelkezik szülő-házirenddel, és nem `<base>` befolyásolja az elem használatát.

Ha meg szeretné tekinteni az aktuális hatókör házirendjeit a házirend-szerkesztőben, kattintson a hatályos **házirend újraszámítása a kijelölt hatókörhöz** elemre.

### <a name="global-scope"></a>Globális hatókör

A globális hatókör a APIM-példányban található **összes API** -hoz konfigurálva van.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon a APIM-példányhoz.
2. Kattintson **a minden API** elemre.

    ![Globális hatókör](./media/api-management-howto-policies/global-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

    A módosításokat a rendszer azonnal propagálja az API Management átjáróra.

### <a name="product-scope"></a>Termék hatóköre

A termék hatóköre konfigurálva van a kiválasztott termékhez.

1. Kattintson a **termékek** elemre.

    ![Termék hatóköre](./media/api-management-howto-policies/product-scope.png)

2. Válassza ki azt a terméket, amelyre alkalmazni kívánja a házirendeket.
3. Kattintson a **házirendek** elemre.
4. Házirendek hozzáadása vagy szerkesztése.
5. Kattintson a **Mentés** gombra. 

### <a name="api-scope"></a>API-hatókör

Az API-hatókör a kiválasztott API **összes műveletére** konfigurálva van.

1. Válassza ki azt az **API** -t, amelyre alkalmazni kívánja a házirendeket.

    ![API-hatókör](./media/api-management-howto-policies/api-scope.png)

2. Válassza ki a **Minden művelet** lehetőséget.
3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

### <a name="operation-scope"></a>Művelet hatóköre 

A művelet hatóköre konfigurálva van a kiválasztott művelethez.

1. Válasszon ki egy **API**-t.
2. Válassza ki azt a műveletet, amelyre alkalmazni kívánja a házirendeket.

    ![Művelet hatóköre](./media/api-management-howto-policies/operation-scope.png)

3. Kattintson a háromszög ikonra.
4. Válassza a **Kódszerkesztő** lehetőséget.
5. Házirendek hozzáadása vagy szerkesztése.
6. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő kapcsolódó témaköröket:

+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)