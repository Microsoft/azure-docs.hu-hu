---
title: Hogyan és miért adhatók hozzá az alkalmazások az Azure AD szolgáltatáshoz
titleSuffix: Microsoft identity platform
description: Mit jelent, hogy egy alkalmazást hozzá kell adni az Azure AD-hez, és hogyan jutnak el oda?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: ac02638dfdef4867e93e277175df82be18be66a7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530098"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Alkalmazások hozzáadása az Azure AD-hez – hogyan és miért

Az Azure AD-alkalmazásoknak két reprezentációja van:

* [Alkalmazásobjektumok](app-objects-and-service-principals.md#application-object) – Bár vannak [kivételek,](#notes-and-exceptions)az alkalmazásobjektumok egy alkalmazás definíciójának tekinthetők.
* [Szolgáltatásnév –](app-objects-and-service-principals.md#service-principal-object) Egy alkalmazás példányának tekinthető. A szolgáltatásnév általában egy alkalmazásobjektumra hivatkozik, és egy alkalmazásobjektumra több szolgáltatásnév is hivatkozhat a különböző könyvtárakban.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Mik azok az alkalmazásobjektumok, és honnan jönnek?

Az [alkalmazásregisztrációk használatával a](app-objects-and-service-principals.md#application-object) Azure Portal kezelheti az [alkalmazásobjektumokat.](https://aka.ms/appregistrations) Az alkalmazásobjektumok leírják az alkalmazást az Azure AD-nek, és figyelembe lehet venni az alkalmazás definícióját, így a szolgáltatás tudni tudja, hogyan kell jogkivonatokat kiíratni az alkalmazásnak a beállításai alapján. Az alkalmazásobjektum csak a saját könyvtárában fog létezni, még akkor is, ha az egy több-bérlős alkalmazás, amely más címtárakban található szolgáltatásnéveket támogat. Az alkalmazásobjektum a következők bármelyikét tartalmazhatja (valamint az itt nem említett további információkat):

* Név, embléma és közzétevő
* Átirányítási URI-k
* Titkos kulcsok (az alkalmazás hitelesítéséhez használt szimmetrikus és/vagy aszimmetrikus kulcsok)
* API-függőségek (OAuth)
* Közzétett API-k/erőforrások/hatókörök (OAuth)
* Alkalmazás-szerepkörök (RBAC)
* SSO-metaadatok és -konfiguráció
* Felhasználóátépítési metaadatok és konfiguráció
* Proxy metaadatai és konfigurációja

Az alkalmazásobjektumok több útvonalon is létre lehet hozva, például:

* Alkalmazásregisztrációk a Azure Portal
* Új alkalmazás létrehozása a Visual Studio és konfigurálása Az Azure AD-hitelesítés használatára
* Amikor egy rendszergazda hozzáad egy alkalmazást az alkalmazáskatatárból (amely szintén létrehoz egy szolgáltatásnévt)
* Új Microsoft Graph létrehozása a Microsoft Graph API vagy a PowerShell használatával
* Sok más, többek között különböző fejlesztői élmények az Azure-ban és az API Explorerben a fejlesztői központokban

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Mik azok a szolgáltatásnévk, és honnan jönnek?

A szolgáltatásnév a vállalati alkalmazások [Azure Portal](app-objects-and-service-principals.md#service-principal-object) kezelheti a [szolgáltatásnévvel.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Az Azure AD-hez csatlakozó alkalmazásokat szolgáltatásnév szabályozza, és az alkalmazás a címtárban található példányának tekinthető. Egy adott alkalmazáshoz legalább egy alkalmazásobjektum lehet (amely egy "home" könyvtárban van regisztrálva), és egy vagy több szolgáltatásnév-objektum, amely az alkalmazás példányait képviseli minden könyvtárban, amelyben működik. 

A szolgáltatásnév a következőket tartalmazhatja:

* Alkalmazásobjektumra való hivatkozás az alkalmazásazonosító tulajdonságon keresztül
* Helyi felhasználók és csoportok alkalmazás-szerepkör-hozzárendeléseit rögzíti
* Az alkalmazásnak megadott helyi felhasználói és rendszergazdai engedélyek rekordjai
  * Például: engedély az alkalmazás számára egy adott felhasználó e-mail-címének elérésére
* Helyi szabályzatok rekordjai, beleértve a feltételes hozzáférési szabályzatot is
* Egy alkalmazás alternatív helyi beállításainak rekordjai
  * Jogcím-átalakítási szabályok
  * Attribútumleképezések (felhasználóátépítés)
  * Címtárspecifikus alkalmazás-szerepkörök (ha az alkalmazás támogatja az egyéni szerepköröket)
  * Címtárspecifikus név vagy embléma

Az alkalmazásobjektumokhoz hasonló szolgáltatásnév is több útvonalon is létre lehet hozva, például:

* Amikor a felhasználók bejelentkeznek egy, az Azure AD-val integrált külső alkalmazásba
  * A bejelentkezés során a felhasználóknak engedélyt kell adniuk az alkalmazásnak a profiljukhoz és egyéb engedélyük eléréséhez. A hozzájárulás első személye az alkalmazást képviselő szolgáltatásnév címtárhoz való hozzáadását okozza.
* Amikor a felhasználók bejelentkeznek a Microsoft online szolgáltatások, például [Microsoft 365](https://products.office.com/)
  * A Microsoft 365 előfizetésekor vagy a próbaverzió megkezdésekor egy vagy több szolgáltatásnév jön létre a címtárban, amelyek a Microsoft 365.
  * Egyes Microsoft 365 szolgáltatások, például a SharePoint, folyamatosan hoznak létre szolgáltatásbiztonsági tagokat, hogy biztonságos kommunikációt biztosítsanak az összetevők, köztük a munkafolyamatok között.
* Amikor egy rendszergazda hozzáad egy alkalmazást az alkalmazáskatatárból (ezzel egy mögöttes alkalmazásobjektumot is létrehoz)
* Alkalmazás hozzáadása az [Azure AD-alkalmazásproxy](../manage-apps/application-proxy.md)
* Alkalmazás csatlakoztatása egyszeri bejelentkezéshez SAML vagy jelszó egyszeri bejelentkezés (SSO) használatával
* Programozott módon a Microsoft Graph API-n vagy a PowerShellen keresztül

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hogyan kapcsolódnak egymáshoz az alkalmazásobjektumok és a szolgáltatásnévk?

Egy alkalmazás egy alkalmazásobjektummal rendelkezik a kezdőkönyvtárában, amelyet egy vagy több szolgáltatásnév hivatkozik minden olyan címtárban, amelyben működik (beleértve az alkalmazás kezdőkönyvtárát).

![Megjeleníti az alkalmazásobjektumok és a szolgáltatásnév közötti kapcsolatot][apps_service_principals_directory]

Az előző ábrán a Microsoft két belső könyvtárat tart fenn (a bal oldalon látható), amelyek segítségével közzéteheti az alkalmazásokat:

* Egy a Microsoft Apps (Microsoft-szolgáltatások könyvtár) számára
* Az egyik az előre integrált külső alkalmazásokhoz (alkalmazásgyűjtemény könyvtára)

Az Azure AD-val integrálható alkalmazás-közzétevőknek/szállítóknak közzétevői címtárral kell (a jobb oldalon "Néhány SaaS-címtárként" jelennek meg).

A saját maga által (a **diagramon alkalmazásként (sajátként) ábrázolt)** alkalmazások a következők:

* Az Ön által fejlesztett alkalmazások (az Azure AD-val integrálva)
* Az egyszeri bejelentkezéshez csatlakoztatott alkalmazások
* Az Azure AD-alkalmazásproxyval közzétett alkalmazások

### <a name="notes-and-exceptions"></a>Megjegyzések és kivételek

* Nem minden szolgáltatásnév mutat vissza egy alkalmazásobjektumra. Amikor az Azure AD eredetileg létre lett hozva, az alkalmazásoknak nyújtott szolgáltatások korlátozottabbak voltak, és a szolgáltatásnév elegendő volt az alkalmazásidentitás létrehozásához. Az eredeti egyszerű szolgáltatás közelebb volt a Windows Server Active Directory szolgáltatásfiókhoz. Emiatt továbbra is lehetséges szolgáltatásnévket létrehozni különböző útvonalakon keresztül, például az Azure AD PowerShell használatával anélkül, hogy először létrehoznál egy alkalmazásobjektumot. A Microsoft Graph API-nak szüksége van egy alkalmazásobjektumra a szolgáltatásnév létrehozása előtt.
* Jelenleg nem minden fent leírt információ érhető el programozott módon. A következők csak a felhasználói felületen érhetők el:
  * Jogcím-átalakítási szabályok
  * Attribútumleképezések (felhasználóátépítés)
* A szolgáltatásnévvel és az alkalmazásobjektumokkal kapcsolatos részletesebb információkért tekintse meg az Microsoft Graph API referenciadokumentációját:
  * [Alkalmazás](/graph/api/resources/application)
  * [Szolgáltatásnév](/graph/api/resources/serviceprincipal)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Miért integrálhatók az alkalmazások az Azure AD-be?

Az Azure AD-hez hozzáadott alkalmazások kihasználják az azure-beli szolgáltatások egy vagy több szolgáltatását, többek között a következőket:

* Alkalmazáshitelesítés és -engedélyezés
* Felhasználói hitelesítés és engedélyezés
* SSO összevonás vagy jelszó használatával
* Felhasználók kiépítése és szinkronizálása
* Szerepköralapú hozzáférés-vezérlés – A címtár használatával alkalmazás-szerepköröket határozhat meg szerepköralapú engedélyezési ellenőrzések végrehajtásához egy alkalmazásban
* OAuth-engedélyezési szolgáltatások – A microsoftos Microsoft 365 más Microsoft-alkalmazások használják az API-khoz/erőforrásokhoz való hozzáférés engedélyezése érdekében
* Alkalmazás közzététele és proxy – Alkalmazás közzététele magánhálózatról az internetre
* Címtársémák bővítményattribútumai – [A szolgáltatásnév](active-directory-schema-extensions.md) és a felhasználói objektumok sémájának kiterjesztése további adatok Azure AD-beli tárolására 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kinek van engedélye alkalmazások hozzáadására az Azure AD-példányhoz?

Bár vannak olyan feladatok, amelyeket csak a globális rendszergazdák tudnak elvégezni (például alkalmazások hozzáadása az alkalmazáskatatárból, vagy egy alkalmazás konfigurálása az alkalmazásproxy használatára), alapértelmezés szerint a címtárban lévő összes felhasználónak jogosultsága van a fejlesztés alatt lévő alkalmazásobjektumok regisztrálására, valamint saját belátása szerint, hogy mely alkalmazásokat osztják meg vagy osztják meg hozzájárulással a szervezeti adataikhoz. Ha egy személy az első olyan felhasználó a címtárban, aki bejelentkezik egy alkalmazásba, és megadja a hozzájárulást, az létrehoz egy szolgáltatásnévt a bérlőben; Ellenkező esetben a hozzájárulási engedély adatait a meglévő szolgáltatásnév tárolja.

Ha engedélyezi a felhasználóknak az alkalmazások regisztrálását és az alkalmazásokba való beleegyezést, az elsőre fontosnak tűnik, de tartsa szem előtt a következőket:


* Az alkalmazások évek óta képesek voltak Windows Server Active Directory felhasználók hitelesítésére anélkül, hogy az alkalmazásnak regisztrálva vagy rögzítve kellene lennie a címtárban. A szervezet így jobban át tudja látni, hogy pontosan hány alkalmazás használja a címtárat, és milyen célra.
* Ezeknek a feladatoknak a felhasználókra való delegálásával nincs szükség rendszergazda által vezérelt alkalmazásregisztrációs és -közzétételi folyamatra. A Active Directory összevonási szolgáltatások (AD FS) (ADFS) esetében valószínű volt, hogy egy rendszergazdának egy alkalmazást kellett hozzáadnia függő félként a fejlesztők nevében. Most már a fejlesztők önkiszolgálók is tudnak.
* Az alkalmazásokba vállalati fiókjukkal üzleti célból bejelentkező felhasználók jó dolog. Ha ezt követően elhagyják a szervezetet, automatikusan elveszítik a hozzáférésüket a fiókjukhoz a használt alkalmazásban.
* Jó, ha van egy rekord arról, hogy mely adatok melyik alkalmazással vannak megosztva. Az adatok minden másnál jobban szállíthatók, és hasznos, ha egyértelműen rögzíti, hogy ki mely alkalmazásokkal osztotta meg az adatokat.
* Az Azure AD for OAuthot használó API-tulajdonosok írják le, hogy a felhasználók pontosan milyen engedélyeket adhatnak meg az alkalmazásoknak, és mely engedélyekhez kell egy rendszergazdának jóváhagyást kérnie. Csak a rendszergazdák járulnak hozzá a nagyobb hatókörök és a jelentősebb engedélyekhez, a felhasználói hozzájárulás hatóköre pedig a felhasználók saját adataira és képességeire terjed ki.
* Amikor egy felhasználó hozzáad egy alkalmazást az adataihoz, vagy engedélyezi az adatokhoz való hozzáférést, az esemény naplóba is bekerülhet, így a Azure Portal auditjelentéseit megtekintve megállapíthatja, hogyan lett hozzáadva egy alkalmazás a címtárhoz.

Ha továbbra is meg szeretné akadályozni, hogy a címtár felhasználói alkalmazásokat regisztrálnak, és rendszergazdai jóváhagyás nélkül jelentkezzenek be az alkalmazásokba, két beállítással kikapcsolhatja ezeket a képességeket:

* Annak megakadályozása, hogy a felhasználók saját nevében járulnak hozzá az alkalmazásokhoz:
  1. A Azure Portal a Vállalati alkalmazások területen kattintson [a](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) Felhasználói beállítások szakaszra.
  2. Módosítsa **a Users can consent to apps accessing company data on theirhalf to be (A felhasználók a nevükben** hozzáférhetnek a vállalati adatokhoz) helyett a **No (Nem) adatokat.**
     
     > [!NOTE]
     > Ha úgy dönt, hogy kikapcsolja a felhasználói jóváhagyást, a rendszergazdának hozzá kell majd járulni minden új alkalmazáshoz, amit a felhasználónak használnia kell.

* Annak megakadályozása, hogy a felhasználók saját alkalmazásokat regisztrálnak:
  1. A Azure Portal kattintson a Felhasználói [beállítások szakaszra](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) a Azure Active Directory
  2. Módosítsa a **Users can register applications (Felhasználók regisztrálják az alkalmazásokat) et No** **(Nem) -re.**

> [!NOTE]
> Maga a Microsoft az alapértelmezett konfigurációt használja, és a felhasználók saját nevükben regisztrálják az alkalmazásokat és járulnak hozzá az alkalmazásokhoz.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
