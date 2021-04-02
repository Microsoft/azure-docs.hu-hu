---
title: 'Azure AD Connect: átmenő hitelesítés – hogyan működik | Microsoft Docs'
description: Ez a cikk bemutatja, hogyan működik Azure Active Directory átmenő hitelesítés
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, telepítési Active Directory, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996593"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory átmenő hitelesítés: technikai mély merülés
Ez a cikk áttekintést nyújt az Azure Active Directory (Azure AD) átmenő hitelesítésének működéséről. A részletes technikai és biztonsági tudnivalókat lásd a [biztonsági Deep Dive](how-to-connect-pta-security-deep-dive.md) -cikkben.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hogyan működik Azure Active Directory átmenő hitelesítés?

>[!NOTE]
>Az átmenő hitelesítés használatának előfeltétele, hogy a felhasználóknak Azure AD Connect használatával kell kiépíteni az Azure AD-t a helyszíni Active Directory. Az áteresztő hitelesítés nem vonatkozik a csak felhőalapú felhasználókra.

Amikor egy felhasználó megpróbál bejelentkezni egy Azure AD által védett alkalmazásba, és ha engedélyezve van az átmenő hitelesítés a bérlőn, a következő lépések történnek:

1. A felhasználó megpróbál elérni egy alkalmazást, például az [Outlook Web App](https://outlook.office365.com/owa/)alkalmazást.
2. Ha a felhasználó még nincs bejelentkezve, a rendszer átirányítja a felhasználót az Azure AD **felhasználói bejelentkezési** oldalára.
3. A felhasználó beírja a felhasználónevét az Azure AD bejelentkezési oldalára, majd kiválasztja a **tovább** gombot.
4. A felhasználó beírja a jelszavát az Azure AD bejelentkezési oldalára, majd kiválasztja a **Bejelentkezés** gombot.
5. Az Azure AD a bejelentkezésre irányuló kérés fogadásakor elhelyezi a felhasználónevet és a jelszót (a hitelesítési ügynökök nyilvános kulcsával titkosítva) egy várólistában.
6. A helyszíni hitelesítési ügynök lekéri a felhasználónevet és a titkosított jelszót a várólistából. Vegye figyelembe, hogy az ügynök nem kérdezi le gyakran a várólistáról érkező kérelmeket, de a kérelmeket egy előre kiállított állandó kapcsolaton keresztül kéri le.
7. Az ügynök a titkos kulcs használatával visszafejti a jelszót.
8. Az ügynök a normál Windows API-k használatával ellenőrzi a felhasználónevet és a jelszót Active Directoryon, ami hasonló módszer a Active Directory összevonási szolgáltatások (AD FS) (AD FS) által használt rendszerekhez. A Felhasználónév lehet a helyszíni alapértelmezett Felhasználónév, általában `userPrincipalName` vagy más, Azure ad Connect (más néven) konfigurált attribútum `Alternate ID` .
9. A helyszíni Active Directory tartományvezérlő (DC) kiértékeli a kérést, és visszaadja a megfelelő választ (sikeres, sikertelen, jelszó lejárt vagy felhasználó által zárolt) az ügynöknek.
10. A hitelesítési ügynök viszont visszaadja ezt a választ az Azure AD-nek.
11. Az Azure AD kiértékeli a választ, és szükség szerint válaszol a felhasználónak. Például az Azure AD vagy azonnal aláírja a felhasználót vagy az Azure AD-Multi-Factor Authenticationra vonatkozó kéréseket.
12. Ha a felhasználói bejelentkezés sikeres, a felhasználó elérheti az alkalmazást.

A következő ábra az összes összetevőt és a benne foglalt lépéseket szemlélteti:

![Átmenő hitelesítés](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Következő lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): megtudhatja, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Gyorskonfigurálás](how-to-connect-pta-quick-start.md): az Azure ad átmenő hitelesítésének megkezdése és futtatása.
- [Migrálás ad FSról áteresztő hitelesítésre](https://aka.ms/adfstoPTADP) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): konfigurálja az intelligens zárolási funkciót a bérlőn a felhasználói fiókok védetté tételéhez.
- [Gyakori](how-to-connect-pta-faq.md)kérdések: válaszok keresése a gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): megtudhatja, Hogyan oldhatók fel az áteresztő hitelesítési szolgáltatással kapcsolatos gyakori problémák.
- [Biztonsági](how-to-connect-pta-security-deep-dive.md)részletes információk: részletes technikai információkat kaphat az átmenő hitelesítés funkcióról.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md): További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): használja a Azure Active Directory fórumot az új szolgáltatásokra vonatkozó kérelmek fájljának megjelenítéséhez.

