---
title: Az Age kapuzás engedélyezése a Azure Active Directory B2Cban | Microsoft Docs
description: Ismerje meg, hogyan azonosíthatók a kiskorúak az alkalmazás használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522419"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Az Age kapuzás engedélyezése a Azure Active Directory B2Cban

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) Age kapuzás lehetővé teszi, hogy azonosítsa azokat a kiskorúakat, akik a szülői beleegyezikés nélkül vagy anélkül szeretnék használni az alkalmazást. Dönthet úgy is, hogy letiltja a kiskorú bejelentkezését az alkalmazásba. Vagy engedélyezze a használatának a bejelentkezés befejezését, és adja meg az alkalmazásnak a másodlagos állapotot. 

>[!IMPORTANT]
>Ez a funkció nyilvános előzetes verzióban érhető el. Ne használja a szolgáltatást éles alkalmazásokhoz.
>

Ha a kapuzás engedélyezve van a felhasználói folyamatnál, a rendszer a felhasználókat a születési dátum és a lakóhely országa szerint kéri le. Ha egy felhasználó bejelentkezik, amely korábban nem adta meg az adatokat, akkor a következő bejelentkezéskor meg kell adnia. A szabályok minden alkalommal érvényesek, amikor egy felhasználó bejelentkezik.

![Képernyőkép az Age kapuzás-adatok gyűjtésének folyamatáról](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C a felhasználó által megadott adatok alapján azonosítja, hogy azok kisebbek-e. A rendszer ezután frissíti a **beszerzésimennyiség** mezőt a fiókjában. Az érték lehet:,,, `null` `Undefined` `Minor` `Adult` és `NotAdult` .  A rendszer ezután a **beszerzésimennyiség** és a **consentProvidedForMinor** mezőket használja a **legalAgeGroupClassification** értékének kiszámításához.


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>A bérlő beállítása az Age kapuzás

Ahhoz, hogy a kapuzás a felhasználói folyamatokban használhassa, úgy kell beállítania a bérlőt, hogy további tulajdonságokkal rendelkezzen.

1. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza a **címtár + előfizetés** szűrőt. Válassza ki a bérlőt tartalmazó könyvtárat.
1. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget, keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza ki a bérlő **tulajdonságait** a bal oldali menüben.
1. A **kor kapuzás** alatt válassza a **Konfigurálás** lehetőséget.
1. Várjon, amíg a művelet befejeződik, és a bérlő az Age kapuzás lesz beállítva.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Az életkor kapuzás engedélyezése a felhasználói folyamatokban

Miután a bérlő beállította az Age kapuzás használatát, ezt a funkciót használhatja a [felhasználói folyamatokban](user-flow-versions.md) , ahol engedélyezve van. Az Age kapuzás a következő lépésekkel engedélyezhető:

1. Hozzon létre egy olyan felhasználói folyamatot, amelynél engedélyezve van az Age kapuzás.
1. A felhasználói folyamat létrehozása után válassza a menü **Tulajdonságok** elemét.
1. Az **Age kapuzás** szakaszban válassza az **engedélyezve** lehetőséget.
1. A **regisztrációhoz vagy bejelentkezéshez** válassza ki, hogyan szeretné kezelni a felhasználókat:
    - Az alkalmazáshoz való hozzáférés engedélyezése a kiskorúak számára.
    - Az alkalmazáshoz való hozzáféréshez csak a beleegyező időtartam alatti kiskorúakat blokkolja.
    - Az alkalmazáshoz való hozzáférés letiltása az összes kiskorú számára.
1. A **on Block** esetében válassza az alábbi lehetőségek egyikét:
    - **JSON küldése az alkalmazásnak** – ez a beállítás visszaküldi a választ az alkalmazásnak, amelyet a rendszer letiltott.
    - **Hibaüzenet megjelenítése** – a felhasználó egy lap jelenik meg, amely tájékoztatja őket arról, hogy nem férhetnek hozzá az alkalmazáshoz.

## <a name="test-your-user-flow"></a>A felhasználói folyamat tesztelése

1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. Helyi vagy közösségi fiókkal történő bejelentkezés. Ezután válassza ki a tartózkodási országát és a születési dátumot, amely szimulálja a kis-és nagyvállalatokat. 
1. Ismételje meg a tesztet, és válassza ki a születési dátumot, amely szimulálja a felnőtteket.  

Ha kiskorúként jelentkezik be, a következő hibaüzenet jelenik meg: *sajnos a bejelentkezés le van tiltva. Az Ön országának adatvédelmi és online biztonsági törvényei megakadályozzák a gyermekekhez tartozó fiókokhoz való hozzáférést.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Az életkor kapuzás engedélyezése az egyéni házirendben

1. Tekintse meg a kapuzás szabályzatának példáját a [githubon](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .
1. Töltse fel a szabályzat fájljait.

::: zone-end

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [kezelheti Azure ad B2C felhasználói hozzáférését](manage-user-access.md).

