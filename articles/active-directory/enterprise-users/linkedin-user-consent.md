---
title: A LinkedIn adatmegosztása és belekötése – Azure Active Directory | Microsoft Docs
description: A cikk azt ismerteti, hogyan osztja meg a LinkedIn Integration az adatmegosztást a Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545910"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>A LinkedIn-fiókok kapcsolatainak megosztása és beleegyezett hozzáférése

Engedélyezheti a Active Directory (Azure AD) szervezet felhasználói számára a Microsoft munkahelyi vagy iskolai fiókjához való kapcsolódást a LinkedIn-fiókkal. Miután egy felhasználó összekapcsolta a fiókokat, a LinkedIn információi és kiemelései bizonyos Microsoft-alkalmazásokban és-szolgáltatásokban érhetők el. A felhasználók a LinkedIn-ben is számíthatnak a hálózati élmény javítására és a Microsoft információinak bővítésére.

Ha meg szeretné tekinteni a LinkedIn-információkat a Microsoft-alkalmazásokban és-szolgáltatásokban, a felhasználóknak hozzá kell adniuk a saját Microsoft-és LinkedIn-fiókjaikat. A rendszer felkéri a felhasználókat a fiókok összekapcsolására, amikor először rákattintanak a LinkedIn-információk megtekintésére a profil kártyán az Outlook, a OneDrive vagy a SharePoint Online szolgáltatásban. A LinkedIn-fiókok kapcsolatai nem teljes mértékben engedélyezve vannak a felhasználók számára, amíg nem veszik figyelembe a felhasználói élményt és a fiókjaik összekapcsolását.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>A LinkedIn-információk megosztásának előnyei

A Microsoft-alkalmazásokon és-szolgáltatásokon belüli LinkedIn-adatokhoz való hozzáférés megkönnyíti a felhasználók számára, hogy a szervezeten belüli és kívüli munkatársakkal, ügyfelekkel és partnerekkel csatlakozzanak, kapcsolódjanak és építsenek szakmai kapcsolatokat. Az új felhasználók gyorsabban, a munkatársakkal való csatlakozással, a róluk való ismerkedéssel és a további információk elérésével gyorsabbá válnak. Íme egy példa arra, hogyan jelennek meg a LinkedIn-információk a Microsoft-alkalmazások profil kártyáján:

