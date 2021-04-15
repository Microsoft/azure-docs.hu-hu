---
title: A rendszergazdai jóváhagyás munkafolyamatának konfigurálása – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja úgy a végfelhasználókat, hogy rendszergazdai jóváhagyást igénylő alkalmazásokhoz kérjenek hozzáférést.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: iangithinji
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9811c3d1833a02ad3cbaf22b9f0b31fd2da5bb6d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375185"
---
# <a name="configure-the-admin-consent-workflow"></a>A rendszergazdai jóváhagyás munkafolyamatának konfigurálása

Ez a cikk azt ismerteti, hogyan engedélyezheti a rendszergazdai jóváhagyás munkafolyamat-funkcióját, amellyel a végfelhasználók rendszergazdai jóváhagyást igénylő alkalmazásokhoz kérhetnek hozzáférést.

Rendszergazdai hozzájárulási munkafolyamat nélkül a felhasználói jóváhagyást letiltó bérlő felhasználója le lesz tiltva, amikor olyan alkalmazáshoz próbál hozzáférni, amelyhez engedélyre van szükség a szervezeti adatok eléréséhez. A felhasználó egy általános hibaüzenetet kap, amely szerint nem jogosult hozzáférni az alkalmazáshoz, és segítséget kell kérnie a rendszergazdától. A felhasználó azonban gyakran nem tudja, hogy kit kell kapcsolatba lépnie, ezért vagy fel kell vennie a kapcsolatot, vagy új helyi fiókot kell létrehoznia az alkalmazásban. Még ha a rendszergazda értesítést is kap, nem mindig van olyan gördülékeny folyamat, amely lehetővé teszi a rendszergazda számára a hozzáférés megadását és a felhasználók értesítését.
 
A rendszergazdai jóváhagyás munkafolyamata biztonságos lehetőséget biztosít a rendszergazdáknak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz való hozzáférés megadására. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tud hozzájárulást adni, rendszergazdai jóváhagyásra vonatkozó kérést küldhet. A kérelmet a rendszer e-mailben küldi el a felülvizsgálóként kijelölt rendszergazdáknak. A felülvizsgáló a kérelemre vonatkozó műveletet is el fog sajátni, és a felhasználó értesítést kap a műveletről.

A kérelmek jóváhagyásához a felülvizsgálónak globális rendszergazdának, felhőalkalmazás-rendszergazdának vagy alkalmazás-rendszergazdának kell lennie. A felülvizsgálónak már hozzá kell rendelnie egy ilyen rendszergazdai szerepkört; A jogosultságai egyszerűen csak felülvizsgálóként való megtervezése nem emeli a jogosultsági szintjüket.

## <a name="enable-the-admin-consent-workflow"></a>A rendszergazdai jóváhagyás munkafolyamatának engedélyezése

