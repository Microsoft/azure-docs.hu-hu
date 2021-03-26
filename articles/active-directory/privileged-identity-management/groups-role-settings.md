---
title: Privilegizált hozzáférési csoportok beállításainak konfigurálása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat szerepkörökhöz hozzárendelhető csoportok beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cfb09f383d8425a644d3e2e87d190b350f5f41a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564636"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>A Kiemelt jogosultságú hozzáférési csoport beállításainak konfigurálása (előzetes verzió) a Privileged Identity Management

A szerepkör-beállítások az alapértelmezett beállítások, amelyeket a rendszer az Privileged Identity Management (PIM) csoport tulajdonosának és csoportjának privilegizált hozzáférési hozzárendeléseire alkalmaz. A következő lépésekkel állíthatja be a jóváhagyási munkafolyamatot annak megadásához, hogy ki hagyhat jóvá vagy tagadhat meg kérelmeket a jogosultság megemeléséhez.

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépéseket követve megnyithatja az Azure privilegizált hozzáférési csoport szerepkör beállításait.

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com/) a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörrel rendelkező felhasználóval.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

1. Válassza ki a kezelni kívánt csoportot.

    ![Csoport neve alapján szűrt privilegizált hozzáférési csoportok](./media/groups-role-settings/group-select.png)

1. Válassza a **Beállítások** lehetőséget.

    ![Beállítások lap listázási csoportjának beállításai a kiválasztott csoporthoz](./media/groups-role-settings/group-settings-select-role.png)

1. Válassza ki azt a tulajdonosi vagy tagsági szerepkört, amelynek a beállításait meg szeretné tekinteni vagy módosítani kívánja. A szerepkör aktuális beállításait a **szerepkör-beállítás részletei** lapon tekintheti meg.

    ![Szerepkör-beállítás részletei lap, amely több hozzárendelési és aktiválási beállítást listáz](./media/groups-role-settings/group-role-setting-details.png)

1. Válassza a **Szerkesztés** lehetőséget a **szerepkör-beállítás szerkesztése** lap megnyitásához. Az **aktiválás** lapon módosíthatja a szerepkör-aktiválási beállításokat, beleértve, hogy az állandó jogosultságú és aktív hozzárendelések engedélyezve legyenek-e.

    ![Szerepkör-beállítások szerkesztése lap az aktiválás lapon megnyitva](./media/groups-role-settings/role-settings-activation-tab.png)

