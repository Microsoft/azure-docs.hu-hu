---
title: 'Azure AD Connect: átmenő hitelesítés | Microsoft Docs'
description: Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítését, valamint az Azure AD-bejelentkezések használatát mutatja be a felhasználók jelszavainak a helyszíni Active Directory való érvényesítésével.
services: active-directory
keywords: Mi az Azure AD Connect átmenő hitelesítés, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279465"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Mi az Azure Active Directory átmenő hitelesítés?

Azure Active Directory (Azure AD) átmenő hitelesítés lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be mind a helyszíni, mind a felhőalapú alkalmazásokba. Ezzel a szolgáltatással javítható a felhasználók felhasználói élménye, mivel eggyel kevesebb jelszót kell megjegyezni, továbbá csökkenthetők az informatikai ügyfélszolgálat költségei, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezési adataikat. Amikor a felhasználók bejelentkeznek az Azure AD-be, a szolgáltatás közvetlenül a helyszíni Active Directory ellenőrzi a felhasználók jelszavát.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ez a funkció az [Azure ad Password hash-szinkronizálás](how-to-connect-password-hash-synchronization.md)alternatívája, amely ugyanazt az előnyt nyújtja a felhőalapú hitelesítésnek a szervezeteknek. Azonban bizonyos szervezetek, akik szeretnék érvényesíteni a helyszíni Active Directory biztonsági és jelszavas házirendeket, dönthetnek az átmenő hitelesítés használatával. Tekintse át [ezt az útmutatót](./choose-ad-authn.md) a különböző Azure ad bejelentkezési módszereinek összehasonlításához, és hogyan választhatja ki a szervezete megfelelő bejelentkezési módszerét.

![Azure AD átmenő hitelesítés](./media/how-to-connect-pta/pta1.png)

Az átmenő hitelesítés a [zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md) funkcióval kombinálható. Így ha a felhasználók a vállalati hálózaton belül érik el a vállalati számítógépeken lévő alkalmazásokat, nem kell beírniuk a jelszavukat a bejelentkezéshez.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Az Azure AD átmenő hitelesítés használatának legfontosabb előnyei

- *Nagyszerű felhasználói élmény*
  - A felhasználók ugyanazt a jelszót használják a helyszíni és a felhőalapú alkalmazásba való bejelentkezéshez.
  - A felhasználók kevesebb időt fordítanak az informatikai ügyfélszolgálatnak a jelszóval kapcsolatos problémák megoldására.
  - A felhasználók önkiszolgáló [jelszavas kezelési](../authentication/concept-sspr-howitworks.md) feladatokat végezhetnek a felhőben.
- *Egyszerűen üzembe helyezhető & felügyelet*
  - Nincs szükség bonyolult helyszíni telepítésekre vagy hálózati konfigurációra.
  - Csak egy egyszerűsített ügynököt kell telepíteni a helyszínen.
  - Nincs felügyeleti terhelés. Az ügynök automatikusan megkapja a javításokat és hibajavításokat.
- *Biztonságos*
  - A helyszíni jelszavakat a rendszer soha nem tárolja semmilyen formában a felhőben.
  - Az [Azure ad feltételes hozzáférési szabályzatok](../conditional-access/overview.md), például a multi-Factor Authentication (MFA) és az [örökölt hitelesítés blokkolásával](../conditional-access/concept-conditional-access-conditions.md) , valamint a [találgatásos jelszó elleni támadások kiszűrésével](../authentication/howto-password-smart-lockout.md)védi a felhasználói fiókokat.
  - Az ügynök csak a hálózatán belüli kimenő kapcsolatokat teszi elérhetővé. Ezért nincs szükség arra, hogy az ügynököt egy peremhálózati hálózatban, más néven DMZ-ben telepítse.
  - Az ügynök és az Azure AD közötti kommunikáció tanúsítványalapú hitelesítés használatával van védve. Ezeket a tanúsítványokat az Azure AD minden hónapban automatikusan megújítja.
- *Magas rendelkezésre állású*
  - További ügynökök több helyszíni kiszolgálóra is telepíthetők a bejelentkezési kérések magas rendelkezésre állásának biztosításához.

## <a name="feature-highlights"></a>A funkciók Kiemelt jellemzői

- A lehetővé teszi a felhasználók bejelentkezését az összes webböngésző-alapú alkalmazásba és a [modern hitelesítést](https://aka.ms/modernauthga)használó Microsoft Office ügyfélalkalmazásokba.
- A bejelentkezési felhasználónevek lehetnek a helyszíni alapértelmezett felhasználónevek ( `userPrincipalName` ) vagy a Azure ad Connect konfigurált más attribútumok (más néven `Alternate ID` ).
- A szolgáltatás zökkenőmentesen működik együtt a feltételes hozzáférési funkciókkal, mint például a Multi-Factor Authentication (MFA) a felhasználók biztonságossá [tételéhez](../conditional-access/overview.md) .
- Integrált a felhőalapú [önkiszolgáló jelszavas felügyelettel](../authentication/concept-sspr-howitworks.md), beleértve a jelszó visszaírási a helyszíni Active Directory és jelszavas védelmet a gyakran használt jelszavak betiltásával.
- A többerdős környezetek akkor támogatottak, ha erdőszintű megbízhatósági kapcsolat van az AD-erdők között, és ha a névutótagok útválasztása megfelelően van konfigurálva.
- Ez egy ingyenes funkció, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.
- [Azure ad Connect](whatis-hybrid-identity.md)használatával engedélyezhető.
- Egy egyszerűsített helyszíni ügynököt használ, amely figyeli és válaszol a jelszó-ellenőrzési kérelmekre.
- Több ügynök telepítése biztosítja a bejelentkezési kérések magas rendelkezésre állását.
- A felhőben [védi](../authentication/howto-password-smart-lockout.md) a helyszíni fiókokat a találgatásos támadásokkal szemben.

## <a name="next-steps"></a>Következő lépések

- Rövid [útmutató – az](how-to-connect-pta-quick-start.md) Azure ad átmenő hitelesítésének megkezdése és futtatása.
- [Migrálás ad FSról áteresztő hitelesítésre](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md) – az intelligens zárolási képesség konfigurálása a bérlőn a felhasználói fiókok védetté tételéhez.
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md) – megtudhatja, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Technikai](how-to-connect-pta-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [Gyakori kérdések](how-to-connect-pta-faq.md) – válaszok a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- A [Biztonság](how-to-connect-pta-security-deep-dive.md) részletes bemutatása – a szolgáltatással kapcsolatos részletesebb technikai információk.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md) – további információ a kiegészítő szolgáltatásról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.