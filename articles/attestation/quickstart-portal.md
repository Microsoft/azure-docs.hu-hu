---
title: Azure-igazolás beállítása Azure Portal
description: Igazolási szolgáltató beállítása és konfigurálása Azure Portal használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429157"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Gyors útmutató: Azure-igazolás beállítása Azure Portal

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az alábbi lépések végrehajtásával felügyelheti az igazolási szolgáltatót Azure Portal használatával.

## <a name="1-attestation-provider"></a>1. igazolási szolgáltató

### <a name="11-create-an-attestation-provider"></a>1,1 igazolási szolgáltató létrehozása

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 a szolgáltató aláírás nélküli házirendekkel való konfigurálásához

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza az **erőforrás létrehozása** lehetőséget.
2.  A keresőmezőbe írja be az **igazolás** kifejezést.
3.  Az eredmények listából válassza a **Microsoft Azure igazolás** elemet.
4.  Az Microsoft Azure igazolás lapon válassza a **Létrehozás** lehetőséget.
5.  Az igazoló szolgáltató létrehozása lapon adja meg a következő bemeneti adatokat:
    
    **Előfizetés**: válasszon egy előfizetést
    
    **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét
    
    **Név**: egyedi név megadása kötelező

    **Hely**: válasszon egy helyet 
    
    **Házirend-aláíró tanúsítvány fájlja**: ne töltse fel a házirend-aláíró tanúsítványok fájlját a szolgáltató aláíratlan házirendekkel való konfigurálásához 
6.  A szükséges bemenetek megadása után kattintson a **felülvizsgálat + létrehozás** gombra.
7.  Javítsa ki az érvényesítési problémákat, és kattintson a **Létrehozás** gombra.

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 a szolgáltató aláírt házirendekkel való konfigurálásához

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza az **erőforrás létrehozása** lehetőséget.
2.  A keresőmezőbe írja be az **igazolás** kifejezést.
3.  Az eredmények listából válassza a **Microsoft Azure igazolás** elemet.
4.  Az Microsoft Azure igazolás lapon válassza a **Létrehozás** lehetőséget.
5.  Az igazoló szolgáltató létrehozása lapon adja meg a következő információkat:
    
    a. **Előfizetés**: válasszon egy előfizetést
    
    b. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét
    
    c. **Név**: egyedi név megadása kötelező

    d. **Hely**: válasszon egy helyet 
    
    e. **Házirend-aláíró tanúsítvány fájlja**: az igazolási szolgáltató házirend-aláírási tanúsítványokkal való konfigurálásához, a tanúsítványok fájljának feltöltéséhez. Tekintse meg [a példákat](./policy-signer-examples.md) 
6.  A szükséges bemenetek megadása után kattintson a **felülvizsgálat + létrehozás** gombra.
7.  Javítsa ki az érvényesítési problémákat, és kattintson a **Létrehozás** gombra.

### <a name="12-view-attestation-provider"></a>1,2 tanúsító szolgáltató megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét, és jelölje ki

### <a name="13-delete-attestation-provider"></a>1,3 tanúsítvány-ellenőrző szolgáltató törlése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Jelölje be a jelölőnégyzetet, majd kattintson a **Törlés** gombra.
4.  Írja be az Igen értéket, és kattintson a **Törlés** [vagy] gombra.
1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  A felső menüben kattintson a **Törlés** elemre, majd kattintson az **Igen** gombra.


## <a name="2-attestation-policy-signers"></a>2. igazolási házirend aláírói

### <a name="21-view-policy-signer-certificates"></a>2,1 házirend-aláíró tanúsítványok megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  Kattintson a házirend-aláíró **tanúsítványok letöltése** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatók esetében)
6.  A letöltött szövegfájl minden tanúsítványt JWS formátumban fog tartalmazni.
a.  Ellenőrizze a letöltött tanúsítványok darabszámát és tanúsítványait.

