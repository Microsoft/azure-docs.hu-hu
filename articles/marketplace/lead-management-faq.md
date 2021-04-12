---
title: Az érdeklődők felügyeletével kapcsolatos kérdések és hibaelhárítás – Microsoft partner Center
description: További információ a kereskedelmi Piactéri érdeklődők a partner Centerben való konfigurálásakor előforduló gyakori hibákról és kérdésekről
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330920"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Az érdeklődők konfigurálásával kapcsolatos gyakori kérdések és hibaelhárítás

Ez a cikk néhány gyakori kérdést választ a kereskedelmi Marketplace-ajánlatokkal kapcsolatos érdeklődők felügyeletével kapcsolatban. Emellett az ügyfelek ügyfélkapcsolat-kezelési (CRM) rendszerének a partner Centerben való konfigurálásakor felmerülő hibákat is tárgyalja.

## <a name="common-questions-about-lead-management"></a>Az érdeklődők felügyeletével kapcsolatos gyakori kérdések

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Hol kaphatok segítséget az érdeklődői cél beállításakor?

Tekintse [meg a kereskedelmi piactéren elérhető vásárlói érdeklődőket](partner-center-portal/commercial-marketplace-get-customer-leads.md) , és tekintse át, hogyan CSATLAKOZTATHATÓ a CRM-rendszer a kereskedelmi Piactéri ajánlatokhoz. Ha hibát észlel, tekintse át az alábbi hibaelhárítási útmutatót. További támogatásért küldje el a támogatási jegyet a [partner Center Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásán keresztül. Ezután válassza az **ajánlat létrehozása** lehetőséget  >  **az ajánlati**  >  **érdeklődők felügyeletének beállításához**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Meg kell-e adni egy vezető célhelyet, hogy közzé lehessen tenni egy ajánlatot a kereskedelmi piactéren?

A válasz a közzétett ajánlat típusától függ. A szolgáltatott szoftverként (SaaS) és a Dynamics 365 Customer engagement használatával **kapcsolatba léphetnek** az összes Dynamics 365 a pénzügyi és üzemeltetési ajánlatok, a Dynamics 365 üzleti központi ajánlatai és az összes tanácsadási szolgáltatás ajánlatának listázásához. Ennek eredményeképpen szükségük van egy érdeklődői célhoz való kapcsolódásra. Ha az ajánlat típusa nem szerepel a felsorolásban, nem kötelező csatlakozni egy érdeklődő céljához. Javasoljuk, hogy állítson be egy érdeklődői célhelyet, hogy ne hagyjon ki üzleti lehetőségeket.

#### <a name="how-can-i-find-the-test-lead"></a>Hogyan találhatom meg a tesztelési érdeklődőt?

Keresse meg a kifejezést az `"MSFT_TEST"` érdeklődő célhelyén. Az alábbiakban egy minta tesztet vezetünk be a Microsofttól. Vegye figyelembe, hogy a tesztelési érdeklődő formátuma a vezető célhelytől függően változhat.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Élő ajánlatom van, de miért nem látok semmilyen érdeklődőt?

Győződjön meg arról, hogy a vezető célhoz való kapcsolódás érvényes. A partner Centerben a **Közzététel** lehetőségre kattintva elküldünk egy tesztelési érdeklődőt. Ha a tesztelési érdeklődőt látja, a rendszer érvényes lesz a kapcsolatban. Azt is tesztelheti az érdeklődői kapcsolatokat, hogy az előzetes verzióban próbálja megkeresni az ajánlat előzetes verzióját. Válassza a **Letöltés most**, **Kapcsolatfelvétel** vagy **ingyenes próbaverzió** lehetőséget a kereskedelmi piactéren.

Győződjön meg arról is, hogy a megfelelő adathalmazokat keresi. Az érdeklődői célhelyre küldött érdeklődői információk megismeréséhez tekintse meg az [érdeklődői információk](partner-center-portal/commercial-marketplace-get-customer-leads.md) ismertetése című témakört.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Az Azure Blob Storage-t az érdeklődői célként konfiguráltam, de miért nem látom az érdeklődőt?

Az Azure Blob Storage már nem támogatott vezető célhelyként, így az ajánlat által létrehozott összes ügyfél-érdeklődő hiányzik. Váltson át a többi [érdeklődő célhelyére](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Kaptam egy e-mailt a kereskedelmi piactéren, de miért nem találom a vezetőt a CRM-ben?

Lehetséges, hogy a végfelhasználó e-mail-tartománya az. edu címről származik. Adatvédelmi okokból nem adunk át személyes adatokat az. edu tartományból. Támogatási jegy beküldése a [partner Center Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásán keresztül.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Hogyan tekinthetem meg az érdeklődőket?

Az Azure-táblában tárolt érdeklődői adatok a Azure Portalból érhetők el. Az Azure Storage-fiók táblázatos adatai megtekintéséhez letöltheti és telepítheti az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ingyen is.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Kaphatok értesítést, amikor új kereskedelmi Piactéri érdeklődőt küldenek?

Igen. Kövesse a következő témakör utasításait: az [érdeklődők felügyeletének konfigurálása Azure-táblázat segítségével](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) , amely egy olyan Microsoft flow-t állít be, amely e-mailt küld, ha az Azure-táblához érdeklődőt adnak hozzá.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Konfiguráltam Salesforce, de miért nem találom az érdeklődőket?

Ellenőrizze, hogy a webes és a vezető űrlap kötelező mező-e a kivételezési lista alapján. Ha igen, váltson a mezőre egy nem kötelező szövegmezőbe.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Probléma merült fel a vezető célhelyen, és néhány érdeklődőt kihagytam. Küldhetek nekem egy e-mailt?

Személyes információs szabályzatok miatt a nem biztonságos e-mailen keresztül nem oszthatjuk meg az érdeklődői adatokat.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Egy Azure-táblázatot konfiguráltam vezető célként. Mennyibe fog kerülni?

Az ólom létrehozási adatkészlete alacsony. Majdnem minden közzétevőnél kevesebb, mint 1 GB. A díj a kapott érdeklődők számától függ. Ha például az 1 000-es érdeklődők egy hónapban érkeznek, a díjak körülbelül 50 centet kapnak. További információ a Storage díjszabásáról: az [Azure Storage áttekintésének díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Ha a kérdés nem válaszol, lépjen kapcsolatba Microsoft ügyfélszolgálata a [partner Center Súgó és támogatás](https://aka.ms/marketplacepublishersupport)szolgáltatásán keresztül. Ezután válassza az **ajánlat létrehozása** lehetőséget  >  **az ajánlati**  >  **érdeklődők felügyeletének beállításához**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Értesítő e-mailt kapok az új ügyfelek fogadásakor. Hogyan állíthatom be valaki másnak az e-mailek fogadását?

Nyissa meg az ajánlatát a partner Centerben, és nyissa meg az **ajánlat beállítása** lapot > az **érdeklődői felügyelet**  >  **szerkesztése** lehetőséget. Frissítse a **kapcsolattartási e-mail** mezőben szereplő e-mail-címeket.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Az érdeklődők konfigurálásával kapcsolatos hibák elhárítása

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. Keresse meg a Dynamics CRM-fiók beállításait. LastCRMError: nem lehet bejelentkezni a Dynamics CRM-be, LastCRMException:** 

> Ha Microsoft 365 hitelesítés lett kiválasztva, ellenőrizze, hogy érvényes-e a felhasználói fiók és a jelszó. Ha Azure Active Directory lett kiválasztva, ellenőrizze, hogy a bérlő azonosítója, az alkalmazás azonosítója és az alkalmazás titkos kulcsa megegyezik-e a Azure Active Directory beállításával. Kövesse az [alábbi utasításokat.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Ha a fiók felhasználóneve/jelszava érvényes, ellenőrizze, hogy rendelkezik-e hozzáféréssel a Dynamics 365-hez, és van-e hozzárendelve licence (11-15. lépés, ha az Office-felhasználó használata esetén Azure Active Directory vagy biztonsági beállításokat használ). 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. A felhasználó nem rendelkezik a leadsourcecode attribútum létrehozásához szükséges engedélyekkel az érdeklődő entitásban.** 

> Az alkalmazás/felhasználó hiányzó biztonsági szerepkör (ek) Microsoft Marketplace vezető író számára. Kövesse az 11-15-es lépéseket, ha Azure Active Directory vagy biztonsági beállításokat használ, ha [itt](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)az Office-felhasználó használata szükséges.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a bérlő nem található. Ez a példány akkor fordulhat elő, ha a bérlőhöz nem tartoznak aktív előfizetések.**  

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. A könyvtár azonosítóját a 2. lépésben ( [Azure Active Directory) található](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)utasítások alapján szerezheti be.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: SecLib:: a RetrievePrivilegeForUser sikertelen – egyetlen szerepkör sincs hozzárendelve a felhasználóhoz.**  

> Megoldás: rendeljen biztonsági szerepkört Microsoft Marketplace vezető írója számára. Kövesse az utasításokat [itt](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) : biztonsági beállítások.

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: az azonosítójú alkalmazás nem található a címtárban.** 

> A vezető felügyeleti szakaszban megadott alkalmazás-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)a 8. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: a kért bérlői azonosító érvénytelen, és nem érvényes külső tartomány formátuma** 

> A vezető felügyeleti szakaszban megadott címtár-azonosító nem érvényes könyvtár. Szerezze be a [címtár-azonosítót](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)a 2. lépés (Azure Active Directory alatt) utasításai alapján. 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be a HRE használatával. Kivétel:: hiba történt a hitelesítő adatok érvényesítésekor.: Érvénytelen az ügyfél titkos kulcsa.** 

> Megoldás: Jelentkezzen be a Azure Portalba, és ellenőrizze, hogy az alkalmazás kulcsa megfelel-e a partneri központban. Adja meg a jelszót a 10. lépésben megadott utasítás alapján ( [Azure Active Directory).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nem lehetett menteni a vezetőt a Dynamics CRM-be. LastCRMError: a kérelem csatornájának időkorlátja lejárt, miközben a 00:02:00 utáni választ várt. Növelje meg a híváshoz átadott időtúllépési értéket a kérelemhez, vagy növelje a SendTimeout értékét a kötésben. Előfordulhat, hogy a művelethez megadott idő egy hosszú időtúllépés részét képezi.**  

> Megoldás: Jelentkezzen be a partner központba, és ellenőrizze, hogy az ajánlat telepítője >> ügyfél >> URL-címét, ellenőrizze, hogy érvényes-e a dinamikus CRM-példány.

