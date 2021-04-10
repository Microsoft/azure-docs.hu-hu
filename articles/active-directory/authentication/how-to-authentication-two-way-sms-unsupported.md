---
title: A kétirányú SMS már nem támogatott – Azure Active Directory
description: A cikk azt ismerteti, hogyan engedélyezhető egy másik módszer olyan felhasználók számára, akik továbbra is kétirányú SMS-t használnak.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689028"
---
# <a name="two-way-sms-unsupported"></a>Nem támogatott kétirányú SMS

Az Azure AD Multi-Factor Authentication (MFA) kiszolgáló kétirányú SMS-je eredetileg 2018-ban elavult, és a 2021. február 24. után már nem támogatott. A rendszergazdáknak engedélyezniük kell egy másik módszert azon felhasználók számára, akik továbbra is kétirányú SMS-t használnak.

Az e-mailes értesítések és a Azure Portal Service Health Notifications (Portal pirítós) az érintett rendszergazdáknak a 2020. december 8. és 2021. január 28. között lettek elküldve. A riasztások a tulajdonos, a társ-tulajdonos, a rendszergazda és a szolgáltatás-rendszergazdai RBAC szerepkörökhöz vannak kötve, amelyek az előfizetésekhez kötődnek. Ha már elvégezte a következő lépéseket, nincs szükség beavatkozásra.

## <a name="required-actions"></a>Szükséges műveletek

1. Engedélyezze a mobil alkalmazást a felhasználók számára, ha még nem tette volna meg. További információ: [a Mobile App Authentication engedélyezése az MFA-kiszolgálóval](howto-mfaserver-deploy-mobileapp.md).
1. Értesítse a végfelhasználókat az MFA-kiszolgáló [felhasználói portáljának](howto-mfaserver-deploy-userportal.md) megkereséséhez a Mobile App aktiválásához. Az ajánlott ellenőrzési lehetőség a [Microsoft Authenticator alkalmazás](https://www.microsoft.com/en-us/account/authenticator) , mivel biztonságosabb, mint a kétirányú SMS-ben. További információkért lásd: itt az ideje, hogy lekérje [a telefonos átvitelt a hitelesítéshez](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Módosítsa a felhasználói beállításokat kétirányú szöveges üzenetről a Mobile App értékre alapértelmezett módszerként.

## <a name="faq"></a>GYIK

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Mi a teendő, ha nem módosítom az alapértelmezett módszert kétirányú SMS-ből a Mobile-alkalmazásra?
A kétirányú SMS 2021. február 24-én meghiúsult. A felhasználók hibaüzenetet kapnak, amikor megpróbálnak bejelentkezni, és az MFA-t átadni.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Hogyan módosítja a felhasználói beállításokat kétirányú szöveges üzenetről a Mobile apps szolgáltatásra?

A felhasználói beállításokat a következő lépésekkel kell módosítania:

1. Az MFA-kiszolgálón szűrje a felhasználói listát a kétirányú szöveges üzenethez.
1. Válassza a minden felhasználó lehetőséget.
1. Nyissa meg a felhasználók szerkesztése párbeszédpanelt.
1. Felhasználók módosítása szöveges üzenetből a mobil alkalmazásba.

   ![Képernyőkép a végfelhasználók számára](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>A felhasználóknak bármilyen műveletet kell tenniük? Ha igen, hogyan?
Igen. A végfelhasználóknak meg kell keresniük az adott MFA-kiszolgáló felhasználói portálját a Mobile alkalmazás aktiválásához, ha még nem tette meg. A 3. lépés elvégzése után minden olyan felhasználó, aki nem látogatta meg a felhasználói portált a Mobile App beállításakor, a bejelentkezés sikertelen lesz, amíg újra nem regisztrálja a felhasználói portált.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Mi a teendő, ha a felhasználók nem tudják telepíteni a Mobile alkalmazást? Milyen egyéb lehetőségek vannak?
A kétirányú SMS vagy a Mobile alkalmazás egy telefonhívás. A Microsoft Authenticator alkalmazás azonban az ajánlott ellenőrzési módszer.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Az egyirányú SMS is elavult?
Nem, csak kétirányú SMS-t elavult a rendszer. Az MFA-kiszolgáló esetében az egyirányú SMS a forgatókönyvek egy részhalmazára működik:

- AD FS adapter
- IIS-hitelesítés (felhasználói portál és konfiguráció szükséges hozzá)
- RADIUS (megköveteli, hogy a RADIUS-ügyfelek támogassák a hozzáférési kihívást, és a PAP protokollt használják)

Az egyirányú SMS-ben olyan korlátozások vannak érvényben, amelyek lehetővé teszik, hogy a Mobile alkalmazás jobb alternatíva legyen, mert nem igényli az ellenőrző kód megadását.
Ha bizonyos helyzetekben továbbra is szeretne egyirányú SMS-t használni, akkor hagyja meg ezeket a bejelölve, de módosítsa a **Vállalati beállítások** szakaszt, az **általános** lap **felhasználója a szöveges üzenetet** **egyirányú** módon, **kétirányú** értékre módosítja. Végül, ha olyan címtár-szinkronizálást használ, amely alapértelmezés szerint az SMS-t használja, a kétirányú helyett módosítania kell One-Way.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Hogyan ellenőrizhetem, hogy mely felhasználók továbbra is kétirányú SMS-t használnak?
Ezeknek a felhasználóknak a listázásához indítsa el az **MFA-kiszolgálót**, válassza a **felhasználók** szakaszt, kattintson a **felhasználói lista szűrése** lehetőségre, és szűrje a **kétirányú szöveges üzenetet**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Hogyan rejthető el a kétirányú SMS lehetőség az MFA-portálon annak megakadályozása érdekében, hogy a felhasználók a jövőben is kiválasszák?
Az MFA-kiszolgáló felhasználói portálján kattintson a **Beállítások** lehetőségre, így törölheti a **szöveges üzenetet** , hogy az nem érhető el. Ugyanez igaz a **AD FS** szakaszban, ha AD FS használ a felhasználó beléptetéséhez.