![A LinkedIn-integráció engedélyezése a szervezetben](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>A LinkedIn-integráció engedélyezése és bejelentése

A szervezet beállításának kezeléséhez Azure Active Directory rendszergazdának kell lennie. Engedélyezheti az összes felhasználó számára, vagy egy adott felhasználói csoport számára.

1. Az integráció engedélyezéséhez vagy letiltásához kövesse az [Azure ad-szervezetének LinkedIn Integration-integrációjának](linkedin-integration.md)lépéseit.
2. Amikor bejelenti a LinkedIn-integrációt a szervezetében, a felhasználókat a [Microsoft-alkalmazások és-szolgáltatások LinkedIn-adataival](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)kapcsolatos gyakori kérdések között találhatja meg. A cikk tájékoztatást nyújt arról, hogy hol jelennek meg a LinkedIn-információk, az [adatok megosztása és az adatvédelem](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), valamint a [fiókok összekapcsolásának módja](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) .

Be kell jelentenie a LinkedIn-integrációt a felhasználók számára a [LinkedIn-integrációval kapcsolatos adatmegosztással és adatvédelemmel](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)kapcsolatos összes információ biztosításához. 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>A Microsoft és a LinkedIn adatelérési szolgáltatásának felhasználói beleegyezett

A LinkedIn szolgáltatásból elért adatok nem tárolódnak véglegesen a Microsoft szolgáltatásaiban. A Microsofttól elért adatok nem tárolódnak véglegesen a LinkedIn-mel, kivéve a névjegyeket. A Microsoft névjegyek a LinkedIn [-ben tárolódnak](https://www.linkedin.com/help/linkedin/answer/43377), amíg a felhasználók el nem távolítják azokat

Amikor a felhasználók összekapcsolhatják a fiókjaikat, a LinkedIn információi és adatai bizonyos Microsoft-alkalmazásokban, például a profil kártyában is elérhetők. A felhasználók a LinkedIn-ben is számíthatnak a hálózati élmény javítására és a Microsoft információinak bővítésére.
Ha a cégen belüli felhasználók a LinkedIn és a Microsoft munkahelyi vagy iskolai fiókjaikat használják, két lehetőség közül választhatnak:

* Adja meg az adatok mindkét fiókból való elérésének engedélyét. Ez azt jelenti, hogy a Microsoft vagy a munkahelyi fiók számára engedélyt adnak a LinkedIn-fiókjából származó adatok elérésére, illetve a [LinkedIn-fiókjuk számára a Microsoft munkahelyi vagy iskolai fiókjából származó adatok elérésére](https://www.linkedin.com/help/linkedin/answer/84077).
* A Microsoft munkahelyi és iskolai fiókja által elérhetővé tett csak a LinkedIn-adatelérési engedélyek megadása.

A felhasználók bármikor leválaszthatják a fiókokat és eltávolíthatják az adatelérési engedélyeket, és a [felhasználók vezérelhetik a saját LinkedIn-profiljuk megtekintését](https://www.linkedin.com/help/linkedin/answer/83), beleértve azt is, hogy a profil megtekinthető-e a Microsoft-alkalmazásokban.

### <a name="linkedin-account-data"></a>LinkedIn-fiókadatok

A Microsoft-és a LinkedIn-fiókok összekapcsolásával a LinkedIn a következő adatait biztosítja a Microsoftnak:

* Profil adatai – ide tartozik a LinkedIn-identitás, a kapcsolattartási adatok és a [LinkedIn-profilban](https://www.linkedin.com/help/linkedin/answer/15493)másokkal megosztott információk.
* Érdeklődési körök – a LinkedIn érdeklődéseit is magában foglalja, például az Ön által követett személyeket és témákat, a tanfolyamok csoportjait és a megosztható tartalmakat.
* Előfizetések – a LinkedIn-alkalmazásokra és-szolgáltatásokra vonatkozó előfizetések, valamint a hozzájuk kapcsolódó adatai. 
* Kapcsolatok adatai – magába foglalja a [LinkedIn-hálózatot](https://www.linkedin.com/help/linkedin/answer/110) , beleértve az 1. szintű kapcsolatok profiljait és kapcsolattartási adatait.

A LinkedIn szolgáltatásból elért adatok nem tárolódnak véglegesen a Microsoft szolgáltatásaiban. A személyes adatok Microsoft általi felhasználásával kapcsolatos további információkért lásd a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsoft munkahelyi vagy iskolai fiókadatok

A Microsoft-és a LinkedIn-fiókok összekapcsolásával lehetővé teszi, hogy a Microsoft a következő adatait adja meg a LinkedIn számára:

* Profil adatai – olyan információkat tartalmaz, mint például az utónév, a vezetéknév, a profil fényképe, az e-mail-cím, a felettes és a kezelt személyek.
* Naptáradatok – a naptárakban, az időpontokban, a helyszíneken és a résztvevők kapcsolattartási adatait tartalmazza. Az értekezletre, például a napirendre, a tartalomra vagy az értekezlet címére vonatkozó információk nem szerepelnek a naptári adatokban.
* Érdeklődési körök – a Microsoft-szolgáltatások (például a Cortana és a Bing for Business) alapján a fiókjához társított érdekeket foglalja magában.
* Előfizetések adatai – a szervezet által a Microsoft-alkalmazások és-szolgáltatások, például a Microsoft 365 számára biztosított előfizetéseket tartalmazza.
* Kapcsolattartási adatok – az Outlookban, a Skype-ban és más Microsoft-fiók-szolgáltatásokban található kapcsolattartási listát is tartalmazza, beleértve a gyakran kommunikáló vagy együttműködő személyekhez tartozó kapcsolattartási adatokat is. A névjegyeket a LinkedIn rendszeresen importálja, tárolja és használja, például a kapcsolatok javaslatára, a névjegyek rendszerezésére és a névjegyek frissítéseinek megjelenítésére.

A Microsofttól elért adatok nem tárolódnak véglegesen a LinkedIn-mel, kivéve a névjegyeket. A Microsoft névjegyek a LinkedIn-ben tárolódnak, amíg a felhasználók el nem távolítják őket. További információ az [importált névjegyek a linkedinből való törléséről](https://www.linkedin.com/help/linkedin/answer/43377).

A LinkedIn személyes adatainak használatával kapcsolatos további információkért tekintse meg a [LinkedIn adatvédelmi szabályzatát](https://www.linkedin.com/legal/privacy-policy). A LinkedIn-szolgáltatások, az adatforgalom és a tárolás esetében az adatok az Európai Unióból a Egyesült Államokba és vissza is áramlanak, és az adatvédelem védelme az [Európai Unió adatátviteli](https://www.linkedin.com/help/linkedin/answer/62533)szolgáltatásában leírtaknak megfelelően történik.

## <a name="next-steps"></a>Következő lépések

* [A LinkedIn a munkahelyi vagy iskolai fiókkal rendelkező Microsoft-alkalmazásokban](https://www.linkedin.com/help/linkedin/answer/84077)
