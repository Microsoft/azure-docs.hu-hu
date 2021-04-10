---
title: Azure-beli biztonsági funkciók, amelyek segítenek az Identitáskezelés kezelésében | Microsoft Docs
description: Ismerje meg az Azure alapvető biztonsági funkcióit, amelyek segítenek az Identitáskezelés kezelésében. Tekintse meg a témaköröket, például az egyszeri bejelentkezést és a fordított proxyt.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
ms.openlocfilehash: 1bb4f22c6ef114a224597ede2b053d076aba5997
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065837"
---
# <a name="azure-identity-management-security-overview"></a>Az Azure identitáskezelésének biztonsági áttekintése

 Az Identitáskezelés a [rendszerbiztonsági tag](/windows/security/identity-protection/access-control/security-principals)hitelesítésének és engedélyezésének folyamata. Emellett magában foglalja az ilyen rendszerbiztonsági tagok (identitások) adatainak szabályozását is. A rendszerbiztonsági tag (identitások) tartalmazhatnak szolgáltatásokat, alkalmazásokat, felhasználókat, csoportokat stb. A Microsoft identitás-és hozzáférés-kezelési megoldásai segítenek a vállalati adatközpontban és a felhőben lévő alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében. Az ilyen védelem további ellenőrzési szinteket tesz lehetővé, például a Multi-Factor Authentication és a feltételes hozzáférési házirendeket. A gyanús tevékenységek speciális biztonsági jelentéskészítéssel, naplózással és riasztással való figyelése segít csökkenteni a lehetséges biztonsági problémákat. A [prémium szintű Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) egyszeri bejelentkezést (SSO-t) biztosít több ezer felhőalapú szoftver-(SaaS-) alkalmazáshoz, és hozzáfér a helyszínen futtatott webalkalmazásokhoz.
 
Ha kihasználja Azure Active Directory (Azure AD) biztonsági előnyeit, a következőket teheti:

* A hibrid vállalat minden felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek. 
* Egyszeri bejelentkezéses hozzáférés biztosítása az alkalmazásokhoz, beleértve több ezer előre integrált SaaS-alkalmazást.
* Szabályalapú többtényezős hitelesítés kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban.
* Biztonságos távoli hozzáférés biztosítása helyszíni webalkalmazásokhoz az Azure AD Application Proxy használatával.

Ennek a cikknek a célja, hogy áttekintést nyújtson az Azure-beli alapvető biztonsági funkciókról, amelyek segítik az Identitáskezelés kezelését. Olyan cikkekre mutató hivatkozásokat is biztosítunk, amelyek részletesen ismertetik az egyes funkciók részleteit, így többet is megtudhat.  

A cikk a következő alapvető Azure Identity Management-képességekre összpontosít:

* Egyszeri bejelentkezés
* Fordított proxy
* Többtényezős hitelesítés
* Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)
* Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések
* Felhasználói identitás és hozzáférés-kezelés
* Eszközregisztráció
* Privileged Identity Management
* Identity protection
* Hibrid Identitáskezelés/Azure AD-kapcsolat
* Azure AD hozzáférési felülvizsgálatok

## <a name="single-sign-on"></a>Egyszeri bejelentkezés

Az SSO azt jelenti, hogy az összes szükséges alkalmazáshoz és erőforráshoz csak egyszer kell bejelentkeznie egyetlen felhasználói fiók használatával. A bejelentkezést követően az összes szükséges alkalmazáshoz hozzáférhet a hitelesítéshez (például egy jelszó megadásához).

Számos szervezet SaaS-alkalmazásokra (például Microsoft 365, Box és Salesforce) támaszkodik a felhasználói hatékonyság érdekében. Az informatikai részlegnek az egyes SaaS-alkalmazásokban egyenként kell létrehoznia és frissítenie a felhasználói fiókokat, és a felhasználóknak minden SaaS-alkalmazáshoz meg kellett emlékezniük a jelszót.

Az Azure AD kiterjeszti a helyszíni Active Directory környezeteket a felhőbe, így a felhasználók az elsődleges szervezeti fiók használatával nem csak a tartományhoz csatlakoztatott eszközökre és a vállalati erőforrásokra, hanem a feladatokhoz szükséges webes és SaaS-alkalmazásokra is bejelentkezhetnek.

Nem csak a felhasználóknak több felhasználónevet és jelszót kell kezelnie, a szervezeti csoportok és az alkalmazotti állapotuk alapján automatikusan kiépítheti vagy kiépítheti az alkalmazásokhoz való hozzáférést. Az Azure AD olyan biztonsági és hozzáférési irányítási ellenőrzéseket vezet be, amelyekkel központilag kezelheti a felhasználók hozzáférését az SaaS-alkalmazásokban.

További információ:

