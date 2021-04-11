---
title: Felhasználók hozzáadása és kezelése a kereskedelmi piactéren – Azure Marketplace
description: Ismerje meg, hogyan kezelheti a felhasználókat a kereskedelmi piactér programban a Microsoft kereskedelmi Piactéri fiókjához a partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108284"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Felhasználók hozzáadása és kezelése a kereskedelmi piactéren

**Megfelelő szerepkörök**

- Tulajdonos
- Manager

A partner Center **felhasználói** szakasza (a **Fiókbeállítások** részben) lehetővé teszi, hogy az Azure ad segítségével kezelje a partner Center-fiókhoz hozzáférő felhasználókat, csoportokat és Azure ad-alkalmazásokat. A fióknak felettes szintű engedélyekkel kell rendelkeznie ahhoz a [munkahelyi fiókhoz (Azure ad-bérlőhöz)](company-work-accounts.md) , amelyben felhasználókat kíván hozzáadni vagy szerkeszteni. Ha egy másik munkahelyi fiókon vagy bérlőn belül szeretné felügyelni a felhasználókat, ki kell jelentkeznie, majd újra be kell jelentkeznie egy **kezelői** engedélyekkel rendelkező felhasználóként az adott munkahelyi fiókra/bérlőre.

Miután bejelentkezett a munkahelyi fiókjával (Azure AD-Bérlővel), felhasználókat adhat hozzá és kezelhet.

## <a name="add-existing-users"></a>Meglévő felhasználók hozzáadása

Felhasználók hozzáadása a partner Center-fiókhoz, amely már szerepel a vállalat [munkahelyi fiókjában (Azure ad-bérlő)](company-work-accounts.md):

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), és válassza a **felhasználók hozzáadása** lehetőséget.
1. Válasszon ki egy vagy több felhasználót a megjelenő listából. A keresőmező segítségével megkeresheti az adott felhasználókat. * Ha egynél több felhasználót választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több felhasználót szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
1. Ha végzett a felhasználók kiválasztásával, válassza a **kijelölt hozzáadása** elemet.
1. A **szerepkörök** szakaszban határozza meg a kiválasztott felhasználó (k) szerepkörét vagy testreszabott engedélyeit.
1. Kattintson a **Mentés** gombra.

## <a name="create-new-users"></a>Új felhasználók létrehozása

Új felhasználói fiókok létrehozásához [globális rendszergazdai](/azure/active-directory/roles/permissions-reference) jogosultságokkal rendelkező fiókkal kell rendelkeznie.

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), válassza a **felhasználók hozzáadása**, majd az **új felhasználók létrehozása** lehetőséget.
1. Adja meg az összes új felhasználó utónevét, vezetéknevét és felhasználónevét.
1. Ha azt szeretné, hogy az új felhasználó globális rendszergazdai fiókkal rendelkezzen a szervezet címtárában, jelölje be a **felhasználó globális rendszergazdaként való megjelölése az Azure ad-ben jelölőnégyzetet az összes címtár-erőforrás teljes körű vezérlése mellett**. Ez teljes hozzáférést biztosít a felhasználónak a vállalat Azure AD összes felügyeleti funkciójához. A szervezet munkahelyi fiókjában (Azure AD-bérlő) lévő felhasználók hozzáadhatók és kezelhetők, bár nem a partner Centerben, hacsak nem a megfelelő szerepkört/engedélyeket adja meg a fióknak.
1. Ha bejelölte a jelölőnégyzetet, hogy a **felhasználó globális rendszergazda legyen**, *Jelszó-helyreállítási e-mailt* kell megadnia a felhasználónak, ha szükséges.
1. A **csoporttagság** szakaszban válassza ki azokat a csoportokat, amelyekhez az új felhasználót be szeretné állítani.
1. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
1. Kattintson a **Mentés** gombra.

