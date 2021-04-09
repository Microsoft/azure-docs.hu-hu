---
title: Kereskedelmi Piactéri fiók kezelése a Microsoft partner Centerben
description: Ismerje meg, hogyan kezelhetők a kereskedelmi piactér-fiókok a Microsoft partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: cc853285e193dd3ac4e348525b8bf0db9f8cb023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533944"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>A kereskedelmi piactér-fiók kezelése a partner Centerben

Ha [létrehozta a partner Center-fiókot](./create-account.md), a [kereskedelmi piactér irányítópultján](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) kezelheti a fiókját és ajánlatait.

Ebben a cikkben bemutatjuk, hogyan kezelheti a partneri központ fiókját, többek között a következőket:

- [A partner Center-fiók beállításainak elérése](#access-your-account-settings)
- [Megkeresheti a közzétevő AZONOSÍTÓját, a Symantec-azonosítót, az eladó AZONOSÍTÓját, a felhasználói azonosítót, az MPN-azonosítót és az Azure AD-bérlőket](#account-details)
- [Kapcsolattartási adatok frissítése](#contact-info)
- [Nyomkövetési GUID-azonosítók beállítása az ügyfelek használatának figyeléséhez](#tracking-guids)
- [Felhasználók kezelése](#manage-users)
- [Csoportok kezelése](#manage-groups)
- [Azure AD-alkalmazások kezelése](#manage-azure-ad-applications)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)
- [Azure AD-bérlők kezelése (munkahelyi fiókok)](#manage-tenants)
- [A partner Center-szerződések kezelése](#agreements)

## <a name="access-your-account-settings"></a>Fiók beállításainak elérése

Ha még nem tette meg, akkor (vagy a szervezet rendszergazdája) el kell érnie a fiókpartner fiókjának [beállításait](https://partner.microsoft.com/dashboard/account/management) a következőre:

- A vállalat fiók-ellenőrzési állapotának ellenőrzése.
- Erősítse meg a Symantec-AZONOSÍTÓját, az eladó AZONOSÍTÓját, Microsoft Partner Network (MPN) AZONOSÍTÓját, a közzétevő AZONOSÍTÓját, valamint a kapcsolattartási adatokat, beleértve a vállalat jóváhagyóját és az értékesítői kapcsolatot.
- Hozzon létre felhasználói fiókokat mindenki számára, aki az Ön üzleti fiókját fogja használni a partner Centerben.

### <a name="open-account-settings"></a>Fiókbeállítások megnyitása

A [kereskedelmi piactér irányítópultján](https://partner.microsoft.com/dashboard/commercial-marketplace) a partner Centerben válassza a **Beállítások** (fogaskerék ikon) lehetőséget a jobb felső sarokban, majd a **Fiókbeállítások** menüpontot.

![Fiókbeállítások menü a partner Centerben](./media/dashboard-developer-settings.png)

A **Fiókbeállítások** megtekintheti a következőket:

- **Fiók adatai**: fióktípus és fiók állapota
- **Közzétevői azonosítók**: eladó azonosítója, felhasználói azonosító, közzétevő azonosítója, Azure ad-bérlők stb.
- **Kapcsolattartási adatok**: közzétevő megjelenítendő neve, eladó kapcsolattartási neve, e-mail címe, telefonszáma és címe
- **Követési GUID azonosítók**: a fiókhoz társított követési GUID azonosítók

### <a name="account-details"></a>Fiók adatai

A fiók részletei szakaszban megtekintheti az alapvető adatokat, például a **fiók típusát** (vállalat vagy egyéni) és a fiókja **ellenőrzési állapotát** . A fiók-ellenőrzési folyamat során ezek a beállítások megjelenítik az egyes szükséges lépéseket, beleértve az e-mailek ellenőrzését, a foglalkoztatás ellenőrzését és az üzleti ellenőrzést. Itt frissítheti az e-mail-címét, és szükség esetén újra elküldheti az ellenőrzést.

### <a name="publisher-ids"></a>Közzétevő azonosítói

A közzétevői azonosítók szakaszban megtekintheti a **Symantec-azonosítót**, az **eladó azonosítóját**, a **felhasználói azonosítót**, az **MPN-azonosítót** és az **Azure ad-bérlőket**. Ezeket az értékeket a Microsoft rendeli hozzá a fejlesztői fiók egyedi azonosításához, és nem szerkeszthető.

### <a name="contact-info"></a>Kapcsolattartási adatok

A kapcsolattartási adatok szakaszban megtekintheti a **közzétevő megjelenítendő nevét**, az **eladó kapcsolattartási adatait** (a partner nevét, az e-mail-címet, a telefonszámot és a céges értékesítő címét), valamint a **vállalat jóváhagyóját** (az egyén nevét, e-mail-címét és telefonszámát a vállalat döntéseinek jóváhagyása céljából).

#### <a name="payout-account"></a>Kifizetési fiók

A kifizetési fiók az a bankszámla, amelynek a bevételeit az értékesítések küldik. Ennek a bankszámlának ugyanabban az országban/régióban kell lennie, ahol regisztrálta a partner Center-fiókját.

A kifizetési fiók beállítása:

1. Lépjen a [kereskedelmi piactér – áttekintés lapra](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a partner Centerben.
2. A profil szakaszban a **kifizetési profil** mellett válassza a **frissítés** lehetőséget.
3. **Válasszon fizetési módot**: bankszámla vagy PayPal.
4. **Fizetési adatok hozzáadása**: Ez magában foglalhatja a fiók típusának (ellenőrzés vagy megtakarítás), a fiók tulajdonosának nevét, a fiók számát, valamint az útválasztási számot, a számlázási címet, a telefonszámot vagy a PayPal e-mail-címét. * Ha többet szeretne megtudni a PayPal használatáról a fiók fizetési módjaként, és annak megállapításához, hogy az támogatott-e a piaci régióban, tekintse meg a [PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)-információkat.

> [!IMPORTANT]
> A kifizetési fiók módosítása akár egy fizetési ciklusban is késleltetheti a befizetését. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ugyanúgy, mint a kifizetési fiók első beállításakor. A fiók ellenőrzése után továbbra is a teljes összegért kell fizetnie. a rendszer a jelenlegi fizetési ciklus miatt esedékes összes kifizetést hozzáadja a következőhöz.  

#### <a name="tax-profile"></a>Adózási profil

Tekintse át az aktuális adózási profil állapotát, és ellenőrizze, hogy a megfelelő **entitás típusa** és az **adóhatóság információi** láthatók-e. Válassza a **Szerkesztés** lehetőséget a szükséges űrlapok frissítéséhez vagy befejezéséhez.

Az adó állapotának megállapításához meg kell adnia a lakóhely és az állampolgárság országát/régióját, és el kell végeznie az országához/régiójához tartozó megfelelő adózási formákat.

A tartózkodási hely vagy az állampolgárság országa vagy régiója, illetve a Microsoft által kínált ajánlatok eladása előtt ki kell töltenie Egyesült Államok. Azoknak a partnereknek, akik bizonyos Egyesült Államok tartózkodási követelményeknek eleget tesznek, ki kell tölteniük egy IRS 9-es űrlapot. A Egyesült Államokon kívüli többi partnernek ki kell töltenie egy IRS-8 űrlapot. Ezeket az űrlapokat online is kitöltheti az adózási profil befejezése után.

Egy Egyesült Államok egyedi adófizető-azonosító szám (vagy ITIN) nem szükséges a Microsofttól érkező fizetések fogadására, vagy az adó-szerződésből származó előnyök igénylésére.

A partner Centerben elektronikus formában is elvégezheti és elküldheti az adó-formanyomtatványokat; a legtöbb esetben nem kell űrlapokat kinyomtatnia és e-maileket felvennie.

A különböző országok és régiók esetében eltérő az adózási követelmények. Az adó összegének pontos összege attól függ, hogy milyen országokban és régiókban vásárolja meg az ajánlatait. A Microsoft bizonyos országokban/régiókban az Ön nevében elvégezte az értékesítést és a használati adót. Ezek az országok/régiók az ajánlat listázásának folyamatában lesznek azonosítva. Más országokban/régiókban, attól függően, hogy hol van regisztrálva, előfordulhat, hogy az értékesítési és használati adót közvetlenül a helyi adózási szolgáltatónak kell elvégeznie. Emellett a kapott eladások bevétele jövedelemként is adóköteles lehet. Javasoljuk, hogy lépjen kapcsolatba az országa vagy régiója megfelelő hatóságánál, amely a legjobban segít meghatározni a Microsoft értékesítési tranzakcióinak megfelelő adózási adatokat.

##### <a name="withholding-rates"></a>Visszatartási díjak

Az adózási űrlapokon beküldött adatok meghatározzák a megfelelő adó-visszatartási arányt. A visszatartási arány csak az Egyesült Államokba helyezett értékesítésekre vonatkozik; a nem USA-beli helyszínekre irányuló értékesítések nem tartoznak a forrásadó alá. A visszatartási arányok eltérőek, de a legtöbb fejlesztő számára a Egyesült Államokon kívüli regisztráció esetén az alapértelmezett érték 30%. Ha az országa/régiója egyetért a jövedelemadó-szerződéssel, akkor csökkentheti ezt a mértéket, ha a Egyesült Államok.

##### <a name="tax-treaty-benefits"></a>Adózási szerződés előnyei

Ha a Egyesült Államokon kívül esik, lehetősége lesz az adózási szerződés előnyeinek kihasználására. Ezek az előnyök országonként vagy régiónként eltérőek, és lehetővé tehetik a Microsoft által visszatartott adók mennyiségének csökkentését. Az adózási szerződés előnyeit a 8BEN űrlap II. részének befejezésével veheti igénybe. Javasoljuk, hogy az Ön országában vagy régiójában lévő megfelelő erőforrásokkal kommunikáljon annak megállapításához, hogy ezek az előnyök Önre vonatkoznak-e.

[További információ a Windows app/Game Developers és az Azure Marketplace-közzétevők adózási adatairól](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Kifizetés holdjának állapota

Alapértelmezés szerint a Microsoft havi rendszerességgel küldi a kifizetéseket. Igény szerint azonban megtarthatja a befizetését, ami megakadályozza a befizetések küldését a fiókjába. Ha úgy dönt, hogy megtartja a befizetését, továbbra is rögzítjük a beszerzett bevételt, és megadhatjuk a **nyeremények összegzésének** részleteit. Azonban addig nem küldünk fizetést a fiókjába, amíg el nem távolítja a tárolót.

A befizetések tárolásához lépjen a **Fiókbeállítások** menüpontra. A **pénzügyi részletek** alatt, a **kifizetési Hold állapota** szakaszban kapcsolja be a csúszkát a **be** értékre. Bármikor módosíthatja a kifizetési állapotot, de vegye figyelembe, hogy a döntés a következő havi kifizetésre is hatással van. Ha például a április végét szeretné megtartani, mindenképpen **a március vége előtt állítsa** be a kifizetési Holding állapotát.

Miután beállította a kifizetési állapotot **a be** értékre, a rendszer minden kifizetést megtart, amíg vissza **nem kapcsolja** a csúszkát. Ha így tesz, a következő havi kifizetési ciklusban is szerepelni fog (ha teljesülnek az érvényes fizetési küszöbértékek). Ha például megtartotta a befizetését, de a júniusi kifizetést szeretné kinyerni, akkor a határidő lejárta előtt mindenképpen **kapcsolja ki** a kifizetési Hold állapotot.

> [!NOTE]
> A **kifizetési Holding status** Selection a Microsoft partner centeren keresztül fizetett **összes** bevételi forrásra vonatkozik, beleértve az Azure Marketplace-t, a AppSource-t, a Microsoft Storet, a reklámozást stb.). Az egyes bevételi forrásokhoz nem választhat különböző tartási állapotokat.

### <a name="devices"></a>Eszközök

Az eszközkezelés beállításai csak a UWP-közzétételre érvényesek. [További információk](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Követési GUID-azonosítók

A globálisan egyedi azonosítók (GUID azonosítók) egyedi hivatkozási számok (32 hexadecimális számjegyekkel), amelyek segítségével nyomon követheti az Azure-használatot. 

A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia. Az Azure Storage csapata létrehozta a [GUID generátort](https://aka.ms/StoragePartners) , amely a megfelelő formátumú GUID-t e-mailben küldi el, és újra felhasználható a különböző nyomkövető rendszerek között.

Javasoljuk, hogy hozzon létre egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához az egyes termékekhez. Dönthet úgy, hogy egyetlen GUID azonosítót használ a termék több terjesztési csatornához, ha nem szeretné, hogy a jelentés szét legyen osztva.

Ha egy terméket sablon alapján telepít, és az Azure piactéren és a GitHubon is elérhető, két különálló GUID-t hozhat létre és regisztrálhat:

- Az A termék az Azure piactéren
- A termék a GitHubon

A jelentéskészítést a partner értéke (Microsoft partner azonosítója) és a GUID-azonosítók végzik. A GUID-azonosítókat részletesebben is nyomon követheti az ajánlaton belüli egyes csomagokhoz igazítva.

További információkért lásd: az [Azure-ügyfelek használatának nyomon követése a guidokkal – gyakori kérdések](../azure-partner-customer-usage-attribution.md#faq).

## <a name="create-a-billing-profile"></a>Számlázási profil létrehozása

Ha a & Power apps vagy a [dynamics 365 for Operations](./create-new-operations-offer.md) ajánlatra [vonatkozó Dynamics 365](./create-new-customer-engagement-offer.md) -et tesz közzé, akkor el kell végeznie a **Számlázási profilt**.

A számlázási cím előre ki van töltve a jogi személyből, és később is frissíthető. Az ÁFA és az ÁFA-azonosító mezők megadása nem kötelező.  Az ország/régió neve és a vállalat neve nem szerkeszthető.

## <a name="multi-user-account-management"></a>Többfelhasználós fiókok felügyelete

A partner Center [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) szolgáltatást használ a többfelhasználós fiókok hozzáféréséhez és felügyeletéhez. A beléptetési folyamat részeként a szervezet Azure AD-fiókja automatikusan társítva lesz a partner Center-fiókjához.

## <a name="manage-users"></a>Felhasználók kezelése

A partner Center **felhasználói** szakasza (a **Fiókbeállítások** részben) lehetővé teszi, hogy az Azure ad segítségével kezelje a partner Center-fiókhoz hozzáférő felhasználókat, csoportokat és Azure ad-alkalmazásokat. A fióknak [**felettes szintű**](#define-user-roles-and-permissions) engedélyekkel kell rendelkeznie ahhoz a [munkahelyi fiókhoz (Azure ad-bérlőhöz)](./company-work-accounts.md) , amelyben felhasználókat kíván hozzáadni vagy szerkeszteni. Ha egy másik munkahelyi fiókon vagy bérlőn belül szeretné felügyelni a felhasználókat, ki kell jelentkeznie, majd újra be kell jelentkeznie egy **kezelői** engedélyekkel rendelkező felhasználóként az adott munkahelyi fiókra/bérlőre.

Miután bejelentkezett a munkahelyi fiókjával (Azure AD-Bérlővel), a következőket teheti:

- [Felhasználók hozzáadása vagy eltávolítása](#add-existing-users)
- [Felhasználói jelszó módosítása](#change-a-user-password)
- [Csoportok hozzáadása vagy eltávolítása](#manage-groups)
- [Azure AD-alkalmazások hozzáadása vagy eltávolítása](#add-new-azure-ad-applications)
- [Azure AD-alkalmazás kulcsainak kezelése](#manage-keys-for-an-azure-ad-application)
- [Felhasználói szerepkörök és engedélyek definiálása](#define-user-roles-and-permissions)

Ne feledje, hogy az összes partner Center-felhasználónak (beleértve a csoportokat és az Azure AD-alkalmazásokat) aktív munkahelyi fiókkal kell rendelkeznie a partner Center-fiókhoz társított [Azure ad-bérlőben](#manage-tenants) .

### <a name="add-existing-users"></a>Meglévő felhasználók hozzáadása

Felhasználók hozzáadása a partner Center-fiókhoz, amely már szerepel a vállalat [munkahelyi fiókjában (Azure ad-bérlő)](./company-work-accounts.md):

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), és válassza a **felhasználók hozzáadása** lehetőséget.
2. Válasszon ki egy vagy több felhasználót a megjelenő listából. A keresőmező segítségével megkeresheti az adott felhasználókat.
* Ha egynél több felhasználót választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több felhasználót szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3. Ha végzett a felhasználók kiválasztásával, válassza a **kijelölt hozzáadása** elemet.
4. A **szerepkörök** szakaszban határozza meg a kiválasztott felhasználó (k) szerepkörét vagy testreszabott engedélyeit.
5. Kattintson a **Mentés** gombra.

### <a name="create-new-users"></a>Új felhasználók létrehozása

Új felhasználói fiókok létrehozásához [**globális rendszergazdai**](../../active-directory/roles/permissions-reference.md) jogosultságokkal rendelkező fiókkal kell rendelkeznie.

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), válassza a **felhasználók hozzáadása**, majd az **új felhasználók létrehozása** lehetőséget.
1. Adja meg az összes új felhasználó utónevét, vezetéknevét és felhasználónevét. 
1. Ha azt szeretné, hogy az új felhasználó globális rendszergazdai fiókkal rendelkezzen a szervezet címtárában, jelölje be a **felhasználó globális rendszergazdaként való megjelölése az Azure ad-ben jelölőnégyzetet az összes címtár-erőforrás teljes körű vezérlése mellett**. Ez teljes hozzáférést biztosít a felhasználónak a vállalat Azure AD összes felügyeleti funkciójához. A szervezet munkahelyi fiókjában (Azure AD-bérlő) lévő felhasználók hozzáadhatók és kezelhetők, bár nem a partner Centerben, hacsak nem a megfelelő szerepkört/engedélyeket adja meg a fióknak.
1. Ha bejelölte a jelölőnégyzetet, hogy a **felhasználó globális rendszergazda legyen**, **Jelszó-helyreállítási e-mailt** kell megadnia a felhasználónak, ha szükséges.
1. A **csoporttagság** szakaszban válassza ki azokat a csoportokat, amelyekhez az új felhasználót be szeretné állítani.
1. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
1. Kattintson a **Mentés** gombra.

Ha új felhasználót hoz létre a partner Centerben, az adott felhasználóhoz tartozó fiókot is létrehoz a munkahelyi fiókban (Azure AD-bérlőben), amelyhez be van jelentkezve. Ha módosítja egy felhasználó nevét a partner Centerben, ugyanazokat a módosításokat fogja végezni a szervezet munkahelyi fiókjában (Azure AD-bérlő).

### <a name="invite-new-users-by-email"></a>Új felhasználók meghívása e-mailben

Ha olyan felhasználókat szeretne meghívni, akik jelenleg nem részei a munkahelyi munkahelyi fióknak (Azure AD-bérlőnek) e-mailben, [**globális rendszergazdai**](../../active-directory/roles/permissions-reference.md) jogosultságokkal rendelkező fiókkal kell rendelkeznie.

1. Nyissa meg a **felhasználók** (a **Fiókbeállítások** szakaszban), válassza a **felhasználók hozzáadása** lehetőséget, majd válassza **a felhasználók meghívása e-mailben** lehetőséget.
2. Írjon be egy vagy több e-mail-címet (legfeljebb 10), vesszővel vagy pontosvesszővel elválasztva.
3. A **szerepkörök** szakaszban határozza meg a felhasználó szerepkör (eke) t vagy a testreszabott engedélyeket.
4. Kattintson a **Mentés** gombra.

A meghívott felhasználók e-mailben meghívót kapnak a partner Center-fiókhoz való csatlakozáshoz. A rendszer létrehoz egy új vendég-felhasználói fiókot a munkahelyi fiókjában (Azure AD-bérlő). Minden felhasználónak el kell fogadnia a meghívót, mielőtt hozzáférhessen a fiókjához.

Ha újra el kell küldenie egy meghívót, látogasson el a **felhasználók** oldalra, keresse meg a meghívót a felhasználók listájában, válassza ki az e-mail-címét (vagy a *meghívót függőben lévő* szöveget). Ezután a lap alján válassza a **meghívás újraküldése** lehetőséget.

> [!NOTE]
> Ha a szervezete [címtár-integrációt](/previous-versions/azure/azure-services/jj573653(v=azure.100)) használ a helyszíni CÍMTÁRSZOLGÁLTATÁS Azure ad-val való szinkronizálásához, nem fog tudni új felhasználókat, csoportokat vagy Azure ad-alkalmazásokat létrehozni a partner Centerben. Önnek (vagy egy másik rendszergazdának a helyszíni címtárban) közvetlenül a helyszíni címtárban kell létrehoznia őket, mielőtt látni és hozzá tudná adni őket a partner Centerben.

### <a name="remove-a-user"></a>Felhasználó eltávolítása

Ha el szeretne távolítani egy felhasználót a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** (a **Fiókbeállítások** alatt) lehetőségre, válassza ki azt a felhasználót, akit el szeretne távolítani a jobb szélső oszlopban lévő jelölőnégyzetből, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt felhasználó (ka) t.

### <a name="change-a-user-password"></a>Felhasználói jelszó módosítása

Ha az egyik felhasználónak módosítania kell a jelszavát, akkor megteheti, ha **Jelszó-helyreállítási e-mailt** adott a felhasználói fiók létrehozásakor. A felhasználó jelszavát az alábbi lépések végrehajtásával is frissítheti. Ha módosítani szeretné egy felhasználó jelszavát a munkahelyi munkahelyi fiókjában (Azure AD-bérlő), [**globális rendszergazdai**](../../active-directory/roles/permissions-reference.md) jogosultságokkal rendelkező fiókba kell bejelentkeznie. Ez megváltoztatja a felhasználó jelszavát az Azure AD-bérlőben, valamint a partner Center eléréséhez használt jelszót.

1. A **felhasználók** lapon ( **Fiókbeállítások** alatt) válassza ki a szerkeszteni kívánt felhasználói fiók nevét.
2. A lap alján kattintson a **jelszó alaphelyzetbe állítása** gombra.
3. Ekkor megjelenik egy megerősítést kérő oldal, amely megjeleníti a felhasználó bejelentkezési adatait, beleértve az ideiglenes jelszót is. Ügyeljen arra, hogy kinyomtassa vagy átmásolja ezt az információt, és megadja azt a felhasználónak, mivel az oldal elhagyása után nem fog tudni hozzáférni az ideiglenes jelszóhoz.

## <a name="manage-groups"></a>Csoportok kezelése

A csoportok lehetővé teszik, hogy egyszerre több felhasználói szerepkört és engedélyt is vezérel.

### <a name="add-an-existing-group"></a>Meglévő csoport hozzáadása

A szervezet munkahelyi fiókjában (Azure AD-bérlőben) már meglévő csoport hozzáadása a partner Center-fiókhoz:

1. A **felhasználók** lap **fiókok beállításai** területén válassza a **csoportok hozzáadása** lehetőséget.
2. Válasszon ki egy vagy több csoportot a megjelenő listából. A keresőmező segítségével megkeresheti az adott csoportokat.
Ha egynél több csoportot választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több csoportot szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3. Ha végzett a csoportok kiválasztásával, válassza a **kijelölt hozzáadása** elemet.
4. A **szerepkörök** szakaszban határozza meg a kijelölt csoport (ok) szerepkör (oka) t vagy a testreszabott engedélyeket. A csoport minden tagja hozzáférhet a partner Center-fiókjához a csoportra vonatkozó engedélyekkel, az egyes fiókokhoz tartozó szerepköröktől és engedélyektől függetlenül.
5. Kattintson a **Mentés** gombra.

Ha hozzáad egy meglévő csoportot, akkor minden felhasználó, aki tagja ennek a csoportnak, hozzáférhet a partner Center-fiókjához a csoport hozzárendelt szerepköréhez tartozó engedélyekkel.

### <a name="add-a-new-group"></a>Új csoport hozzáadása

Új csoport hozzáadása a partner Center-fiókhoz:

1. A **felhasználók** lap **fiókok beállításai** területén válassza a **csoportok hozzáadása** lehetőséget.
2. A következő lapon válassza az **új csoport** lehetőséget.
3. Adja meg az új csoport megjelenítendő nevét.
4. Határozza meg a csoport szerepkör (eke) t vagy a testreszabott engedélyeket. A csoport minden tagja hozzáférhet a partner Center-fiókjához az itt megadott engedélyekkel függetlenül az egyes fiókokhoz tartozó szerepköröktől/engedélyektől.
5. Válassza ki a felhasználó (ka) t az új csoport számára a megjelenő listából. A keresőmező segítségével megkeresheti az adott felhasználókat.
6. Ha végzett a felhasználók kiválasztásával, válassza a **kijelölt hozzáadása** elemet az új csoportba való felvételhez.
7. Kattintson a **Mentés** gombra.

Ez az új csoport a szervezet munkahelyi fiókjában (Azure AD-bérlő) is létrejön, nem csupán a partner Center-fiókban.

### <a name="remove-a-group"></a>Csoport eltávolítása

Ha el szeretne távolítani egy csoportot a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** elemre (a **Fiókbeállítások** szakaszban), válassza ki azt a csoportot, amelyet el szeretne távolítani a jobb szélső oszlopban lévő jelölőnégyzetből, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt csoport (oka) t.

## <a name="manage-azure-ad-applications"></a>Azure AD-alkalmazások kezelése

A partner Center-fiók eléréséhez engedélyezheti a vállalat Azure AD-beli részét képező alkalmazásokat és szolgáltatásokat.

### <a name="add-existing-azure-ad-applications"></a>Meglévő Azure AD-alkalmazások hozzáadása

A vállalat Azure Active Directory már meglévő alkalmazások hozzáadásához:

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza az **Azure ad-alkalmazások hozzáadása** lehetőséget.
2. Válasszon ki egy vagy több Azure AD-alkalmazást a megjelenő listából. A keresőmező segítségével megkeresheti az adott Azure AD-alkalmazásokat. Ha egynél több Azure AD-alkalmazást választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több Azure AD-alkalmazást szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
3. Ha végzett az Azure AD-alkalmazások kiválasztásával, válassza a **kijelölt hozzáadása** elemet.
4. A **szerepkörök** szakaszban határozza meg a kijelölt Azure ad-alkalmazás (ok) szerepkörét vagy testreszabott engedélyeit.
5. Kattintson a **Mentés** gombra.

### <a name="add-new-azure-ad-applications"></a>Új Azure AD-alkalmazások hozzáadása

Ha egy új Azure AD-alkalmazáshoz szeretne hozzáférést adni a partneri Központ számára, létrehozhat egyet a **felhasználók** szakaszban. Ezzel létrehoz egy új fiókot a munkahelyi munkafiókjában (Azure AD-bérlő), nem csupán a partner Center-fiókban. Ha elsődlegesen ezt az Azure AD-alkalmazást használja a fiókpartner-hitelesítéshez, és nincs szükség arra, hogy a felhasználók közvetlenül hozzáférjenek, megadhat bármely érvényes címet a **Válasz URL** -címéhez és az **alkalmazás-azonosító URI azonosítóhoz**, feltéve, hogy ezeket az értékeket nem használja más Azure ad-alkalmazás a címtárában.

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza az **Azure ad-alkalmazások hozzáadása** lehetőséget.
2. A következő lapon válassza az **új Azure ad-alkalmazás** lehetőséget.
3. Adja meg az új Azure AD **-alkalmazás válaszának URL-címét** . Ez az URL-cím, ahol a felhasználók bejelentkezhetnek és használhatják az Azure AD-alkalmazást (más néven az alkalmazás URL-címe vagy Sign-On URL-cím). A **Válasz URL-címe** nem lehet hosszabb 256 karakternél, és a címtárban egyedinek kell lennie.
4. Adja meg az új Azure AD-alkalmazáshoz tartozó **alkalmazás-azonosító URI** -t. Ez az Azure AD-alkalmazáshoz tartozó logikai azonosító, amely akkor jelenik meg, ha egy egyszeri bejelentkezésre vonatkozó kérést küld az Azure AD-nek. Az **alkalmazás-azonosító URI** azonosítójának egyedinek kell lennie minden egyes Azure ad-alkalmazáshoz a címtárban. Ez az azonosító nem lehet hosszabb 256 karakternél. Az alkalmazás-azonosító URI-val kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorytel](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. A **szerepkörök** szakaszban megadhatja az Azure ad-alkalmazáshoz tartozó szerepkör (eke) t vagy testreszabott engedélyeket.
6. Kattintson a **Mentés** gombra.

Az Azure AD-alkalmazás hozzáadása vagy létrehozása után visszatérhet a **felhasználók** szakaszhoz, és kiválaszthatja az alkalmazás nevét az alkalmazás beállításainak áttekintéséhez, beleértve a bérlő azonosítóját, az ügyfél-azonosítót, a válasz URL-címét és az alkalmazás-azonosító URI-t.

### <a name="remove-an-azure-ad-application"></a>Azure AD-alkalmazás eltávolítása

Ha el szeretne távolítani egy alkalmazást a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** (a **Fiókbeállítások** alatt) lehetőségre, válassza ki azt az alkalmazást, amelyet el szeretne távolítani a jobb szélső oszlopban található jelölőnégyzettel, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt alkalmazás (oka) t.

### <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD-alkalmazás kulcsainak kezelése

Ha az Azure AD-alkalmazás adatokat olvas és ír a Microsoft Azure ADban, szüksége lesz egy kulcsra. Az Azure AD-alkalmazásokhoz kulcsokat hozhat létre a partner Centerben található információk szerkesztésével. A már nem szükséges kulcsok eltávolítására is lehetőség van.

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza ki az Azure ad-alkalmazás nevét. Ekkor megjelenik az Azure AD-alkalmazás összes aktív kulcsa, beleértve a kulcs létrehozásának dátumát és a 50-os lejárati időpontot is.
2. A már nem szükséges kulcsok eltávolításához válassza az **Eltávolítás** lehetőséget.
3. Új kulcs hozzáadásához válassza az **új kulcs hozzáadása** lehetőséget.
4. Ekkor megjelenik egy képernyő, amely az **ügyfél-azonosítót** és a **kulcs értékeit** mutatja. Ügyeljen arra, hogy nyomtassa ki vagy másolja ezeket az adatokat, mert a lap elhagyása után többé nem fog tudni hozzáférni.
5. Ha további kulcsokat szeretne létrehozni, válassza a **másik kulcs hozzáadása** elemet.

## <a name="define-user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek definiálása

A vállalat felhasználóinak a következő szerepköröket és engedélyeket rendelhetik a kereskedelmi piactér programhoz a partner Centerben:

- **Tulajdonos**
  - Ez az a személy, aki először hozta létre a fiókot, és teljes hozzáféréssel rendelkezik hozzá, beleértve az összes fiók felhasználójának létrehozását és szerkesztését, valamint az összes pénzügyi és Fiókbeállítások módosítását. Egy fióknak általában csak egy tulajdonosa van.
- **Manager**
  - Elérheti az összes Microsoft-fiók funkciót az adó-és kifizetési beállítások kivételével
  - Felügyelheti a felhasználókat, a szerepköröket és a munkahelyi fiókokat (bérlőket)
- **Fejlesztő**
  - Kezelheti és közzéteheti az ajánlatokat
  - Megtekintheti a kiadói jelentéseket

> [!NOTE]
> A kereskedelmi piactér programban a globális rendszergazda, az üzleti közreműködő, a pénzügyi közreműködő és a marketinges szerepkörök nem használatosak. A szerepkörök felhasználóknak való kiosztása nincs hatással. Csak a kezelő és a fejlesztői szerepkörök adnak engedélyeket a felhasználóknak.

További információ a szerepkörök és engedélyek kezeléséről a partner Center más területein, például a Azure Active Directory (AD), a felhőalapú megoldás-szolgáltató (CSP), a Vezérlőpult-gyártó (CPV), a vendég felhasználók vagy a Microsoft Partner Network (MPN): [felhasználói szerepkörök és engedélyek társítása a partner Centerben](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Bérlők kezelése

Egy Azure Active Directory (AD) bérlő, amelyet a dokumentációban is a "munkahelyi fióknak" nevezünk, a szervezet a Azure Portalban beállított képviselete, és segít a Microsoft Cloud Services adott példányának kezelésében a belső és külső felhasználók számára. Ha a szervezet előfizetett egy Microsoft Cloud Service-re (például Azure, Microsoft Intune vagy Microsoft 365), akkor egy Azure AD-bérlő lett létrehozva.

Több bérlőt is beállíthat a partner centerrel való használatra. A partner Center-fiókban a **Manager** szerepkörrel rendelkező felhasználók hozzáadhatnak és eltávolíthatnak Azure ad-bérlőket a fiókból.  

### <a name="add-an-existing-tenant"></a>Meglévő bérlő hozzáadása

Másik Azure AD-bérlő hozzárendelése a partner Center-fiókhoz:

1. A **bérlők** lapon (a **Fiókbeállítások** alatt) válassza a **másik Azure ad-bérlő hozzárendelése** lehetőséget.
2. Adja meg a hozzárendelni kívánt bérlő Azure AD-beli hitelesítő adatait.
3. Tekintse át az Azure AD-bérlő szervezetének és tartománynevének nevét. A társítás befejezéséhez kattintson a **Confirm (megerősítés**) gombra.

Ha a társítás sikeres, akkor készen áll a fiók felhasználóinak hozzáadására és felügyeletére a partner Center **felhasználók** szakaszában.

### <a name="create-a-new-tenant"></a>Új bérlő létrehozása

Új Azure AD-bérlő létrehozása a partner Center-fiókkal:

1. A **bérlők** lapon (a **Fiókbeállítások** alatt) válassza az **új Azure ad-bérlő létrehozása** lehetőséget.
2. Adja meg az új Azure AD-címtár adatait:
    - **Tartománynév**: az Azure ad-tartományhoz használni kívánt egyedi név, a ". onmicrosoft.com" névvel együtt. Ha például a "példa" kifejezést adta meg, az Azure AD-tartománya "example.onmicrosoft.com" lesz.
    - **Kapcsolattartó e-mail** címe: e-mail-cím, ahol szükség esetén felveheti a kapcsolatot a fiókjával.
    - **Globális rendszergazdai felhasználói fiók adatai**: az új globális rendszergazdai fiókhoz használni kívánt keresztnév, vezetéknév, Felhasználónév és jelszó.
3. A **Létrehozás** gombra kattintva erősítse meg az új tartomány és fiókadatok adatait.
4. A [felhasználók hozzáadásának és kezelésének](#manage-users)megkezdéséhez jelentkezzen be az új Azure ad globális rendszergazdai felhasználónevével és jelszavával.

Ha további információt szeretne arról, hogyan hozhat létre új bérlőket a Azure Portal belül, és nem a partneri központ portálon keresztül, tekintse meg a következő cikket: [új bérlő létrehozása a Azure Active Directoryban](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Bérlő eltávolítása

Ha el szeretne távolítani egy bérlőt a partner Center-fiókból, keresse meg a **bérlők** lapon található nevet a **fiók beállításai** között, majd válassza az **Eltávolítás** lehetőséget. A rendszer felszólítja, hogy erősítse meg, hogy el kívánja távolítani a bérlőt. Ha így tesz, az adott bérlő egyik felhasználója sem tud bejelentkezni a partner Center-fiókba, és a felhasználók számára konfigurált engedélyek el lesznek távolítva.

Ha eltávolít egy bérlőt, az adott bérlőtől a partner Center-fiókba felvett összes felhasználó nem fog tudni bejelentkezni a fiókba.

> [!TIP]
> Nem távolíthat el bérlőt, ha a partner központba van bejelentkezve ugyanazzal a Bérlővel. Bérlő eltávolításához be kell jelentkeznie a partner központba **egy másik** , a fiókhoz társított bérlőhöz. Ha a fiókhoz csak egy bérlő van társítva, akkor a bérlő csak a fiókot megnyitó Microsoft-fiók való bejelentkezés után távolítható el.

## <a name="agreements"></a>Egyezmények

A partner központ **szerződések** szakasza (a **Fiókbeállítások** szakaszban) lehetővé teszi az Ön által jóváhagyott közzétételi szerződések listájának megtekintését. Ezek a szerződések neve és verziószáma szerint vannak felsorolva, beleértve az elfogadott dátumot és a szerződést elfogadó felhasználó nevét.

A **szükséges műveletek** a lap tetején jelennek meg, ha vannak olyan szerződési frissítések, amelyek szükségesek a beavatkozáshoz. Egy frissített szerződés elfogadásához először olvassa el a csatolt szerződés verzióját, majd válassza a **Szerződés elfogadása** lehetőséget.
