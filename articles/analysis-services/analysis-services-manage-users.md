---
title: Azure Analysis Services hitelesítés és felhasználói engedélyek | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használja a Azure Analysis Services az Azure Active Directoryt (Azure AD) az Identitáskezelés és a felhasználók hitelesítéséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487439"
---
# <a name="authentication-and-user-permissions"></a>Hitelesítés és felhasználói engedélyek

A Azure Analysis Services a Azure Active Directory (Azure AD) szolgáltatást használja az Identitáskezelés és a felhasználók hitelesítéséhez. A Azure Analysis Services-kiszolgáló létrehozásával, kezelésével vagy csatlakozásával rendelkező felhasználóknak érvényes felhasználói identitással kell rendelkezniük ugyanahhoz az előfizetéshez tartozó [Azure ad-bérlőben](../active-directory/fundamentals/active-directory-whatis.md) .

Azure Analysis Services támogatja az [Azure ad B2B-együttműködést](../active-directory/external-identities/what-is-b2b.md). A B2B használatával a szervezeten kívüli felhasználók meghívhatók vendégként egy Azure AD-címtárban. A vendégek egy másik Azure AD-bérlői címtárból vagy bármely érvényes e-mail-címről is származnak. A meghívást követően a felhasználó elfogadja az Azure-ból e-mailben küldött meghívót, a rendszer hozzáadja a felhasználói identitást a bérlői címtárhoz. Ezek az identitások hozzáadhatók biztonsági csoportokhoz vagy kiszolgálói rendszergazda vagy adatbázis-szerepkör tagjaiként.

