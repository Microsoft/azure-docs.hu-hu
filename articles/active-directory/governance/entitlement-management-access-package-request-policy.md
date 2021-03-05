---
title: Hozzáférési csomagra vonatkozó kérelmek beállításainak módosítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja a hozzáférési csomagokra vonatkozó kérelmek beállításait a Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4a1aa737b1f671c08d51d780fa98db50f2869a1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211651"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomagra vonatkozó kérelmek beállításainak módosítása az Azure AD-jogosultságok kezelésében

Hozzáférés-csomagkezelőként módosíthatja azokat a felhasználókat, akik bármikor igényelhetnek hozzáférési csomagot a szabályzat szerkesztésével vagy új szabályzat hozzáadásával. Ez a cikk azt ismerteti, hogyan lehet módosítani egy meglévő hozzáférési csomag kérésének beállításait.

## <a name="choose-between-one-or-multiple-policies"></a>Válasszon egy vagy több házirend közül

Azt határozza meg, hogy ki kérheti a hozzáférési csomag igénylését egy szabályzattal. Mielőtt új házirendet hozna létre, vagy meglévő házirendet szeretne szerkeszteni egy hozzáférési csomagban, meg kell határoznia, hogy hány házirendre van szükség a hozzáférési csomaghoz. 

Hozzáférési csomag létrehozásakor meg kell adnia azt a kérési beállítást, amely létrehoz egy házirendet. A legtöbb hozzáférési csomag egyetlen házirenddel fog rendelkezni, de egyetlen hozzáférési csomag több házirenddel is rendelkezhet. Hozzon létre több szabályzatot egy hozzáférési csomaghoz, ha engedélyezni szeretné, hogy a különböző felhasználók különböző kérelmeket és jóváhagyási beállításokat kapjanak a hozzárendelésekhez. 

Például egyetlen házirend nem használható belső és külső felhasználók ugyanahhoz a hozzáférési csomaghoz való hozzárendeléséhez. Azonban két házirendet is létrehozhat ugyanabban a hozzáférési csomagban, egyet a belső felhasználók számára, egyet pedig a külső felhasználók számára. Ha több házirend is érvényes a felhasználóra, a rendszer a kérésük időpontjában kérni fogja, hogy válassza ki azt a szabályzatot, amelyet hozzá szeretne rendelni. Az alábbi ábrán két házirendet tartalmazó hozzáférési csomag látható.