### <a name="22-add-policy-signer-certificate"></a>2,2 házirend-aláíró tanúsítvány hozzáadása

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Hozzáadás** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatóknál)
6.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és kattintson a **Hozzáadás** gombra. Tekintse meg [a példákat](./policy-signer-examples.md)

### <a name="23-delete-policy-signer-certificate"></a>2,3 házirend-aláíró tanúsítvány törlése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend-aláíró tanúsítványok** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Törlés** elemre (a gomb le lesz tiltva a házirend-aláírási követelmény nélkül létrehozott igazolási szolgáltatók esetében)
6.  Töltse fel a házirend-aláíró tanúsítvány fájlját, és kattintson a **Törlés** gombra. Tekintse meg [a példákat](./policy-signer-examples.md) 

## <a name="3-attestation-policy"></a>3. igazolási szabályzat

### <a name="31-view-attestation-policy"></a>3,1 igazolási szabályzat megtekintése

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  Válassza ki az előnyben részesített **igazolási típust** , és tekintse meg az **aktuális szabályzatot**

### <a name="32-configure-attestation-policy"></a>3,2 igazolási házirend konfigurálása

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 Ha a tanúsítvány-szolgáltató házirend-aláírási követelmény nélkül jön létre

##### <a name="upload-policy-in-jwt-format"></a>Szabályzat feltöltése JWT formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha a tanúsító szolgáltató házirend-aláírási követelmény nélkül jön létre, a felhasználó feltöltheti a szabályzatot **JWT** vagy **szöveges** formátumban
7.  **Házirend formátumának** kiválasztása **JWT**
8.  Az **aláíratlan/aláírt JWT** formátumban töltse fel a házirend tartalmát tartalmazó fájlt, majd kattintson a **Save (Mentés**) gombra. Tekintse meg [a példákat](./policy-examples.md)
    
    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.

7.  A beállított szabályzat megtekintéséhez kattintson a felső menüben a **frissítés** gombra.

##### <a name="upload-policy-in-text-format"></a>Szabályzat feltöltése szöveges formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha a tanúsító szolgáltató házirend-aláírási követelmény nélkül jön létre, a felhasználó feltöltheti a szabályzatot **JWT** vagy **szöveges** formátumban
7.  **Házirend formátumának** kiválasztása **szövegként**
8.  **Szöveges** formátumban töltse fel a fájl tartalmát, vagy írja be a házirend tartalmát a szövegmezőbe, majd kattintson a **Mentés** gombra. Tekintse meg [a példákat](./policy-examples.md)

    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.

8.  Kattintson a **frissítés** gombra a beállított szabályzat megtekintéséhez.

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 az igazolási szolgáltató házirend-aláírási követelménysel való létrehozásakor

##### <a name="upload-policy-in-jwt-format"></a>Szabályzat feltöltése JWT formátumban

1.  A Azure Portal menüben vagy a Kezdőlap lapon válassza a **minden erőforrás** elemet.
2.  A szűrő mezőbe írja be az igazoló szolgáltató nevét.
3.  Válassza ki az igazolási szolgáltatót, és navigáljon az Áttekintés lapra
4.  Kattintson a **házirend** elemre a bal oldali erőforrás menüben vagy az alsó ablaktáblán
5.  A felső menüben kattintson a **Konfigurálás** elemre.
6.  Ha az igazolási szolgáltató házirend-aláírási követelménysel jön létre, a felhasználó csak **aláírt JWT formátumban** tölthet fel szabályzatot
7.  A feltöltési házirend fájljának **aláírása JWT** , majd kattintson a **Save (Mentés**) gombra. Tekintse meg [a példákat](./policy-examples.md)

    A fájlfeltöltés beállításnál a házirend-előnézet szöveges formátumban jelenik meg, és a házirend-előnézet nem szerkeszthető.
    
8.  Kattintson a **frissítés** gombra a beállított szabályzat megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [SGX ENKLÁVÉHOZ enklávé igazolása kód-minták használatával](/samples/browse/?expanded=azure&terms=attestation)

