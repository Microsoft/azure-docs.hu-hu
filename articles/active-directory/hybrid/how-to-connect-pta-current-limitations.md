---
title: 'Azure AD Connect: átmenő hitelesítés – jelenlegi korlátozások | Microsoft Docs'
description: Ez a cikk a Azure Active Directory (Azure AD) átmenő hitelesítésének aktuális korlátozásait ismerteti
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280196"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory átmenő hitelesítés: jelenlegi korlátozások

>[!IMPORTANT]
>Az Azure Active Directory (Azure AD) átmenő hitelesítés ingyenes szolgáltatás, és nincs szüksége az Azure AD fizetős kiadásaira a használatához. Az átmenő hitelesítés csak az Azure AD világméretű példányában érhető el, nem pedig a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) vagy a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek támogatottak:

- Felhasználói bejelentkezések a webböngésző-alapú alkalmazásokhoz.
- Felhasználói bejelentkezések az Outlook-ügyfeleknek örökölt protokollok, például Exchange ActiveSync, EAS, SMTP, POP és IMAP használatával.
- Felhasználói bejelentkezések a [modern hitelesítést](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)támogató örökölt Office-ügyfélalkalmazások és Office-alkalmazások számára: Office 2013 és 2016 verzió.
- Felhasználói bejelentkezések az örökölt protokoll-alkalmazásokhoz, például a PowerShell 1,0-es és egyéb verzióihoz.
- Azure AD-csatlakozások Windows 10-es eszközökhöz.
- Multi-Factor Authentication alkalmazás jelszavai.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek _nem_ támogatottak:

- A [kiszivárgott hitelesítő adatokkal](../identity-protection/overview-identity-protection.md)rendelkező felhasználók észlelése.
- Azure AD Domain Services jelszó-kivonatolási szinkronizálást kell engedélyezni a bérlőn. Az _átmenő hitelesítést használó_ bérlők ezért nem működnek olyan helyzetekben, amelyeknek Azure ad domain Servicesra van szükségük.
- Az átmenő hitelesítés nincs integrálva a [Azure ad Connect Healthba](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Megkerülő megoldásként _csak_ a nem támogatott forgatókönyvek esetében (Azure ad Connect Health integráció kivételével) engedélyezze a jelszó-kivonatok szinkronizálását a Azure ad Connect varázsló [választható szolgáltatások](how-to-connect-install-custom.md#optional-features) lapján.
> 
> [!NOTE]
> A jelszó-kivonat szinkronizálásának engedélyezése lehetőséget biztosít a feladatátvétel hitelesítésére, ha a helyszíni infrastruktúra megszakad. Ez a feladatátvétel az átmenő hitelesítéstől a jelszó-kivonat szinkronizálására nem automatikus. A bejelentkezési módszert manuálisan kell átváltania Azure AD Connect használatával. Ha a Azure AD Connect-t futtató kiszolgáló leáll, segítségre van szüksége Microsoft ügyfélszolgálata az átmenő hitelesítés kikapcsolásához.

## <a name="next-steps"></a>Következő lépések
- [Gyors üzembe helyezés](how-to-connect-pta-quick-start.md): az Azure ad átmenő hitelesítésének megkezdése.
- [Migrálás ad FSról áteresztő hitelesítésre](https://aka.ms/ADFSTOPTADPDownload) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): megtudhatja, hogyan konfigurálhatja az intelligens zárolási funkciót a bérlőn a felhasználói fiókok védetté tételéhez.
- [Technikai](how-to-connect-pta-how-it-works.md)részletes információ: az átmenő hitelesítés funkciójának megismerése.
- [Gyakori kérdések](how-to-connect-pta-faq.md): válaszokat talál az átmenő hitelesítés szolgáltatással kapcsolatos gyakori kérdésekre.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): megtudhatja, Hogyan oldhatók fel az áteresztő hitelesítési szolgáltatással kapcsolatos gyakori problémák.
- [Biztonsági](how-to-connect-pta-security-deep-dive.md)részletes információk: részletes technikai információkat kaphat az átmenő hitelesítés funkcióról.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md): További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): használja a Azure Active Directory fórumot az új szolgáltatásokra vonatkozó kérelmek fájljának megjelenítéséhez.