* [Az SSO áttekintése](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Videó a hitelesítés alapjairól](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Gyorsindítás sorozat az alkalmazás-kezelésben](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Fordított proxy

Az Azure AD Application Proxy lehetővé teszi a helyszíni alkalmazások, például a [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) -webhelyek, az [Outlook Web App](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)és az [IIS](https://www.iis.net/)-alapú alkalmazások közzétételét a magánhálózaton belül, és biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára. Az alkalmazásproxy távoli hozzáférést és egyszeri bejelentkezést biztosít számos különböző típusú helyszíni webalkalmazáshoz az Azure AD által támogatott több ezer SaaS-alkalmazással. Az alkalmazottak bejelentkezhetnek az alkalmazásaiba otthonról a saját eszközein, és ezen a felhőalapú proxyn keresztül hitelesíthetők.

További információ:

* [Az Azure AD Application Proxy engedélyezése](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Alkalmazások közzététele az Azure AD-alkalmazásproxyval](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Egyszeri bejelentkezés alkalmazásproxyval](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Feltételes hozzáférés használata](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Többtényezős hitelesítés

Az Azure AD Multi-Factor Authentication olyan hitelesítési módszer, amely több ellenőrzési módszer használatát igényli, és kritikus második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókra. Multi-Factor Authentication segít megőrizni az adathozzáférést és az alkalmazásokhoz való hozzáférést, miközben a felhasználói igényeket egy egyszerű bejelentkezési folyamaton keresztül teljesíti. Erős hitelesítést biztosít számos ellenőrzési lehetőség használatával: telefonhívások, szöveges üzenetek vagy Mobile apps-értesítések, valamint ellenőrző kódok és külső OAuth-tokenek.

További információ:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Az Azure AD többtényezős hitelesítés működése](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC-vel

Az Azure RBAC egy Azure Resource Managerra épülő engedélyezési rendszer, amely részletes hozzáférés-kezelést biztosít az Azure-beli erőforrásokhoz. Az Azure RBAC segítségével részletesen szabályozhatja a felhasználók hozzáférésének szintjét. Például korlátozhatja, hogy egy felhasználó csak a virtuális hálózatokat és egy másik felhasználót kezelje az erőforráscsoport összes erőforrásának kezeléséhez. Az Azure számos beépített szerepkört biztosít, amelyeket használhat. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

- [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) – teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a hozzáférés mások számára való delegálásának jogát is. 
- [Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) – Bármilyen típusú Azure-erőforrást létrehozhat és felügyelhet, hozzáférést azonban nem adhat mások számára.
- [Olvasó](../../role-based-access-control/built-in-roles.md#reader) – megtekintheti a meglévő Azure-erőforrásokat.
- [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) – kezelheti a felhasználók Azure-erőforrásokhoz való hozzáférését.

További információ:

* [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések

A nem konzisztens hozzáférési mintákat azonosító biztonsági monitorozási, riasztási és gépi tanulási jelentések segíthetnek a vállalata védelmében. Az Azure AD-hozzáférési és-használati jelentések segítségével megtekintheti a szervezete címtárának integritását és biztonságát. Ezekkel az információkkal a címtár-rendszergazdák jobban meghatározhatják, hogy az esetleges biztonsági kockázatok hol lehetnek a kockázatok enyhítése érdekében.

A Azure Portal a jelentések a következő kategóriákba sorolhatók:

* **Anomália-jelentések**: olyan bejelentkezési eseményeket tartalmaz, amelyeket a rendszer rendellenesnek talált. Célunk, hogy tisztában legyenek az ilyen tevékenységekkel, és lehetővé teszik annak meghatározását, hogy az esemény gyanús-e.
* **Integrált alkalmazások jelentései**: megtudhatja, hogyan használják a Felhőbeli alkalmazásokat a szervezetében. Az Azure AD több ezer felhőalapú alkalmazással való integrációt biztosít.
* **Hibajelentések**: a fiókok külső alkalmazásokba való kiosztásakor előforduló hibákat jelzi.
* **Felhasználó-specifikus jelentések**: az eszköz bejelentkezési tevékenységére vonatkozó információk megjelenítése egy adott felhasználó számára.
* **Tevékenységnaplók**: az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban található összes naplózott esemény rekordját tartalmazza, valamint a csoport tevékenységeinek változásait, valamint a jelszó-visszaállítási és a regisztrációs tevékenységet.

További információ:

* [A hozzáférési és használati jelentések megtekintése](../../active-directory/reports-monitoring/overview-reports.md)
* [Ismerkedés a Azure Active Directory jelentéskészítéssel](../../active-directory/reports-monitoring/overview-reports.md)
* [Azure Active Directory jelentéskészítési útmutató](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Felhasználói identitás és hozzáférés-kezelés

A Azure AD B2C egy nagyszámú, globális, identitás-kezelési szolgáltatás, amely több száz millió identitásra méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók a meglévő közösségi fiókjaik használatával vagy új hitelesítő adatok létrehozásával jelentkezhetnek be az összes alkalmazásba.

A múltban az alkalmazások fejlesztői számára, akik szeretnék regisztrálni az ügyfeleket, és saját alkalmazásaikban is aláírják őket, saját programkódot kellett volna beírniuk. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Azure AD B2C a biztonságos, szabványon alapuló platform és a bővíthető házirendek nagy készletének segítségével a szervezetnek jobb megoldást kínál a fogyasztói Identitáskezelés alkalmazásba való integrálására.

Azure AD B2C használatakor a felhasználók a meglévő közösségi fiókjaik (Facebook, Google, Amazon, LinkedIn) használatával regisztrálhatják alkalmazásaikat, vagy új hitelesítő adatokat (e-mail-címet, jelszót vagy felhasználónevet és jelszót) hoznak létre.

További információ:

* [Mi az az Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Az Azure Active Directory B2C előzetes verziója: felhasználók regisztrálása és beléptetése az alkalmazásokba ](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C előzetes verzió: alkalmazások típusai](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Eszközregisztráció

Az Azure AD-eszközök regisztrációja az eszköz alapú [feltételes hozzáférési](../../active-directory/devices/device-management-azure-portal.md) forgatókönyvek alapja. Ha egy eszköz regisztrálva van, az Azure AD-eszköz regisztrálása olyan identitást biztosít az eszköz számára, amelyet az eszköz hitelesítéséhez használ, amikor a felhasználó bejelentkezik. A rendszer ezután a hitelesített eszközt és az eszköz attribútumait felhasználhatja a felhőben és a helyszínen üzemeltetett alkalmazások feltételes hozzáférési házirendjeinek érvénybe léptetéséhez.

A mobileszköz-kezelési megoldással, például az Intune-nal kombinálva az Azure AD-ben az eszköz attribútumai frissülnek az eszközre vonatkozó további információkkal. Ezután létrehozhat olyan feltételes hozzáférési szabályokat, amelyek kikényszerítik az eszközök hozzáférését, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

További információ:

* [Ismerkedés az Azure AD-eszközök regisztrálásával](../../active-directory/devices/device-management-azure-portal.md)
* [Automatikus eszköz regisztrálása az Azure AD-vel Windows-tartományhoz csatlakoztatott eszközökhöz](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [A Windows-tartományhoz csatlakoztatott eszközök automatikus regisztrációjának beállítása az Azure AD-vel](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

A Azure AD Privileged Identity Management segítségével felügyelheti, felügyelheti és figyelheti a Kiemelt identitásokat, és hozzáférhet az Azure AD-beli erőforrásokhoz, valamint más Microsoft-online szolgáltatásokokhoz, például a Microsoft 365hoz és a Microsoft Intunehoz.

A felhasználóknak időnként magas jogosultsági szintű műveleteket kell végezniük az Azure-ban vagy Microsoft 365 erőforrásokban vagy más SaaS-alkalmazásokban. Ez gyakran azt jelenti, hogy a szervezeteknek állandó jogosultsági szintű hozzáférést kell biztosítaniuk a felhasználóknak az Azure AD-ben. Az ilyen hozzáférés egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások számára, mivel a szervezetek nem tudják eléggé megfigyelni, hogy a felhasználók hogyan használják a rendszergazdai jogosultságokat. Emellett, ha egy emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, az egyik megszegés befolyásolhatja a szervezet teljes Felhőbeli biztonságát. Azure AD Privileged Identity Management segít csökkenteni ezt a kockázatot.

A Azure AD Privileged Identity Management a következőket teheti:

* Megtekintheti, hogy mely felhasználók Azure AD-rendszergazdák.
* Igény szerinti (JIT) rendszergazdai hozzáférés engedélyezése a Microsoft-szolgáltatásokhoz, például a Microsoft 365hoz és az Intune-hoz.
* A rendszergazdai hozzáférési előzményekkel és a rendszergazdai hozzárendelések változásaival kapcsolatos jelentések beolvasása.
* Riasztást kaphat a Kiemelt szerepkörhöz való hozzáférésről.

További információ:

* [Mi az az Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD-címtárbeli szerepkörök kiosztása a PIM-ben](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity protection

A Azure AD Identity Protection egy olyan biztonsági szolgáltatás, amely összevont nézetet biztosít a szervezet identitásait érintő kockázati észlelések és potenciális sebezhetőségek számára. Az Identity Protection kihasználja a meglévő Azure AD-anomáliák – észlelési képességeket, amelyek az Azure AD rendellenes tevékenységi jelentéseiben érhetők el. Az Identity Protection olyan új kockázati észlelési típusokat is tartalmaz, amelyek valós időben észlelik a rendellenességeket.

További információ:

* [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [9. csatorna: Azure AD-és identitás-megjelenítés: Identity Protection előnézet](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hibrid Identitáskezelés/Azure AD-kapcsolat

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük. A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Microsoft 365-, Azure- és SaaS-alkalmazásokhoz. Ez a tevékenység a következő jellemzőkkel bír:

* Szinkronizálás
* AD FS és összevonás-integráció
* Hitelesítés továbbítása
* Állapotfigyelés

További információ:

* [Hibrid identitás – tanulmány](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-csapat blogja](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a kiemelt szerepkörök hozzárendeléseit.

További információ:

* [Azure AD hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)
* [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](../../active-directory/governance/access-reviews-overview.md)