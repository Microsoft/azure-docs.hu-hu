---
title: Kereskedelmi piactéri fiók kezelése a Microsoft Partnerközpont – Azure Marketplace
description: Útmutató kereskedelmi piactéri fiók kezeléséhez a Microsoft Partnerközpont.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 6b721e7acb7907743c0696aff6c11ad59f5ceba9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812569"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>A kereskedelmi piactéri fiók kezelése a Partnerközpont

**Megfelelő szerepkörök**

- Tulajdonos
- Manager

Miután létrehozott egy [Partnerközpont-fiókot,](./create-account.md)a kereskedelmi piactér irányítópultján kezelheti a fiókját és ajánlatát. [](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

## <a name="access-your-account-settings"></a>Hozzáférés a fiókbeállításokhoz

Ha még nem tette meg, Önnek (vagy a szervezet [](https://partner.microsoft.com/dashboard/account/management) rendszergazdájának) hozzá kell férni a fiókhoz Partnerközpont fiókhoz.

1. Jelentkezzen be a kereskedelmi [piactér irányítópultjára](https://partner.microsoft.com/dashboard/commercial-marketplace) a Partnerközpont az elérni kívánt fiókkal. Ha ön több fiók tagja, és más fiókkal jelentkezett be, fiókokat [válthat.](switch-accounts.md)
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon), majd a **Fiókbeállítások lehetőséget.**

    [![Képernyőkép a fiókbeállítások menüjéről a Partnerközpont. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. A **Fiókbeállítások alatt válassza** a Jogi **lehetőséget.** Ezután válassza a **Fejlesztő lapot** a kereskedelmi piactéri fiókjával kapcsolatos részletek megtekintéséhez.

    [![Képernyőkép a fiókbeállítások jogi oldalán található fejlesztői lapról. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Fiókbeállítások oldal

Ha a Beállítások lehetőséget **választja,** és **kibontja a Fiókbeállítások** gombra, az alapértelmezett nézet **a Jogi adatok.** Ezen az oldalon legfeljebb három lap lehet attól függően, hogy milyen programokban regisztrált: _Partner,_ _Viszonteladó_ és _Fejlesztő._

Az  MPN-ban regisztrált partnerek Partner lapja a következőket tartalmazza:

- Az összes jogi üzleti információ, például a vállalat regisztrált jogi neve és címe
- Elsődleges kapcsolattartó
- Üzleti helyek.

A CSP-üzleti partnerek **Reseller** (Viszonteladó) lapja a következőket tartalmazza:

- Elsődleges kapcsolattartási adatok
- Ügyfélszolgálati profil
- Programinformációk

A **Fejlesztői** lap a fejlesztői programokban regisztrált partnerek számára a következő információkat tartalmazza:

- **Fiók adatai:** Fiók típusa és Fiók állapota
- **Közzétevői azonosítók:** Értékesítői azonosító, felhasználói azonosító, közzétevő azonosítója, Azure AD-bérlők és sok más
- **Kapcsolattartási adatok:** Közzétevő megjelenített neve, értékesítői kapcsolattartó (név, e-mail-cím, telefon és cím) és Céges jóváhagyó (név, e-mail-cím, telefon)

### <a name="account-settings---developer-tab"></a>Fiókbeállítások – Fejlesztői lap

Az alábbi információk a Fejlesztők lapon található információkat ismertetik.

#### <a name="account-details"></a>Fiókadatok

A _Fejlesztő lap_ Fiókadatok szakaszában olyan alapvető információkat láthat, mint a Fiók típusa  **(Vállalat** vagy egyéni) és a fiók Ellenőrzési állapota.  A fiók ellenőrzési folyamata során ezek a beállítások minden szükséges lépést megjelenítnek, beleértve az e-mailes ellenőrzést, a foglalkoztatás-ellenőrzést és az üzleti ellenőrzést.

#### <a name="publisher-ids"></a>Közzétevői 1D-ket

A Közzétevői azonosítók szakaszban láthatja a **Symantec-azonosítóját** (ha van), az Értékesítői **azonosítót,** a Felhasználói **azonosítót,** az **MPN-azonosítót** és az **Azure AD-bérlőket.** Ezeket az értékeket a Microsoft rendeli hozzá a fejlesztői fiók egyedi azonosításához, és nem szerkeszthetők.

Ha nincs Symantec-azonosítója, a hivatkozást választva kérhet egyet.

### <a name="contact-info"></a>Kapcsolattartási adatok

A _Kapcsolattartási adatok_ szakaszban láthatja a Közzétevő megjelenített **nevét,** az Értékesítő kapcsolattartási adatait **(a** kapcsolattartó nevét, e-mail-címét, telefonszámát és címét) és a Vállalat jóváhagyóját **(a** vállalattal kapcsolatos döntéseket jóváhagyó személy nevét, e-mail-címét és telefonszámát).

A Frissítés hivatkozásra  kattintva módosíthatja a kapcsolattartási adatokat, például a közzétevő megjelenített nevét és e-mail-címét.

### <a name="account-settings-identifiers"></a>Fiókbeállítások azonosítói

A **Fiókbeállítások**  >  **Szervezeti profil alatt** válassza az **Azonosítók lehetőséget** a következő információkhoz:

- **MPN-edik:** A fiókjához társított MPN-edik száma
- **CSP:** Az ehhez a fiókhoz tartozó CSP-programhoz társított MPN-edik.
- **Közzétevő:** A fiókjához társított értékesítői ellenőrzések
- **Nyomkövetési GUID-azonosítók:** A fiókjához társított nyomkövetési GUID-azonosítók

#### <a name="tracking-guids"></a>GUID-azonosítók nyomon követése

A globálisan egyedi azonosítók (GUID-k) egyedi referenciaszámok (32 hexadecimális számjegyekkel), amelyek az Azure-használat nyomon követésére használhatók.

A nyomkövetési GUID azonosítók létrehozásához egy GUID-generátort kell használnia. Az Azure Storage csapata létrehozott egy GUID generátorűrlapt, amely e-mailt küld Önnek a megfelelő formátumú [GUID](https://aka.ms/StoragePartners) azonosítóval, és újra felhasználható a különböző követő rendszerekben.

Javasoljuk, hogy minden termékhez hozzon létre egy egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához. Dönthet úgy, hogy egyetlen GUID-t használ a termék több terjesztési csatornához, ha nem szeretné felosztani a jelentéseket.

Ha sablon használatával helyez üzembe egy terméket, és az elérhető a Azure Marketplace és a GitHubon is, két különálló GUID-t hozhat létre és regisztrálhat:

- Az A termék a Azure Marketplace
- A termék a GitHubon

A jelentéskészítés a partnerérték (Microsoft Partnerazonosító) és a GUID azonosítók alapján történik. A GUID azonosítókat részletesebben is nyomon követheti, igazodva az ajánlat egyes csomagjaihoz.

További információ: Az Azure-ügyfelek használatának nyomon [követése GUID azonosítók használatával – gyakori kérdések).](azure-partner-customer-usage-attribution.md#faq)

### <a name="agreements"></a>Egyezmények

A **Szerződések** lapon megtekintheti az Ön által engedélyezett közzétételi szerződések listáját. Ezek a szerződések név és verziószám szerint vannak felsorolva, beleértve az elfogadás dátumát és a szerződést elfogadó felhasználó nevét.

A Szerződések lap elérése:

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)
1. A jobb felső sarokban válassza a **Beállítások**  >  **Fiókbeállítások lehetőséget.**
1. A **Fiókbeállítások alatt** válassza a **Szerződések lehetőséget.**

**A szükséges** műveletek a lap tetején jelennek meg, ha olyan szerződésfrissítések vannak, amelyekre figyelmet kell fordítani. A frissített szerződés elfogadásához először olvassa el a csatolt szerződés verzióját, majd válassza a **Szerződés elfogadása lehetőséget.**

## <a name="set-up-a-payout-profile"></a>Kifizetési profil beállítása

A kifizetési profil az a bankszámla, amelyre a bevételeket az értékesítések küldik. Ennek a fióknak ugyanabban az országban vagy régióban kell lennie, ahol regisztrálta Partnerközpont fiókját. A kifizetési profilokkal kapcsolatos további információkért lásd: [Ösztönzők](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) kifizetési és adóprofiljainak létrehozása és kezelése a Partnerközpont és a Kifizetési fiók és adó űrlapok [beállítása.](/partner-center/set-up-your-payout-account)

A kifizetési profil beállítása:

1. A kereskedelmi [piactér áttekintési lapját a](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Partnerközpont.
2. A Profil **szakaszban,** a **Kifizetési profil mellett válassza** a Frissítés **lehetőséget.**
3. **Fizetési mód kiválasztása:** Banki számla vagy PayPal.
4. **Fizetési adatok hozzáadása:** Ide tartozhat a fiók típusának kiválasztása (ellenőrzés vagy megtakarítás), a fióktulajdonos nevének, a fiókszámnak és az útválasztási számnak, a számlázási címnek, a telefonszámnak vagy a PayPal e-mail-címének megadása. A PayPal fiókkal történő fizetési módként való használatával kapcsolatos további információkért és annak a piacon vagy régióban való támogatásával kapcsolatos további információkért lásd: [PayPal-adatok.](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)

> [!IMPORTANT]
> A kifizetési fiók módosítása legfeljebb egy fizetési ciklussal késleltetheti a kifizetéseket. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ahogyan a kifizetési fiók első beállításakor. A fiók ellenőrzése után is a teljes összegért fizet. Az aktuális fizetési ciklushoz esedékes kifizetések a következő fizetési ciklushoz lesznek hozzáadva.  

## <a name="tax-profile"></a>Adóprofil

Tekintse át az adóprofil aktuális  állapotát, és ellenőrizze, hogy a megfelelő Entitástípus és Adózási tanúsítvány adatai  jelennek-e meg. Válassza **a Szerkesztés** lehetőséget a szükséges űrlapok frissítéséhez vagy befejezéséhez.

Adóállapotának létrehozására meg kell adnia az országát vagy régióját, és meg kell adnia az országához vagy régiójához tartozó adózási űrlapokat.

Országától vagy régiójától függetlenül meg kell töltenie az adóbevallási űrlapokat, Egyesült Államok Microsofton keresztüli értékesítéshez. Azok a partnerek, akik megfelelnek bizonyos Egyesült Államok követelményeknek, ki kell tölteniük az IRS W-9 űrlapot. A nem Egyesült Államok partnereknek ki kell tölteniük az IRS W-8 űrlapot. Ezeket az űrlapokat online is kitöltheti az adóprofil kitöltése után.

A Egyesült Államok azonosító szám (vagy ITIN) nem szükséges a Microsofttól való kifizetéshez vagy az adóval kapcsolatos előnyök igényléséhez.

Adóbevallási űrlapokat elektronikus úton küldhet be a Partnerközpont; A legtöbb esetben nem kell űrlapokat kinyomtatni és leveleket küldeni.

A különböző országokra és régiókra különböző adózási követelmények vonatkoznak. Az, hogy pontosan mennyi adókat kell fizetnie, attól függ, hogy mely országokban és régiókban értékesíti az ajánlatát. A Microsoft bizonyos országokban és régiókban átveszi az értékesítéseket, és az Ön nevében használja az adókat. Ezeket az országokat és régiókat az ajánlat listázása során azonosítjuk. Más országokban és régiókban– a regisztráció helyétől függően – előfordulhat, hogy közvetlenül a helyi adóhatóságnak kell átcsoportosoznia az értékesítéseket, és adóhasználatot kell használnia. Emellett előfordulhat, hogy az Ön által kapott értékesítési bevétel bevételként is adózható. Határozottan javasoljuk, hogy lépjen kapcsolatba az országa vagy régiója megfelelő hatóságával, amely a legjobban segíthet meghatározni a Megfelelő adózási adatokat a Microsoft értékesítési tranzakcióihoz.

Az adóprofilokkal kapcsolatos további információkért lásd: [Ösztönzők](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) kifizetési és adóprofiljainak létrehozása és kezelése a Partnerközpont kifizetési számla és adó űrlapok [beállítása.](/partner-center/set-up-your-payout-account)

### <a name="withholding-rates"></a>Visszatartott díjak

Az adózási űrlapokon beadott információ határozza meg a megfelelő adóelőleg-rátát. A visszatartott ár csak azokra az értékesítésre vonatkozik, amelyek a Egyesült Államok; A nem EGYESÜLT ÁLLAMOK-helyről történő értékesítésekért nem kell visszatartanunk. A forrásadók aránya eltérő, de a legtöbb, a Egyesült Államok regisztráló fejlesztő esetén az alapértelmezett arány 30%. Lehetősége van csökkenteni ezt a mértéket, ha országa vagy régiója elfogadta a jövedelemadót a Egyesült Államok.

### <a name="tax-treaty-benefits"></a>Adózási előnyök

Ha kívül esik a Egyesült Államok, igénybe veheti az adóelőnyt. Ezek az előnyök országonként/régiónként eltérőek lehetnek, és csökkentheti a Microsoft által visszatartott adókat. Az adóval kapcsolatban a W-8BEN űrlap II. részének kitöltésével igényelhet kedvezményt. Javasoljuk, hogy kommunikáljon az ország vagy régió megfelelő erőforrásaival annak megállapításához, hogy ezek az előnyök érvényesek-e Az Ön számára.

[További információ a Windows-alkalmazások/játékfejlesztők](/windows/uwp/publish/tax-details-for-paid-apps)és a Azure Marketplace adó részleteiről.

### <a name="payout-hold-status"></a>Kifizetési hold állapota

Alapértelmezés szerint a Microsoft havonta küld kifizetéseket. A kifizetéseket azonban igény szerint visszatarthatja, ami megakadályozza a kifizetések küldését a fiókjába. Ha úgy dönt, hogy a kifizetéseket visszatartja, továbbra is rögzítjük a bevételeket, és a részleteket a Kifizetési **összegzésben adhatja meg.** Addig azonban nem küldünk kifizetéseket a fiókjába, amíg el nem távolítja a visszatartott fizetést.

**A kifizetések visszatartott helyére:**

1. Ugrás a **Fiókbeállítások elemre.** 
1. A bal oldali navigációs sávon bontsa ki **a Kifizetés és adó lehetőséget,** majd válassza a Kifizetési és **adóprofilok lehetőséget.**
1. Válassza ki azt a programot, amelynek kifizetését szeretné tartani, majd jelölje be a **Fizetésem visszatartása** jelölőnégyzetet.

A kifizetési hold állapot bármikor megváltoztatható, de vegye figyelembe, hogy a döntés hatással lesz a következő havi kifizetésre. Ha például áprilisi kifizetést szeretne tartani, ügyeljen arra, hogy  a kifizetési hold állapotát március vége előtt Be állapotúra állítsa.

Miután beállította a kifizetési hold állapot be van **kapcsolva,** minden kifizetés fel lesz függve, amíg a csúszkát vissza nem húzza a Ki **állapotra.** Ha így történik, a következő havi kifizetési ciklusban szerepelni fog (feltéve, hogy a vonatkozó kifizetési küszöbértékek teljesültek). Ha például a kifizetései fel vannak függve, de júniusban szeretne kifizetést generálni, akkor mindenképpen a  május vége előtt a kifizetés hátrán állapotra kell áttenné a Ki állapotot.

> [!NOTE]
> A **Kifizetési hold** állapot  kiválasztása a Microsoft Partnerközpont-on keresztül kifizetett összes bevételi forrásra vonatkozik, beleértve a Azure Marketplace, Microsoft AppSource, Microsoft Store, hirdetés stb.). Nem választhat különböző hold állapotokat az egyes bevételi forrásokhoz.

## <a name="devices"></a>Eszközök

Az eszközkezelési beállítások csak az univerzális Windows-platformon (UWP) történő közzétételre vonatkoznak. [További információ](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Számlázási profil létrehozása

Ha Dynamics [365 for Customer Engagement-& Power Apps](dynamics-365-customer-engage-offer-setup.md) vagy [Dynamics 365 for Operations-ajánlatot](./partner-center-portal/create-new-operations-offer.md) ad közzé, ki kell egész körű számlázási *profilt.*

A számlázási cím előre ki van töltve a jogi személytől, és ezt a címet később frissítheti. Az ADÓ- és áfaazonosító mezők egyes országok esetében kötelezőek, másoknál nem kötelezőek. Az ország/régió neve és a vállalat neve nem szerkeszthető.

1. Ugrás a **Fiókbeállítások elemre.**
1. Ezután a bal oldali navigációs sávon bontsa ki a **Szervezeti profil adatokat,** és válassza a **Számlázási profil lehetőséget.**


## <a name="multi-user-account-management"></a>Többfelhasználós fiókkezelés

Partnerközpont a [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) használatával többfelhasználós fiókokat használ a hozzáféréshez és a felügyelethez. A szervezet Azure AD-ját a rendszer automatikusan társítja a Partnerközpont-fiókjához a regisztrációs folyamat részeként.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók hozzáadása és kezelése](add-manage-users.md)