![Több házirend egy hozzáférési csomagban](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Hány házirendre van szükségem?

| Eset | Szabályzatok száma |
| --- | --- |
| Szeretném, hogy a címtárban lévő összes felhasználó ugyanazzal a kéréssel és jóváhagyási beállításokkal rendelkezzen a hozzáférési csomaghoz | Eggyel |
| Azt szeretném, hogy az egyes csatlakoztatott szervezetek összes felhasználója hozzáférhessen egy hozzáférési csomag igényléséhez | Eggyel |
| Szeretném engedélyezni a címtárban lévő felhasználókat és a címtáron kívüli felhasználókat a hozzáférési csomag igényléséhez | Többszörös |
| Különböző jóváhagyási beállításokat szeretnék megadni egyes felhasználók számára | Többszörös |
| Azt szeretném, hogy egyes felhasználók a csomagok hozzárendelései lejárnak, míg más felhasználók is kiterjeszthetik a hozzáférésüket | Többszörös |

További információ a több házirend alkalmazása esetén használt prioritási logikáról: [több szabályzat](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Nyisson meg egy meglévő hozzáférési csomagot, és adjon hozzá egy új szabályzatot a kérelmek beállításaihoz

Ha olyan felhasználói készlettel rendelkezik, amelyeknek különböző kérés-és jóváhagyási beállításokkal kell rendelkezniük, valószínűleg létre kell hoznia egy új szabályzatot. Kövesse az alábbi lépéseket egy új szabályzat meglévő hozzáférési csomaghoz való hozzáadásának megkezdéséhez:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** , majd a **házirend hozzáadása** elemre.

1. Ekkor elindul az **alapok** lapon. Adja meg a szabályzat nevét és leírását.

    ![Szabályzat létrehozása névvel és leírással](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. A **tovább** gombra kattintva nyissa meg a **kérelmek** lapot.

1. Módosítsa azokat a **felhasználókat, akik hozzáférési beállításokat igényelhetnek** . A következő részekben ismertetett lépésekkel módosíthatja a beállítást az alábbi lehetőségek egyikére: 
    - [A címtárban lévő felhasználók számára](#for-users-in-your-directory) 
    - [A címtárban nem szereplő felhasználók számára](#for-users-not-in-your-directory)
    - [Nincs (csak a rendszergazdai közvetlen hozzárendelések)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárban lévő felhasználók számára a hozzáférési csomag igénylését. A kérelem szabályzatának meghatározásakor megadhat egyéni felhasználókat vagy gyakrabban felhasználói csoportokat. Előfordulhat például, hogy a szervezet már rendelkezik egy csoporttal, például az **összes alkalmazottal**.  Ha ez a csoport hozzá van adva a szabályzatban azon felhasználók számára, akik hozzáférést igényelhetnek, akkor a csoport bármelyik tagja hozzáférhet a hozzáféréshez.

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban lévő felhasználók** elemre.

    Ha ezt a beállítást választja, az új beállítások úgy jelennek meg, hogy tovább pontosítsa, hogy kik a címtárban is igényelhetik ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – kérelmek – a címtárban lévő felhasználók számára](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Válasszon az alábbi lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott felhasználók és csoportok** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy csak a címtárban lévő felhasználók és csoportok férhessenek hozzá a hozzáférési csomaghoz. |
    | **Minden tag (a vendégek kivételével)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtárban lévő összes tag felhasználó kérje ezt a hozzáférési csomagot. Ez a lehetőség nem tartalmazza a címtárban esetleg meghívott vendég felhasználókat. |
    | **Minden felhasználó (beleértve a vendégeket is)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtár összes tagja és vendég felhasználója hozzáférhessen a hozzáférési csomaghoz. |

    A vendég felhasználók a címtárban az [Azure ad B2B](../external-identities/what-is-b2b.md)használatával meghívott külső felhasználókra vonatkoznak. A felhasználók és a vendég felhasználók közötti különbségekről a [Mi az alapértelmezett felhasználói engedélyek a Azure Active Directoryban?](../fundamentals/users-default-permissions.md)című témakörben talál további információt.

1. Ha **meghatározott felhasználókat és csoportokat adott** meg, kattintson a **felhasználók és csoportok hozzáadása** lehetőségre.

1. A felhasználók és csoportok kiválasztása panelen válassza ki a hozzáadni kívánt felhasználókat és csoportokat.

    ![Hozzáférési csomag – kérelmek – felhasználók és csoportok kiválasztása](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. A felhasználók és csoportok hozzáadásához kattintson a **kiválasztás** gombra.

1. Ha jóvá szeretné hagyni a jóváhagyást, a jóváhagyási beállítások konfigurálásához kövesse az [Azure ad jogosultságok kezelése hozzáférési csomag jóváhagyási beállításainak módosítása](entitlement-management-access-package-approval-policy.md) című témakör lépéseit.

1. Lépjen a [kérelmek engedélyezése](#enable-requests) szakaszra.
 
## <a name="for-users-not-in-your-directory"></a>A címtárban nem szereplő felhasználók számára

 A **címtárban lévő felhasználók** egy másik Azure ad-címtárban vagy-tartományban lévő felhasználókra hivatkoznak. Előfordulhat, hogy ezek a felhasználók még nem voltak meghívva a címtárba. Az Azure AD-címtárakat úgy kell konfigurálni, hogy az **együttműködési korlátozásokban** engedélyezze a meghívásokat. További információkért tekintse meg a [külső B2B-együttműködés engedélyezése és a vendégek meghívására alkalmas személyek kezelése](../external-identities/delegate-invitations.md)című témakört.

> [!NOTE]
> Egy vendég felhasználói fiók jön létre egy olyan felhasználó számára, aki még nem szerepel a címtárban, amelynek a kérelmét jóváhagyták vagy automatikusan jóváhagyták. A vendég meghívásra kerül, de nem kap meghívót e-mailben. Ehelyett e-mailt kapnak a hozzáférési csomag hozzárendelésének megérkezése után. Alapértelmezés szerint később, amikor a vendég felhasználó már nem rendelkezik hozzáférési csomagbeli hozzárendelésekkel, mert az utolsó hozzárendelésük lejárt vagy meg lett szakítva, a vendég felhasználói fiók le lesz tiltva a bejelentkezésből, és azt követően törlődik. Ha azt szeretné, hogy a vendég felhasználók határozatlan ideig maradjanak a címtárban, akkor is, ha nem rendelkeznek hozzáférési csomag hozzárendeléseivel, módosíthatja a jogosultsági felügyeleti konfiguráció beállításait. További információ a vendég felhasználói objektumról: [Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../external-identities/user-properties.md).

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárában lévő felhasználók számára a következő hozzáférési csomag kérését:

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban nem szereplő felhasználók** elemre.

    Ha ezt a beállítást választja, az új beállítások jelennek meg.

    ![Hozzáférési csomag – kérelmek – a címtárban nem szereplő felhasználók számára](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Válasszon az alábbi lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott csatlakoztatott szervezetek** | Akkor válassza ezt a lehetőséget, ha ki szeretné választani a rendszergazda által korábban hozzáadott szervezetek listáját. A kiválasztott szervezetek összes felhasználója kérheti ezt a hozzáférési csomagot. |
    | **Minden konfigurált csatlakoztatott szervezet** | Akkor válassza ezt a lehetőséget, ha az összes konfigurált csatlakoztatott szervezet összes felhasználója ezt a hozzáférési csomagot kéri. Csak a konfigurált csatlakoztatott szervezetek felhasználói igényelhetnek olyan hozzáférési csomagokat, amelyek minden konfigurált szervezet felhasználói számára láthatók. |
    | **Minden felhasználó (minden csatlakoztatott szervezet + minden új külső felhasználó)** | Akkor válassza ezt a lehetőséget, ha az interneten található bármely felhasználó számára elérhetővé kell tenni ezt a hozzáférési csomagot.  Ha nem tartoznak a címtár egyik csatlakoztatott szervezetéhez sem, akkor a rendszer automatikusan létrehoz egy csatlakoztatott szervezetet, amikor a csomagot kérik. Az automatikusan létrehozott csatlakoztatott szervezet **javasolt** állapotban lesz. További információ a javasolt állapotról: [csatlakoztatott szervezetek állapotának tulajdonságai](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

1. Ha az **adott csatlakoztatott szervezetek** lehetőséget választotta, kattintson a **címtárak hozzáadása** lehetőségre a rendszergazda által korábban hozzáadott csatlakoztatott szervezetek listájából való kiválasztáshoz.

1. A korábban csatlakoztatott szervezet kereséséhez írja be a név vagy a tartomány nevét.

    ![Hozzáférési csomag – kérelmek – könyvtárak kiválasztása](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Ha a szervezetet, amelyekkel együtt szeretne működni, nem szerepel a listán, megkérheti a rendszergazdát, hogy a hozzá csatlakoztatott szervezetként adja hozzá. További információt a [csatlakoztatott szervezet hozzáadása](entitlement-management-organization.md)című témakörben talál.

1. Miután kiválasztotta az összes csatlakoztatott szervezetet, kattintson a **kiválasztás** elemre.

    > [!NOTE]
    > A kiválasztott csatlakoztatott szervezetek összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ez magában foglalja az Azure AD-beli felhasználókat a szervezethez társított összes altartományból, kivéve, ha ezeket a tartományokat az Azure B2B engedélyezési vagy megtagadási listája blokkolja. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](../external-identities/allow-deny-list.md).

1. Ha jóvá szeretné hagyni a jóváhagyást, a jóváhagyási beállítások konfigurálásához kövesse az [Azure ad jogosultságok kezelése hozzáférési csomag jóváhagyási beállításainak módosítása](entitlement-management-access-package-approval-policy.md) című témakör lépéseit.
 
1. Lépjen a [kérelmek engedélyezése](#enable-requests) szakaszra.

## <a name="none-administrator-direct-assignments-only"></a>Nincs (csak a rendszergazdai közvetlen hozzárendelések)

Kövesse az alábbi lépéseket, ha szeretné megkerülni a hozzáférési kérelmeket, és lehetővé teszi, hogy a rendszergazdák közvetlenül rendeljenek hozzá konkrét felhasználókat a hozzáférési csomaghoz. A felhasználóknak nem kell a hozzáférési csomagot igényelnie. Továbbra is beállíthatja az életciklus-beállításokat, de nincsenek a kérelmek beállításai.

1. A **hozzáférést kérő felhasználók** részen kattintson a **nincs (csak rendszergazdai közvetlen hozzárendelések** elemre.

    ![Hozzáférési csomag – kérelmek – nincsenek rendszergazdai közvetlen hozzárendelések](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    A hozzáférési csomag létrehozása után közvetlenül rendelhet hozzá konkrét belső és külső felhasználókat a hozzáférési csomaghoz. Ha külső felhasználót ad meg, a rendszer létrehoz egy vendég felhasználói fiókot a címtárban. A felhasználók közvetlen hozzárendelésével kapcsolatos információkért tekintse meg a [hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása](entitlement-management-access-package-assignments.md)című témakört.

1. Ugorjon a [kérelmek engedélyezése](#enable-requests) szakaszra.


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>A kérések beállításainak meglévő házirendjének megnyitása és szerkesztése

Egy hozzáférési csomag kérésének és jóváhagyási beállításainak módosításához meg kell nyitnia a megfelelő szabályzatot. Az alábbi lépéseket követve megnyithatja és szerkesztheti a hozzáférési csomagra vonatkozó kérelmek beállításait:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** elemre, majd kattintson a szerkeszteni kívánt szabályzatra.

    Megnyílik a szabályzat részletei ablaktábla az oldal alján.

    ![Hozzáférési csomag – szabályzat részletei ablaktábla](./media/entitlement-management-shared/policy-details.png)

1. A szabályzat szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Hozzáférési csomag – szabályzat szerkesztése](./media/entitlement-management-shared/policy-edit.png)

1. Kattintson a **kérelmek** lapra a kérelmek beállításainak megnyitásához.

1. Az előző szakaszban ismertetett lépések segítségével szükség szerint módosíthatja a kérelmek beállításait.

1. Lépjen a [kérelmek engedélyezése](#enable-requests) szakaszra.

## <a name="enable-requests"></a>Kérelmek engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhető legyen a kérési házirendben szereplő felhasználók számára a kérelemhez, helyezze át az engedélyezés kapcsolót **Igen** értékre.

    A jövőben bármikor engedélyezheti azt a hozzáférési csomag létrehozása után.

    Ha a nincs lehetőséget választotta **(csak a rendszergazda közvetlen hozzárendelései)** , és az engedélyezés a **nem** értékre van állítva, akkor a rendszergazdák nem tudják közvetlenül hozzárendelni ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Kattintson a **Tovább** gombra.

1. Az életciklus beállításainak konfigurálása.

1. Ha szerkeszt egy házirendet, kattintson a **frissítés** gombra. Ha új szabályzatot ad hozzá, kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag jóváhagyási beállításainak módosítása](entitlement-management-access-package-approval-policy.md)
- [Hozzáférési csomag életciklus-beállításainak módosítása](entitlement-management-access-package-lifecycle-policy.md)
- [Hozzáférési csomagra vonatkozó kérelmek megtekintése](entitlement-management-access-package-requests.md)
