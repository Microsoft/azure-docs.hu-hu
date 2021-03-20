---
title: Azure Active Directory biztonsági alapértelmezett beállítások
description: Biztonsági alapértelmezett házirendek, amelyek segítenek a szervezetek számára az Azure AD közös támadásai elleni védelemben
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efaf6060c0b09e071546038d9e30f2c8065059e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600140"
---
# <a name="what-are-security-defaults"></a>Mik azok a biztonsági alapértékek?

A biztonság kezelése nehéz lehet az identitással kapcsolatos gyakori támadásokkal, például a jelszó-spray, a visszajátszás és az adathalászat egyre népszerűbbvé válásával. A biztonsági alapértékek segítségével az előre konfigurált biztonsági beállításokkal könnyebben megvédheti szervezetét ezektől a támadásoktól:

- Az összes felhasználónak regisztrálnia kell az Azure AD-Multi-Factor Authentication.
- A többtényezős hitelesítés végrehajtásának megkövetelése a rendszergazdától.
- Örökölt hitelesítési protokollok blokkolása.
- Ha szükséges, a felhasználóknak többtényezős hitelesítést kell végezniük.
- A Kiemelt tevékenységek védelme, például a Azure Portalhoz való hozzáférés.

![Képernyőkép a Azure Portalról a biztonsági Alapértelmezések engedélyezésére szolgáló váltógomb használatával](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
A biztonsági alapbeállítások elérhetővé tételéről további részleteket az Alex Weinert blogbejegyzésében talál, amely a [biztonsági beállítások](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)megadását ismerteti.

## <a name="availability"></a>Rendelkezésre állás

A Microsoft mindenki számára elérhetővé teszi a biztonsági beállításokat. A cél annak biztosítása, hogy az összes szervezet külön díj nélkül engedélyezze az alapszintű biztonsági szintet. A biztonsági alapértelmezéseket be kell kapcsolni a Azure Portal. Ha a bérlőt 2019. október 22-én hozták létre, akkor lehetséges, hogy a biztonsági alapértékek már engedélyezve vannak a bérlőben. Az összes felhasználó védelme érdekében a biztonsági alapértelmezéseket a rendszer az összes létrehozott új bérlőre bevezeti.

### <a name="whos-it-for"></a>Kinek szól?

- Ha Ön olyan szervezet, amely szeretné javítani a biztonsági helyzetét, de nem tudja, hogyan vagy hol szeretné elindítani, a biztonsági alapértékek az Ön számára.
- Ha Ön olyan szervezet, amely az Azure Active Directory licencelés ingyenes szintjét használja, a biztonsági beállítások a következők:.

### <a name="who-should-use-conditional-access"></a>Kinek kell használnia a feltételes hozzáférést?

- Ha olyan szervezet, amely jelenleg feltételes hozzáférési szabályzatokat használ a jelek összekapcsolásához, a döntések meghozatalához és a szervezeti szabályzatok betartatásához, a biztonsági alapértékek valószínűleg nem megfelelőek. 
- Ha prémium szintű Azure Active Directory licenccel rendelkező szervezet, a biztonsági alapértékek valószínűleg nem megfelelőek Önnek.
- Ha a szervezete összetett biztonsági követelményekkel rendelkezik, érdemes megfontolnia a feltételes hozzáférést.

## <a name="policies-enforced"></a>Szabályzatok érvénybe léptetése

### <a name="unified-multi-factor-authentication-registration"></a>Egységesített Multi-Factor Authentication regisztráció

A bérlő összes felhasználójának regisztrálnia kell a többtényezős hitelesítést (MFA) az Azure AD-Multi-Factor Authentication formájában. A felhasználók 14 napig regisztrálhatnak az Azure AD-Multi-Factor Authenticationre a Microsoft Authenticator alkalmazás használatával. A 14 nap eltelte után a felhasználó nem fog tudni bejelentkezni, amíg be nem fejeződik a regisztráció. A felhasználók 14 napos időszaka az első sikeres interaktív bejelentkezés után kezdődik az alapértelmezett biztonsági beállítások engedélyezése után.

### <a name="protecting-administrators"></a>A rendszergazdák védelme

A Kiemelt hozzáféréssel rendelkező felhasználók nagyobb hozzáférést biztosítanak a környezethez. Ezeknek a fiókoknak a kihasználása miatt speciális gondossággal kell kezelnie őket. A rendszerjogosultságú fiókok védelmének egyik gyakori módszere a fiók-ellenőrzés erősebb formájának megkövetelése a bejelentkezéshez. Az Azure AD-ben a többtényezős hitelesítés megkövetelésével erősebb fiókokat lehet ellenőrizni.

Az Azure AD Multi-Factor Authentication való regisztrációt követően a következő kilenc Azure AD-rendszergazdai szerepkörrel kell elvégeznie a további hitelesítést minden alkalommal, amikor bejelentkeznek:

- Globális rendszergazda
- SharePoint-rendszergazda
- Exchange-rendszergazda
- Feltételes hozzáférésű rendszergazda
- Biztonsági rendszergazda
- Segélyszolgálat rendszergazdája
- Számlázási adminisztrátor
- Felhasználói rendszergazda
- Hitelesítés rendszergazdája

### <a name="protecting-all-users"></a>Az összes felhasználó védelme

Általában úgy gondoljuk, hogy a rendszergazdai fiókok az egyetlen olyan fiókok, amelyeknek további hitelesítési rétegre van szükségük. A rendszergazdák széles körű hozzáféréssel rendelkeznek a bizalmas adatokhoz, és módosíthatják az előfizetési szintű beállításokat. A támadók azonban gyakran célozzák meg a végfelhasználók számára. 

Miután ezek a támadók hozzáférnek, az eredeti fiók tulajdonosának nevében kérhetnek hozzáférést az emelt szintű információhoz. Akár le is tölthetik a teljes könyvtárat, hogy elvégezzenek egy adathalászat elleni támadást a teljes szervezetben. 

Az egyik gyakori módszer az, hogy javítsa az összes felhasználó védelmét, hogy mindenki számára a fiókok ellenőrzésének erősebb formáját kell megkövetelni, például Multi-Factor Authentication. Miután a felhasználók beMulti-Factor Authenticationák a regisztrációt, a rendszer szükség esetén további hitelesítésre kéri. A felhasználókat a rendszer először az új eszköz vagy alkalmazás használatával történő hitelesítéskor, illetve kritikus szerepkörök és feladatok végrehajtásakor fogja kérni. Ez a funkció védi az Azure AD-ben regisztrált összes alkalmazást, beleértve az SaaS-alkalmazásokat is.

### <a name="blocking-legacy-authentication"></a>Örökölt hitelesítés blokkolása

Ahhoz, hogy a felhasználók könnyen hozzáférjenek a felhőalapú alkalmazásokhoz, az Azure AD számos hitelesítési protokollt támogat, beleértve az örökölt hitelesítést is. Az *örökölt hitelesítés* olyan kifejezés, amely a által végzett hitelesítési kérelemre hivatkozik:

- A modern hitelesítést nem használó ügyfelek (például az Office 2010-ügyfél).
- Minden olyan ügyfél, amely régebbi levelezési protokollokat használ, mint például az IMAP, az SMTP vagy a POP3.

Napjainkban a kiegyezéses bejelentkezési kísérletek többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a Multi-Factor Authentication. Ha a címtárban engedélyezve van a Multi-Factor Authentication szabályzat, a támadó egy régebbi protokoll használatával is hitelesítheti magát, és megkerülheti a Multi-Factor Authentication. 

Ha a biztonsági alapértékek engedélyezve vannak a bérlőben, a régebbi protokoll által végrehajtott összes hitelesítési kérelem le lesz tiltva. A biztonsági alapértékek blokkolja Exchange Active Sync egyszerű hitelesítést.

> [!WARNING]
> A biztonsági beállítások engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem használnak régebbi hitelesítési protokollokat. További információkért lásd: [a korábbi hitelesítéstől való elmozdulás](concept-fundamentals-block-legacy-authentication.md).

- [Többfunkciós eszköz vagy alkalmazás beállítása az e-mailek küldéséhez Microsoft 365 használatával](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>A Kiemelt műveletek védelme

A szervezetek a Azure Resource Manager API-n keresztül felügyelt különböző Azure-szolgáltatásokat használják, beleértve a következőket:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

A Azure Resource Manager használata a szolgáltatások kezeléséhez magas jogosultsági szintű művelet. Azure Resource Manager megváltoztathatja a bérlői szintű konfigurációkat, például a szolgáltatás beállításait és az előfizetés számlázását. Az egytényezős hitelesítés számos különböző támadáshoz, például az adathalászathoz és a jelszó-permetezéshez van kitéve. 

Fontos ellenőrizni azon felhasználók identitását, akik szeretnének hozzáférni Azure Resource Manager és frissíteni a konfigurációkat. A hozzáférés engedélyezése előtt ellenőrizze az identitását további hitelesítés megkövetelésével.

Miután engedélyezte a biztonsági alapértelmezett beállításokat a bérlőben, a Azure Portalt, Azure PowerShellt vagy az Azure CLI-t elérő bármely felhasználónak további hitelesítést kell végeznie. Ez a szabályzat minden olyan felhasználóra vonatkozik, aki Azure Resource Managerhoz fér hozzá, függetlenül attól, hogy rendszergazda vagy felhasználó. 

> [!NOTE]
> Az előzetes 2017 Exchange Online-bérlők alapértelmezés szerint le vannak tiltva modern hitelesítéssel. Ha nem szeretné, hogy a bejelentkezési hurok a fenti bérlők hitelesítése közben ne legyen engedélyezve, engedélyeznie kell a [modern hitelesítést](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> A Azure AD Connect szinkronizálási fiók ki van zárva a biztonsági alapbeállításokból, és a rendszer nem kéri a többtényezős hitelesítés regisztrálását vagy elvégzését. A szervezetek más célokra nem szabad ezt a fiókot használni.

## <a name="deployment-considerations"></a>Telepítési szempontok

A következő további szempontok a biztonsági Alapértelmezések telepítésével kapcsolatosak.

### <a name="authentication-methods"></a>Hitelesítési módszerek

Ezek az ingyenes biztonsági beállítások lehetővé teszik az Azure AD-Multi-Factor Authentication regisztrációját és használatát, és **csak az Microsoft Authenticator alkalmazást használják az értesítések használatával**. A feltételes hozzáférés lehetővé teszi bármely hitelesítési módszer használatát, amelyet a rendszergazda az engedélyezéshez választ.

| Metódus | Alapértelmezett biztonsági szabályok | Feltételes hozzáférés |
| --- | --- | --- |
| Értesítés a Mobile App használatával | X | X |
| Ellenőrző kód a Mobile App vagy a Hardware tokenből | X * * | X |
| SMS-üzenet a telefonra |   | X |
| Telefonos hívás |   | X |
| Alkalmazásjelszavak. |   | X * * * |

- * * A felhasználók a Microsoft Authenticator alkalmazásból is használhatnak ellenőrző kódokat, de csak az értesítési lehetőséggel regisztrálhatnak.
- Az alkalmazás jelszavai csak akkor érhetők el felhasználónkénti MFA-ban, ha az örökölt hitelesítési forgatókönyvek csak a rendszergazdák által engedélyezettek.

### <a name="disabled-mfa-status"></a>Letiltott MFA-állapot

Ha a szervezete a felhasználónkénti Azure AD-Multi-Factor Authentication korábbi felhasználója, ne felébressze, hogy az **engedélyezett** vagy **kényszerített** állapotú felhasználók ne lássák a multi-Factor Auth állapota lapot. A **Letiltva** érték a biztonsági alapértékeket vagy a feltételes hozzáférésű Azure AD-multi-Factor Authentication használó felhasználók számára megfelelő állapot.

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáférés használatával a biztonsági alapértékekhez hasonló házirendeket konfigurálhat, de részletesebben is megadhatja a felhasználói kivételeket, amelyek nem érhetők el a biztonsági alapbeállításokban. Ha feltételes hozzáférést használ, és a környezetében engedélyezve vannak feltételes hozzáférési szabályzatok, a biztonsági beállítások nem lesznek elérhetők az Ön számára. Ha olyan licenccel rendelkezik, amely feltételes hozzáférést biztosít, de nem rendelkezik a környezetben engedélyezett feltételes hozzáférési szabályzatokkal, akkor a feltételes hozzáférési szabályzatok engedélyezése előtt üdvözli a biztonsági beállítások használatát. Az Azure AD-licenceléssel kapcsolatos további információkért tekintse meg az [Azure ad díjszabási oldalát](https://azure.microsoft.com/pricing/details/active-directory/).

![Figyelmeztető üzenet arról, hogy a biztonsági alapértelmezett beállítások vagy a feltételes hozzáférés nem mindkét esetben](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Az alábbiakban részletes útmutatók láthatók arról, hogy a feltételes hozzáférés hogyan konfigurálható a biztonsági alapértékek által engedélyezett szabályzatoknak megfelelő házirendekkel:

- [MFA megkövetelése rendszergazdák számára](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA megkövetelése az Azure-beli felügyelethez](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Örökölt hitelesítés tiltása](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA megkövetelése minden felhasználótól](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- Az [Azure ad MFA-regisztráció megkövetelése](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – a prémium szintű Azure AD P2 Azure ad Identity Protection részét igényli.

## <a name="enabling-security-defaults"></a>Biztonsági alapértékek engedélyezése

A biztonsági alapértelmezett beállítások engedélyezése a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)   biztonsági rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory**   >  **tulajdonságokat**.
1. Válassza a **biztonsági beállítások kezelése** lehetőséget.
1. Állítsa a **biztonsági Alapértelmezések engedélyezése** kapcsolót **Igen** értékre.
1. Kattintson a **Mentés** gombra.

## <a name="disabling-security-defaults"></a>Biztonsági alapértékek letiltása

Azok a szervezetek, amelyek a biztonsági alapértékeket lecserélő feltételes hozzáférési házirendek megvalósítását választják, le kell tiltaniuk a biztonsági alapértékeket. 

![Figyelmeztető üzenet a feltételes hozzáférés engedélyezéséhez tiltsa le a biztonsági alapértelmezéseket](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

A biztonsági alapértelmezett beállítások letiltása a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com)   biztonsági rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory**   >  **tulajdonságokat**.
1. Válassza a **biztonsági beállítások kezelése** lehetőséget.
1. Állítsa be a **biztonsági beállítások engedélyezése** beállítást a **nem** értékre.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Általános feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)