Ha új felhasználót hoz létre a partner Centerben, az adott felhasználóhoz tartozó fiókot is létrehoz a munkahelyi fiókban (Azure AD-bérlőben), amelyhez be van jelentkezve. Ha módosítja egy felhasználó nevét a partner Centerben, ugyanazokat a módosításokat fogja végezni a szervezet munkahelyi fiókjában (Azure AD-bérlő).

## <a name="invite-new-users-by-email"></a>Új felhasználók meghívása e-mailben

Ha olyan felhasználókat szeretne meghívni, akik jelenleg nem részei a munkahelyi munkahelyi fióknak (Azure AD-bérlőnek) e-mailben, [globális rendszergazdai](/azure/active-directory/roles/permissions-reference) jogosultságokkal rendelkező fiókkal kell rendelkeznie.

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), válassza a **felhasználók hozzáadása** lehetőséget, majd válassza **a felhasználók meghívása e-mailben** lehetőséget.
1. Írjon be egy vagy több e-mail-címet (legfeljebb 10), vesszővel vagy pontosvesszővel elválasztva.
1. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
1. Kattintson a **Mentés** gombra.

A meghívott felhasználók e-mailben meghívót kapnak a partner Center-fiókhoz való csatlakozáshoz. A rendszer létrehoz egy új vendég-felhasználói fiókot a munkahelyi fiókjában (Azure AD-bérlő). Minden felhasználónak el kell fogadnia a meghívót, mielőtt hozzáférhessen a fiókjához.

Ha újra el kell küldenie egy meghívót, látogasson el a *felhasználók* oldalra, keresse meg a meghívót a felhasználók listájában, válassza ki az e-mail-címét (vagy a *meghívót függőben lévő* szöveget). Ezután a lap alján válassza a **meghívás újraküldése** lehetőséget.

Ha a szervezete [címtár-integrációt](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) használ a helyszíni CÍMTÁRSZOLGÁLTATÁS Azure ad-val való szinkronizálásához, nem fog tudni új felhasználókat, csoportokat vagy Azure ad-alkalmazásokat létrehozni a partner Centerben. Önnek (vagy egy másik rendszergazdának a helyszíni címtárban) közvetlenül a helyszíni címtárban kell létrehoznia őket, mielőtt látni és hozzá tudná adni őket a partner Centerben.

## <a name="remove-a-user"></a>Felhasználó eltávolítása

Ha el szeretne távolítani egy felhasználót a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** (a **Fiókbeállítások** alatt) lehetőségre, válassza ki azt a felhasználót, akit el szeretne távolítani a jobb szélső oszlopban lévő jelölőnégyzetből, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt felhasználó (ka) t.

## <a name="change-a-user-password"></a>Felhasználói jelszó módosítása

Ha az egyik felhasználónak módosítania kell a jelszavát, akkor megteheti, ha *Jelszó-helyreállítási e-mailt* adott a felhasználói fiók létrehozásakor. A felhasználó jelszavát az alábbi lépések végrehajtásával is frissítheti. Ha módosítani szeretné egy felhasználó jelszavát a munkahelyi munkahelyi fiókjában (Azure AD-bérlő), [globális rendszergazdai](/azure/active-directory/roles/permissions-reference) jogosultságokkal rendelkező fiókba kell bejelentkeznie. Ez megváltoztatja a felhasználó jelszavát az Azure AD-bérlőben, valamint a partner Center eléréséhez használt jelszót.

1. A **felhasználók** lapon ( **Fiókbeállítások** alatt) válassza ki a szerkeszteni kívánt felhasználói fiók nevét.
1. A lap alján kattintson a **jelszó alaphelyzetbe állítása** gombra.
1. Ekkor megjelenik egy megerősítést kérő oldal, amely megjeleníti a felhasználó bejelentkezési adatait, beleértve az ideiglenes jelszót is. Ügyeljen arra, hogy kinyomtassa vagy átmásolja ezt az információt, és megadja azt a felhasználónak, mivel az oldal elhagyása után nem fog tudni hozzáférni az ideiglenes jelszóhoz.
