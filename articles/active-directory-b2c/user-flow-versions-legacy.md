---
title: A Azure Active Directory B2C korábbi felhasználói flow-verziói | Microsoft Docs
description: Ismerje meg a Azure Active Directory B2Cban elérhető felhasználói folyamatok örökölt verzióit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1fbe93c93b5ede2c6b031dab53a1450da473f802
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517803"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C korábbi felhasználói flow-verziói

> [!IMPORTANT]
> Ez a cikk a felhasználói folyamatok örökölt verziószámozási módszerét ismerteti, amely a felhasználói folyamatok v1 (termelésre kész) verzióit, valamint a V 1.1 és v2 (előzetes verzió) verzióját kínálja. Az Azure-beli nyilvános felhőn kívüli környezetek továbbra is ezt az örökölt verziószámozási módszert használják. Az Azure nyilvános felhőben ez a módszer [új **ajánlott** és **előzetes** verziójú verziókra](user-flow-versions.md)vált.
> 
A Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatai segítségével olyan általános [házirendeket](user-flow-overview.md) állíthat be, amelyek teljes mértékben leírják az ügyfelek identitásának élményét. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése.

Az alábbi táblázatban, kivéve, ha a felhasználói folyamat **ajánlottként** van azonosítva, az *előzetes* verzióban tekinthető meg. Az üzemi alkalmazások esetében csak az ajánlott felhasználói folyamatokat kell használnia.

## <a name="v1"></a>1. verzió

| Felhasználói folyamat | Ajánlott | Description |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Yes | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Profil szerkesztése | Yes | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés a ROPC használatával | No | Lehetővé teszi, hogy a felhasználó helyi fiókkal jelentkezzen be közvetlenül a natív alkalmazásokban (nincs szükség böngészőre). A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li></ul> |
| Bejelentkezés | No | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>Bejelentkezés letiltása</li><li>Jelszó alaphelyzetbe állításának kényszerítése</li><li>Bejelentkezés megtartása (KMSI)</ul><br>Ezzel a felhasználói folyamattal nem szabhatja testre a felhasználói felületet. |
| Regisztráció | No | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Yes | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul>|

## <a name="v11"></a>1.1-es verzió

| Felhasználói folyamat | Ajánlott | Description |
| --------- | ----------- | ----------- |
| Jelszó-visszaállítási v 1.1 | No | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után (az új lapelrendezés elérhető). A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |

## <a name="v2"></a>2. verzió

| Felhasználói folyamat | Ajánlott | Description |
| --------- | ----------- | ----------- |
| Jelszó alaphelyzetbe állítása v2 | No | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Profil szerkesztése v2 | Yes | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés v2-ben | No | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>Bejelentkezési oldal testreszabása</li></ul> |
| Regisztráció v2 | No | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés v2-ben | No | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjával. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |