---
title: Az alkalmazás regisztrációs portáljának referenciája | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft app Registration portál funkcióinak leírása.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1699aad41d8a5da153199f79f65af67bd04c6b2c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966095"
---
# <a name="app-registration-reference"></a>Alkalmazásregisztráció-referencia

Ez a dokumentum a Azure Portal [Alkalmazásregisztrációk](https://aka.ms/appregistrations) felületén található különféle funkciók kontextusát és leírásait tartalmazza.

## <a name="my-applications-or-converged-applications"></a>Saját alkalmazások vagy konvergens alkalmazások

Ez a lista tartalmazza az összes, a Microsoft Identity platform (v 2.0) végponttal való használatra regisztrált alkalmazást. Ezek az alkalmazások személyes Microsoft-fiókokkal és munkahelyi/iskolai fiókokkal is bejelentkezhetnek a felhasználóktól Azure Active Directory. További információ az Identity platform végpontról: [v 2.0 – Áttekintés](active-directory-appmodel-v2-overview.md). Ezek az alkalmazások a Microsoft-fiók hitelesítési végponttal való integrálására is használhatók `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Csak Azure AD-alkalmazások

Ez a lista tartalmazza az Azure AD 1.0-s verziójának végpontján regisztrált összes alkalmazást. Ezeknek az alkalmazásoknak csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be Azure Active Directoryból. Ez a lista olyan alkalmazásokat tartalmaz, amelyek a [Azure Portal](https://portal.azure.com) **Alkalmazásregisztrációk** tapasztalatai alapján lettek regisztrálva.

## <a name="live-sdk-applications"></a>Élő SDK-alkalmazások

Ez a lista tartalmazza az összes olyan alkalmazást, amely kizárólag a Microsoft-fiók használatára van regisztrálva. Nincsenek engedélyezve a Azure Active Directorysal való használatra. Itt találja azokat az alkalmazásokat, amelyek korábban már regisztrálva lettek a MSA fejlesztői portálon a `https://account.live.com/developers/applications`címen. Az `https://account.live.com/developers/applications`on korábban végrehajtott összes funkció most már [Alkalmazásregisztrációkon](https://aka.ms/appregistrations)is elvégezhető.

## <a name="application-secrets"></a>Titkos alkalmazáskulcsok

Az alkalmazás titkos kulcsai olyan hitelesítő adatok, amelyek lehetővé teszik, hogy az alkalmazás megbízható [ügyfél-hitelesítést](https://tools.ietf.org/html/rfc6749#section-2.3) végezzen az Azure ad-vel. Az OAuth & OpenID Connect esetében az alkalmazás titkos kulcsát általában `client_secret`nevezzük. A v 2.0 protokollban minden olyan alkalmazás, amely a biztonsági jogkivonatot egy webcímes helyen (`https` sémán keresztül) fogadja, a biztonsági jogkivonat beváltásakor az Azure AD-hoz való azonosításához az alkalmazás titkos kulcsát kell használnia. Továbbá minden olyan natív ügyfél, amely az eszközön jogkivonatokat fogad, nem fogja tudni használni az alkalmazás titkos kulcsát az ügyfél-hitelesítés végrehajtásához. Ez nem akadályozza meg a titkok tárolását a nem biztonságos környezetekben.

Minden alkalmazás tartalmazhat két érvényes alkalmazás-titkot egy adott időpontban. Két titok fenntartásával lehetősége van arra, hogy az alkalmazás teljes környezetében rendszeres időközönként átváltást végezzen. Miután áttelepítette az alkalmazást egy új titokba, törölheti a régi titkot, és kiépítheti az újat.

Jelenleg a regisztrációs portálon csak két típusú alkalmazás-titok engedélyezett. Ha az **új jelszó létrehozása** lehetőséget választja, a rendszer létrehoz egy közös titkot a megfelelő adattárban, amelyet az alkalmazásban használhat. Az **új kulcspár létrehozása** lehetőség választásával egy új, nyilvános/titkos kulcspár hozható létre, amely letölthető, és az ügyfél-hitelesítéshez használható az Azure ad-ben. A **nyilvános kulcs feltöltésének** kiválasztása lehetővé teszi a saját nyilvános/titkos kulcspár használatát.
Egy nyilvános kulcsot tartalmazó tanúsítványt kell feltöltenie.

## <a name="profile"></a>Profil

Az alkalmazás regisztrációs portál profil szakasza segítségével testre szabhatja az alkalmazás bejelentkezési lapját. Ekkor megváltoztathatja a bejelentkezési oldal alkalmazás emblémáját, a szolgáltatási URL-címet és az adatvédelmi nyilatkozat URL-címét. Az emblémának transzparens 48 x 48 vagy 50 x 50 képpont-képnek kell lennie, amely legalább 15 KB méretű GIF, PNG vagy JPEG formátumú. Próbálja meg módosítani az értékeket, és tekintse meg az eredményül kapott bejelentkezési oldalt!

## <a name="live-sdk-support"></a>Élő SDK-támogatás

Ha engedélyezi az "élő SDK-támogatás" lehetőséget, az Ön által létrehozott összes alkalmazás-titok az Azure AD-és a Microsoft-fiók adattárolókban lesz kiépítve. Ez lehetővé teszi, hogy az alkalmazás közvetlenül integrálható legyen a Microsoft account Service (login.live.com) szolgáltatással. Ha közvetlenül a Microsoft-fiókkal szeretne létrehozni egy alkalmazást (a 2.0-s végpont használata helyett), akkor győződjön meg arról, hogy az élő SDK-támogatás engedélyezve van.

Az élő SDK-támogatás letiltása biztosítja, hogy az alkalmazás titkos kulcsa csak az Azure AD-adattárba legyen beírva. Az Azure AD-adattár olyan nagyvállalati szintű szabályozásokat tartalmaz, amelyek lehetővé teszik bizonyos szabványok, például a FISMA megfelelőség teljesítését. Ha engedélyezi az élő SDK támogatását, előfordulhat, hogy az alkalmazás nem fog megfelelni ezeknek a szabványoknak.

Ha csak a v 2.0-s végpontot szeretné használni, nyugodtan letilthatja az élő SDK-támogatást.
