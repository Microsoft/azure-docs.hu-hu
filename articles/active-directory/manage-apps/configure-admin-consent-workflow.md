---
title: A rendszergazdai beleegyező munkafolyamat konfigurálása – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a végfelhasználók számára, hogy hozzáférést kérjenek a rendszergazdai jogosultságot igénylő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d89ea0cbc7d1e0379a9cbfce40f11d4f8ac93f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643762"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Rendszergazdai engedélyezési munkafolyamat (előzetes verzió) konfigurálása

Ez a cikk azt ismerteti, hogyan engedélyezhető a rendszergazdai engedélyezési munkafolyamat (előzetes verzió) funkció, amely lehetővé teszi a végfelhasználók számára, hogy hozzáférést kérjenek a rendszergazdai jogosultságot igénylő alkalmazásokhoz.

Rendszergazdai jogosultsági szintű munkafolyamat nélkül a felhasználó egy olyan bérlőnél, amelynél a felhasználói beleegyezik, le lesz tiltva, amikor olyan alkalmazáshoz próbálnak hozzáférni, amely a szervezeti adathozzáféréshez szükséges engedélyekkel rendelkezik. A felhasználó általános hibaüzenetet kap arról, hogy nem engedélyezett az alkalmazáshoz való hozzáférés, és a rendszergazdától kérhet segítséget. Gyakran azonban a felhasználó nem tudja, hogy kivel kell kapcsolatba lépnie, hogy az alkalmazásban új helyi fiókot adjon meg vagy hozzon létre. Még akkor is, ha a rendszergazda értesítést kap, nincs mindig olyan gördülékeny folyamat, amely segítséget nyújt a rendszergazda számára a hozzáférés engedélyezéséhez és a felhasználók értesítéséhez.
 
A rendszergazdai hozzájárulási munkafolyamat lehetővé teszi, hogy a rendszergazdák biztonságos módon biztosítsák a hozzáférést a rendszergazdai jóváhagyást igénylő alkalmazásokhoz. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tud beleegyezést adni, akkor a rendszergazdai jóváhagyásra vonatkozó kérelmet küldhet. A rendszer e-mailben küldi el a kérelmet a véleményezők számára kijelölt rendszergazdáknak. A felülvizsgáló végrehajtja a kérést, és a felhasználó értesítést kap a műveletről.

A kérések jóváhagyásához a véleményezőnek globális rendszergazdának, felhőalapú alkalmazás-rendszergazdának vagy alkalmazás-rendszergazdának kell lennie. A véleményezőnek már rendelkeznie kell a hozzájuk rendelt rendszergazdai szerepkörök valamelyikével. Egyszerűen jelölje ki őket, mivel a véleményezők nem emelik fel a jogosultságokat.

## <a name="enable-the-admin-consent-workflow"></a>Rendszergazdai engedélyekkel rendelkező munkafolyamat engedélyezése