A rendszergazdai jóváhagyás munkafolyamatának engedélyezése és a felülvizsgálók kiválasztása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Kattintson **a minden szolgáltatás** elemre a bal oldali navigációs menü tetején. Megnyílik **a Azure Active Directory bővítmény.**
3. A szűrő keresőmezőbe írja be a "**Azure Active Directory"** parancsot, és válassza ki **Azure Active Directory** elemet.
4. A navigációs menüben kattintson a Vállalati **alkalmazások elemre.** 
5. A **Kezelés alatt** válassza a Felhasználói beállítások **lehetőséget.**
6. A **Rendszergazdai hozzájárulási kérelmek alatt** állítsa be, hogy A **felhasználók** rendszergazdai jóváhagyást kérhetnek az olyan alkalmazásokhoz, amelyekhez nem tudnak igen **engedélyt adni.**

   ![Rendszergazdai jóváhagyás munkafolyamat-beállításainak konfigurálása](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Adja meg az alábbi beállításokat:

   * **Válassza ki a felhasználókat a rendszergazdai hozzájárulási kérelmek áttekintésére.** Jelölje ki a munkafolyamat felülvizsgálóit olyan felhasználók közül, akik globális rendszergazdai, felhőalkalmazás-rendszergazdai és alkalmazás-rendszergazdai szerepkörökkel rendelkezik.
   * **A kiválasztott felhasználók e-mailes értesítéseket kapnak a kérelmekről.** E-mail-értesítések engedélyezése vagy letiltása a felülvizsgálóknak kérés esetén.  
   * **A kiválasztott felhasználók a kérelem lejáratára vonatkozó emlékeztetőket kapnak.** Emlékeztető e-mail-értesítések engedélyezése vagy letiltása a felülvizsgálóknak, amikor egy kérés hamarosan lejár.  
   * **A hozzájárulási kérelem (nap) után lejár.** Adja meg, hogy a kérelmek mennyi ideig maradnak érvényesek.

7. Kattintson a **Mentés** gombra. A funkció engedélyezése akár egy órát is el is vehet.

> [!NOTE]
> A munkafolyamat felülvizsgálóit a Rendszergazdai hozzájárulási kérelmek kiválasztása felülvizsgálók listájának módosításával adhat hozzá **vagy távolíthat el.** Vegye figyelembe, hogy a funkció jelenlegi korlátozása az, hogy a felülvizsgálók továbbra is felülvizsgálhatják a felülvizsgálóként megjelölt kérelmeket.

## <a name="how-users-request-admin-consent"></a>Hogyan kérik a felhasználók a rendszergazdai jóváhagyást?

A rendszergazdai jóváhagyás munkafolyamatának engedélyezése után a felhasználók rendszergazdai jóváhagyást kérhetnek egy olyan alkalmazáshoz, amely jóváhagyására jogosulatlanok. A következő lépések ismertetik a jóváhagyás kérelmezése során szerzett felhasználói élményt. 

1. A felhasználó megpróbál bejelentkezni az alkalmazásba.

2. Megjelenik **a Jóváhagyás szükséges** üzenet. A felhasználó bejeleti az alkalmazáshoz való hozzáférésre vonatkozó indoklást, majd kiválasztja **a Jóváhagyás kérése lehetőséget.**

   ![A Jóváhagyás szükséges párbeszédpanel képernyőképe, ahol jóváhagyást kérhet.](media/configure-admin-consent-workflow/end-user-justification.png)

3. A **Kérés elküldve** üzenet megerősíti, hogy a kérés el lett küldve a rendszergazdának. Ha a felhasználó több kérelmet küld, csak az első kérés lesz elküldve a rendszergazdának.

   ![A kérés elküldését megerősítő üzenetet bemutató képernyőkép.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. A felhasználó e-mailes értesítést kap, ha a kérését jóváhagyják, elutasítják vagy blokkolják. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Rendszergazdai hozzájárulási kérelmek áttekintése és a szükséges intézkedések

A rendszergazdai hozzájárulási kérelmek áttekintése és a művelet:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) rendszergazdai hozzájárulási munkafolyamat egyik regisztrált felülvizsgálójaként.
2. Válassza **a minden** szolgáltatás lehetőséget a bal oldali navigációs menü tetején. Megnyílik **a Azure Active Directory bővítmény.**
3. A szűrő keresőmezőbe írja be a "**Azure Active Directory"** parancsot, és válassza ki **Azure Active Directory** elemet.
4. A navigációs menüben kattintson a Vállalati **alkalmazások elemre.**
5. A **Tevékenység alatt** válassza a **Rendszergazdai hozzájárulási kérelmek lehetőséget.**

   > [!NOTE]
   > A felülvizsgálók csak azokat a rendszergazdai kérelmeket látják, amelyek azután jöttek létre, hogy felülvizsgálóként jelölték meg őket.

1. Válassza ki a kért alkalmazást.
2. Tekintse át a kérelem részleteit:  

   * A hozzáférést kérő felhasználók és azok okának a megtekintése érdekében válassza a **Kért lapfület.**
   * Az alkalmazás által kért engedélyeket az Engedélyek és jóváhagyás áttekintése lehetőséget **választva láthatja.**

8. Értékelje ki a kérést, és a megfelelő műveletet:

   * **Hagyja jóvá a kérelmet.** Kérelem jóváhagyásához adjon rendszergazdai jóváhagyást az alkalmazásnak. A kérelem jóváhagyása után minden kérelmező értesítést kap arról, hogy hozzáférést kaptak.  
   * **Tiltsa le a kérést.** A kérelem megtagadása érdekében meg kell indokolni, hogy a kérelem minden kérelmezője számára biztosított legyen. A kérelem megtagadás után minden kérelmező értesítést kap arról, hogy az alkalmazáshoz való hozzáférése meg lett tagadva. A kérelmek elutasítása nem akadályozza meg, hogy a felhasználók a jövőben ismét rendszergazdai jóváhagyást kérjenek az alkalmazáshoz.  
   * **Tiltsa le a kérést.** A kérelem blokkolása érdekében meg kell indokolni, hogy az összes kérelmezőt meg tudja adni. Ha egy kérés blokkolva van, a rendszer értesíti az összes kérelmezőt az alkalmazáshoz való hozzáférés megtagadásról. A kérések blokkolása szolgáltatásnév-objektumot hoz létre a bérlőben letiltott állapotban. A felhasználók a jövőben nem kérhetnek rendszergazdai jóváhagyást az alkalmazáshoz.
 
## <a name="email-notifications"></a>E-mail-értesítések
 
Ha konfigurálva van, az összes felülvizsgáló e-mailes értesítést kap, ha:

* Új kérés lett létrehozva
* Egy kérés lejárt
* Egy kérés közeledik a lejárati dátumhoz  
 
A kérelmező a következő e-mail-értesítéseket kapja:

* Új hozzáférési kérést küldnek be
* A kérésük lejárt
* A kérésüket elutasították vagy blokkolták
* A kérelem jóvá lett hagyva
 
## <a name="audit-logs"></a>Naplók 
 
Az alábbi táblázat a rendszergazdai jóváhagyás munkafolyamatához elérhető forgatókönyveket és auditértékeket ismerteti.

|Eset  |Naplózási szolgáltatás  |Naplózási kategória  |Naplózási tevékenység  |Audit Actor  |Az auditnaplók korlátozásai  |
|---------|---------|---------|---------|---------|---------|
|A hozzájárulási kérelem munkafolyamatát engedélyező rendszergazda        |Hozzáférési felülvizsgálatok           |UserManagement           |Cégirányítási szabályzat sablonjának létrehozása          |Alkalmazáskörnyezet            |A felhasználói környezet jelenleg nem található            |
|A hozzájárulási kérelem munkafolyamatának rendszergazdai letiltása       |Hozzáférési felülvizsgálatok           |UserManagement           |Cégirányítási szabályzat sablonjának törlése          |Alkalmazáskörnyezet            |A felhasználói környezet jelenleg nem található           |
|A hozzájárulási munkafolyamat konfigurációjának rendszergazdai frissítése        |Hozzáférési felülvizsgálatok           |UserManagement           |Cégirányítási szabályzat sablonjának frissítése          |Alkalmazáskörnyezet            |A felhasználói környezet jelenleg nem található           |
|Rendszergazdai hozzájárulási kérelmet hozó végfelhasználó egy alkalmazáshoz       |Hozzáférési felülvizsgálatok           |Szabályzat         |Kérés létrehozása           |Alkalmazáskörnyezet            |A felhasználói környezet jelenleg nem található           |
|Rendszergazdai hozzájárulási kérelmet jóváhagyó felülvizsgálók       |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználó-hozzáférés)           |Az üzleti folyamat összes kérésének jóváhagyása          |Alkalmazáskörnyezet            |Jelenleg nem találja azt a felhasználói környezetet vagy alkalmazásazonosítót, amely rendszergazdai jóváhagyást kapott.           |
|Rendszergazdai hozzájárulási kérelmet elutasító felülvizsgálók       |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználó-hozzáférés)           |Az üzleti folyamat összes kérésének jóváhagyása          |Alkalmazáskörnyezet            | Jelenleg nem találja annak az aktornak a felhasználói környezetét, amely megtagadta a rendszergazdai hozzájárulási kérelmet          |

