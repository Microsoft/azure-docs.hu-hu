---
title: Azure-költségek felosztása
description: Ez a cikk a költségfelosztási szabályok létrehozását ismerteti az előfizetések, erőforráscsoportok és címkék költségeinek másokkal való megosztásához.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 414ce626d76c9b7a7d073d6cbfa5a5f4446c3073
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025526"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Azure-beli költségfelosztási szabályok létrehozása és kezelése (előzetes verzió)

A nagyméretű vállalatok gyakran rendelkeznek olyan Azure-szolgáltatásokkal vagy -erőforrásokkal, amelyek központilag felügyeltek, de különböző belső részlegek vagy vállalati egységek használják őket. Általában a központi kezelőcsapat szeretné visszacsoportosítani a megosztott szolgáltatások költségeit a szolgáltatásokat aktívan használó belső részlegeknek vagy vállalati egységeknek. Ebből a cikkből megtudhatja, hogyan használhatja a költségfelosztási funkciót az Azure Cost Managementben.

A költségfelosztási funkcióval újra- vagy megoszthatja a megosztott szolgáltatások költségeit az előfizetések, erőforráscsoportok vagy címkék felől a szervezeten belüli más előfizetések, erőforráscsoportok vagy címkék felé. A költségfelosztás során a megosztott szolgáltatások költségei más, a szolgáltatást használó belső részlegekhez vagy vállalati egységekhez tartozó előfizetésekhez, erőforráscsoportokhoz vagy címkékhez kerülnek át. Más szóval a költségfelosztási funkció elősegíti a költségek kezelését és _elszámolhatóvá tételét_ a helyek között.

A költségfelosztási funkció nincs hatással a számlázásra. A számlázás felelőssége nem változik. A költségfelosztási funkció elsődleges célja lehetővé tenni költségek visszaosztását másoknak. A költséghelyi elszámolás minden folyamata a szervezetben, az Azure-on kívül megy végbe. A költségfelosztási funkció a hozzárendelt vagy kiosztott költségek megjelenítésével segíti a költségek elszámolását.

A kiosztott költségek megjelennek a költségelemzésben. További elemként jelennek meg a költségfelosztási szabály létrehozásakor célként megadott előfizetéseknél, erőforráscsoportoknál és címkéknél.

> [!NOTE]
> Az Azure Cost Management költségfelosztási funkciója jelenleg nyilvános előzetes verzióban érhető el. Előfordulhat, hogy a Cost Management néhány funkciója nem támogatott, vagy korlátozott képességekkel rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

- A költségfelosztási funkció jelenleg csak a [Microsoft Ügyfélszerződéssel](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) és a [Nagyvállalati Szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) rendelkező ügyfeleket támogatja.
- Költségfelosztási szabály létrehozásához [Nagyvállalati Szerződéshez](../manage/understand-ea-roles.md) tartozó vállalati rendszergazdai fiókot kell használnia. Vagy egy Microsoft Ügyfélszerződés [számlázási fiókja](../manage/understand-mca-roles.md) tulajdonosának kell lennie.

## <a name="create-a-cost-allocation-rule"></a>Költségfelosztási szabály létrehozása

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.
2. Keresse meg a **Költségkezelés + számlázás** > **Cost Management** elemet.
3. A **Beállítások** > **Konfiguráció** területen válassza a **Költségfelosztás (előzetes verzió)** lehetőséget.
4. Ellenőrizze, hogy a megfelelő EA-regisztráció vagy számlázási fiók van-e kiválasztva.
5. Válassza a **+Hozzáadás** lehetőséget.
6. A költségfelosztási szabály neveként adjon meg egy leíró szöveget.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Szabálynév létrehozását szemléltető példa" lightbox="./media/allocate-costs/rule-name.png" :::

Megjelenik a költségfelosztás előre megadott százalékos aránya a szabály kiértékelési kezdődátuma alapján.

1. Válassza a **Források hozzáadása** lehetőséget, majd válasszon ki előfizetéseket, erőforráscsoportokat vagy címkéket a felosztani kívánt költségek kiválasztásához.
2. Válassza a **Célok hozzáadása** lehetőséget, majd válassza ki az előfizetéseket, erőforráscsoportokat vagy címkéket, amelyeknek kiosztja a költségeket.
3. Ha további költségfelosztási szabályokat szeretne létrehozni, ismételje meg ezt az eljárást.

## <a name="configure-the-allocation-percentage"></a>A felosztás százalékos arányának konfigurálása

