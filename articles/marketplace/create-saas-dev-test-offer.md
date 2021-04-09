---
title: Tesztelési ajánlat létrehozása
description: Hogyan hozhat létre egy külön fejlesztési ajánlatot a Microsoft partner Center kereskedelmi piactér programjának üzemi ajánlatának teszteléséhez.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 893d38d7dcf2ef0910bc46d3e9bfd168c2a89162
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543363"
---
# <a name="create-a-test-offer"></a>Tesztelési ajánlat létrehozása

Az éles környezetből származó, különálló környezetben való fejlesztéshez létre kell hoznia egy külön tesztelési és fejlesztési (DEV) ajánlatot és egy külön éles üzemi (PROD) ajánlatot. További információ a különböző fejlesztői ajánlatok használatának előnyeiről: [SaaS-ajánlat megtervezése](plan-saas-offer.md#test-offer).

A legtöbb beállítást a fejlesztői és a PROD-ajánlatokban is megadhatja. Például a hivatalos marketing nyelvének és eszközeinek, például a képernyőképeknek és a emblémáknak azonosnak kell lenniük. Abban az esetben, ha a konfiguráció azonos, a fejlesztői ajánlat csomagjaiból másolhatja és beillesztheti a mezőket a PROD ajánlat csomagjaira.

A következő szakaszok ismertetik a fejlesztői és a gyártási ajánlatok közötti konfigurációs különbségeket.

## <a name="offer-setup-page"></a>Ajánlat beállítása lap

Javasoljuk, hogy ugyanazt az aliast használja mindkét ajánlat **alias** mezőjében, de fűzze hozzá a "_test" kifejezést a fejlesztői ajánlat aliasához. Ha például a PROD-ajánlat aliasa "contososolution", akkor a fejlesztői ajánlat aliasának "contososolution_test"-nek kell lennie. Így könnyedén azonosíthatja, hogy melyik fejlesztői ajánlata legyen a PROD-ajánlatból.

Az **ügyfél-érdeklődők** szakaszban a fejlesztői ajánlathoz használjon egy Azure-táblázatot vagy egy TESZTELÉSi CRM-környezetet. Használja a tervezett ólom felügyeleti rendszerét a PROD ajánlathoz.

## <a name="properties-page"></a>Tulajdonságok lap

Ugyanezt a lapot konfigurálja a fejlesztői és a PROD-ajánlatokban is.

## <a name="offer-listing-page"></a>Ajánlati lista lapja

Ugyanezt a lapot konfigurálja a fejlesztői és a PROD-ajánlatokban is.

## <a name="preview-audience"></a>Előnézet célközönsége

A fejlesztői ajánlatban adja meg a fejlesztők és tesztelők Azure Active Directory (HRE) egyszerű felhasználóneveit vagy Microsoft-fiók (MSA) e-mail-címét, beleértve a saját magát is. A HRE felhasználó egyszerű felhasználóneve eltérő lehet az adott felhasználó e-mail-címével. Például jane.doe@contoso.com nem fog működni, de a következő janedoe@contoso.com lesz:. A kijelölt felhasználók hozzáférhetnek a fejlesztői ajánlathoz, amikor megosztják az **előzetes** verzió hivatkozását a fejlesztési és tesztelési fázisban.

A PROD ajánlatban adja meg az Azure AD egyszerű felhasználónevet vagy a Microsoft-fiók e-mail-címét azon felhasználók számára, akik érvényesíteni fogják az ajánlatot, mielőtt a **Go Live (élő) gombra kattint** az ajánlat közzétételéhez.

## <a name="technical-configuration-page"></a>Technikai konfiguráció lap

Ez a táblázat a fejlesztői ajánlatok és a PROD-ajánlatok beállításai közötti különbségeket ismerteti.

***1. táblázat: technikai konfigurációs különbségek***

| Beállítás | FEJLESZTŐI ajánlat | GYÁRTÁSI ajánlat |
| ------------ | ------------- | ------------- |
| Kezdőlap URL-címe | Adja meg a fejlesztési és tesztelési végpontot. | Adja meg az üzemi végpontot. |
| Kapcsolat webhook | Adja meg a fejlesztési és tesztelési végpontot. | Adja meg az üzemi végpontot. |
| Azure Active Directory bérlő azonosítója | Adja meg a teszt alkalmazás regisztrációs bérlői AZONOSÍTÓját (HRE Directory-azonosító). | Adja meg az üzemi alkalmazás regisztrációs bérlői AZONOSÍTÓját. |
| Azure Active Directory alkalmazás azonosítója | Adja meg a teszt alkalmazás regisztrációs alkalmazásának AZONOSÍTÓját (ügyfél-azonosító). | Adja meg az üzemi alkalmazás regisztrációs alkalmazásának AZONOSÍTÓját. |
||||

## <a name="plan-overview-page"></a>Terv – Áttekintés lap

A csomagok létrehozásakor azt javasoljuk, hogy ugyanazt a _díjcsomag-azonosítót_ és- _csomagot_ használja, mint a fejlesztői és a Prod-ajánlatban, kivéve a csomag azonosítójának hozzáfűzését a fejlesztői ajánlatban **_test**. Ha például a csomag azonosítója a "Enterprise", akkor a fejlesztői ajánlatban a terv azonosítója legyen "enterprise_test". Így könnyedén azonosíthatja, hogy melyik fejlesztői ajánlata legyen a PROD-ajánlatból. A GYÁRTÁSIRENDELÉS-ajánlatban terveket hozhat létre, amelyek az Ön által választott díjszabási modellekkel és árakkal rendelkeznek.

### <a name="plan-listing"></a>Csomag listázása

A **terv áttekintő**  >  **csomag listázása** lapon adja meg ugyanazt a tervezési leírást a fejlesztői és a gyártási tervekben is.

### <a name="pricing-and-availability-page"></a>Díjszabás és rendelkezésre állás lap

Ez a szakasz útmutatást nyújt a **terv áttekintő**  >  **díjszabásának és rendelkezésre állási** oldalának végrehajtásához.

#### <a name="markets"></a>Piacok

Válassza ki ugyanazt a piacot a DEV és a PROD ajánlatok számára.

#### <a name="pricing"></a>Díjszabás

A fejlesztői ajánlat használatával kísérletezzen az árképzési modellekkel. Miután meggyőződött arról, hogy melyik díjszabási modell vagy modellek működnek a legjobban, a kívánt díjszabási modellekkel és árakkal hozza létre a terveket a PROD-ajánlatban.

A fejlesztői ajánlatnak nulla vagy alacsony díjszabású csomagokkal kell rendelkeznie a csomagokban. A PROD-ajánlat árait az ügyfeleknek felszámítani kívánt árak teszik elérhetővé.

> [!IMPORTANT]
> Az előzetes verzióban megvásárolt vásárlások mind a fejlesztői, mind a PROD-ajánlatok esetében feldolgozva lesznek. Ha egy ajánlat 100 USD/hó árával rendelkezik, a vállalata $100 díjat számít fel. Ha ez történik, nyisson meg egy [támogatási jegyet](support.md) , és a teljes összegért (és az áruházi szolgáltatási díj nélkül) ki kell fizetnie a kifizetést.

#### <a name="pricing-model"></a>Díjszabási modell

Használja ugyanazt az árképzési modellt a DEV és a PROD ajánlatok csomagjaiban. Ha például a GYÁRTÁSIRENDELÉS-ajánlat terve átalánydíjas, havi számlázási időszakot használ, majd konfigurálja a csomagot a fejlesztői ajánlatban ugyanazzal a modellel.

Ha csökkenteni szeretné a díjszabási modellek tesztelésének költségeit, beleértve a piactér egyéni mérőszámának dimenzióit, javasoljuk, hogy **konfigurálja a** díjszabást **és a rendelkezésre állást** lapon a fejlesztői ajánlatban, amely alacsonyabb árakat biztosít, mint a Prod ajánlat. Íme néhány irányelv, amely a fejlesztői ajánlatban szereplő csomagok árának beállításakor követhető el.

***2. táblázat: díjszabási irányelvek***

| Ár | Megjegyzés |
| ------------ | ------------- |
| 0,00 USD | Állítsa be a teljes tranzakciós költségeket nulla értékre, ha nincs pénzügyi hatása. Használja ezt az árat a mérési API-k hívásakor, illetve az ajánlatban lévő vásárlási tervek teszteléséhez a megoldás fejlesztése során. |
| $0,01 – $49,99 | Használja ezt az árlistát az elemzések, a jelentéskészítés és a vásárlási folyamat teszteléséhez. |
| $50,00 és újabb verziók | A kifizetés teszteléséhez használja ezt az árat. A fizetési ütemtervről a [kifizetési ütemtervek és folyamatok](/partner-center/payout-policy-details)című témakörben olvashat bővebben. |
|||

Ha el szeretné kerülni, hogy egy áruházi szolgáltatási díjat ne kelljen fizetnie a teszten, nyisson meg egy [támogatási jegyet](support.md).

#### <a name="free-trial"></a>Ingyenes próbaidőszak

Ne engedélyezzen ingyenes próbaverziót a fejlesztői ajánlathoz.

## <a name="co-sell-with-microsoft-page"></a>Közös értékesítés a Microsoft oldallal

Ne konfigurálja a **közös értékesítést a Microsoft** LAPJÁN a fejlesztői ajánlathoz.

## <a name="resell-through-csps"></a>Viszonteladás CSP-ken keresztül

Ne konfigurálja a DEV-ajánlaton **keresztüli viszonteladás a CSP** -ket lapon.

## <a name="next-steps"></a>Következő lépések

- [SaaS-ajánlat tesztelése és közzététele](test-publish-saas-offer.md)
