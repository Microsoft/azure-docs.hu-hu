---
title: A számla rendszerezése az igényeinek megfelelően – Azure
description: Ismerje meg, hogyan rendszerezheti a számlán szereplő költségeket. Számlázási fiókját számlázási profilok és számlázási szakaszok létrehozásával szabhatja testre.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c3ee643d525cb5394369aba3b0e803bd1a94c852
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036235"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>A költségek rendszerezése a számlázási fiók testreszabásával

A Microsoft Ügyfélszerződéshez tartozó számlázási fiókkal rugalmasan rendszerezheti a költségeit az igényeinek megfelelően, például részleg, projekt vagy fejlesztési környezet szerint.

Ez a cikk azt ismerteti, hogyan rendszerezheti a költségeit az Azure Portal használatával. A Microsoft Ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

Tekintse meg a [megszervezési költségeket úgy, hogy testreszabja a Microsoft Customer Agreement számlázási fiókjának](https://www.youtube.com/watch?v=7RxTfShGHwU) videóját, hogy megtudja, hogyan szervezheti meg a Számlázási fiók költségeit.

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>A fiók strukturálása számlázási profilok és számlaszakaszok segítségével

A Microsoft Ügyfélszerződésekhez tartozó számlázási fiókokban számlázási profilokkal és számlaszakaszokkal rendszerezheti a költségeket.

![Képernyőkép a Microsoft Ügyfélszerződés számlázási hierarchiájáról](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Számlázási profil

A számlázási profil a számlázást és a kapcsolódó számlázási adatokat, például a fizetési módokat és a számlázási címet jeleníti meg. A hónap elején a fiókhoz tartozó összes számlázási profilhoz létrejön egy havi számla. A számla az Azure-használat és az egyéb vásárlások előző havi díjait tartalmazza.

Amikor regisztrál az Azure-ba, a számlázási fiókkal együtt automatikusan létrejön egy számlázási profil is. A több havi számlához tartozó költségek rendszerezéséhez létrehozhat további számlázási profilokat.

> [!IMPORTANT]
>
> A további számlázási profilok létrehozása hatással lehet a teljes költségre. További információért tekintse meg [az új számlázási profilok hozzáadásakor megfontolandó szempontokat](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Számlázási szakasz

A számlaszakasz egy költségcsoportosítást jelöl a számlán belül. A rendszer automatikusan létrehoz egy számlaszakaszt a fiók egyes számlázási profiljai számára. A költségek igényei szerinti rendszerezéséhez létrehozhat további szakaszokat. Az egyes számlaszakaszok az adott hónapban felszámolt díjakat jelenítik meg a számlán.

Az alábbi kép egy két számlaszakasszal (mérnöki tevékenység és marketing) rendelkező számlát mutat. A számlán megjelennek az egyes szakaszok összegzései és részletes díjai. A képen látható árak csak példák, és nem az Azure-szolgáltatások tényleges árát jelzik.

![Egy szakaszokkal rendelkező számlát bemutató kép](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Számlázásifiók-struktúra gyakori forgatókönyvekhez

Ez a szakasz a költségek rendszerezésével és a hozzájuk tartozó számlázásifiók-struktúrákkal kapcsolatos gyakori forgatókönyveket ismerteti:

|Forgatókönyv  |Struktúra  |
|---------|---------|
|Jack regisztrál az Azure-ba, és egyetlen havi számlát szeretne. | Egy számlázási profil és egy számlaszakasz. Ezt a struktúrát a rendszer automatikusan létrehozza Jack számára az Azure-ba történő regisztráláskor, így nincs szükség további lépésekre. |

![Egyszeres számlázási forgatókönyv infografikája.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Forgatókönyv  |Struktúra  |
|---------|---------|
|A Contoso egy kis szervezet, amelynek egyetlen havi számlára van szüksége, ugyanakkor csoportosítani szeretné a költségeket a kapcsolódó részlegek (marketing és mérnöki) alapján.  | A Contoso számlázási profilja és egy-egy számlaszakasz a marketing és a mérnöki részleghez. |

![Többszörös számlázási forgatókönyv infografikája.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Forgatókönyv  |Struktúra  |
|---------|---------|
|A Fabrikam egy közepes szervezet, amelynek külön számlára van szüksége a mérnöki és a marketing részleghez. A mérnöki részleg esetében csoportosítani szeretnék a költségeket a környezet (éles és fejlesztési) szerint.  | Egy-egy számlázási profil a marketing és a mérnöki részleghez. A mérnöki részleg esetében egy-egy számlaszakasz az éles és a fejlesztési környezetekhez. |

![Többszörös számlázási forgatókönyv infografikája, ahol külön történik az éles és a fejlesztési környezet számlázása.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Új számlaszakasz létrehozása

Számlázási szakasz létrehozásához a **számlázási profil tulajdonosának** vagy a **számlázási profil közreműködőjének** kell lennie. További információkért lásd [a számlázási profil számlázási szakaszainak kezelését](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/mca-section-invoice/search-cmb.png)

3. Válassza a **Számlázási profilok** lehetőséget a bal oldali panelen. Válasszon ki egy számlázási profilt a listából. Az új szakasz megjelenik a kiválasztott számlázási profilhoz tartozó számlán.

   [![Képernyőkép a számlázási profilok listájáról.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. A bal oldali panelen válassza az **Számlaszakaszok** elemet, majd az oldal tetején található **Hozzáadás** elemet.

   [![Képernyőkép a számlaszakaszok hozzáadásáról](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Adjon egy nevet a számlaszakasznak.

   [![Képernyőkép a számlaszakasz-létrehozási oldalról](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Kattintson a **Létrehozás** gombra.

## <a name="create-a-new-billing-profile"></a>Új számlázási profil létrehozása

Számlázási profil létrehozásához a **számlázási fiók tulajdonosának** vagy a **számlázási fiók közreműködőjének** kell lennie. További információkért lásd [a számlázási fiókhoz tartozó számlázási profilok kezelését](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> A további számlázási profilok létrehozása hatással lehet a teljes költségre. További információért tekintse meg [az új számlázási profilok hozzáadásakor megfontolandó szempontokat](#things-to-consider-when-adding-new-billing-profiles).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/mca-section-invoice/search-cmb.png)

3. A bal oldali panelen válassza az **Számlázási profilok** elemet, majd az oldal tetején található **Hozzáadás** elemet.

   [![Képernyőkép a számlázási profilok listájáról, amelyen a Hozzáadás lehetőség van kiválasztva.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Ha nem látja a Hozzáadás gombot a Számlázási profil oldalon, akkor a funkció nem érhető el a fiókjához. A funkció jelenleg csak olyan fiókokhoz érhető el, amelyek létrehozása egy Microsoft-képviselővel együtt történt.

4. Töltse ki az űrlapot, és kattintson a **Létrehozás** lehetőségre.

   [![Képernyőkép számlázási profil létrehozási oldaláról](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Mező  |Meghatározás  |
    |---------|---------|
    |Name (Név)     | A megjelenítendő név segítségével egyszerűen azonosíthatja a számlázási profilt az Azure Portalon.  |
    |Rendelésszám    | Opcionális rendelésszám. A rendelésszám megjelenik a számlázási profilhoz létrehozott számlákon. |
    |Számlázási cím   | A számlázási cím megjelenik a számlázási profilhoz létrehozott számlákon. |
    |E-mail-számla   | Ha e-mailben szeretné megkapni a számlázási profilhoz tartozó számlákat, jelölje be az E-mail-számla jelölőnégyzetet. Ha nem jelöli be, a számlákat az Azure Portalon tekintheti meg és töltheti le.|

5. Kattintson a **Létrehozás** gombra.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Díjak csatolása számlaszakaszokhoz és számlázási profilokhoz

Miután az igényeinek megfelelően személyre szabta a számlázási fiókot, a kívánt számlaszakaszhoz és számlázási profilhoz csatolhatja az előfizetéseket és egyéb termékeket.

### <a name="link-a-new-subscription"></a>Új előfizetés csatolása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá az **Előfizetések** kifejezésre.

   [![Képernyőkép az előfizetés-keresésről az Azure Portalon.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. A lap tetején válassza a **Hozzáadás** elemet.

   ![Képernyőkép egy új előfizetés Előfizetések nézetének Hozzáadás gombjáról.](./media/mca-section-invoice/subscription-add.png)

4. Ha több számlázási fiókhoz is hozzáféréssel rendelkezik, válassza ki a Microsoft Ügyfélszerződéshez tartozó számlázási fiókot.

   ![Képernyőkép az Előfizetés létrehozása panelről.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Válassza ki azt a számlázási profilt, amelyre az előfizetés használata ki lesz számlázva. Az előfizetéshez tartozó Azure-használat és egyéb vásárlások díjai a kiválasztott számlázási profil számlájára lesznek kiszámlázva.

6. Válassza ki azt a számlaszakaszt, amelyhez csatolni szeretné az előfizetés költségeit. A díjak a számlázási profilhoz tartozó számla ezen szakasza alatt jelennek meg.

7. Válasszon ki egy Azure-csomagot, és adjon egy felhasználóbarát nevet az előfizetésnek.

9. Kattintson a **Create** (Létrehozás) gombra.  

### <a name="link-existing-subscriptions-and-products"></a>Meglévő előfizetések és termékek csatolása

Ha már rendelkezik Azure-előfizetésekkel vagy egyéb termékekkel (például Azure Marketplace- és AppSource-erőforrásokkal), a költségek átrendezéséhez áthelyezheti őket a meglévő számlaszakaszból egy másik számlaszakaszba.

> [!IMPORTANT]
>
> Az előfizetések és egyéb termékek csak olyan számlaszakaszok között helyezhetők át, amelyek ugyanahhoz a számlázási profilhoz tartoznak. Az előfizetések és termékek áthelyezése nem támogatott olyan számlaszakaszok között, amelyek különböző számlázási profilokhoz tartoznak.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a Költségkezelés + számlázás keresőkifejezéssel.](./media/mca-section-invoice/search-cmb.png)

3. Az előfizetés új számlaszakaszhoz való csatolásához válassza az **Azure-előfizetések** lehetőséget a képernyő bal oldalán. Egyéb termékek (például Azure Marketplace- és AppSource-erőforrások) esetében válassza az **Ismétlődő díjak** lehetőséget.

   [![A számlaszakasz módosításának lehetőségét mutató képernyőkép](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Ezen az oldalon kattintson a három pontra amellett az előfizetés vagy termék mellett, amelyet egy új számlaszakaszhoz szeretne csatolni. Válassza a **Számlázási szakasz módosítása** lehetőséget.

5. A legördülő listából válassza ki az új számlaszakaszt. A legördülő lista csak azokat a számlaszakaszokat jeleníti meg, amelyek ugyanahhoz a számlázási profilhoz tartoznak, mint a meglévő számlaszakasz.

    [![Képernyőkép egy új számlázási szakasz kiválasztásáról](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Kattintson a **Mentés** gombra.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Az új számlázási profilok hozzáadásakor megfontolandó szempontok

### <a name="azure-usage-charges-may-be-impacted"></a>Ez hatással lehet az Azure-használati díjakra

A Microsoft Ügyfélszerződéshez tartozó számlázási fiókban az egyes számlázási profilok Azure-használatának összesítése havonta történik. A szintenként meghatározott díjszabással rendelkező Azure-erőforrások árainak meghatározása külön, az egyes számlázási profilok használata alapján történik. Az ár kiszámításakor a számlázási profilok használata nincs összesítve. Ez hatással lehet a több számlázási profillal rendelkező fiókok Azure-használatának teljes költségére.

Vegyünk egy példát arra, hogyan térnek el a költségek két forgatókönyv esetében. A forgatókönyvekben szereplő árak csak példák, és nem az Azure-szolgáltatások tényleges árát jelzik.

#### <a name="you-only-have-one-billing-profile"></a>Csak egy számlázási profillal rendelkezik.

Tegyük fel, hogy az Azure-blokkblobtárolót használja, amely gigabájtonként (GB) 0,00184 amerikai dollárba kerül az első 50 terabájt (TB), majd gigabájtonként 0,00177 amerikai dollárba a következő 450 terabájt (TB) esetében. 100 TB-ot használt fel a számlázási profiljához számlázott előfizetésekben. Az alábbiakban láthatja, hogy mennyi lesz a teljes díj.

|  Szint díjszabása (USD) |Mennyiség | Összeg (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB esetében havonta    | 50 TB        | 92,0   |
|1,77/TB a következő 450 TB esetében havonta    |  50 TB         | 88,5   |
|Összesen     |     100 TB  | 180,5

Ebben a forgatókönyvben 100 TB adat használatának teljes díja **180,5** amerikai dollár.

#### <a name="you-have-multiple-billing-profiles"></a>Több számlázási profillal rendelkezik.

Most tegyük fel, hogy létrehozott egy másik számlázási profilt, és 50 GB-ot használt fel az első számlázási profilhoz kiszámlázott, valamint 50 GB-ot a második számlázási profilhoz kiszámlázott előfizetéseken keresztül. Az alábbiakban láthatja, hogy mennyi számlázunk ki.

`Charges for the first billing profile`

|  Szint díjszabása (USD) |Mennyiség | Összeg (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB esetében havonta    | 50 TB        | 92,0  |
|1,77/TB a következő 450 TB esetében havonta    |  0 TB         | 0,0  |
|Összesen     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Szint díjszabása (USD) |Mennyiség | Összeg (USD)|
|---------|---------|---------|
|1,84/TB az első 50 TB esetében havonta    | 50 TB        | 92,0  |
|1,77/TB a következő 450 TB esetében havonta    |  0 TB         | 0,0  |
|Összesen     |     50 TB  | 92,0

Ebben a forgatókönyvben 100 TB adat használatának teljes díja **184,0** (92,0*2) amerikai dollár.

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Az Azure-foglalásokra vonatkozó kedvezmények nem feltétlenül érvényesek minden előfizetésre

A megosztott hatókörrel rendelkező Azure-foglalások az egyetlen számlázási profilhoz tartozó előfizetésekre érvényesek, és nincsenek megosztva a számlázási profilok között.

![A foglalás különböző fiókstruktúrák esetében történő alkalmazását bemutató infografika](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

A fenti képen a Contoso két előfizetéssel rendelkezik. Az Azure-foglalási kedvezmény alkalmazása a számlázási fiók struktúrájától függően eltér. A bal oldali forgatókönyvben a foglalási kedvezmény a mérnöki számlázási profilhoz kiszámlázott mindkét előfizetésre alkalmazva lesz. A jobb oldali forgatókönyvben a foglalási kedvezmény csak az 1. előfizetésre lesz alkalmazva, mivel ez az egyetlen előfizetés, amely a mérnöki számlázási profilhoz van kiszámlázva.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [További Azure-előfizetés létrehozása a Microsoft-ügyfélszerződésben](create-subscription.md)
- [Számlázási szerepkörök kezelése az Azure Portalon](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Azure-előfizetések számlázási tulajdonjogának beszerzése más számlázási fiókok felhasználóitól](mca-request-billing-ownership.md)