A rendszergazdai engedélyekkel rendelkező munkafolyamat engedélyezése és a véleményezők kiválasztása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. A bal oldali navigációs menü tetején kattintson a **minden szolgáltatás** elemre. Megnyílik a **Azure Active Directory bővítmény** .
3. A szűrő keresése mezőbe írja be a "**Azure Active Directory**" kifejezést, majd válassza ki **a Azure Active Directory** elemet.
4. A navigációs menüben kattintson a **vállalati alkalmazások** elemre. 
5. A **kezelés** területen válassza a **felhasználói beállítások** lehetőséget.
6. A **rendszergazda által megadott kérések (előzetes verzió)** alatt állítsa be a felhasználók számára, hogy rendszergazdai jogosultságot **kérjenek az alkalmazásoknak, amelyek nem tudnak** hozzájárulni az **Igen** értékre

   ![Rendszergazdai engedélyekkel rendelkező munkafolyamat beállításainak konfigurálása](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Adja meg az alábbi beállításokat:

   * **Válassza a felhasználók lehetőséget a rendszergazdai engedélyezési kérelmek áttekintéséhez**. Válassza ki a munkafolyamathoz tartozó véleményezőket a globális rendszergazda, a Felhőbeli alkalmazás-rendszergazda és az alkalmazás-rendszergazda szerepkörrel rendelkező felhasználók készletében.
   * **A kiválasztott felhasználók e-mail-értesítéseket kapnak a kérelmekről**. Az e-mail-értesítések engedélyezése vagy letiltása a felülvizsgálók számára a kérelem elküldésekor.  
   * A **kiválasztott felhasználók megkapják a kérelmek lejáratára vonatkozó emlékeztetőket**. Az emlékeztető e-mail-értesítéseinek engedélyezése vagy letiltása a felülvizsgálók számára, ha egy kérés hamarosan lejár.  
   * A **beleegyező kérelem érvényessége (nap) után lejár**. Itt adhatja meg, hogy a kérelmek meddig maradjanak érvényesek.

7. Kattintson a **Mentés** gombra. Akár egy óráig is eltarthat, amíg a funkció engedélyezve lesz.

> [!NOTE]
> A munkafolyamat felülvizsgálók hozzáadásához vagy eltávolításához módosítsa a rendszergazdai jogosultságok **kérése – véleményezők listájának kiválasztása lehetőséget** . Vegye figyelembe, hogy a szolgáltatás jelenlegi korlátozása, hogy a felülvizsgálók megőrzik a véleményezők számára kijelölt kérelmek felülvizsgálatának lehetőségét.

## <a name="how-users-request-admin-consent"></a>Rendszergazdai engedély kérése a felhasználóktól

Ha a rendszergazdai hozzájárulási munkafolyamat engedélyezve van, a felhasználók eligényelhetik a rendszergazdai jóváhagyást egy olyan alkalmazáshoz, amelyhez nem jogosultak beleegyezésre. A következő lépések a felhasználó felhasználói élményét írják le jóváhagyás kérelmezése során. 

1. A felhasználó megpróbál bejelentkezni az alkalmazásba.

2. A **jóváhagyás szükséges** üzenet jelenik meg. A felhasználó megadhatja az alkalmazáshoz való hozzáféréshez szükséges indoklást, majd kiválasztja a **kérelem jóváhagyását**.

   ![A képernyőképen egy kötelező jóváhagyást kérő párbeszédpanel jelenik meg, amelyen jóváhagyást igényelhet.](media/configure-admin-consent-workflow/end-user-justification.png)

3. A **kérelem elküldött** üzenete megerősíti, hogy a kérést elküldték a rendszergazdának. Ha a felhasználó több kérést küld, csak az első kérést küldi el a rendszer a rendszergazdának.

   ![A képernyőképen látható a kérelem elküldése megerősítve.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. A felhasználó e-mailben értesítést kap, ha a kérelmét jóváhagyják, megtagadják vagy letiltották. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Rendszergazdai engedélyekkel kapcsolatos kérelmek áttekintése és beavatkozása

A rendszergazdai engedélyezési kérelmek áttekintése és a művelet végrehajtása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a rendszergazdai engedélyezési munkafolyamat egyik regisztrált véleményezője.
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali navigációs menü tetején. Megnyílik a **Azure Active Directory bővítmény** .
3. A szűrő keresése mezőbe írja be a "**Azure Active Directory**" kifejezést, majd válassza ki a **Azure Active Directory** elemet.
4. A navigációs menüben kattintson a **vállalati alkalmazások** elemre.
5. A **tevékenység** területen válassza a **rendszergazdai engedélyezési kérelmek (előzetes verzió)** lehetőséget.

   > [!NOTE]
   > A felülvizsgálók csak azokat a rendszergazdai kérelmeket láthatják, amelyeket a rendszer felülvizsgáló való kijelölése után hozott létre.

1. Válassza ki a kért alkalmazást.
2. Tekintse át a kérelem részleteit:  

   * Ha szeretné megtekinteni, hogy ki kér hozzáférést, és miért, válassza a **kért:** lapot.
   * Ha szeretné megtekinteni, hogy az alkalmazás milyen engedélyeket kér, válassza az **engedélyek ellenőrzése és a beleegyezikés** engedélyezése lehetőséget.

8. Értékelje ki a kérést, és végezze el a megfelelő műveletet:

   * **A kérelem jóváhagyása**. A kérelem jóváhagyásához adja meg a rendszergazdai jóváhagyást az alkalmazáshoz. A kérelem jóváhagyása után az összes kérelmező értesítést kap arról, hogy hozzáférést kapott.  
   * **A kérés elutasítása**. A kérelem elutasításához meg kell adnia egy indoklást, amely az összes kérelmező számára elérhető lesz. A kérések elutasítása után az összes kérelmező értesítést kap arról, hogy megtagadták az alkalmazáshoz való hozzáférést. A kérések elutasítása nem akadályozza meg, hogy a felhasználók a jövőben ismét rendszergazdai jogosultságot kérjenek az alkalmazáshoz.  
   * **A kérelem letiltása**. Egy kérelem letiltásához meg kell adnia egy indoklást, amely az összes kérelmező számára elérhető lesz. A kérések letiltása után minden kérelmező értesítést kap arról, hogy megtagadták az alkalmazáshoz való hozzáférést. A kérések blokkolása egy egyszerű szolgáltatásnév-objektumot hoz létre a bérlőben, letiltott állapotban. A felhasználók a jövőben nem igényelhetnek rendszergazdai jogosultságot az alkalmazáshoz.
 
## <a name="email-notifications"></a>E-mail-értesítések
 
Ha be van állítva, az összes felülvizsgáló e-mail-értesítést kap, ha:

* Új kérelem lett létrehozva
* Egy kérelem lejárt
* Egy kérelem a lejárati dátum közelében van  
 
A kérelmező e-mail-értesítéseket fog kapni, ha:

* Új hozzáférési kérést küldenek
* A kérelem lejárt
* A kérést a rendszer megtagadta vagy letiltotta
* A kérelem jóváhagyása megtörtént
 
## <a name="audit-logs"></a>Naplók 
 
Az alábbi táblázat a rendszergazdai engedélyezési munkafolyamathoz elérhető forgatókönyveket és naplózási értékeket ismerteti. 

> [!NOTE]
> A naplózási színész felhasználói környezete jelenleg minden esetben hiányzik. Ez egy ismert korlátozás az előzetes verzióban.


|Eset  |Naplózási szolgáltatás  |Naplózási kategória  |Naplózási tevékenység  |Könyvvizsgálói színész  |Naplózási naplók korlátai  |
|---------|---------|---------|---------|---------|---------|
|A beleegyező kérelem munkafolyamatát engedélyező rendszergazda        |Hozzáférési felülvizsgálatok           |UserManagement           |Irányítási házirend létrehozása sablon          |Alkalmazás környezete            |Jelenleg nem található a felhasználói környezet            |
|Rendszergazda letilthatja a beleegyező kérelem munkafolyamatát       |Hozzáférési felülvizsgálatok           |UserManagement           |Irányítási házirend sablonjának törlése          |Alkalmazás környezete            |Jelenleg nem található a felhasználói környezet           |
|Rendszergazda a beleegyező munkafolyamat-konfigurációk frissítése        |Hozzáférési felülvizsgálatok           |UserManagement           |Irányítási házirend sablonjának frissítése          |Alkalmazás környezete            |Jelenleg nem található a felhasználói környezet           |
|A végfelhasználó rendszergazdai beleegyező kérelmet hoz létre az alkalmazáshoz       |Hozzáférési felülvizsgálatok           |Szabályzat         |Kérelem létrehozása           |Alkalmazás környezete            |Jelenleg nem található a felhasználói környezet           |
|Rendszergazdai jóváhagyást kérő véleményezők jóváhagyása       |Hozzáférési felülvizsgálatok           |UserManagement           |Minden kérelem jóváhagyása a Business flow-ban          |Alkalmazás környezete            |Jelenleg nem található a rendszergazdai jogosultsággal rendelkező felhasználói környezet vagy alkalmazás-azonosító.           |
|Rendszergazdai beleegyező kérést elutasító felülvizsgálók       |Hozzáférési felülvizsgálatok           |UserManagement           |Minden kérelem jóváhagyása a Business flow-ban          |Alkalmazás környezete            | Jelenleg nem találja a rendszergazdai jóváhagyásra vonatkozó kérést elutasító színész felhasználói környezetét.          |

## <a name="faq"></a>GYIK 

**Bekapcsoltam ezt a munkafolyamatot, de a funkciók kipróbálásakor miért nem látom az új "jóváhagyás szükséges" kérést, amely lehetővé teszi a hozzáférést?**

A funkció bekapcsolását követően akár 60 percet is igénybe vehet, amíg a végfelhasználók megtekinthetik a frissítést. A konfiguráció megfelelő működésének ellenőrzéséhez tekintse meg a **EnableAdminConsentRequests** értéket az API-ban `https://graph.microsoft.com/beta/settings` .

**Felülvizsgáló miért nem látom az összes függőben lévő kérelmet?**

A felülvizsgálók csak azokat a rendszergazdai kérelmeket láthatják, amelyeket a rendszer felülvizsgáló való kijelölése után hozott létre. Így ha a közelmúltban hozzáadott egy véleményezőt, akkor a hozzárendelés előtt létrehozott kérelmek nem jelennek meg.

**Ha felülvizsgáló, akkor miért látok több kérelmet ugyanahhoz az alkalmazáshoz?**
  
Ha egy alkalmazás fejlesztője úgy konfigurálta az alkalmazást, hogy statikus és dinamikus beleegyezik, hogy hozzáférést Kérjen a végfelhasználói adatszolgáltatáshoz, két rendszergazdai beleegyező kérés jelenik meg. Az egyik kérelem a statikus engedélyeket jelöli, a másik pedig a dinamikus engedélyeket.

**A kérelmező megtekintheti a kérésem állapotát?**  

Nem, most a kérelmezőknek csak e-mail-értesítéseken keresztül kaphatják meg a frissítéseket.

**Felülvizsgáló lehet jóváhagyni az alkalmazást, de nem mindenki számára?**
 
Ha aggódik a rendszergazdai jóváhagyás megadásával, és a bérlő összes felhasználója számára lehetővé teszi az alkalmazás használatát, javasoljuk, hogy tiltsa le a kérést. Ezután adja meg manuálisan a rendszergazdai jóváhagyást az alkalmazáshoz való hozzáférés korlátozásával, a felhasználó hozzárendelésének megkövetelésével, valamint a felhasználók vagy csoportok az alkalmazáshoz való hozzárendelésével. További információ: [felhasználók és csoportok hozzárendelésének módszerei](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Következő lépések

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

[A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)

[Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)

[Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)

[Azure AD a Microsoft Q&A](/answers/topics/azure-active-directory.html)