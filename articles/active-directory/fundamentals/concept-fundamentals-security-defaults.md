---
title: Azure Active Directory biztonsági alapértelmezések
description: Alapértelmezett biztonsági szabályzatok, amelyek segítenek megvédeni a szervezeteket az Azure AD gyakori támadásaitól
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efa88e1be5c5df5dd09cb5a97c8ece352496ccdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769696"
---
# <a name="what-are-security-defaults"></a>Mik azok a biztonsági alapértelmezések?

A biztonság kezelése nehézkes lehet az olyan gyakori identitással kapcsolatos támadások esetén, mint a szórásos jelszó szórása, visszajátszás és adathalászat egyre népszerűbb. A biztonsági alapértelmezések megkönnyítik a szervezet előre konfigurált biztonsági beállításokkal való védelmét ezekkel a támadásokkal szemben:

- Minden felhasználónak regisztrálnia kell az Azure AD Multi-Factor Authenticationre.
- Többtényezős hitelesítés megkövetelve a rendszergazdáktól.
- Az örökölt hitelesítési protokollok letiltása.
- Többtényezős hitelesítés megkövetelve a felhasználóktól, ha szükséges.
- Az emelt szintű tevékenységek, például a hozzáférés védelme a Azure Portal.

![Képernyőkép a Azure Portal alapértelmezett biztonsági beállítások engedélyezéséhez szükséges váltógombkal](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
További részleteket a biztonsági alapértelmezések elérhetővé tenni való bevezetéséről Alex Weinert a következő blogbejegyzésében [talál: Introducing security defaults](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)(A biztonsági alapértelmezések bevezetése).

## <a name="availability"></a>Rendelkezésre állás

A Microsoft mindenki számára elérhetővé teszi a biztonsági alapértelmezéseket. A cél annak biztosítása, hogy minden szervezet számára alapszintű biztonság legyen engedélyezve többletköltség nélkül. Az alapértelmezett biztonsági beállításokat a Azure Portal. Ha a bérlő 2019. október 22-én vagy azt követően lett létrehozva, lehetséges, hogy a bérlőben már engedélyezve vannak a biztonsági alapértelmezések. Az összes felhasználó védelme érdekében a rendszer az összes újonnan létrehozott bérlő számára kiveszi az alapértelmezett biztonsági beállításokat.

### <a name="whos-it-for"></a>Ki a jó?

- Ha Ön olyan szervezet, amely növelni szeretné a biztonsági tartását, de nem tudja, hogyan és hol kezdjen, a biztonsági alapértékek az Ön számára vannak.
- Ha Ön egy olyan szervezet, amely a licencelés ingyenes Azure Active Directory, a biztonsági alapértelmezések az Ön számára vannak megszabadva.

### <a name="who-should-use-conditional-access"></a>Kiknek érdemes feltételes hozzáférést használniuk?

- Ha Ön olyan szervezet, amely jelenleg feltételes hozzáférési szabályzatokat használ a jelek egymáshoz hozása, a döntéshozatal és a szervezeti szabályzatok betartatása érdekében, a biztonsági alapértelmezések valószínűleg nem megfelelőek az Ön számára. 
- Ha Ön egy olyan szervezet, amely prémium szintű Azure Active Directory rendelkezik licenccel, a biztonsági alapértelmezések valószínűleg nem megfelelőek az Ön számára.
- Ha a szervezet összetett biztonsági követelményekkel rendelkezik, érdemes megfontolni a feltételes hozzáférést.

## <a name="policies-enforced"></a>Betartatott szabályzatok

### <a name="unified-multi-factor-authentication-registration"></a>Egyesített Többtényezős hitelesítés regisztrációja

A bérlőben minden felhasználónak regisztrálnia kell a többtényezős hitelesítésre (MFA) az Azure AD Multi-Factor Authentication formájában. A felhasználóknak 14 napjuk van arra, hogy regisztrálják az Azure AD Multi-Factor Authenticationt a Microsoft Authenticator használatával. A 14 nap eltelte után a felhasználó nem tud bejelentkezni a regisztráció befejezéséig. A felhasználó 14 napos időszaka az első sikeres interaktív bejelentkezés után kezdődik a biztonsági alapértelmezések engedélyezése után.

### <a name="protecting-administrators"></a>Rendszergazdák védelme

Az emelt szintű hozzáféréssel rendelkező felhasználók nagyobb hozzáféréssel férnek hozzá a környezethez. Ezeknek a fiókoknak a nagy ereje miatt különös gondgal kell kezelni őket. Az emelt szintű fiókok védelmének javítására gyakran használt módszer, ha erősebb fiókellenőrzésre van szükség a bejelentkezéshez. Az Azure AD-ban erősebb fiókellenőrzést kaphat, ha többtényezős hitelesítést követel meg.

Az Azure AD Multi-Factor Authentication-regisztráció befejezése után a következő kilenc Azure AD-rendszergazdai szerepkörre lesz szükség a további hitelesítés végrehajtásához minden bejelentkezéskor:

- Globális rendszergazda
- SharePoint-rendszergazda
- Exchange-rendszergazda
- Feltételes hozzáférés rendszergazdája
- Biztonsági rendszergazda
- ügyfélszolgálati adminisztrátor
- Számlázási adminisztrátor
- Felhasználói rendszergazda
- hitelesítési rendszergazda

> [!WARNING]
> Győződjön meg arról, hogy a címtár legalább két fiókkal rendelkezik, amelyekhez globális rendszergazdai jogosultságok vannak hozzárendelve. Ez segít abban az esetben, ha egy globális rendszergazda ki van zárva. További részletekért tekintse meg a Vészelérési fiókok kezelése [az Azure AD-ban](../roles/security-emergency-access.md)cikket.

### <a name="protecting-all-users"></a>Az összes felhasználó védelme

Általában úgy gondoljuk, hogy csak a rendszergazdai fiókokhoz van szükség további hitelesítési rétegekre. A rendszergazdák széles körben hozzáférhetnek a bizalmas információkhoz, és az egész előfizetésre kiterjedő beállításokat módosíthatnak. A támadók azonban gyakran célként használják a végfelhasználókat. 

Miután ezek a támadók hozzáférést szereznek, hozzáférést kérhetnek a kiemelt információkhoz az eredeti fióktulajdonos nevében. Akár le is tölthetik a teljes könyvtárat, hogy adathalász támadásokat hajtsanak végre a teljes szervezeten. 

Az egyik gyakori módszer a védelem javítására minden felhasználó számára, ha mindenki számára erősebb fiókellenőrzésre van szükség, például többtényezős hitelesítésre. Miután a felhasználók befejezték a Multi-Factor Authentication regisztrációját, a rendszer további hitelesítést fog kérni, amikor szükséges. A rendszer elsősorban akkor kéri a felhasználókat, amikor új eszközzel vagy alkalmazással végeznek hitelesítést, vagy ha kritikus fontosságú szerepköröket és feladatokat végeznek. Ez a funkció az Azure AD-ban regisztrált összes alkalmazást védi, beleértve az SaaS-alkalmazásokat is.

### <a name="blocking-legacy-authentication"></a>Az örökölt hitelesítés letiltása

Annak érdekében, hogy a felhasználók könnyen hozzáférjenek a felhőalkalmazásokhoz, az Azure AD számos hitelesítési protokollt támogat, beleértve az örökölt hitelesítést is. *Az örökölt hitelesítés* egy olyan kifejezés, amely a következő által kért hitelesítési kérésre vonatkozik:

- Modern hitelesítést nem felhasználó ügyfelek (például Office 2010-ügyfelek).
- Bármely ügyfél, amely régebbi levelezési protokollokat használ, például IMAP, SMTP vagy POP3.

Napjainkban a legtöbb biztonsági rést veszélyeztető bejelentkezési kísérletek a régi hitelesítésből adnak vissza. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítést. A támadók akkor is hitelesíthetik magukat egy régebbi protokoll használatával, ha engedélyezve van a címtárban egy Multi-Factor Authentication-házirend, és megkerülik a többtényezős hitelesítést. 

Miután engedélyezte a biztonsági alapértelmezéseket a bérlőben, a régebbi protokoll által kért hitelesítési kérések le lesznek tiltva. A biztonsági alapértelmezések Exchange Active Sync alapszintű hitelesítést.

> [!WARNING]
> Az alapértelmezett biztonsági beállítások engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem régebbi hitelesítési protokollokat használnak. További információkért lásd: A régi [hitelesítéstől való eltúdás.](concept-fundamentals-block-legacy-authentication.md)

- [Többfunkciós eszköz vagy alkalmazás beállítása e-mail-küldéshez a Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Kiemelt műveletek védelme

A szervezetek a Azure Resource Manager API-val felügyelt különböző Azure-szolgáltatásokat használják, beleértve a következőket:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

A Azure Resource Manager kezelése kiemelt jogosultságokkal rendelkező művelet. Azure Resource Manager módosíthatja a bérlői szintű konfigurációkat, például a szolgáltatásbeállításokat és az előfizetések számlázását. Az egytényezős hitelesítés ki van téve a különböző támadásoknak, például az adathalászatnak és a szórásos jelszó szórásának. 

Fontos ellenőrizni a hozzáféréssel és frissítéssel Azure Resource Manager felhasználók identitását. Az identitásuk ellenőrzéséhez további hitelesítésre van szükség a hozzáférés engedélyezése előtt.

Miután engedélyezi az alapértelmezett biztonsági beállításokat a bérlőben, minden olyan felhasználónak, aki hozzáfér a Azure Portal, Azure PowerShell vagy az Azure CLI-hez, további hitelesítést kell végrehajtania. Ez a szabályzat minden olyan felhasználóra vonatkozik, aki hozzáfér Azure Resource Manager, akár rendszergazda, akár felhasználó. 

> [!NOTE]
> A 2017 előtti Exchange Online-bérlők modern hitelesítése alapértelmezés szerint le van tiltva. Annak érdekében, hogy elkerülje a bejelentkezési ciklus lehetőségét a bérlők hitelesítése során, engedélyeznie kell a [modern hitelesítést.](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> A Azure AD Connect fiók ki van zárva az alapértelmezett biztonsági beállításokból, és a rendszer nem kéri a többtényezős hitelesítésre való regisztrációt vagy a hitelesítés elvégzését. A szervezeteknek más célokra nem szabad használniuk ezt a fiókot.

## <a name="deployment-considerations"></a>Telepítési szempontok

Az alábbi további szempontok a biztonsági alapértelmezések telepítésével kapcsolatosak.

### <a name="authentication-methods"></a>Hitelesítési módszerek

Ezek az ingyenes biztonsági alapértelmezések lehetővé teszik az Azure AD Multi-Factor Authentication regisztrációját és használatát, amely csak **az Microsoft Authenticator használ értesítéseket használó alkalmazással.** A feltételes hozzáférés bármilyen hitelesítési módszer használatát lehetővé teszi, amit a rendszergazda engedélyez.

| Metódus | Alapértelmezett biztonsági szabályok | Feltételes hozzáférés |
| --- | --- | --- |
| Értesítés mobilalkalmazáson keresztül | X | X |
| Mobilalkalmazásból vagy hardvertokenből származó ellenőrzőkód | X** | X |
| Szöveges üzenet a telefonra |   | X |
| Hívás telefonra |   | X |
| Alkalmazásjelszavak. |   | X• |

- ** A felhasználók a Microsoft Authenticator ellenőrzőkódokat használhatnak, de csak az értesítési beállítással regisztrálhatnak.
- Az alkalmazásjelszavak csak a rendszergazdák által engedélyezett, felhasználónkénti MFA-ban érhetők el, örökölt hitelesítési forgatókönyvekkel.

### <a name="disabled-mfa-status"></a>Letiltott MFA-állapot

Ha a szervezet a felhasználóalapú Azure AD Multi-Factor Authentication korábbi felhasználója, ne riasztsa, ha a Multi-Factor Auth állapotlapját nézve nem látja a felhasználókat Engedélyezett vagy Kényszerítve állapotúként.   **A Disabled** (Letiltva) a megfelelő állapot az alapértelmezett biztonsági beállításokat vagy feltételes hozzáférésen alapuló Azure AD Multi-Factor Authenticationt használó felhasználók számára.

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáféréssel az alapértelmezett biztonsági beállításokhoz hasonló szabályzatokat konfigurálhat, de részletesebben is, beleértve a felhasználói kizárásokat is, amelyek nem érhetők el a biztonsági alapértékek között. Ha feltételes hozzáférést használ, és a környezetében engedélyezve vannak a feltételes hozzáférési szabályzatok, a biztonsági alapértelmezések nem lesznek elérhetők az Ön számára. Ha olyan licenccel rendelkezik, amely feltételes hozzáférést biztosít, de a környezetében nincsenek engedélyezve feltételes hozzáférési szabályzatok, a feltételes hozzáférési szabályzatok engedélyezéséig használhatja az alapértelmezett biztonsági beállításokat. Az Azure AD-licencelésről további információt az [Azure AD díjszabási oldalán talál.](https://azure.microsoft.com/pricing/details/active-directory/)

![Figyelmeztető üzenet arról, hogy a biztonsági alapértelmezések vagy a feltételes hozzáférés nem mindkettővel rendelkezik](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Az itt részletes útmutatók segítségével konfigurálhatja a feltételes hozzáféréssel a biztonsági alapértelmezések által engedélyezett szabályzatokkal egyenértékű szabályzatokat:

- [MFA megkövetelve a rendszergazdáktól](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA megkövetelése az Azure-beli felügyelethez](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Régi hitelesítés blokkolása](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA megkövetelése minden felhasználótól](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Regisztráció Azure AD MFA megkövetelve](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – Ehhez Azure AD Identity Protection P2 prémium szintű Azure AD szükséges.

## <a name="enabling-security-defaults"></a>Biztonsági alapértelmezések engedélyezése

Az alapértelmezett biztonsági beállítások engedélyezése a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)   rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse **meg Azure Active Directory**   >  **tulajdonságokat.**
1. Válassza **az Alapértelmezett biztonsági beállítások kezelése lehetőséget.**
1. Állítsa a **Biztonsági alapértelmezések engedélyezése** beállítást Igen **beállításra.**
1. Kattintson a **Mentés** gombra.

## <a name="disabling-security-defaults"></a>Biztonsági alapértelmezések letiltása

A biztonsági alapértelmezéseket lecserélő feltételes hozzáférési szabályzatok implementált szervezeteknek le kell tiltani az alapértelmezett biztonsági beállításokat. 

![Figyelmeztető üzenet: A feltételes hozzáférés engedélyezéséhez tiltsa le az alapértelmezett biztonsági beállításokat](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Az alapértelmezett biztonsági beállítások letiltása a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)   rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse **meg Azure Active Directory**   >  **tulajdonságokat.**
1. Válassza **az Alapértelmezett biztonsági beállítások kezelése lehetőséget.**
1. Állítsa az **Alapértelmezett biztonsági beállítások engedélyezése** kapcsolót a Nem **beállításra.**
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Gyakori feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)
