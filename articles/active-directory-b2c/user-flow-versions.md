---
title: A felhasználói folyamatok verziói a Azure Active Directory B2Cban | Microsoft Docs
description: A Azure Active Directory B2Cban elérhető felhasználói folyamatok verzióinak megismerése.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517854"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>A felhasználói folyamatok verziói Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) felhasználói folyamatai segítségével olyan általános [házirendeket](user-flow-overview.md) állíthat be, amelyek teljes mértékben leírják az ügyfelek identitásának élményét. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése. Az alábbi táblázat a Azure AD B2Cban elérhető felhasználói folyamatokat ismerteti.

> [!IMPORTANT]
> Megváltoztattuk a felhasználói folyamatok verzióinak elnevezéseit. Korábban V1 (az éles környezetben való használatra kész) verziókat, valamint V1.1 és V2 (előzetes) verziókat kínáltunk. Most összevontuk a felhasználói folyamatokat két verzióra:
>
>- Az **ajánlott** felhasználói folyamatok a felhasználói folyamatok új előzetes verzióit jelentik. Alaposan tesztelték és egyesítik az örökölt **v2** és a **v 1.1** verzió összes funkcióját. Az új javasolt felhasználói folyamatok továbbra is megmaradnak és frissülnek. Ha áthelyezi ezeket az új ajánlott felhasználói folyamatokat, hozzáférhet a kiadott új funkciókhoz.
>- A korábban **v1**-ként ismert **általános** felhasználói folyamatok általánosan elérhetők, a termelésre kész felhasználói folyamatok. Ha a felhasználói folyamatok kritikus fontosságúak, és a nagymértékben stabil verzióktól függenek, továbbra is használhatja a normál felhasználói folyamatokat, és felismerheti, hogy ezek a verziók nem lesznek karbantartva és frissítve.
>
>Az összes korábbi előzetes verziójú felhasználói folyamat (V 1.1 és v2) a 2021-es **augusztus 1-től** az elavult elérési úton van. Ahol csak lehetséges, javasoljuk, hogy a lehető leghamarabb [váltson az új **javasolt** verzióra](#how-to-switch-to-a-new-recommended-user-flow) , így mindig kihasználhatja a legújabb funkciókat és frissítéseket. *Ezek a módosítások csak az Azure nyilvános felhőre vonatkoznak. Más környezetek továbbra is a [korábbi felhasználói folyamatok verziószámozását](user-flow-versions-legacy.md)használják.*

## <a name="recommended-user-flows"></a>Ajánlott felhasználói folyamatok

Az ajánlott felhasználói folyamatok előzetes verzióként használhatók, amelyek az örökölt v2 és V 1.1 képességekkel rendelkező új szolgáltatásokat ötvözik. Továbbra is a javasolt felhasználói folyamatok lesznek karbantartva és frissítve.

| Felhasználói folyamat | Leírás |
| --------- | ----------- |
| Jelszó alaphelyzetbe állítása (előzetes verzió) | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Profil szerkesztése (előzetes verzió) | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés (előzetes verzió) | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>Bejelentkezési oldal testreszabása</li></ul> |
| Regisztráció (előzetes verzió) | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés (előzetes verzió) | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjával. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Korhatáralapú hozzáférés-korlátozás](age-gating.md)</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Normál felhasználói folyamatok

Az általános jogú felhasználói folyamatok (korábbi nevén v1) általánosan elérhetők, éles használatra kész felhasználói folyamatok. A normál felhasználói folyamatok frissítése nem történik meg.

| Felhasználói folyamat | Leírás |
| --------- | ----------- | ----------- |
| Új jelszó létrehozása | Lehetővé teszi, hogy a felhasználó új jelszót válasszon az e-mailek ellenőrzése után. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Profil szerkesztése | Lehetővé teszi a felhasználó számára a felhasználói attribútumok konfigurálását. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li></ul> |
| Bejelentkezés | Lehetővé teszi, hogy a felhasználó bejelentkezzen a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>Bejelentkezés letiltása</li><li>Jelszó alaphelyzetbe állításának kényszerítése</li><li>Bejelentkezés megtartása (KMSI)</ul><br>Ezzel a felhasználói folyamattal nem szabhatja testre a felhasználói felületet. |
| Regisztráció | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul> |
| Regisztráció és bejelentkezés | Lehetővé teszi, hogy a felhasználó fiókot hozzon létre, vagy jelentkezzen be a fiókjába. A felhasználói folyamat használatával a következőket állíthatja be: <ul><li>[Többtényezős hitelesítés](multi-factor-authentication.md)</li><li>[Jogkivonat élettartama](tokens-overview.md)</li><li>Jogkivonat-kompatibilitási beállítások</li><li>Munkamenet viselkedése</li><li>[Jelszó bonyolultságára vonatkozó követelmények](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Váltás egy új, javasolt felhasználói folyamatra

Ha a felhasználói folyamat örökölt verziójáról szeretne váltani az új **ajánlott** előzetes verzióra, kövesse az alábbi lépéseket:

1. Hozzon létre egy új felhasználói folyamatra vonatkozó házirendet az [oktatóanyag: felhasználói folyamatok létrehozása a Azure Active Directoryban](tutorial-create-user-flows.md)című témakör lépéseit követve. A felhasználói folyamat létrehozásakor válassza ki a **javasolt** verziót.

3. Konfigurálja az új felhasználói folyamatot ugyanazokkal a beállításokkal, mint amelyeket az örökölt szabályzatban konfiguráltak.

4. Frissítse az alkalmazás bejelentkezési URL-címét az újonnan létrehozott szabályzatba.

5. Miután tesztelte a felhasználói folyamatot, és megerősítette, hogy működik, törölje az örökölt felhasználói folyamatot a következő lépések végrehajtásával:
   1. A Azure AD B2C bérlő áttekintő menüjében válassza a **felhasználói folyamatok** elemet.
   2. Keresse meg a törölni kívánt felhasználói folyamatot.
   3. Az utolsó oszlopban válassza a helyi menüt (**...**), majd válassza a **Törlés** lehetőséget.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Továbbra is Létrehozhatok örökölt v2 és V 1.1 felhasználói folyamatokat?

Az örökölt v2 és a V 1.1 verziók alapján nem fog tudni új felhasználói folyamatokat létrehozni, de továbbra is olvashatja, frissítheti és törölheti a jelenleg használt örökölt v2 és V 1.1 felhasználói folyamatokat.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Van valamilyen ok arra, hogy továbbra is használhassa az örökölt v2 és a V 1.1 felhasználói folyamatokat?

Nem igazán. Az új **ajánlott** előzetes verziók ugyanazt a funkciót tartalmazzák, mint az örökölt v2 és a v 1.1 verziója. Semmi nem lett eltávolítva, és valójában már tartalmaz további funkciókat.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Ha nem váltok át örökölt v2-es és V 1.1-es szabályzatokból, hogyan befolyásolják az alkalmazásom?

Ha örökölt v2 vagy V 1.1 felhasználói folyamatot használ, az alkalmazás nem lesz hatással a verziószámozás változására. Ahhoz azonban, hogy hozzáférjen az új szolgáltatásokhoz vagy a szabályzatok módosításaihoz, át kell váltania az új **ajánlott** verzióra.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>A Microsoft továbbra is támogatja az örökölt v2 vagy V 1.1 felhasználói flow-szabályzatot?

A felhasználói folyamatok örökölt v2 és V 1.1 verziói továbbra is teljes mértékben támogatottak lesznek.