![Azure Analysis Services hitelesítési architektúra](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Hitelesítés

Minden ügyfélalkalmazás és eszköz egy vagy több Analysis Services [ügyféloldali kódtárat](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (amo, MSOLAP, ADOMD) használ a kiszolgálóhoz való kapcsolódáshoz. 

Mindhárom ügyfél-függvénytár támogatja az Azure AD interaktív folyamatot és a nem interaktív hitelesítési módszereket. A AMOMD-t és MSOLAP-t használó alkalmazásokban a két nem interaktív módszer, Active Directory jelszó és a Active Directory integrált hitelesítési módszer is használható. Ez a két módszer soha nem eredményez előugró párbeszédpanelt.

Az olyan ügyfélalkalmazások, mint az Excel és a Power BI Desktop, valamint a Visual studióhoz készült SSMS és Analysis Services projects bővítmény a legújabb verzióra való frissítéskor telepíti a kódtárak legújabb verzióit. A Power BI Desktop, a SSMS és a Analysis Services projects bővítmény havonta frissül. Az Excel [Microsoft 365tel frissül](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365 frissítések kevésbé gyakoriak, és egyes szervezetek a késleltetett csatornát használják, ami azt jelenti, hogy a frissítések három hónapra vannak késleltetve.

Az ügyfélalkalmazás vagy az Ön által használt eszköztől függően a hitelesítés típusa és a bejelentkezés módja eltérő lehet. Az egyes alkalmazások különböző funkciókat is támogatnak a felhőalapú szolgáltatásokhoz, például a Azure Analysis Serviceshoz való csatlakozáshoz.

A Power BI Desktop, a Visual Studio és a SSMS támogatja a Active Directory univerzális hitelesítést, amely az Azure AD Multi-Factor Authentication (MFA) támogató interaktív metódust is támogatja. Az Azure AD MFA segít az adatkezelésben és az alkalmazásokban való hozzáférésben, miközben egyszerű bejelentkezési folyamatot biztosít. Erős hitelesítést biztosít több ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, PIN-kóddal ellátott intelligens kártya vagy mobil alkalmazás értesítése). Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti. **Az univerzális hitelesítés ajánlott**.

Ha Windows-fiókkal jelentkezik be az Azure-ba, és az univerzális hitelesítés nincs kiválasztva vagy elérhető (Excel), [Active Directory összevonási szolgáltatások (AD FS) (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) szükséges. Az összevonás, az Azure AD és a Microsoft 365 felhasználók hitelesítése helyszíni hitelesítő adatokkal történik, és hozzáférhet az Azure-erőforrásokhoz.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-kiszolgálók támogatják a [SSMS v 17.1](/sql/ssms/download-sql-server-management-studio-ssms) és újabb kapcsolatok használatát Windows-hitelesítéssel, Active Directory jelszó-hitelesítéssel és Active Directory univerzális hitelesítéssel. Általánosságban ajánlott Active Directory univerzális hitelesítést használni, mivel:

*  Támogatja az interaktív és nem interaktív hitelesítési módszereket.

*  Támogatja az Azure B2B vendég felhasználókat, akik bérlőként meghívottak az Azure-ba. Kiszolgálóhoz való kapcsolódáskor a vendég felhasználóknak ki kell választaniuk Active Directory univerzális hitelesítést a kiszolgálóhoz való csatlakozáskor.

*  Támogatja a Multi-Factor Authentication (MFA) használatát. Az Azure AD MFA számos ellenőrzési lehetőséggel segíti az adatelérést és az alkalmazásokhoz való hozzáférést: telefonhívást, szöveges üzenetet, intelligens kártyákat PIN-kóddal vagy Mobile apps-értesítéssel. Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti.

### <a name="visual-studio"></a>Visual Studio

A Visual Studio az MFA-támogatással Active Directory univerzális hitelesítés használatával csatlakozik a Azure Analysis Serviceshoz. A rendszer felkéri a felhasználókat, hogy jelentkezzenek be az Azure-ba az első telepítéskor. A felhasználóknak be kell jelentkezniük az Azure-ba egy olyan fiókkal, amely kiszolgálói rendszergazdai jogosultságokkal rendelkezik azon a kiszolgálón, amelyre telepítik. Amikor első alkalommal jelentkezik be az Azure-ba, a rendszer tokent rendel hozzá. A token memóriában van gyorsítótárazva a jövőbeli újrakapcsolásokhoz.

### <a name="power-bi-desktop"></a>Power BI Desktop

A Power BI Desktop az Active Directory univerzális hitelesítés és az MFA-támogatás használatával csatlakozik a Azure Analysis Serviceshoz. A rendszer felkéri a felhasználókat, hogy jelentkezzenek be az Azure-ba az első csatlakozáskor. A felhasználóknak be kell jelentkezniük az Azure-ba egy olyan fiókkal, amely egy kiszolgáló-rendszergazda vagy adatbázis-szerepkör része.

### <a name="excel"></a>Excel

Az Excel-felhasználók Windows-fiókkal, szervezeti AZONOSÍTÓval (e-mail-címmel) vagy külső e-mail-címmel csatlakozhatnak egy kiszolgálóhoz. A külső e-mail-identitásoknak vendég felhasználóként kell szerepelniük az Azure AD-ben.

## <a name="user-permissions"></a>Felhasználói engedélyek

A **kiszolgálói rendszergazdák** egy Azure Analysis Services Server-példányra jellemzőek. Olyan eszközökkel csatlakoznak, mint például a Azure Portal, a SSMS és a Visual Studio, hogy olyan feladatokat hajtsanak végre, mint például az adatbázisok hozzáadása és a felhasználói szerepkörök kezelése. Alapértelmezés szerint a kiszolgálót létrehozó felhasználó automatikusan Analysis Services kiszolgáló rendszergazdája lesz hozzáadva. Más rendszergazdák is hozzáadhatók Azure Portal vagy SSMS használatával. A kiszolgáló rendszergazdáinak ugyanahhoz az előfizetéshez tartozó fiókkal kell rendelkezniük az Azure AD-bérlőben. További információ: a [kiszolgáló-rendszergazdák kezelése](analysis-services-server-admins.md). 

Az **adatbázis-felhasználók** olyan ügyfélalkalmazások használatával csatlakoznak a modell-adatbázisokhoz, mint az Excel vagy a Power bi. A felhasználókat hozzá kell adni az adatbázis-szerepkörökhöz. Az adatbázis-szerepkörök rendszergazdai, feldolgozási vagy olvasási engedélyeket határoznak meg egy adatbázishoz. Fontos megérteni, hogy az adatbázis felhasználói a rendszergazdai engedélyekkel rendelkező szerepkörben nem különböznek a kiszolgálói rendszergazdáktól. Alapértelmezés szerint azonban a kiszolgálói rendszergazdák is adatbázis-rendszergazdák. További információ: az [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).

**Azure-erőforrás tulajdonosai**. Az erőforrás-tulajdonosok az Azure-előfizetéshez tartozó erőforrásokat kezelik. Az erőforrás-tulajdonosok hozzáadhatnak Azure AD felhasználói identitásokat az előfizetésben lévő tulajdonosi vagy közreműködői szerepkörökhöz Azure Portal vagy Azure Resource Manager-sablonokkal **való hozzáférés-vezérlés** használatával. 

![Hozzáférés-vezérlés Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Az ezen a szinten lévő szerepkörök azokra a felhasználókra vagy fiókokra vonatkoznak, amelyeknek a portálon vagy Azure Resource Manager-sablonok használatával végezhető feladatokat kell végrehajtaniuk. További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Adatbázis-szerepkörök

 A táblázatos modellhez definiált szerepkörök adatbázis-szerepkörök. Ez a szerepkör olyan tagokat tartalmaz, amelyek Azure AD-felhasználókból és biztonsági csoportokból állnak, és amelyek meghatározott engedélyekkel rendelkeznek, amelyek meghatározzák, hogy a tagok milyen műveleteket végezhetnek a modell-adatbázisokban. A rendszer az adatbázis-szerepkört külön objektumként hozza létre az adatbázisban, és csak arra az adatbázisra vonatkozik, amelyben az adott szerepkör létre lett hozva.   
  
 Alapértelmezés szerint, amikor új táblázatos modell-projektet hoz létre, a modell projektnek nincsenek szerepkörei. A szerepkörök a Visual Studióban a szerepkör-kezelő párbeszédpanel használatával definiálhatók. Ha a szerepkörök a modell tervezése során vannak meghatározva, csak a modell munkaterület-adatbázisra lesznek alkalmazva. A modell telepítésekor ugyanazok a szerepkörök lesznek alkalmazva az üzembe helyezett modellre. A modell telepítése után a kiszolgáló-és adatbázis-rendszergazdák a SSMS használatával kezelhetik a szerepköröket és a tagokat. További információ: az [adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Következő lépések

[Erőforrásokhoz való hozzáférés kezelése Azure Active Directory csoportokkal](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Adatbázis-szerepkörök és-felhasználók kezelése](analysis-services-database-users.md)  
[A kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md)  
[Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)