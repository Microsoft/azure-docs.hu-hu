---
title: Egyéni Azure AD-szerepkör konfigurálása – Privileged Identity Management (PIM)
description: Egyéni Azure AD-szerepkörök konfigurálása Privileged Identity Managementban (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23e60539c704dac457ab6e8706ec0cfe350ed9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835322"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Egyéni Azure AD-szerepkörök konfigurálása Privileged Identity Management

A Kiemelt szerepkörű rendszergazdák megváltoztathatják a felhasználókra vonatkozó szerepkör-beállításokat, amikor a hozzárendelést egy egyéni szerepkörhöz és más, egyéni szerepköröket hozzárendelő alkalmazás-rendszergazdákhoz használják.

> [!NOTE]
> Az Azure AD egyéni szerepkörei nincsenek integrálva az előzetes verzióban a beépített címtárbeli szerepkörökbe. Miután a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök felületén történik. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörökkel kapcsolatos felhasználói élményben](pim-how-to-activate-role.md) kell kezelni, és ez a cikk nem alkalmazható:
>
> [![Válassza az Azure AD > Privileged Identity Management](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Kövesse az alábbi lépéseket egy Azure AD-szerepkör beállításainak megnyitásához.

1. Jelentkezzen be [Privileged Identity Managementra](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) a Azure Portal egy olyan felhasználói fiókkal, amely hozzá van rendelve a Kiemelt szerepkörű rendszergazda szerepkörhöz.
1. Válassza az **Egyéni Azure ad-szerepkörök (előzetes verzió)** lehetőséget.

    ![A jogosult szerepkör-hozzárendelések megtekintéséhez válassza az Azure AD egyéni szerepkörök előzetes verzióját.](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Válassza a **beállítás** lehetőséget a **Beállítások** lap megnyitásához. Válassza ki a konfigurálni kívánt beállítások szerepkörét.
1. A **szerepkör-beállítások** lap megnyitásához válassza a **Szerkesztés** lehetőséget.

    ![A "szerepkör-beállítás részletei" oldalt megjelenítő képernyőkép a "szerkesztés" művelet kiválasztásával.](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Szerepkör-beállítások

Több beállítás is konfigurálható.

### <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkörök beállításainak konfigurálásakor két hozzárendelési időtartam közül választhat az egyes hozzárendelési típusoknál (jogosult vagy aktív). Ezek a lehetőségek lesznek az alapértelmezett maximális időtartam, amikor egy tag a szerepkörhöz van rendelve Privileged Identity Managementban.

Kiválaszthatja az alábbi *jogosult* hozzárendelések időtartamának beállításait.

- **Állandó jogosult hozzárendelés engedélyezése**: a rendszergazdák állandó jogosult tagságot rendelhetnek hozzá.
- **Jogosult hozzárendelés lejárta** a következő után: a rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelés rendelkezzen a megadott kezdési és befejezési dátummal.

Emellett az alábbi *aktív* hozzárendelések időtartamának beállításai közül is választhat:

- **Állandó aktív hozzárendelés engedélyezése**: a rendszergazdák állandó aktív tagságot adhatnak hozzá.
- **Aktív hozzárendelés lejárta** a következő után: a rendszergazdák megkövetelhetik, hogy az összes aktív hozzárendelés megadott kezdő és záró dátummal rendelkezzen.

### <a name="require-azure-ad-multi-factor-authentication"></a>Azure AD többtényezős hitelesítés szükséges

A Privileged Identity Management két különböző forgatókönyv esetén az Azure AD Multi-Factor Authentication opcionális kényszerítését teszi lehetővé.

- **Multi-Factor Authentication megkövetelése aktív hozzárendeléskor**

  Ha csak rövid ideig szeretné hozzárendelni egy tagot egy szerepkörhöz (például egy nap), előfordulhat, hogy túl lassú ahhoz, hogy a hozzárendelt tagok bekérjenek aktiválást. Ebben a forgatókönyvben a Privileged Identity Management nem tudja kikényszeríteni a többtényezős hitelesítést, ha a felhasználó aktiválja a szerepkör-hozzárendelést, mert már aktívak a szerepkörben a hozzárendelésük pillanatától kezdve. Annak biztosítása érdekében, hogy a hozzárendelést teljesítő rendszergazda ki mondja őket, jelölje be a **multi-Factor Authentication megkövetelése az aktív hozzárendeléshez** jelölőnégyzetet.

- **Multi-Factor Authentication megkövetelése aktiváláskor**

  Megkövetelheti, hogy egy szerepkörhöz hozzárendelt jogosult felhasználók az aktiválás előtt regisztrálhatnak az Azure AD-Multi-Factor Authenticationba. Ez a folyamat biztosítja, hogy az aktiválást kérő felhasználó számára ésszerű bizonyosságot biztosítson. Ha ezt a beállítást választja, akkor a kritikus fontosságú szerepkörök védelme olyan helyzetekben, amikor a felhasználói fiók biztonsága sérül. Ha az aktiválás előtt szeretné megkövetelni, hogy egy jogosult tag futtassa az Azure AD Multi-Factor Authentication, válassza a **multi-Factor Authentication megkövetelése aktiváláskor** jelölőnégyzetet.

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **aktiválás maximális időtartama** csúszkával állíthatja be azt a maximális időtartamot (óra), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték a következő lehet:, 1 és 24 óra.

### <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a tagok indoklást adjanak az aktív hozzárendeléshez vagy aktiváláskor. Az indoklás megköveteléséhez jelölje be az **aktív hozzárendelésre vonatkozó indoklás megkövetelése** jelölőnégyzetet, vagy a **szükséges indoklást az aktiválási** mezőben.

### <a name="require-approval-to-activate"></a>Jóváhagyás megkövetelése az aktiváláshoz

Ha szeretne jóváhagyást kérni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be a **jóváhagyás megkövetelése** jelölőnégyzetet.
1. Válassza a **jóváhagyók kiválasztása** lehetőséget a **tag vagy csoport kiválasztása** lista megnyitásához.

    ![A beállítások szerkesztéséhez nyissa meg az Azure AD egyéni szerepkörét](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Válasszon ki legalább egy tagot vagy csoportot, majd kattintson a **kiválasztás** elemre. Legalább egy jóváhagyót ki kell választania. Nincsenek alapértelmezett jóváhagyók. A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.
1. A szerepkör-beállítások megadása után válassza a **frissítés** lehetőséget a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-activate.md)
- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Szerepkör-definíciók az Azure AD-ben](../roles/permissions-reference.md)