1. Válassza a **hozzárendelés** fület a hozzárendelési beállítások lap megnyitásához. Ezek a beállítások vezérlik a szerepkör Privileged Identity Management-hozzárendelési beállításait.

    ![Szerepkör-hozzárendelés lap a szerepkör-beállítások lapon](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Az oldal alján található **értesítés** lapon vagy a **következő: aktiválás** gombra kattintva megtekintheti a szerepkör értesítési beállítások lapját. Ezek a beállítások vezérlik az ehhez a szerepkörhöz kapcsolódó e-mail-értesítéseket.

    ![Szerepkör-értesítések lap a szerepkör-beállítások lapon](./media/groups-role-settings/role-settings-notification-tab.png)

1. A szerepkör-beállítások frissítéséhez kattintson a **frissítés** gombra.

A szerepkör-beállítások lap **értesítések** lapján Privileged Identity Management lehetővé teszi, hogy a rendszer részletesen szabályozza, hogy kik kapják meg az értesítéseket és milyen értesítéseket fogadnak.

- **E-mail kikapcsolása**<br>A megadott e-maileket kikapcsolhatja az alapértelmezett címzett jelölőnégyzetének törlésével és a további címzettek törlésével.  
- **E-mailek korlátozása a megadott e-mail-címekre**<br>Kikapcsolhatja az alapértelmezett címzetteknek küldött e-maileket az alapértelmezett címzett jelölőnégyzetének törlésével. Ezután további címzettként is hozzáadhat további e-mail-címeket. Ha több e-mail-címet szeretne hozzáadni, pontosvesszővel válassza el őket (;).
- **E-mailek küldése az alapértelmezett címzetteknek és a további címzetteknek**<br>Az alapértelmezett címzett jelölőnégyzet bejelölésével és a további címzettek e-mail címeinek hozzáadásával az e-maileket is elküldheti az alapértelmezett címzettnek és a további címzetteknek.
- **Csak kritikus e-mailek**<br>Minden egyes e-mail típus esetében bejelölheti a jelölőnégyzetet a kritikus e-mailek fogadásához. Ez azt jelenti, hogy a Privileged Identity Management csak akkor folytatja az e-mailek küldését a konfigurált címzetteknek, ha az e-mailben azonnali műveletre van szükség. Például az e-mailek, amelyek arra kérik a felhasználókat, hogy bővítsék szerepkör-hozzárendelését, nem lesznek aktiválva, miközben egy, a bővítmények jóváhagyását kérő e-mailt aktivál a rendszer.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkörök beállításainak konfigurálásakor két hozzárendelési időtartam közül választhat az egyes hozzárendelési típusoknál (jogosult és aktív). Ezek a beállítások az alapértelmezett maximális időtartam lesz, amikor egy felhasználó a szerepkörhöz van rendelve Privileged Identity Managementban.

A következő **jogosult** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | Description |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó jogosult hozzárendelést rendelhetnek hozzá. |
| **Jogosult hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelés megadott kezdési és befejezési dátummal rendelkezzen. |

Emellett az alábbi **aktív** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | Description |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák kioszthatnak állandó aktív hozzárendelést. |
| **Aktív hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden aktív hozzárendeléshez meg legyen adva a kezdő és a záró dátum. |

> [!NOTE]
> Az erőforrás-rendszergazdák megújítják a megadott befejezési dátummal rendelkező hozzárendeléseket. Emellett a felhasználók önkiszolgáló kérelmeket is indíthatnak a [szerepkör-hozzárendelések meghosszabbításához vagy megújításához](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A Privileged Identity Management két különböző forgatókönyv esetén az Azure AD Multi-Factor Authentication opcionális kényszerítését teszi lehetővé.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication megkövetelése aktív hozzárendeléskor

Bizonyos esetekben előfordulhat, hogy egy felhasználót vagy csoportot egy adott szerepkörhöz szeretne hozzárendelni egy rövid ideig (például egy nap). Ebben az esetben a hozzárendelt felhasználóknak nem kell aktiválást kérniük. Ebben a forgatókönyvben a Privileged Identity Management nem tudja kikényszeríteni a többtényezős hitelesítést, ha a felhasználó a szerepkör-hozzárendelését használja, mert már aktívak a szerepkörben a hozzárendelésük időpontjában.

Annak biztosítása érdekében, hogy az erőforrás-rendszergazda teljesítse a hozzárendelést, ki is mondhatja őket, a többtényezős hitelesítés az aktív hozzárendelésre való kikényszeríthető az **aktív hozzárendelés multi-Factor Authenticationának megkövetelése** jelölőnégyzet bejelölésével.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication megkövetelése aktiváláskor

Megkövetelheti, hogy azok a felhasználók, akik jogosultak a szerepkörre, bizonyítani tudják, kik használják az Azure AD Multi-Factor Authentication az aktiválás előtt. A többtényezős hitelesítés biztosítja, hogy a felhasználó, aki azt mondják, ésszerű bizonyossággal rendelkeznek. A beállítás érvényesítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérült.

Ha a többtényezős hitelesítést az aktiválás előtt szeretné megkövetelni, jelölje be a **multi-Factor Authentication aktiválása az aktiváláskor** jelölőnégyzetet.

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **aktiválás maximális időtartama** csúszkával állíthatja be azt a maximális időtartamot (óra), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a felhasználók az aktiváláskor üzleti indoklást adjanak. Az indoklás megköveteléséhez jelölje be az **aktív hozzárendeléshez szükséges indoklás** vagy az **aktiválás kötelező indoklása** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Jóváhagyás megkövetelése az aktiváláshoz

Ha szeretne jóváhagyást kérni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be a **jóváhagyás kötelező aktiválása** jelölőnégyzetet.

1. Válassza a **jóváhagyók kiválasztása** lehetőséget a **tag vagy csoport kiválasztása** lap megnyitásához.

    ![Válassza ki a felhasználó vagy csoport ablaktáblát a jóváhagyók kiválasztásához.](./media/groups-role-settings/group-settings-select-approvers.png)

1. Válasszon ki legalább egy felhasználót vagy csoportot, majd kattintson a **kiválasztás** gombra. A felhasználók és csoportok tetszőleges kombinációját felveheti. Legalább egy jóváhagyót ki kell választania. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, válassza a **frissítés** lehetőséget a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések

- [Emelt szintű hozzáférési csoporttagság vagy tulajdonjog kiosztása a PIM-ben](groups-assign-member-owner.md)
