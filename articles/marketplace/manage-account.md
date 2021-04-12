---
title: Kereskedelmi Piactéri fiók kezelése a Microsoft partner Centerben – Azure Marketplace
description: Ismerje meg, hogyan kezelhetők a kereskedelmi piactér-fiókok a Microsoft partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108343"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>A kereskedelmi piactér-fiók kezelése a partner Centerben

**Megfelelő szerepkörök**

- Tulajdonos
- Manager

Ha [létrehozta a partner Center-fiókot](./create-account.md), a [kereskedelmi piactér irányítópultján](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) kezelheti a fiókját és ajánlatait.

## <a name="access-your-account-settings"></a>Fiók beállításainak elérése

Ha még nem tette meg, akkor (vagy a szervezet rendszergazdája) el kell érnie a fiókpartner fiókjának [beállításait](https://partner.microsoft.com/dashboard/account/management) .

1. Jelentkezzen be a [kereskedelmi piactér irányítópultra](https://partner.microsoft.com/dashboard/commercial-marketplace) a partner Centerben az elérni kívánt fiókkal. Ha több fiókot is tartalmaz, és más fiókkal jelentkezett be, a [fiókokat válthat](switch-accounts.md).
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon) lehetőséget, majd válassza a **Fiókbeállítások** lehetőséget.

    [![Képernyőfelvétel a partner Center Fiókbeállítások menüjéről. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. A **Fiókbeállítások** területen válassza a **jogi** lehetőséget. Ezután válassza a **fejlesztő** lapot a kereskedelmi piactér-fiókjához kapcsolódó részletek megtekintéséhez.

    [![Képernyőfelvétel a fiók beállításai között a jogi lap Developer (fejlesztő) lapján. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Fiókbeállítások lap

A **Beállítások** kiválasztása és a **Fiókbeállítások** kibontásakor Az alapértelmezett nézet a **jogi információk**. Ezen a lapon legfeljebb három lap lehet a regisztrált programoktól függően: _partner_, _viszonteladó_ és _fejlesztő_.

Az MPN-ben regisztrált partnerekhez tartozó **partner** lap a következőket tartalmazza:

- Az összes jogi üzleti információ, például a vállalatnál bejegyzett jogi név és lakcím
- Elsődleges kapcsolattartó
- Üzleti helyszínek.

A **viszonteladók** lap a CSP businesst végző partnereink számára a következőket tartalmazza:

- Elsődleges kapcsolattartási adatok
- Ügyfélszolgálati profil
- Program adatai

A **fejlesztői lapon bármely** fejlesztői programban regisztrált partnereknek a következő információkat kell megadniuk:

- **Fiók adatai**: fióktípus és fiók állapota
- **Kiadói azonosítók**: eladó azonosítója, felhasználói azonosító, közzétevő azonosítója, Azure ad-bérlők és egyebek
- **Kapcsolattartási adatok**: közzétevő megjelenítendő neve, értékesítő névjegye (név, e-mail, telefonszám és cím) és a vállalat jóváhagyója (név, e-mail, telefon)

### <a name="account-settings---developer-tab"></a>Fiókbeállítások – fejlesztői lap

Az alábbi információk a fejlesztői lapon található információkat ismertetik.

#### <a name="account-details"></a>Fiók adatai

A _fejlesztői_ lap _fiókadatok_ szakaszában megtekintheti az alapvető adatokat, például a **fiók típusát** (vállalat vagy személy) és a fiók **ellenőrzési állapotát** . A fiók-ellenőrzési folyamat során ezek a beállítások megjelenítik az egyes szükséges lépéseket, beleértve az e-mailek ellenőrzését, a foglalkoztatás ellenőrzését és az üzleti ellenőrzést.

#### <a name="publisher-ids"></a>Közzétevő azonosítói

A kiadói azonosítók szakaszban megtekintheti a **Symantec-azonosítót** (ha van ilyen), az **eladó azonosítóját**, a **felhasználói azonosítót**, az **MPN-azonosítót** és az **Azure ad-bérlőket**. Ezeket az értékeket a Microsoft rendeli hozzá a fejlesztői fiók egyedi azonosításához, és nem szerkeszthető.

Ha nem rendelkezik Symantec-AZONOSÍTÓval, kiválaszthatja a kérelemre mutató hivatkozást.

### <a name="contact-info"></a>Kapcsolattartási adatok

A _kapcsolattartási adatok_ szakaszban megtekintheti a **közzétevő megjelenítendő nevét**, az **eladó kapcsolattartási adatait** (a partner nevét, az e-mail-címet, a telefonszámot és a céges értékesítő címét), valamint a **vállalat jóváhagyóját** (az egyén nevét, e-mail-címét és telefonszámát a vállalat döntéseinek jóváhagyása céljából).

A **frissítési** hivatkozásra kattintva módosíthatja a kapcsolattartási adatokat, például a közzétevő megjelenítendő nevét és az e-mail-címet.

### <a name="account-settings-identifiers"></a>Fiókbeállítások azonosítói

A **Fiókbeállítások**  >  **szervezeti profil** területen válassza az **azonosítók** lehetőséget a következő információk megtekintéséhez:

- **MPN-azonosítók**: a fiókhoz társított összes MPN-azonosító
- **CSP**: az ehhez a fiókhoz tartozó CSP-programhoz társított MPN-azonosítók.
- **Közzétevő**: a fiókjához társított értékesítői azonosítók
- **Követési GUID azonosítók**: a fiókhoz társított követési GUID azonosítók

#### <a name="tracking-guids"></a>Követési GUID-azonosítók

A globálisan egyedi azonosítók (GUID azonosítók) egyedi hivatkozási számok (32 hexadecimális számjegyekkel), amelyek segítségével nyomon követheti az Azure-használatot.

A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia. Az Azure Storage csapata létrehozta a [GUID generátort](https://aka.ms/StoragePartners) , amely a megfelelő formátumú GUID-t e-mailben küldi el, és újra felhasználható a különböző nyomkövető rendszerek között.

Javasoljuk, hogy hozzon létre egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához minden termékhez. Dönthet úgy, hogy egyetlen GUID azonosítót használ a termék több terjesztési csatornához, ha nem szeretné, hogy a jelentés szét legyen osztva.

Ha egy terméket sablon alapján telepít, és az Azure piactéren és a GitHubon is elérhető, két különálló GUID-t hozhat létre és regisztrálhat:

- Az A termék az Azure piactéren
- A termék a GitHubon

A jelentéskészítést a partner értéke (Microsoft partner azonosítója) és a GUID-azonosítók végzik. A GUID-azonosítókat részletesebben is nyomon követheti az ajánlaton belüli egyes csomagokhoz igazítva.

További információkért lásd: az [Azure-ügyfelek használatának nyomon követése a guidokkal – gyakori kérdések](azure-partner-customer-usage-attribution.md#faq).

### <a name="agreements"></a>Egyezmények

A **szerződések** lapon megtekintheti az Ön által jóváhagyott közzétételi szerződések listáját. Ezek a szerződések neve és verziószáma szerint vannak felsorolva, beleértve az elfogadott dátumot és a szerződést elfogadó felhasználó nevét.

A szerződések oldal elérése:

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
1. A jobb felső sarokban válassza a **Beállítások**  >  **Fiókbeállítások** lehetőséget.
1. A **Fiókbeállítások** területen válassza a **szerződések** lehetőséget.

A **szükséges műveletek** a lap tetején jelennek meg, ha vannak olyan szerződési frissítések, amelyek szükségesek a beavatkozáshoz. Egy frissített szerződés elfogadásához először olvassa el a csatolt szerződés verzióját, majd válassza a **Szerződés elfogadása** lehetőséget.

## <a name="set-up-a-payout-profile"></a>Kifizetési profil beállítása

A kifizetési profil a bankszámlát adja meg, amelybe a rendszer elküldi a bevételt az értékesítésből. Ennek a bankszámlának ugyanabban az országban vagy régióban kell lennie, ahol regisztrálta a partner Center-fiókját. A kifizetési profilokról további információt a következő témakörben talál: [ösztönzők létrehozása és kezelése a partner Centerben](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) , valamint [a kifizetési fiók és az adózási űrlapok beállítása](/partner-center/set-up-your-payout-account).

A kifizetési profil beállítása:

1. Lépjen a [kereskedelmi piactér – áttekintés lapra](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a partner Centerben.
2. A **profil** szakaszban a **kifizetési profil** mellett válassza a **frissítés** lehetőséget.
3. **Válasszon fizetési módot**: bankszámla vagy PayPal.
4. **Fizetési adatok hozzáadása**: Ez magában foglalhatja a fiók típusának (ellenőrzés vagy megtakarítás), a fiók tulajdonosának nevét, a fiók számát, valamint az útválasztási számot, a számlázási címet, a telefonszámot vagy a PayPal e-mail-címét. Ha többet szeretne megtudni a PayPal használatáról a fiók fizetési módjaként, és annak megállapításához, hogy az támogatott-e a piacon vagy régióban, tekintse meg a [PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)-információkat.

> [!IMPORTANT]
> A kifizetési fiók módosítása akár egy fizetési ciklusban is késleltetheti a befizetését. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ugyanúgy, mint a kifizetési fiók első beállításakor. A fiók ellenőrzése után továbbra is a teljes összegért kell fizetnie. a rendszer a jelenlegi fizetési ciklus miatt esedékes összes kifizetést hozzáadja a következőhöz.  

## <a name="tax-profile"></a>Adózási profil

Tekintse át az aktuális adózási profil állapotát, és ellenőrizze, hogy a megfelelő **entitás típusa** és az **adóhatóság információi** láthatók-e. Válassza a **Szerkesztés** lehetőséget a szükséges űrlapok frissítéséhez vagy befejezéséhez.

Az adó állapotának megállapításához meg kell adnia az országot vagy régiót, illetve az állampolgárságot, és el kell végeznie az országához vagy régióhoz tartozó megfelelő adózási űrlapokat.

Az Ön lakóhelye vagy állampolgársága szerinti országtól vagy régiótól függetlenül, a Microsoft által kínált ajánlatok kitöltése Egyesült Államok. Azoknak a partnereknek, akik bizonyos Egyesült Államok tartózkodási követelményeknek eleget tesznek, ki kell tölteniük egy IRS 9-es űrlapot. A Egyesült Államokon kívüli többi partnernek ki kell töltenie egy IRS-8 űrlapot. Ezeket az űrlapokat online is kitöltheti az adózási profil befejezése után.

Egy Egyesült Államok egyedi adófizető-azonosító szám (vagy ITIN) nem szükséges a Microsofttól érkező fizetések fogadására, vagy az adó-szerződésből származó előnyök igénylésére.

A partner Centerben elektronikus formában is elvégezheti és elküldheti az adó-formanyomtatványokat; a legtöbb esetben nem kell űrlapokat kinyomtatnia és e-maileket felvennie.

A különböző országok és régiók esetében eltérő az adózási követelmények. Az adó összegének pontos összege attól függ, hogy milyen országokban és régiókban vásárolja meg az ajánlatait. A Microsoft bizonyos országokban és régiókban az Ön nevében elvégezte az értékesítést és a használati adót. Ezek az országok és régiók az ajánlat listázásának folyamatában lesznek azonosítva. Más országokban és régiókban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az értékesítési és a használati adót közvetlenül a helyi adózási szolgáltatónak kell elvégeznie. Emellett a kapott eladások bevétele jövedelemként is adóköteles lehet. Javasoljuk, hogy lépjen kapcsolatba az országa vagy régiója megfelelő hatóságánál, amely a legjobban segít meghatározni a Microsoft értékesítési tranzakcióinak megfelelő adózási adatokat.

Az adózási profilokkal kapcsolatos további információkért tekintse meg a következő témakört: [ösztönzők létrehozása és kezelése a partner Centerben](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) , valamint [a kifizetési fiók és az adózási űrlapok beállítása](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Visszatartási díjak

Az adózási űrlapokon beküldött adatok meghatározzák a megfelelő adó-visszatartási arányt. A visszatartási arány csak az Egyesült Államokba helyezett értékesítésekre vonatkozik; a nem USA-beli helyszínekre irányuló értékesítések nem tartoznak a forrásadó alá. A visszatartási arányok eltérőek, de a legtöbb fejlesztő számára a Egyesült Államokon kívüli regisztráció esetén az alapértelmezett érték 30%. Ha az Ön országa vagy régiója egyetért Egyesült Államok a jövedelemadó-szerződéssel, akkor csökkentheti ezt a díjszabást.

### <a name="tax-treaty-benefits"></a>Adózási szerződés előnyei

Ha a Egyesült Államokon kívül esik, lehetősége lesz az adózási szerződés előnyeinek kihasználására. Ezek az előnyök az ország/régió országára/régiójára változnak, és csökkenthetik a Microsoft által visszatartott adók mennyiségét. Az adózási szerződés előnyeit a 8BEN űrlap II. részének befejezésével veheti igénybe. Javasoljuk, hogy az Ön országában vagy régiójában lévő megfelelő erőforrásokkal kommunikáljon annak megállapításához, hogy ezek az előnyök Önre vonatkoznak-e.

[További információ a Windows app/Game Developers és az Azure Marketplace-közzétevők adózási adatairól](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Kifizetés holdjának állapota

Alapértelmezés szerint a Microsoft havi rendszerességgel küldi a kifizetéseket. Igény szerint azonban megtarthatja a befizetését, ami megakadályozza a befizetések küldését a fiókjába. Ha úgy dönt, hogy megtartja a befizetését, továbbra is rögzítjük a beszerzett bevételt, és megadhatjuk a **nyeremények összegzésének** részleteit. Azonban addig nem küldünk fizetést a fiókjába, amíg el nem távolítja a tárolót.

A **teljesített fizetések elhelyezése**:

1. Lépjen a **Fiókbeállítások** menüpontra. 
1. A bal oldali navigációs panelen bontsa ki a **kifizetés és az adó** elemet, és válassza ki a **kifizetési és adózási profilokat**.
1. Válassza ki azt a programot, amelynek meg szeretné tartani a kifizetéseit, majd jelölje be a **saját fizetés megtartása** jelölőnégyzetet.

Bármikor módosíthatja a kifizetési állapotot, de vegye figyelembe, hogy a döntés a következő havi kifizetésre is hatással van. Ha például a április végét szeretné megtartani, mindenképpen **a március vége előtt állítsa** be a kifizetési Holding állapotát.

Miután beállította a kifizetési állapotot **a** be értékre, a rendszer minden kifizetést megtart, amíg vissza nem **kapcsolja** a csúszkát. Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (ha teljesülnek az érvényes fizetési küszöbértékek). Ha például megtartotta a befizetését, de a júniusi kifizetést szeretné kinyerni, akkor a határidő lejárta előtt mindenképpen **kapcsolja ki** a kifizetési Hold állapotot.

> [!NOTE]
> A kifizető **állapot** kiválasztása a Microsoft partner centeren keresztül fizetett **összes** bevételi forrásra vonatkozik, beleértve az Azure Marketplace-t, a Microsoft AppSourcet, a Microsoft Store, a reklámozást stb.). Az egyes bevételi forrásokhoz nem választhat különböző tartási állapotokat.

## <a name="devices"></a>Eszközök

Az eszközkezelés beállításai csak az univerzális Windows-platform (UWP) közzétételére vonatkoznak. [További információk](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Számlázási profil létrehozása

Ha a & Power apps vagy a [dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) ajánlatra [vonatkozó Dynamics 365](./partner-center-portal/create-new-customer-engagement-offer.md) -et tesz közzé, akkor el kell végeznie a *Számlázási profilt*.

A számlázási cím előre ki van töltve a jogi személyből, és később is frissíthető. Néhány ország esetében kötelező az adó-és az ÁFA-azonosító mező, és nem kötelező megadni mások számára. Az ország/régió neve és a vállalat neve nem szerkeszthető.

1. Lépjen a **Fiókbeállítások** menüpontra.
1. Ezután a bal oldali NAV Expand **szervezeti profil** területen válassza a **Számlázási profil** lehetőséget.


## <a name="multi-user-account-management"></a>Többfelhasználós fiókok felügyelete

A partner Center [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) szolgáltatást használ a többfelhasználós fiókok hozzáféréséhez és felügyeletéhez. A beléptetési folyamat részeként a szervezet Azure AD-fiókja automatikusan társítva lesz a partner Center-fiókjához.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók hozzáadása és kezelése](add-manage-users.md)
