---
title: Önkiszolgáló jelszó-visszaállítás testreszabása – Azure Active Directory
description: Megtudhatja, hogyan szabhatja testre az Azure AD önkiszolgáló jelszó-visszaállítás felhasználói megjelenítési és élmény-beállításait
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741626"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Felhasználói élmény testreszabása Azure Active Directory önkiszolgáló jelszó-visszaállításhoz

Az önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a Azure Active Directory (Azure AD) számára a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha a felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, akkor az utasításokat követve letilthatja önmagát, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására.

A felhasználók SSPR-élményének javításához testreszabhatja a jelszó-visszaállítási oldal, az e-mail-értesítések és a bejelentkezési lapok megjelenését és működését. Ezekkel a testreszabási lehetőségekkel egyértelművé teheti a megfelelő helyen lévő felhasználót, és megbízza számukra, hogy hozzáférnek a vállalati erőforrásokhoz.
    
Ez a cikk bemutatja, hogyan szabhatja testre a SSPR e-mail-hivatkozását a felhasználók, a vállalat arculata és a AD FS bejelentkezési oldal hivatkozása alapján.

## <a name="customize-the-contact-your-administrator-link"></a>A "Kapcsolatfelvétel a rendszergazdával" hivatkozás testreszabása

Ahhoz, hogy a felhasználók segítséget nyújtsanak az önkiszolgáló jelszó-visszaállításhoz, a jelszó-visszaállítási portálon megjelenik a "Kapcsolatfelvétel a rendszergazdával" hivatkozás. Ha a felhasználó kiválasztja ezt a hivatkozást, a következő két dolog egyike jelenik meg:

* Ha ez a kapcsolódási hivatkozás az alapértelmezett állapotban van, akkor a rendszer e-mailt küld a rendszergazdának, és kéri, hogy nyújtson segítséget a felhasználó jelszavának módosításához. Az alábbi e-mail üzenet az alapértelmezett e-mail-üzenetet jeleníti meg:

    ![Példa a rendszergazdának küldött e-mailek visszaállítására](./media/howto-sspr-customization/sspr-contact-admin.png)

* Ha testre van szabva, a felhasználó egy weboldalra vagy e-mail-címre küld segítséget a rendszergazdától.
    * Ha ezt testreszabja, azt javasoljuk, hogy ezt a beállítást olyan felhasználók számára ajánljuk, akik már ismerik a támogatást.

    > [!WARNING]
    > Ha ezt a beállítást egy olyan e-mail-címmel és fiókkal testreszabja, amelynek jelszó-visszaállításra van szüksége, akkor előfordulhat, hogy a felhasználó nem tud segítséget kérni.

### <a name="default-email-behavior"></a>Alapértelmezett e-mail-viselkedés

Az alapértelmezett kapcsolattartási e-mail küldése a címzetteknek a következő sorrendben történik:

1. Ha az *ügyfélszolgálat rendszergazdai* szerepköre vagy a *jelszó-rendszergazdai* szerepkör hozzá van rendelve, a rendszer értesíti a rendszergazdákat ezekkel a szerepkörökkel.
1. Ha nincs hozzárendelve ügyfélszolgálati rendszergazda vagy jelszó-rendszergazda, akkor a rendszer értesíti a rendszergazdákat a *felhasználói rendszergazdai* szerepkörrel.
1. Ha az előző szerepkörök egyike sincs hozzárendelve, akkor a *globális rendszergazdák* értesítést kapnak.

Minden esetben legfeljebb 100 címzett kap értesítést.

Ha többet szeretne megtudni a különböző rendszergazdai szerepkörökről és azok hozzárendeléséről, tekintse meg a [rendszergazdai szerepkörök kiosztása a Azure Active Directory-ben](../roles/permissions-reference.md)című témakört.

### <a name="disable-contact-your-administrator-emails"></a>A "Kapcsolatfelvétel a rendszergazdával" e-mailek letiltása

Ha a szervezet nem szeretné értesíteni a rendszergazdákat a jelszó-visszaállítási kérelmekről, a következő konfigurációs lehetőségek használhatók:

* Testreszabhatja az ügyfélszolgálati hivatkozást egy webes URL-cím vagy mailto: cím megadásához, amellyel a felhasználók segítséget kérhetnek. Ez a beállítás a **jelszó-visszaállítás**  >  **testreszabása**  >  **Egyéni segélyszolgálat e-mail-címe vagy URL-címe** alatt található.
* Az összes felhasználó önkiszolgáló jelszó-visszaállításának engedélyezése. Ez a beállítás a **jelszó-visszaállítás**  >  **tulajdonságai** területen található. Ha nem szeretné, hogy a felhasználók a saját jelszavukat állítsa alaphelyzetbe, akkor egy üres csoporthoz is hozzáférhet. *Ez a beállítás nem ajánlott.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>A bejelentkezési oldal és a hozzáférési panel testreszabása

Testreszabhatja a bejelentkezési oldalt, például hozzáadhat egy emblémát, amely a vállalati arculatnak megfelelő képpel együtt jelenik meg. A vállalati védjegyezés konfigurálásával kapcsolatos további információkért lásd: [vállalati arculat hozzáadása a bejelentkezési laphoz az Azure ad-ben](../fundamentals/customize-branding.md).

A kiválasztott grafikák a következő esetekben jelennek meg:

* Miután a felhasználó beírja a felhasználónevét
* Ha a felhasználó a testreszabott URL-címet éri el:
   * A `whr` paraméternek a jelszó-visszaállítási oldalra való átadásával, például: `https://login.microsoftonline.com/?whr=contoso.com`
   * A `username` paraméternek a jelszó-visszaállítási oldalra való átadásával, például: `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Könyvtár neve

Annak érdekében, hogy a dolgok jobban megnézzék a felhasználókat, a Portálon és az automatikus kommunikációban is megváltoztathatja a szervezet nevét. Ha módosítani szeretné a Azure Portal könyvtárnév attribútumát, keresse meg **Azure Active Directory**  >  **tulajdonságokat**. Ez a felhasználóbarát szervezet neve beállítás a legkönnyebben látható az automatizált e-mailekben, az alábbi példáknak megfelelően:

* A felhasználóbarát név az e-mailben, például "a Microsoft neve a *contoso demo nevében*"
* Az e-mailben szereplő tárgyi sor, például "*contoso demo-fiók e-mail-ellenőrző kódja*"

## <a name="customize-the-ad-fs-sign-in-page"></a>A AD FS bejelentkezési oldalának testreszabása

Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS)-t használ a felhasználói bejelentkezési eseményekhez, hozzáadhat egy hivatkozást a bejelentkezési oldalhoz a bejelentkezési [oldal leírásának hozzáadásához](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)a cikk útmutatása alapján.

Adja meg a felhasználóknak az oldalra mutató hivatkozást, hogy megadják a SSPR-munkafolyamatot, például: *https://passwordreset.microsoftonline.com* . A AD FS bejelentkezési oldalára mutató hivatkozás hozzáadásához használja a következő parancsot a AD FS-kiszolgálón:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Következő lépések

A környezet SSPR használatának megismeréséhez lásd: [jelentéskészítési beállítások az Azure ad jelszavas felügyelethez](howto-sspr-reporting.md).

Ha Ön vagy a felhasználók problémákat tapasztalnak a SSPR kapcsolatban, olvassa el az [önkiszolgáló jelszó-visszaállítás hibaelhárítása](./troubleshoot-sspr.md) című témakört.