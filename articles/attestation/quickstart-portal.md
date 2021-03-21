---
title: 'Gyors útmutató: az Azure-igazolás beállítása a Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja be és konfigurálhatja az igazolási szolgáltatót a Azure Portal használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729391"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Gyors útmutató: az Azure-igazolás beállítása a Azure Portal használatával

Ezt a rövid útmutatót követve megismerheti az Azure-igazolást. Megtudhatja, hogyan kezelheti az igazolási szolgáltatót, a házirend-aláírót és a szabályzatot a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="attestation-provider"></a>Igazolási szolgáltató

Ebben a szakaszban létre fog hozni egy igazolási szolgáltatót, és konfigurálhatja az aláíratlan szabályzatokkal vagy az aláírt szabályzatokkal. Azt is megtudhatja, hogyan tekintheti meg és törölheti az igazolási szolgáltatót.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>A szolgáltató létrehozása és konfigurálása aláíratlan házirendekkel

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **erőforrás létrehozása** lehetőséget.
1. A keresőmezőbe írja be az **igazolás** kifejezést.
1. Az eredmények listájában válassza a **Microsoft Azure igazolás** elemet.
1. Az **Microsoft Azure igazolás** lapon válassza a **Létrehozás** lehetőséget.
1. Az **igazoló szolgáltató létrehozása** lapon adja meg a következő bemeneti adatokat:

   - **Előfizetés**: Válassza ki az előfizetést.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
   - **Név**: adjon meg egy egyedi nevet.
   - **Hely**: válasszon egy helyet.
   - **Házirend-aláíró tanúsítvány fájlja**: ne töltse fel a házirend-aláíró tanúsítványokat tartalmazó fájlt a szolgáltató aláíratlan házirendekkel való konfigurálásához.

1. A szükséges bemenetek megadása után válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Ha érvényesítési problémák léptek fel, javítsa ki őket, majd válassza a **Létrehozás** lehetőséget.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>A szolgáltató létrehozása és konfigurálása aláírt szabályzatokkal

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **erőforrás létrehozása** lehetőséget.
1. A keresőmezőbe írja be az **igazolás** kifejezést.
1. Az eredmények listájában válassza a **Microsoft Azure igazolás** elemet.
1. Az **Microsoft Azure igazolás** lapon válassza a **Létrehozás** lehetőséget.
1. Az **igazoló szolgáltató létrehozása** lapon adja meg a következő információkat:

   - **Előfizetés**: Válassza ki az előfizetést.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
   - **Név**: adjon meg egy egyedi nevet.
   - **Hely**: válasszon egy helyet.
   - **Házirend-aláíró tanúsítvány fájlja**: töltse fel a házirend-aláíró tanúsítványok fájlját az igazolási szolgáltató aláírt házirendekkel való konfigurálásához. [Tekintse meg a házirend-aláíró tanúsítványok példáit](./policy-signer-examples.md).

1. A szükséges bemenetek megadása után válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Ha érvényesítési problémák léptek fel, javítsa ki őket, majd válassza a **Létrehozás** lehetőséget.

### <a name="view-the-attestation-provider"></a>Az igazoló szolgáltató megtekintése

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1. A szűrő mezőbe írja be az igazolási szolgáltató nevét, és jelölje ki.

### <a name="delete-the-attestation-provider"></a>Az igazolási szolgáltató törlése

Az igazolási szolgáltatót kétféleképpen lehet törölni. A következőket teheti:

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1. A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1. Jelölje be a jelölőnégyzetet, majd válassza a **Törlés** lehetőséget.
1. Adja meg az **Igen** értéket, és válassza a **Törlés** lehetőséget.

Vagy a következőket teheti:

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1. A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1. Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1. Válassza a menüsor **Törlés** elemét, és válassza az **Igen** lehetőséget.

## <a name="attestation-policy-signers"></a>Igazolási házirend aláírói

Az ebben a szakaszban ismertetett lépéseket követve megtekintheti, hozzáadhatja és törölheti a házirend-aláíró tanúsítványokat.

### <a name="view-the-policy-signer-certificates"></a>Házirend-aláíró tanúsítványok megtekintése

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1. A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1. Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1. Az ablak bal oldalán vagy az alsó ablaktáblán válassza ki a **házirend-aláíró tanúsítványokat** az erőforrás menüben. A hitelesítéshez a tanúsítvány kiválasztását kérő üzenet jelenik meg. A folytatáshoz válassza ki a megfelelő lehetőséget.
1. Válassza a **házirend-aláíró tanúsítványok letöltése** lehetőséget. A gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatóknál.
1. A letöltött szövegfájl minden tanúsítványt JWS-formátumban fog tartalmazni.
1. Ellenőrizze a tanúsítványok darabszámát és a letöltött tanúsítványokat.