A felosztás százalékos arányának konfigurálásával meghatározhatja a költségek megoszlásának arányát a megadott célok között. A költségfelosztási szabály létrehozásához egész számként, manuálisan adhat meg százalékos értékeket. Vagy megoszthatja a költségeket a megadott célok között arányosan, a számítási igény, a tárterület vagy a hálózat aktuális felhasználásának mértéke alapján.

Ha a költségeket a számítási, tárolási vagy hálózati költségek szerint osztja fel, a százalékos arányt a kijelölt cél költségeinek kiértékelésével állapítja meg a rendszer. A költségek hozzárendelődnek az erőforrástípushoz az aktuális számlázási hónapban.

Ha a költségeket a teljes költség arányában osztja fel, a százalékos arány a kiválasztott célok teljes költségeinek összege alapján lesz meghatározva az aktuális számlázási hónapban.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="A felosztás százalékos arányát szemléltető példa" lightbox="./media/allocate-costs/cost-distribution.png" :::

A beállítás után az előre megadott százalékos arányok rögzítettek. Minden folyamatban lévő felosztás ezeken fog alapulni. A százalékos arányok csak akkor változnak, ha a szabályt manuálisan frissíti.

1. Válassza az alábbi lehetőségek egyikét a **Százalékos arány kitöltése** listán.
    - **Felosztás egyenlően** – Mindegyik cél azonos százalékos arányban részesül a teljes költségből.
    - **Teljes költség** – Arányos elosztás a célok között a teljes költségük alapján. Az arány a kiválasztott forrásokból származó költségek elosztására szolgál.
    - **Számítási költség** – Arányos elosztás a célok között az Azure számítási költségük alapján (erőforrástípusok a [Microsoft.Compute](/azure/templates/microsoft.compute/allversions) névtérben. Az arány a kiválasztott forrásokból származó költségek elosztására szolgál.
    - **Tárolási költség** – Arányos elosztás a célok között az Azure tárolási költségük alapján (erőforrástípusok a [Microsoft.Storage](/azure/templates/microsoft.storage/allversions) névtérben). Az arány a kiválasztott forrásokból származó költségek elosztására szolgál.
    - **Hálózati költség** – Arányos elosztás a célok között az Azure hálózati költségük alapján (erőforrástípusok a [Microsoft.Network](/azure/templates/microsoft.network/allversions) névtérben). Az arány a kiválasztott forrásokból származó költségek elosztására szolgál.
    - **Egyéni** – Lehetővé teszi a százalékos arány manuális megadását egész számként. A megadott százalékok összegének 100%-nak kell lennie.
1. A szabály konfigurálása után válassza a **Létrehozás** lehetőséget.

Megkezdődik a felosztási szabály feldolgozása. Ha a szabály aktív, az összes kiválasztott forrás költségét a megadott célokhoz rendeli.

> [!NOTE] 
> Egy új szabály feldolgozásának befejezése és a szabály aktiválódása akár két órát is igénybe vehet.

## <a name="verify-the-cost-allocation-rule"></a>A költségfelosztási szabály ellenőrzése

Ha a költségfelosztási szabály aktív, a kiválasztott forrásokból származó költségeket a megadott felosztási célokhoz rendeli. Az alábbiak alapján ellenőrizheti, hogy a költségek megfelelően vannak-e hozzárendelve a célokhoz.

### <a name="view-cost-allocation-for-a-subscription"></a>Előfizetés költségfelosztásának megtekintése

A költségfelosztási szabály hatását a költségelemzésben tekintheti meg. Az Azure Portalon lépjen az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldalra. Válasszon ki a listáról egy olyan előfizetést, amely egy aktív költségfelosztási szabály célja. Ezután válassza ki a **Költségelemzés** menüpontot. A költségelemzésben válassza a **Csoportosítás**, majd a **Költségfelosztás** lehetőséget. Az eredményül kapott nézet az előfizetés költségeinek gyors lebontását mutatja. Az előfizetéshez rendelt költségek is megjelennek az alábbi ábrán látható módon.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="A költséglebontást szemléltető példa" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Erőforráscsoport költségfelosztásának megtekintése

Hasonló módszerrel ellenőrizheti a költségfelosztási szabály hatását egy erőforráscsoport esetén. Az Azure Portalon lépjen az [Erőforráscsoportok](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) oldalra. Válasszon ki a listáról egy olyan erőforráscsoportot, amely egy aktív költségfelosztási szabály célja. Ezután válassza ki a **Költségelemzés** menüpontot. A költségelemzésben válassza a **Csoportosítás**, majd a **Költségfelosztás** lehetőséget. A nézet az erőforráscsoport költségeinek gyors lebontását mutatja. Az erőforráscsoporthoz rendelt költségek is megjelennek.

### <a name="view-cost-allocation-for-tags"></a>Címkék költségfelosztásának megtekintése

Az Azure Portalon keresse meg a **Költségkezelés + Számlázás** > **Cost Management** > **Költségelemzés** lehetőséget. A költségelemzésben válassza a **Szűrő hozzáadása** lehetőséget. Válassza a **Címke** lehetőséget, és válassza ki a címkekulcsot és címkeértékeket, amelyekhez költséget rendelt.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="A címkézett elemek költségeit bemutató példa" lightbox="./media/allocate-costs/tagged-costs.png" :::

Íme egy videó, amely bemutatja, hogyan hozhat létre egy Cost foglalási szabályt.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Meglévő költségfelosztási szabály szerkesztése

A költségfelosztási szabályok szerkeszthetők, hogy megváltoztassa a forrásukat vagy a céljukat, vagy frissítse az előre megadott százalékos arányokat a számítási, tárolási vagy hálózati lehetőségekhez. A szabályok szerkesztésekor ugyanúgy járjon el, ahogy a létrehozásukkor. A meglévő szabályok módosításának újbóli feldolgozása akár két órát is igénybe vehet.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Az alábbi szakaszok a költségfelosztással kapcsolatos gyakori kérdéseket tartalmazzák.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Milyen korlátozásokkal működik a költségfelosztási funkció a nyilvános előzetes verzióban?
<a name="limitations"></a>

A költségfelosztási funkció jelenleg a költségelemzésben, a költségvetésekben és az előrejelzési nézetekben támogatott a Cost Managementben. A felosztott költségek megjelennek az előfizetések listáján és az Előfizetések áttekintése oldalon is.

A költségfelosztási funkció nyilvános előzetes verziója jelenleg nem támogatja a következőket:

- Ütemezett [exportálások](tutorial-export-acm-data.md)
- A [Usage Details API](/rest/api/consumption/usagedetails/list) által közzétett adatok
- Számlázási előfizetések terület
- [Cost Management Power BI-alkalmazás](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop-összekötő](/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>A költségek szerepelnek a költségvetésekben és az előrejelzési nézetekben?
<a name="budgets-forecast"></a>

Igen. A költségvetések és az előrejelzések támogatják a költségfelosztást, és szerepelnek bennük a felosztott költségek. A költségvetési és előrejelzési nézetekben megjelennek a költségfelosztási szabályoknak megfelelően konfigurált, hozzájuk rendelt költségek.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>Mi történik egy költségfelosztási szabály törlésekor?
<a name="delete-rule"></a>

Egy költségfelosztási szabály törlésekor az aktuális számlázási hónap minden, a célokhoz hozzárendelt, le nem zárt költsége törlődik. Ha a költségfelosztási szabály több hónapon át érvényben volt, a korábbi hónapok költségfelosztási előzményadatai a költségfelosztási szabály beállításainak megfelelő állapotban maradnak.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Miért szükséges egy regisztrációs rendszergazda vagy számlázásifiók-rendszergazda a költségfelosztási szabályok létrehozásához?
<a name="why-admin"></a>

A költségfelosztási szabályok létrehozása a beléptetési hatókörben (Nagyvállalati Szerződés esetén) vagy a számlázási fiók hatókörében (Microsoft Ügyfélszerződés esetén) történik. Ezekben a hatókörökben csak számlázási rendszergazdai jogosultsággal lehet módosításokat végrehajtani.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>A források és célok száma miért van szabályonként 25-re korlátozva?
<a name="source-target-rule-limit"></a>

Ez egy előzetes verziójú korlátozás a költségfelosztási funkció jó teljesítményének és skálázhatóságának biztosítása érdekében. A korlátokat valószínűleg növelni fogjuk vagy eltávolítjuk, amikor a költségfelosztási funkció az általánosan elérhető (GA) verzióra tér át.

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>Mi történik, ha a költségfelosztási szabályok (források/célok) között átfedés van?
<a name="rule-overlap"></a>

Nem javasolt olyan szabályokat létrehozni, amelyek forrásai vagy céljai átfedik egymást. A költségfelosztási szabályok alkalmazása létrehozásuk időpontja szerint történik, vagyis ha a költségfelosztási szabályok között átfedés van, a legkorábban létrehozott felosztási szabály élvez elsőbbséget.

## <a name="next-steps"></a>Következő lépések

- Felosztási szabályok létrehozása vagy frissítése a [Cost allocation Rest API](/rest/api/cost-management/costallocationrules) használatával
- További információ [a felhővel kapcsolatos befektetés optimalizálásáról az Azure Cost Management használatával](cost-mgt-best-practices.md)