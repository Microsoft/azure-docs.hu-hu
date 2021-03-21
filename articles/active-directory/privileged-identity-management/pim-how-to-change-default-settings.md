---
title: Azure AD-szerepkör beállításainak konfigurálása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD szerepkör beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06eb7e8d1be2e58fd6cf87167a585a2ffeee0d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708483"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management

A Kiemelt szerepkörű rendszergazda testre szabhatja Privileged Identity Management (PIM) Azure Active Directory (Azure AD) szervezetében, beleértve a jogosult szerepkör-hozzárendelést aktiváló felhasználó élményének módosítását is.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörnek.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

  [![Válassza az Azure AD > Privileged Identity Management lehetőséget.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Kövesse a cikkben ismertetett lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Kövesse az alábbi lépéseket egy Azure AD-szerepkör beállításainak megnyitásához.

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com/) a [Kiemelt szerepkörű rendszergazda](../roles/permissions-reference.md#privileged-role-administrator) szerepkörrel rendelkező felhasználóval.

1. Nyissa meg **Azure ad Privileged Identity Management** &gt; **Azure ad-szerepkörök** &gt; **szerepkör-beállításait**.

    ![Szerepkör-beállítások lap Azure AD-szerepkörök listázása](./media/pim-how-to-change-default-settings/role-settings.png)

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni kívánja.

    ![Szerepkör-beállítás részletei lap, amely több hozzárendelési és aktiválási beállítást listáz](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. A szerepkör-beállítások lap megnyitásához válassza a **Szerkesztés** lehetőséget.

    ![Szerepkör-beállítások szerkesztése lap a hozzárendelési és aktiválási beállítások frissítésére szolgáló beállításokkal](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Az egyes szerepkörökhöz tartozó szerepkör-beállítás ablaktáblán több beállítás is konfigurálható.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkörök beállításainak konfigurálásakor két hozzárendelési időtartam közül választhat az egyes hozzárendelési típusoknál (jogosult és aktív). Ezek a beállítások az alapértelmezett maximális időtartam lesz, amikor egy felhasználó a szerepkörhöz van rendelve Privileged Identity Managementban.

A következő **jogosult** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | A globális rendszergazdák és a Kiemelt szerepkörök rendszergazdái állandó jogosult hozzárendelést rendelhetnek hozzá. |
| **Jogosult hozzárendelés lejárta a következő után** | A globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelés megadott kezdő és záró dátumot biztosítson. |

Emellett az alábbi **aktív** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | A globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák állandó aktív hozzárendelést is hozzárendelhet. |
| **Aktív hozzárendelés lejárta a következő után** | A globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák megkövetelhetik, hogy minden aktív hozzárendeléshez meg legyen adva a kezdő és a záró dátum. |

> [!NOTE]
> A globális rendszergazdák és a Kiemelt szerepkörű rendszergazdák a megadott befejezési dátummal rendelkező hozzárendeléseket is megújítják. Emellett a felhasználók önkiszolgáló kérelmeket is indíthatnak a [szerepkör-hozzárendelések meghosszabbításához vagy megújításához](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A Privileged Identity Management két különböző forgatókönyv esetén az Azure AD Multi-Factor Authentication opcionális kényszerítését teszi lehetővé.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication megkövetelése aktív hozzárendeléskor

Bizonyos esetekben előfordulhat, hogy egy felhasználót egy rövid ideig (például egy nap) szeretne hozzárendelni egy szerepkörhöz. Ebben az esetben a hozzárendelt felhasználóknak nem kell aktiválást kérniük. Ebben a forgatókönyvben a Privileged Identity Management nem tudja kikényszeríteni a többtényezős hitelesítést, ha a felhasználó a szerepkör-hozzárendelését használja, mert már aktívak a szerepkörben a hozzárendelésük időpontjában.

Annak biztosítása érdekében, hogy a hozzárendelést teljesítő rendszergazda kik mondják, a többtényezős hitelesítés kikényszeríthető az aktív hozzárendeléshez, ha bejelöli a **kötelező multi-Factor Authentication az aktív hozzárendeléshez** jelölőnégyzetet.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication megkövetelése aktiváláskor

Megkövetelheti, hogy azok a felhasználók, akik jogosultak a szerepkörre, bizonyítani tudják, kik használják az Azure AD Multi-Factor Authentication az aktiválás előtt. A többtényezős hitelesítés biztosítja, hogy a felhasználó, aki azt mondják, ésszerű bizonyossággal rendelkeznek. A beállítás érvényesítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérült.

Ha a többtényezős hitelesítést az aktiválás előtt szeretné megkövetelni, jelölje be a **szerepkör-beállítások szerkesztése** a hozzárendelés lapon a **multi-Factor Authentication megkövetelése aktiváláskor** jelölőnégyzetet.

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **aktiválás maximális időtartama** csúszkával állíthatja be azt a maximális időtartamot (óra), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a felhasználók az aktiváláskor üzleti indoklást adjanak. Az indoklás megköveteléséhez jelölje be az **aktív hozzárendeléshez szükséges indoklás** vagy az **aktiválás kötelező indoklása** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Jóváhagyás megkövetelése az aktiváláshoz

Ha több jóváhagyót állít be, a jóváhagyás azonnal befejeződik, amint az egyikük jóváhagyja vagy megtagadja. Legalább két felhasználó jóváhagyása nem szükséges. A szerepkörök aktiválásához szükséges jóváhagyáshoz kövesse az alábbi lépéseket.

1. Jelölje be a **jóváhagyás kötelező aktiválása** jelölőnégyzetet.

1. Válassza a **jóváhagyók kiválasztása** lehetőséget.

    ![Válassza ki a felhasználó vagy csoport ablaktáblát a jóváhagyók kiválasztásához.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Válasszon ki legalább egy felhasználót, majd kattintson a **kiválasztás** gombra. Legalább egy jóváhagyót ki kell választania. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, válassza a **frissítés** lehetőséget a módosítások mentéséhez.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Szerepkör-beállítások megnyitása (előző verzió)

Kövesse az alábbi lépéseket egy Azure AD-szerepkör beállításainak megnyitásához.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** elemet.

1. Válassza a **Beállítások** lehetőséget.

    ![Azure AD-szerepkörök – beállítások](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Válassza a **szerepkörök** lehetőséget.

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni kívánja.

    ![Azure AD-szerepkörök – beállítások szerepkörei](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Az egyes szerepkörök beállítások lapján több beállítás is konfigurálható. Ezek a beállítások csak azokra a felhasználókra vonatkoznak, akik **jogosult** hozzárendelések, nem **végleges** hozzárendelések.

## <a name="activations"></a>Aktiválások

Az **aktiválások** csúszkával állíthatja be azt a maximális időtartamot (órában), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 72 óra közötti lehet.

## <a name="notifications"></a>Értesítések

Az **értesítések** kapcsoló segítségével megadhatja, hogy a rendszergazdák kapnak-e e-mail-értesítéseket a szerepkörök aktiválása után. Ez az értesítés hasznos lehet a jogosulatlan vagy törvénytelen aktiválások észleléséhez.

Az **Engedélyezés** beállítás megadása esetén a rendszer a következő értesítéseket küldi el:

- Kiemelt szerepkörű rendszergazda
- Biztonsági rendszergazda
- Globális rendszergazda

További információkért tekintse [meg az e-mailes értesítéseket Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incidens/kérelem jegye

Az **incidens/kérelem jegy** kapcsolójának használatával megkövetelheti, hogy a jogosult rendszergazdák a szerepkörük aktiválása után is tartalmazzák a jegy számát. Ezzel a gyakorlattal hatékonyabbá teheti a szerepkör-hozzáférés naplózását.

## <a name="multi-factor-authentication"></a>Többtényezős hitelesítés

Használja a **multi-Factor Authentication** kapcsolót annak megadásához, hogy a felhasználóknak meg kell-e adniuk identitását az MFA-ban, mielőtt aktiválni tudják a szerepköreiket. Csak egyszer kell meggyőződniük a saját identitásukat, nem minden alkalommal, amikor aktiválnak egy szerepkört. Az MFA engedélyezésekor két tippet kell szem előtt tartani:

- Azok a felhasználók, akik rendelkeznek Microsoft-fiókkal az e-mail-címeiknek (általában @outlook.com , de nem mindig), nem regisztrálhatnak az Azure ad-multi-Factor Authentication. Ha a Microsoft-fiókkal rendelkező felhasználók számára szeretné hozzárendelni a szerepköröket, a rendszergazdáknak állandó rendszergazdai jogosultságokkal kell rendelkezniük, vagy le kell tiltaniuk a többtényezős hitelesítést a szerepkörhöz.
- Az Azure ad-Multi-Factor Authentication nem tiltható le magas jogosultsági szintű szerepkörökhöz az Azure AD-ben és a Microsoft 365ban. Ez a biztonsági funkció a következő szerepkörök védelme érdekében nyújt segítséget:  
  
  - Azure Information Protection rendszergazda
  - Számlázási adminisztrátor
  - Cloud Application Administrator
  - Megfelelőségi rendszergazda
  - Feltételes hozzáférésű rendszergazda
  - Dynamics 365-rendszergazda
  - Ügyfél-kulcstároló hozzáférési jóváhagyója
  - Címtár-írók
  - Exchange-rendszergazda
  - Globális rendszergazda
  - Intune-rendszergazda
  - Power BI rendszergazda
  - Kiemelt szerepkörű rendszergazda
  - Biztonsági rendszergazda
  - SharePoint-rendszergazda
  - Skype Vállalati verzió-rendszergazda
  - Felhasználói rendszergazda

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Jóváhagyás megkövetelése

Ha a szükséges jóváhagyást delegálni szeretné a szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Állítsa be a **jóváhagyás megkövetelése** kapcsolót az **engedélyezve** értékre. A panel kibontja a beállításokat a jóváhagyók kiválasztásához.

    ![A "jóváhagyás megkövetelése" kapcsolót megjelenítő képernyőkép a "engedélyezés" beállítással.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha nem ad meg jóváhagyókat, a Kiemelt szerepkörű rendszergazda lesz az alapértelmezett jóváhagyó, és ezután jóvá kell hagynia a szerepkörre vonatkozó összes aktiválási kérést.

1. Jóváhagyók hozzáadásához kattintson a **jóváhagyók kiválasztása** elemre.

    ![Azure AD-szerepkörök – beállítások – jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Válasszon ki egy vagy több jóváhagyót a Kiemelt szerepkörű rendszergazda mellett, majd kattintson a **kiválasztás** elemre. Javasoljuk, hogy legalább két jóváhagyót adjon hozzá. Még ha Ön is jóváhagyja a saját magát, nem hagyhat jóvá önálló szerepkör-aktiválást. A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, kattintson a **Save (Mentés** ) gombra a módosítások mentéséhez.

---

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure AD-szerepkörökhöz tartozó biztonsági riasztások konfigurálása Privileged Identity Management](pim-how-to-configure-security-alerts.md)
