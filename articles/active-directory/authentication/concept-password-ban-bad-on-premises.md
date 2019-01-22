---
title: Az Azure AD jelszó protection előzetes verziója
description: A gyenge jelszavakat letiltása az Azure AD jelszó protection előzetes verziójának a helyszíni Active Directoryban
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 45c77aa63159a8e62f65c0e250c0ace95b0d344e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429905"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Előzetes verzió: Az Azure AD jelszóvédelem a Windows Server Active Directory kényszerítése

|     |
| --- |
| Az Azure AD jelszóvédelem és a letiltott jelszavak egyéni lista a nyilvános előzetes verziójú funkciók az Azure Active Directory. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem működteti az Azure Active Directory (Azure AD) használatával javíthatja a szervezeten belüli jelszóházirendek nyilvános előzetes verzióban érhető el az egyik új szolgáltatása. Az Azure AD jelszóvédelem helyi telepítését használja is a globális és egyéni le van tiltva az Azure AD-ben tárolt jelszavak listáit, és végrehajtja az azonos ellenőrzi a helyszíni Azure AD felhőalapú változásának megfelelően.

Nincsenek Azure AD jelszóvédelem alkotó három szoftverösszetevőket:

* Az Azure AD jelszó védelme proxy szolgáltatás minden olyan tartományhoz csatlakoztatott gép az aktuális Active Directory-erdő futtat. Ez a továbbítja a kéréseket a tartományvezérlőktől az Azure ad-hez, és visszaküldi a választ az Azure ad-ből a tartományvezérlő.
* Az Azure AD jelszó DC védelmi ügynök szolgáltatás jelszó érvényesítése kéréseket fogad a tartományvezérlő ügynök jelszó szűrő dll, helyileg elérhető jelszóházirend használatával dolgozza fel, és visszaadja az eredményt (pass\fail). Ez a szolgáltatás felelős az időnként (óránként egyszer) az Azure AD jelszó védelmi beolvasni a jelszóházirend új verzióinak proxy szolgáltatás hívása. Kommunikáció a hívásokat, és az Azure AD jelszó védelme proxy szolgáltatás a TCP-n keresztül (távoli eljáráshívási) RPC-n keresztül történik. Lekéréséhez, hogy új szabályzatok a sysvol mappa, ahol azok más tartományvezérlők replikálhatja vannak tárolva. A tartományvezérlő-ügynökszolgáltatás is figyeli a sysvol mappában a változásokat, abban az esetben, ha más tartományvezérlők írásos engedélye van az új jelszóházirendek, ha megfelelően legutóbbi házirend már elérhető a az Azure AD jelszó védelme proxy szolgáltatás-ellenőrzés kimarad.
* A tartományvezérlő ügynök jelszó szűrő dll jelszó érvényesítése kéréseket fogad az operációs rendszer, majd továbbítja azokat az Azure AD jelszó DC védelmi ügynök szolgáltatás helyben fut a tartományvezérlő.

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="license-requirements"></a>Licenckövetelmények

A globális letiltott jelszavak lista előnyeit az Azure Active Directory (Azure AD) minden felhasználóra érvényes.

A letiltott jelszavak egyéni lista alapszintű Azure AD-licenc szükséges.

Az Azure AD jelszóvédelem a Windows Server Active Directory prémium szintű Azure AD-licenc szükséges.

További licencelési információk, beleértve a költségek, találhatók a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Letöltés

Nincsenek Azure AD jelszóvédelem, amely letölthető a két szükséges telepítőinek a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Gyakori kérdésekre adott válaszok

* Nincs internetkapcsolat a tartományvezérlőkről szükséges. A futtatása az Azure AD jelszó védelme proxy szolgáltatás (ek) az internetkapcsolatot igénylő csak gépek.
* Nincs hálózati portokat a tartományvezérlőkön.
* Az Active Directory-séma módosítása nélkül szükség.
* A szoftver a meglévő Active Directory-tárolót és a serviceConnectionPoint sémaobjektumok használja.
* Esetében nem minimális Active Directory-tartomány vagy erdő működési szint (DFL\FFL) követelmény.
* A szoftver hozzon létre vagy nem szükséges minden olyan fiókok, az általa védett munkaterhelésekkel Active Directory-tartománynak.
* Növekményes üzembe helyezést támogatott azzal a kompromisszummal jár, hogy jelszót házirend csak van érvényben, a domain controller ügynök telepítve van.
* Javasoljuk, hogy a tartományvezérlő ügynököt telepíteni az összes tartományvezérlőből jelszó kényszerítése-védelem biztosítása érdekében. 
* Az Azure AD jelszóvédelem nem egy valós idejű alkalmazás házirendmotor. A szabályzat konfigurációs jelszómódosítás és az idő eléri a, és a rendszer kényszeríti az összes tartományvezérlő közötti idő késés lehet.

## <a name="next-steps"></a>További lépések

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
