---
title: Hogyan és miért adhatók hozzá az alkalmazások az Azure AD szolgáltatáshoz
titleSuffix: Microsoft identity platform
description: Mit jelent az alkalmazás hozzáadása az Azure AD-hez, és hogyan juthat hozzájuk?
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
ms.openlocfilehash: 1f6fd0160988802e198ff9388cfeb3232b34b100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861119"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Alkalmazások hozzáadása az Azure AD-hez

Az Azure AD-ben két alkalmazás van jelen:

* [Alkalmazásobjektumok](app-objects-and-service-principals.md#application-object) – bár vannak [kivételek](#notes-and-exceptions), az Application Objects egy alkalmazás definíciójának tekinthető.
* [Egyszerű szolgáltatásnév](app-objects-and-service-principals.md#service-principal-object) – az alkalmazás egy példányát lehet tekinteni. Az egyszerű szolgáltatásnév általában egy Application objektumra hivatkozik, és egy alkalmazás-objektumra több egyszerű szolgáltatásnév is hivatkozhat a címtárak között.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Mik azok az alkalmazásobjektumok és honnan származnak?

Az alkalmazás- [objektumok](app-objects-and-service-principals.md#application-object) a Azure Portalban kezelhetők az alkalmazások [regisztrációjának](https://aka.ms/appregistrations) felhasználói felületén keresztül. Az Application Objects leírja az alkalmazást az Azure AD-nek, és az alkalmazás definíciójának is tekinthető, amely lehetővé teszi, hogy a szolgáltatás tudja, hogyan kell jogkivonatokat kibocsátani az alkalmazásnak a beállításai alapján. Az Application objektum csak a saját könyvtárában létezik, még akkor is, ha egy több-bérlős alkalmazás, amely más címtárakban is támogatja az egyszerű szolgáltatásokat. Az Application objektum a következők bármelyikét tartalmazhatja (valamint további, itt nem említett információkat):

* Név, embléma és közzétevő
* Átirányítási URI-k
* Titkos kódok (az alkalmazás hitelesítéséhez használt szimmetrikus és/vagy aszimmetrikus kulcsok)
* API-függőségek (OAuth)
* Közzétett API-k/erőforrások/hatókörök (OAuth)
* Alkalmazás-szerepkörök (RBAC)
* SSO-metaadatok és-konfiguráció
* A felhasználók kiépítési metaadatai és konfigurálása
* Proxy metaadatainak és konfiguráció

Az Application Objects több útvonalon is létrehozható, többek között a következőkkel:

* Alkalmazás-regisztrációk a Azure Portal
* Új alkalmazás létrehozása a Visual Studióval és az Azure AD-hitelesítés használatára való konfigurálásával
* Amikor egy rendszergazda felvesz egy alkalmazást az alkalmazás-katalógusból (amely egy egyszerű szolgáltatásnevet is létrehoz)
* Új alkalmazás létrehozása a Microsoft Graph API vagy a PowerShell használatával
* Számos más, többek között az Azure-ban és az API Explorer fejlesztői központokban való fejlesztői tapasztalatai

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Mik azok az egyszerű szolgáltatások, és honnan származnak?

A Azure Portal a [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) felületén keresztül kezelheti az [egyszerű szolgáltatásokat](app-objects-and-service-principals.md#service-principal-object) . Az egyszerű szolgáltatások az Azure AD-hez csatlakozó alkalmazásra vonatkoznak, és az alkalmazás példányának tekinthetők a címtárban. Bármely adott alkalmazáshoz legfeljebb egy Application objektum tartozhat (amely a "Home" könyvtárban van regisztrálva), és egy vagy több egyszerű szolgáltatásnév, amely az alkalmazás példányait jelképezi minden olyan címtárban, amelyben ez működik. 

Az egyszerű szolgáltatásnév a következőket tartalmazhatja:

* Hivatkozás egy alkalmazás-objektumra az Application ID tulajdonságon keresztül
* A helyi felhasználók és csoportok alkalmazás-szerepkör-hozzárendeléseinek rekordjai
* Az alkalmazáshoz megadott helyi felhasználói és rendszergazdai engedélyek rekordjai
  * Például: engedély az alkalmazás számára egy adott felhasználó e-mail-címének eléréséhez
* A helyi házirendek rekordjai, beleértve a feltételes hozzáférési szabályzatot
* Egy alkalmazás alternatív helyi beállításainak rekordjai
  * Jogcím-átalakítási szabályok
  * Attribútum-hozzárendelések (felhasználói kiépítés)
  * Címtár-specifikus alkalmazás-szerepkörök (ha az alkalmazás támogatja az egyéni szerepköröket)
  * Címtár-specifikus név vagy embléma

Az Application Objects szolgáltatáshoz hasonlóan az egyszerű szolgáltatások is létrehozhatók több útvonalon keresztül, többek között:

* Amikor a felhasználók bejelentkeznek egy, az Azure AD-vel integrált, harmadik féltől származó alkalmazásba
  * A bejelentkezés során a felhasználóknak engedélyt kell adniuk az alkalmazásnak, hogy hozzáférjenek a profiljához és egyéb engedélyekhez. A beleegyezés megadásának első személye egy egyszerű szolgáltatásnév, amely a címtárba felvenni kívánt alkalmazást jelöli.
* Amikor a felhasználók bejelentkeznek a Microsoft online szolgáltatásokba, például [Microsoft 365](https://products.office.com/)
  * Amikor előfizet Microsoft 365re, vagy megkezdi a próbaverziót, a rendszer egy vagy több egyszerű szolgáltatást hoz létre a címtárban a Microsoft 365hoz társított összes funkció biztosításához használt különböző szolgáltatásokhoz.
  * Néhány Microsoft 365-szolgáltatás, például a SharePoint, az egyszerű szolgáltatás létrehozása folyamatban van az összetevők közötti biztonságos kommunikáció érdekében, beleértve a munkafolyamatokat is.
* Amikor egy rendszergazda felvesz egy alkalmazást az alkalmazás-gyűjteményből (ez egy mögöttes alkalmazás-objektumot is létrehoz)
* Alkalmazás hozzáadása az [Azure ad Application proxy](../manage-apps/application-proxy.md) használatához
* Alkalmazás összekapcsolása egyszeri bejelentkezéshez SAML vagy jelszó egyszeri bejelentkezés (SSO) használatával
* Programozott módon a Microsoft Graph API vagy a PowerShell használatával

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hogyan kapcsolódnak egymáshoz az Application Objects és az egyszerű szolgáltatások?

Az alkalmazásnak egy alkalmazás-objektummal kell rendelkeznie a saját könyvtárában, amelyet egy vagy több egyszerű szolgáltatásnév hivatkozik az összes olyan címtárban, ahol az működik (beleértve az alkalmazás saját könyvtárát is).

![Az alkalmazás objektumai és az egyszerű szolgáltatások közötti kapcsolat megjelenítése][apps_service_principals_directory]

Az előző ábrán a Microsoft két könyvtárat tart fenn belsőleg (a bal oldalon), amelyet az alkalmazások közzétételéhez használ:

* Egy Microsoft-alkalmazásokhoz (Microsoft Services Directory)
* Egy az előre integrált külső alkalmazások számára (az App Gallery könyvtára)

Az Azure AD-vel integrált alkalmazás-közzétevők/szállítók számára közzétételi könyvtárat kell megadnia (a jobb oldalon a "néhány SaaS-címtár" lehetőség jelenik meg).

A saját maga által hozzáadott alkalmazások (amelyek a diagramon az **alkalmazásként** jelennek meg) a következők:

* Fejlesztett alkalmazások (az Azure AD-vel integráltan)
* Egyszeri bejelentkezéshez csatlakoztatott alkalmazások
* Az Azure AD alkalmazásproxy használatával közzétett alkalmazások

### <a name="notes-and-exceptions"></a>Megjegyzések és kivételek

* Nem minden egyszerű szolgáltatásnév mutat vissza egy alkalmazás-objektumra. Ha az Azure AD-t eredetileg az alkalmazások számára elérhetővé tettük, a szolgáltatás korlátozottabb volt, és az egyszerű szolgáltatásnév elegendő volt az alkalmazás identitásának létrehozásához. Az eredeti szolgáltatásnév a Windows Server Active Directory Service-fiókhoz közelebb volt. Emiatt még mindig lehetséges az egyszerű szolgáltatás létrehozása különböző elérési utakon, például az Azure AD PowerShell használatával anélkül, hogy először létre kellene hoznia egy alkalmazás-objektumot. A Microsoft Graph API-nak alkalmazás-objektumra van szüksége az egyszerű szolgáltatásnév létrehozása előtt.
* A fent ismertetett információk közül a jelenleg nem érhető el programozott módon. A következő csak a felhasználói felületen érhető el:
  * Jogcím-átalakítási szabályok
  * Attribútum-hozzárendelések (felhasználói kiépítés)
* Az egyszerű szolgáltatással és az alkalmazás-objektumokkal kapcsolatos részletes információkért tekintse meg a Microsoft Graph API-dokumentációt:
  * [Alkalmazás](/graph/api/resources/application)
  * [Szolgáltatásnév](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Az alkalmazások hogyan integrálhatók az Azure AD-vel?

Az alkalmazások az Azure AD-be való hozzáadásával kihasználhatják az általa biztosított szolgáltatások egy vagy több részét:

* Alkalmazás hitelesítése és engedélyezése
* Felhasználói hitelesítés és engedélyezés
* Egyszeri bejelentkezés az összevonás vagy a jelszó használatával
* A felhasználók üzembe helyezése és szinkronizálása
* Szerepköralapú hozzáférés-vezérlés – a címtár használatával határozhatja meg az alkalmazás szerepköreit a szerepköralapú engedélyezési ellenőrzések végrehajtásához egy alkalmazásban.
* OAuth engedélyezési szolgáltatások – a Microsoft 365 és más Microsoft-alkalmazások által használt API-khoz és erőforrásokhoz való hozzáférés engedélyezéséhez
* Alkalmazás közzététele és proxy – alkalmazás közzététele egy magánhálózat és az internet között
* Címtár-séma bővítmény attribútumai – [az egyszerű szolgáltatásnév és a felhasználói objektumok sémájának kiterjesztése](active-directory-schema-extensions.md) további adattárolásra az Azure ad-ben 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kinek van engedélye alkalmazások hozzáadására az Azure AD-példányhoz?

Habár vannak olyan feladatok, amelyeket csak a globális rendszergazdák végezhetnek el (például alkalmazások hozzáadását az alkalmazás-katalógusból, és az alkalmazás konfigurálását az alkalmazásproxy használatára) alapértelmezés szerint a címtárban lévő összes felhasználó rendelkezik jogosultságokkal az alkalmazás objektumainak regisztrálásához, amelyeket a rendszer az általuk megosztott alkalmazások és a szervezeti adatokhoz való hozzáférés biztosítása érdekében adott meg. Ha egy személy a címtár első felhasználója, hogy bejelentkezzen egy alkalmazásba, és megadja a jóváhagyást, akkor az egy egyszerű szolgáltatásnevet hoz létre a bérlőben; Ellenkező esetben a rendszer a jóváhagyáshoz szükséges adatokat a meglévő egyszerű szolgáltatásnév tárolja.

Az alkalmazások regisztrálásának és engedélyezésének lehetővé tétele a felhasználók számára kezdetben hangvételt jelenthet, de a következőket tartsa szem előtt:


* Az alkalmazások számos éve képesek voltak a Windows Server Active Directory használatát a felhasználói hitelesítéshez anélkül, hogy az alkalmazást regisztrálni kellene, vagy fel kellene venni a könyvtárba. A szervezet most már jobb láthatóságot biztosít ahhoz, hogy hány alkalmazás használja a címtárat és milyen célra.
* A felelősségeknek a felhasználókhoz való delegálása cáfolja a rendszergazda által vezérelt alkalmazások regisztrálásának és közzétételi folyamatának szükségességét. A Active Directory összevonási szolgáltatások (AD FS) (ADFS) esetében valószínű, hogy a rendszergazdának a fejlesztők nevében hozzá kellett adnia egy alkalmazást függő entitásként. A fejlesztők mostantól önkiszolgáló szolgáltatásokat is használhatnak.
* Azok a felhasználók, akik a szervezeti fiókjaikat üzleti célokra használják, jó dolog. Ha ezt követően elhagyja a szervezetet, automatikusan elvesztik a fiókjához való hozzáférést az általuk használt alkalmazásban.
* Egy rekord arról, hogy milyen adatok lettek megosztva, és melyik alkalmazás jó dolog. Az adatok minden eddiginél nagyobb mértékben szállíthatók, és hasznos, hogy egyértelmű nyilvántartást kapjanak arról, hogy ki milyen adatokkal rendelkezik az alkalmazásokkal.
* Azok az API-tulajdonosok, akik az Azure AD-t használják a OAuth, döntik el, hogy pontosan milyen engedélyeket adhatnak a felhasználók az alkalmazásokba Csak a rendszergazdák adhatnak hozzá nagyobb hatóköröket és nagyobb jelentőségű engedélyeket, míg a felhasználói beleegyezik a felhasználók saját információi és képességei körébe.
* Amikor egy felhasználó hozzáad vagy engedélyez egy alkalmazást az adateléréshez, az esemény naplózható, így megtekintheti a Azure Portalon belüli naplózási jelentéseket annak meghatározására, hogy az alkalmazás hogyan lett hozzáadva a címtárhoz.

Ha továbbra is meg szeretné akadályozni, hogy a címtárban lévő felhasználók regisztrálják az alkalmazásokat, és ne jelentkezzenek be az alkalmazásokba rendszergazdai jóváhagyás nélkül, két beállítás közül választhat, amelyek kikapcsolhatják ezeket a képességeket:

* Annak megakadályozása, hogy a felhasználók a saját nevében fogadják el az alkalmazásokat:
  1. A Azure Portal nyissa meg a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) szakaszt a vállalati alkalmazások területen.
  2. A felhasználók megváltoztathatják **, hogy az alkalmazások a vállalati adatokhoz való hozzáférést** **nem**.
     
     > [!NOTE]
     > Ha úgy dönt, hogy kikapcsolja a felhasználói jóváhagyást, a felhasználónak hozzá kell járulnia minden olyan új alkalmazáshoz, amelyet a felhasználónak használnia kell.

* Annak megakadályozása, hogy a felhasználók regisztrálják a saját alkalmazásaikat:
  1. A Azure Portal lépjen a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) szakaszra Azure Active Directory
  2. A módosítással a **felhasználók regisztrálhatják az alkalmazásokat** a **nem** értékre.

> [!NOTE]
> A Microsoft maga az alapértelmezett konfigurációt használja a felhasználók számára az alkalmazások regisztrálásához és a saját nevében lévő alkalmazásokhoz való hozzájárulásukat.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
