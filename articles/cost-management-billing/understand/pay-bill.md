---
title: A Microsoft Azure-számlák befizetése
description: Megismerheti, hogyan fizetheti be számláit az Azure Portalon. A portálon történő fizetéshez egy számlázási profil tulajdonosának, közreműködőjének vagy számlakezelőjének kell lennie.
keywords: számlázás, késedelem, egyenleg, azonnali fizetés,
author: banders
ms.reviewer: judupont
tags: billing, past due, pay now, bill, invoice, pay
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ef24c80e50cc45f48b583d721b35f61645b94033
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483180"
---
# <a name="how-to-pay-your-bill-for-microsoft-azure"></a>A Microsoft Azure-számlák befizetésének módja

Ez a cikk a Microsoft Ügyfélszerződéssel (MCA) rendelkező ügyfelekre vonatkozik.

[Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése](#check-access-to-a-microsoft-customer-agreement)

Az Azure-számlákat kétféle módon lehet befizetni. Az egyik a számlázási profil alapértelmezett fizetési módja, a másik pedig az **Azonnali fizetés** néven ismert egyszeri befizetés.

Ha Microsoft-képviselőn keresztül regisztrált az Azure-ra, akkor az Ön fizetési módjának alapértelmezett beállítása *csekk vagy banki átutalás* lesz. Az automatikus hitelkártyás fizetés nem lehetséges, ha Microsoft-képviselővel regisztrált az Azure-ba. Ehelyett [hitelkártyával fizethet az egyes számlákért.](#pay-now-in-the-azure-portal)

Ha rendelkezik Azure-kreditekkel, a rendszer automatikusan alkalmazza őket a számlára az egyes számlázási időszakokban.

## <a name="pay-by-default-payment-method"></a>Fizetés az alapértelmezett fizetési móddal

A számlázási profil alapértelmezett fizetési módja lehet hitel- vagy bankkártyás, illetve csekkes/banki átutalásos.

### <a name="credit-or-debit-card"></a>Hitel- vagy bankkártya

Ha a számlázási profil alapértelmezett fizetési módja hitel- vagy bankkártyás, minden számlázási időszakban automatikusan megterheljük a kártyát.

Ha a hitel- vagy bankkártya automatikus terhelését valamilyen okból elutasítják, egy egyszeri fizetést is elvégezhet hitel- vagy bankkártyával az Azure Portalon, az **Azonnali fizetés** lehetőséggel.

Ha az alapértelmezett fizetési módot szeretné átállítani csekkes/átutalásos fizetésre, tekintse meg [A számlás fizetés működése](../manage/pay-by-invoice.md) című szakaszt.

### <a name="check-or-wire-transfer"></a>Csekk vagy banki átutalás

Ha a számlázási profil alapértelmezett fizetési módja a csekk/banki átutalás, kövesse a PDF-formátumú számlán található fizetési útmutatást.

Abban az esetben, ha a számla összege a pénznem küszöbértéke alatt van, egyszeri fizetést is elvégezhet hitel- vagy bankkártyával az Azure Portalon, az **Azonnali fizetés** lehetőséggel. Ha a számla összege meghaladja a pénznem küszöbértékét, a számla nem fizethető hitel- vagy bankkártyával. A pénznem küszöbértéke az Azure Portalon az **Azonnali fizetés** lehetőség kiválasztása után látható.

#### <a name="bank-details-used-to-send-wire-transfer-payments"></a>Banki adatok, amelyek az átutalásos kifizetések küldését küldik
<a name="wire-bank-details"></a>

Ha az alapértelmezett fizetési mód az átutalás, ellenőrizze a számláján a fizetési utasításokat. Az alábbi listában megtalálja az országára vagy régiójára vonatkozó fizetési utasításokat.

> [!div class="op_single_selector"]
> - **Válassza ki országát vagy régióját**
> - [Afganisztán](/legal/pay/afghanistan)
> - [Albánia](/legal/pay/albania)
> - [Algéria](/legal/pay/algeria)
> - [Angola](/legal/pay/angola)
> - [Argentína](/legal/pay/argentina)
> - [Örményország](/legal/pay/armenia)
> - [Ausztrália](/legal/pay/australia)
> - [Ausztria](/legal/pay/austria)
> - [Azerbajdzsán](/legal/pay/azerbaijan)
> - [Bahamák](/legal/pay/bahamas)
> - [Bahrein](/legal/pay/bahrain)
> - [Banglades](/legal/pay/bangladesh)
> - [Barbados](/legal/pay/barbados)
> - [Belarusz](/legal/pay/belarus)
> - [Belgium](/legal/pay/belgium)
> - [Belize](/legal/pay/belize)
> - [Bermuda](/legal/pay/bermuda)
> - [Bolívia](/legal/pay/bolivia)
> - [Bosznia-Hercegovina](/legal/pay/bosnia-and-herzegovina)
> - [Botswana](/legal/pay/botswana)
> - [Brazília](/legal/pay/brazil)
> - [Brunei](/legal/pay/brunei)
> - [Bulgária](/legal/pay/bulgaria)
> - [Kamerun](/legal/pay/cameroon)
> - [Kanada](/legal/pay/canada)
> - [Cabo Verde](/legal/pay/cape-verde)
> - [Kajmán-szigetek](/legal/pay/cayman-islands)
> - [Chile](/legal/pay/chile)
> - [Kína (EGYEND)](/legal/pay/china-prc)
> - [Kolumbia](/legal/pay/colombia)
> - [Costa Rica](/legal/pay/costa-rica)
> - [Côte d'Ivoire](/legal/pay/cote-divoire)
> - [Horvátország](/legal/pay/croatia)
> - [Curacao](/legal/pay/curacao)
> - [Ciprus](/legal/pay/cyprus)
> - [Cseh Köztársaság](/legal/pay/czech-republic)
> - [Dél-Afrikai Köztársaság](/legal/pay/democratic-republic-of-congo)
> - [Dánia](/legal/pay/denmark)
> - [Dominikai Köztársaság](/legal/pay/dominican-republic)
> - [Ecuador](/legal/pay/ecuador)
> - [Egyiptom](/legal/pay/egypt)
> - [Salvador](/legal/pay/el-salvador)
> - [Észtország](/legal/pay/estonia)
> - [Etiópia](/legal/pay/ethiopia)
> - [Feröer szigetek](/legal/pay/faroe-islands)
> - [Fidzsi](/legal/pay/fiji)
> - [Finnország](/legal/pay/finland)
> - [Franciaország](/legal/pay/france)
> - [Francia Guyana](/legal/pay/french-guiana)
> - [Grúzia](/legal/pay/georgia)
> - [Németország](/legal/pay/germany)
> - [Ghána](/legal/pay/ghana)
> - [Görögország](/legal/pay/greece)
> - [Grenada](/legal/pay/grenada)
> - [Guadeloupe](/legal/pay/guadeloupe)
> - [Guam](/legal/pay/guam)
> - [Guatemala](/legal/pay/guatemala)
> - [Guyana](/legal/pay/guyana)
> - [Haiti](/legal/pay/haiti)
> - [Honduras](/legal/pay/honduras)
> - [Hongkong](/legal/pay/hong-kong)
> - [Magyarország](/legal/pay/hungary)
> - [Izland](/legal/pay/iceland)
> - [India](/legal/pay/india)
> - [Indonézia](/legal/pay/indonesia)
> - [Irak](/legal/pay/iraq)
> - [Írország](/legal/pay/ireland)
> - [Izrael](/legal/pay/israel)
> - [Olaszország](/legal/pay/italy)
> - [Jamaica](/legal/pay/jamaica)
> - [Japán](/legal/pay/japan)
> - [Jordánia](/legal/pay/jordan)
> - [Kazahsztán](/legal/pay/kazakhstan)
> - [Kenya](/legal/pay/kenya)
> - [Dél-Korea](/legal/pay/korea)
> - [Kuvait](/legal/pay/kuwait)
> - [Kirgizisztán](/legal/pay/kyrgyzstan)
> - [Lettország](/legal/pay/latvia)
> - [Libanon](/legal/pay/lebanon)
> - [Líbia](/legal/pay/libya)
> - [Liechtenstein](/legal/pay/liechtenstein)
> - [Litvánia](/legal/pay/lithuania)
> - [Luxemburg](/legal/pay/luxembourg)
> - [Makaó](/legal/pay/macao)
> - [Észak-Afrikai Köztársaság](/legal/pay/macedonia)
> - [Malajzia](/legal/pay/malaysia)
> - [Málta](/legal/pay/malta)
> - [Mauritius](/legal/pay/mauritius)
> - [Mexikó](/legal/pay/mexico)
> - [Moldova](/legal/pay/moldova)
> - [Monaco](/legal/pay/monaco)
> - [Mongólia](/legal/pay/mongolia)
> - [Montenegró](/legal/pay/montenegro)
> - [Marokkó](/legal/pay/morocco)
> - [Namíbia](/legal/pay/namibia)
> - [Nepál](/legal/pay/nepal)
> - [Hollandia](/legal/pay/netherlands)
> - [Új-Zéland](/legal/pay/new-zealand)
> - [Nicaragua](/legal/pay/nicaragua)
> - [Nigéria](/legal/pay/nigeria)
> - [Norvégia](/legal/pay/norway)
> - [Omán](/legal/pay/oman)
> - [Pakisztán](/legal/pay/pakistan)
> - [Palesztin Hatóság](/legal/pay/palestinian-authority)
> - [Panama](/legal/pay/panama)
> - [Paraguay](/legal/pay/paraguay)
> - [Peru](/legal/pay/peru)
> - [Fülöp-szigetek](/legal/pay/philippines)
> - [Lengyelország](/legal/pay/poland)
> - [Portugália](/legal/pay/portugal)
> - [Puerto Rico](/legal/pay/puerto-rico)
> - [Katar](/legal/pay/qatar)
> - [Románia](/legal/pay/romania)
> - [Oroszország](/legal/pay/russia)
> - [Ruanda](/legal/pay/rwanda)
> - [Saint Kitts és Nevis](/legal/pay/saint-kitts-and-nevis)
> - [Saint Lucia](/legal/pay/saint-lucia)
> - [Saint Vincent és Grenadine-szigetek](/legal/pay/saint-vincent-and-the-grenadines)
> - [Szaúd-Arábia](/legal/pay/saudi-arabia)
> - [Szenegál](/legal/pay/senegal)
> - [Szerbia](/legal/pay/serbia)
> - [Szingapúr](/legal/pay/singapore)
> - [Szlovákia](/legal/pay/slovakia)
> - [Szlovénia](/legal/pay/slovenia)
> - [Dél-afrikai Köztársaság](/legal/pay/south-africa)
> - [Spanyolország](/legal/pay/spain)
> - [Srí Lanka](/legal/pay/sri-lanka)
> - [Suriname](/legal/pay/suriname)
> - [Svédország](/legal/pay/sweden)
> - [Svájc](/legal/pay/switzerland)
> - [Tajvan](/legal/pay/taiwan)
> - [Tádzsikisztán](/legal/pay/tajikistan)
> - [Tanzánia](/legal/pay/tanzania)
> - [Thaiföld](/legal/pay/thailand)
> - [Trinidad és Tobago](/legal/pay/trinidad-and-tobago)
> - [Türkmenisztán](/legal/pay/turkmenistan)
> - [Tunézia](/legal/pay/tunisia)
> - [Törökország](/legal/pay/turkey)
> - [Uganda](/legal/pay/uganda)
> - [Ukrajna](/legal/pay/ukraine)
> - [Egyesült Arab Emírségek](/legal/pay/united-arab-emirates)
> - [Egyesült Királyság](/legal/pay/united-kingdom)
> - [Egyesült Államok](/legal/pay/united-states)
> - [Uruguay](/legal/pay/uruguay)
> - [Üzbegisztán](/legal/pay/uzbekistan)
> - [Venezuela](/legal/pay/venezuela)
> - [Vietnam](/legal/pay/vietnam)
> - [Virgin-szigetek, USA](/legal/pay/virgin-islands)
> - [Jemen](/legal/pay/yemen)
> - [Zambia](/legal/pay/zambia)
> - [Zimbabwe](/legal/pay/zimbabwe)

## <a name="pay-now-in-the-azure-portal"></a>Azonnali fizetés az Azure Portalon

Az Azure Portalon történő fizetéshez a megfelelő [MCA-engedélyekkel](../manage/understand-mca-roles.md) kell rendelkeznie, vagy a számlázási fiók adminisztrátorának kell lennie. A számlázási fiók adminisztrátora az a felhasználó, aki eredetileg regisztrálta az MCA-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.
1. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet.
1. Ha a számlák bármelyike esedékes vagy lejárt, akkor az adott számlához megjelenik egy kék **Azonnali fizetés** hivatkozás. Válassza **Azonnali fizetés** lehetőséget.
1. Az Azonnali fizetés ablakban válassza a **Fizetési mód kiválasztása** lehetőséget, majd válasszon ki egy meglévő bankkártyát, vagy adjon hozzá egy újat.
1. Miután kiválasztotta a fizetési módot, válassza az **Azonnali fizetés** lehetőséget.

A számla állapota 24 órán belül *Kifizetve* lesz.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Következő lépések

- A csekkel/banki átutalással való fizetésre való jogosultság megszerzése érdekében tekintse meg [a számlás fizetés működését](../manage/pay-by-invoice.md) ismertető szakaszt