### <a name="add-the-policy-signer-certificate"></a>A házirend-aláíró tanúsítvány hozzáadása

1.  Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1.  A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1.  Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1.  Az ablak bal oldalán vagy az alsó ablaktáblán válassza ki a **házirend-aláíró tanúsítványokat** az erőforrás menüben.
1.  A felső menüben válassza a **Hozzáadás** lehetőséget. A gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatóknál.
1.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és válassza a **Hozzáadás** lehetőséget. [Tekintse meg a házirend-aláíró tanúsítványok példáit](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Házirend-aláíró tanúsítványok törlése

1.  Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1.  A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1.  Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1.  Az ablak bal oldalán vagy az alsó ablaktáblán válassza ki a **házirend-aláíró tanúsítványokat** az erőforrás menüben.
1.  A felső menüben válassza a **Törlés** lehetőséget. A gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatóknál.
1.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és válassza a **Törlés** lehetőséget. [Tekintse meg a házirend-aláíró tanúsítványok példáit](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Igazolási szabályzat

Ez a szakasz azt ismerteti, hogyan lehet megtekinteni egy igazolási házirendet, és hogyan kell konfigurálni a házirend-aláírási követelményekkel és anélkül létrehozott házirendeket.

### <a name="view-an-attestation-policy"></a>Igazolási szabályzat megtekintése

1.  Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1.  A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1.  Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1.  Az ablak bal oldalán vagy az alsó ablaktáblán válassza a **házirend** lehetőséget az erőforrás menüben. A hitelesítéshez a tanúsítvány kiválasztását kérő üzenet jelenik meg. A folytatáshoz válassza ki a megfelelő lehetőséget.
1.  Válassza ki az előnyben részesített **igazolási típust** , és tekintse meg az **aktuális szabályzatot**.

### <a name="configure-an-attestation-policy"></a>Igazolási házirend konfigurálása

A szabályzatok JWT vagy szöveges formátumban való feltöltéséhez kövesse az alábbi lépéseket, ha a tanúsítvány-szolgáltató házirend-aláírási követelmény nélkül lett létrehozva.

1. Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1. A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1. Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1. Az ablak bal oldalán vagy az alsó ablaktáblán válassza a **házirend** lehetőséget az erőforrás menüben.
1. A felső menüben válassza a **Konfigurálás** lehetőséget.
1. **JWT** vagy **szövegként** válassza a **házirend formátumot** .

   Ha az igazolási szolgáltató házirend-aláírási követelmény nélkül lett létrehozva, a felhasználó **JWT** vagy **szöveges** formátumban is feltölthet egy szabályzatot.

      - Ha a JWT formátumot választotta, töltse fel a házirend-tartalmat az **aláíratlan/aláírt JWT** formátumban, majd válassza a **Mentés** lehetőséget. [Lásd: házirend-példák](./policy-examples.md).
      - Ha a szöveges formátumot választotta, töltse fel a fájl tartalmát **szöveges** formátumban, vagy adja meg a házirend tartalmát a szövegmezőben, majd válassza a **Mentés** lehetőséget. [Lásd: házirend-példák](./policy-examples.md).

   A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és nem szerkeszthető.

1. A beállított szabályzat megtekintéséhez válassza a felső menü **frissítés** elemét.


Ha az igazolási szolgáltató házirend-aláírási követelménysel lett létrehozva, kövesse az alábbi lépéseket a szabályzat JWT formátumban való feltöltéséhez.

1.  Lépjen a Azure Portal menüre vagy a kezdőlapra, és válassza az **összes erőforrás** lehetőséget.
1.  A szűrő mezőbe írja be az igazolási szolgáltató nevét.
1.  Válassza ki az igazolási szolgáltatót, és lépjen az Áttekintés oldalra.
1.  Az ablak bal oldalán vagy az alsó ablaktáblán válassza a **házirend** lehetőséget az erőforrás menüben.
1.  A felső menüben válassza a **Konfigurálás** lehetőséget.
1.  Töltse fel a házirend-fájlt **aláírt JWT formátumban** , majd válassza a **Mentés** lehetőséget. [Lásd: házirend-példák](./policy-examples.md).

    Ha az igazolási szolgáltató házirend-aláírási követelménysel lett létrehozva, a felhasználó csak **aláírt JWT formátumban** tölthet fel szabályzatot.

    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és nem szerkeszthető.
    
1.  A konfigurált szabályzat megtekintéséhez válassza a **frissítés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [SGX ENKLÁVÉHOZ enklávé igazolása kód-minták használatával](/samples/browse/?expanded=azure&terms=attestation)