## <a name="faq"></a>GYIK 

**Bekapcsoltam ezt a munkafolyamatot, de a funkció tesztelésekor miért nem látom az új "Jóváhagyás szükséges" kérést, amely hozzáférést kér?**

A funkció bekapcsolása után akár 60 percig is eltarthat, hogy a végfelhasználók látják a frissítést. Az **EnableAdminConsentRequests** érték API-ban való megtekintésével ellenőrizheti, hogy a konfiguráció megfelelően életbe `https://graph.microsoft.com/beta/settings` került-e.

**Felülvizsgálóként miért nem látom az összes függőben lévő kérelmet?**

A felülvizsgálók csak azokat a rendszergazdai kérelmeket láthatják, amelyek azután jöttek létre, hogy felülvizsgálóként jelölték meg őket. Így ha nemrég adva lett felülvizsgálóként, a hozzárendelés előtt létrehozott kérelmek nem fognak látszatra.

**Felülvizsgálóként miért látok több kérést ugyanannak az alkalmazásnak?**
  
Ha egy alkalmazásfejlesztő úgy konfigurálta az alkalmazását, hogy statikus és dinamikus hozzájárulással kérjen hozzáférést a végfelhasználói adatokhoz, két rendszergazdai hozzájárulási kérelem fog látni. Az egyik kérés a statikus engedélyeket, a másik a dinamikus engedélyeket jelöli.

**Kérelmezőként ellenőriznem kell a kérésem állapotát?**  

Nem, a kérelmezők jelenleg csak e-mailes értesítéseken keresztül kaphatják meg a frissítéseket.

**Felülvizsgálóként jóváhagyhatja az alkalmazást, de nem mindenki számára?**
 
Ha aggódik a rendszergazdai jóváhagyás megadása és a bérlő összes felhasználója számára az alkalmazás használatának engedélyezésével kapcsolatban, javasoljuk, hogy tiltsa le a kérést. Ezután manuálisan adja meg a rendszergazdai jóváhagyást az alkalmazáshoz való hozzáférés korlátozásával a felhasználó-hozzárendelés és a felhasználók vagy csoportok alkalmazáshoz való hozzárendelésével. További információ: Felhasználók és csoportok [hozzárendelésének módszerei.](./assign-user-or-group-access-portal.md)

## <a name="next-steps"></a>Következő lépések

Az alkalmazásokhoz való hozzájárulással kapcsolatos további információkért lásd: Azure Active Directory [hozzájárulási keretrendszer.](../develop/consent-framework.md)

[A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)

[Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)

[Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

[Azure AD a Microsoft Q&A-n](/answers/topics/azure-active-directory.html)
