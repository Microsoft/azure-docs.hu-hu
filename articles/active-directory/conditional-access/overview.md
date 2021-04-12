---
title: Mi a feltételes hozzáférés a Azure Active Directory?
description: Ismerje meg, hogy a feltételes hozzáférés hogyan áll az új Identity vezérelt vezérlési sík középpontjába.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 01/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 7b43cf23b3f01e64c0be31ed013aa990ff80d3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938340"
---
# <a name="what-is-conditional-access"></a>Mi az a feltételes hozzáférés?

A modern biztonsági területet mostantól a szervezet hálózatán kívül is kiterjesztheti a felhasználók és az eszközök azonosítására. A szervezetek a hozzáférés-vezérlési döntéseik részeként használhatják ezeket az identitási jeleket. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MwZs]

A feltételes hozzáférés az Azure Active Directory által használt eszköz, amely lehetővé teszi a jelek összekapcsolását, a döntések meghozatalát és a szervezeti házirendek betartatását. A feltételes hozzáférés az új Identity vezérelt vezérlő síkja szívében található.

![Koncepcionális feltételes jel plusz döntés a kényszerítés beszerzéséhez](./media/overview/conditional-access-signal-decision-enforcement.png)

A feltételes hozzáférési szabályzatok a legegyszerűbben az IF-then utasítások, ha egy felhasználó szeretne hozzáférni egy erőforráshoz, végre kell hajtania egy műveletet. Példa: egy bérlista-kezelő szeretné elérni a bérlista-alkalmazást, és a többtényezős hitelesítés végrehajtásához szükséges.

A rendszergazdák két elsődleges céllal szembesülnek:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A szervezet eszközeinek védelmének biztosítása

A feltételes hozzáférési szabályzatok alkalmazásával a megfelelő hozzáférés-vezérlést alkalmazhatja a szervezet biztonságának biztosításához, és nem szükséges, hogy a felhasználó ne használja.

![Fogalmi feltételes hozzáférés folyamata](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. A feltételes hozzáférés nem a szervezet első védelmi vonala, például a szolgáltatásmegtagadási (DoS) támadások esetében, de ezekből az eseményekről származó jeleket is használhat a hozzáférés meghatározásához.

## <a name="common-signals"></a>Gyakori jelek

Azok a gyakori jelek, amelyekben a feltételes hozzáférés a szabályzat meghozatalakor a következő jeleket veszi figyelembe:

- Felhasználó vagy csoport tagsága
   - A házirendek célzott felhasználókat és csoportokat céloznak meg, amelyek lehetővé teszik a rendszergazdák számára a hozzáférés részletes szabályozását.
- IP-hely adatai
   - A szervezetek olyan megbízható IP-címtartományok hozhatók létre, amelyek szabályzati döntések elkészítésekor használhatók. 
   - A rendszergazdák az összes ország/régió IP-tartományát megadhatják a forgalom blokkolásához vagy engedélyezéséhez.
- Eszköz
   - A feltételes hozzáférési szabályzatok kényszerítése esetén a megadott platformmal rendelkező vagy meghatározott állapotú eszközökkel rendelkező felhasználók is használhatók.
- Alkalmazás
   - Azok a felhasználók, akik egy adott alkalmazáshoz próbálnak hozzáférni, különböző feltételes hozzáférési házirendeket válthatnak ki. 
- Valós idejű és számított kockázati észlelés
   - A Azure AD Identity Protection lehetővé teszi az integrációt a feltételes hozzáférési házirendekkel a kockázatos bejelentkezési viselkedés azonosítása érdekében. A házirendek ezt követően kényszerítheti a felhasználókat a jelszó módosítására vagy a többtényezős hitelesítésre a kockázati szint csökkentése vagy a hozzáférés letiltása előtt, amíg a rendszergazda manuális beavatkozást nem végez.
- Microsoft Cloud App Security (MCAS)
   - Lehetővé teszi a felhasználói alkalmazások hozzáférésének és munkameneteinek valós idejű monitorozását és ellenőrzését, valamint a felhőalapú környezetben végrehajtott hozzáférés és a hozzájuk tartozó tevékenységek átláthatóságának növelését és felügyeletét.

## <a name="common-decisions"></a>Gyakori döntések

- Hozzáférés letiltása
   - Legszigorúbb döntés
- Hozzáférés biztosítása
   - A legkevésbé korlátozó döntés továbbra is megkövetelheti a következő lehetőségek közül egyet vagy többet:
      - Többtényezős hitelesítés megkövetelése
      - Eszköz megfelelőként való megjelölésének megkövetelése
      - Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése
      - Jóváhagyott ügyfélalkalmazás megkövetelése
      - Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)

## <a name="commonly-applied-policies"></a>Gyakran alkalmazott szabályzatok

Számos szervezet rendelkezik olyan [általános hozzáféréssel, amelyekkel a feltételes hozzáférési szabályzatok segíthetnek](concept-conditional-access-policy-common.md) , például:

- Többtényezős hitelesítés megkövetelése rendszergazdai szerepkörökkel rendelkező felhasználók számára
- Többtényezős hitelesítés megkövetelése az Azure felügyeleti feladatokhoz
- A régi hitelesítési protokollok használatát megkísérlő felhasználók bejelentkezésének letiltása
- Megbízható helyek megkövetelése az Azure AD-Multi-Factor Authentication regisztrálásához
- Megadott helyekről való hozzáférés letiltása vagy megadása
- Kockázatos bejelentkezési viselkedések blokkolása
- Szervezet által felügyelt eszközök megkövetelése adott alkalmazásokhoz

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

[Microsoft 365 vállalati verzió prémium szintű licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is. 

A [bejelentkezési kockázathoz](concept-conditional-access-conditions.md#sign-in-risk) hozzáférés szükséges az [Identity Protection](../identity-protection/overview-identity-protection.md) szolgáltatáshoz

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférési szabályzat létrehozása darab alapján](concept-conditional-access-policies.md)
- [A feltételes hozzáférés üzembe helyezésének megtervezése](plan-conditional-access.md)
- [Tudnivalók az Identity Protectionről](../identity-protection/overview-identity-protection.md)
- [Tudnivalók a Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Tudnivalók a Microsoft Intune](/intune/index)
