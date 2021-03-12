---
title: Oktatóanyag – Azure-költségvetések létrehozása és kezelése
description: Ez az oktatóanyag segítséget nyújt a felhasznált Azure-szolgáltatások költségeinek megtervezésében és kiszámításában.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012446"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Azure-költségvetések létrehozása és kezelése

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. Segítséget nyújtanak mások tájékoztatásához a kiadásaikról a költségek proaktív módon történő kezelése, és a kiadások adott időszakban való alakulásának monitorozása érdekében. A riasztásokat a tényleges költség vagy az előre jelzett költség alapján állíthatja be, hogy a költségek a szervezeten belüli kiadások körén belül legyenek. A létrehozott költségvetési küszöbértékek túllépése esetén a rendszer csak értesítéseket aktivál. A túllépés egyik erőforrásra sincs hatással, és a felhasználást sem állítja le a rendszer. A költségek elemzése során a költségvetésekkel összehasonlíthatja és nyomon követheti a kiadásokat.

A költségek és a használati adatok jellemzően 8–24 órán belül érhetők el, a költségvetések kiértékelése pedig 24 óránként történik. Fontos, hogy tisztában legyen a [költség- és használati adatok frissítésének](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention) jellemzőivel. Ha elér egy költségvetési küszöbértéket, az e-mail-értesítések általában az értékelést követő egy órán belül megérkeznek.

A költségvetések egy adott időszak (hónap, negyedév vagy év) lejártával automatikusan alaphelyzetbe állnak ugyanazzal a költségvetési összeggel, ha jövőbeni lejárati dátumot választ. Mivel a költségvetések ugyanazzal az összeggel állnak alaphelyzetbe, külön költségvetést kell létrehoznia, ha a jövőbeli időszakokban a költségvetés aktuális összegei változnak. Ha egy költségkeret lejár, automatikusan törlődik.

Az oktatóanyagban található példák bemutatják, hogyan hozhat létre és szerkeszthet költségvetést egy Azure Nagyvállalati Szerződéshez (EA) tartozó előfizetés esetén.

A [költségvetések előfizetésekre az Azure Portal használatával történő alkalmazását](https://www.youtube.com/watch?v=UrkHiUx19Po) ismertető videóban bemutatjuk, hogyan hozhat létre költségvetéseket az Azure-ban a kiadások monitorozásához. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetés létrehozása és szerkesztése a PowerShell-lel
> * Költségvetés létrehozása Azure Resource Manager-sablonnal

## <a name="prerequisites"></a>Előfeltételek

A költségvetések az Azure-fiókok és -hatókörök következő típusai esetén támogatottak:

- Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hatókörök
    - Felügyeleti csoportok
    - Előfizetés
- A Nagyvállalati Szerződés hatókörei
    - Számlázási fiók
    - Részleg
    - Regisztrációs fiók
- Egyedi szerződések
    - Számlázási fiók
- A Microsoft-ügyfélszerződés hatókörei
    - Számlázási fiók
    - Számlázási profil
    - Számlázási szakasz
    - Ügyfél
- AWS-hatókörök
    - Külső fiók
    - Külső előfizetés


A költségvetés megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

Új előfizetés esetén nem hozhat létre azonnal költségvetést, és a Cost Management további szolgáltatásait sem használhatja azonnal. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

Az Azure EA-előfizetések esetében a költségvetések megtekintéséhez olvasási jogosultsággal kell rendelkeznie. A költségvetések létrehozásához és kezeléséhez közreműködői jogosultsággal kell rendelkeznie.

Az alábbi Azure-engedélyek, vagy -hatókörök, támogatottak az egyes előfizetésekben a költségvetések felhasználó vagy csoport általi létrehozásához. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő és Cost Management-közreműködő – A saját költségvetéseit hozhatja létre, módosíthatja vagy törölheti. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és Cost Management-olvasó – Megtekintheti azokat a költségvetéseket, amelyekhez engedéllyel rendelkezik.

További információ a Cost Management adataihoz való hozzáférés hozzárendeléséről: [A Cost Management adataihoz való hozzáférés hozzárendelése](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Költségvetés létrehozása az Azure Portalon

Azure-előfizetési költségvetést havi, negyedéves vagy éves időszakra hozhat létre.

Ha költségvetést szeretne létrehozni vagy megtekinteni, nyisson meg egy hatókört a Azure Portalban, és válassza a **költségvetés** lehetőséget a menüben. Lépjen például az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségvetések** menüpontot. A **Hatókör** elemmel másik hatókörre, például felügyeleti csoportra válthat a Költségvetésekben. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

A költségvetések létrehozása után egy egyszerű nézetben tekintheti meg a hozzájuk tartozó jelenlegi kiadásait.

Válassza a **Hozzáadás** lehetőséget.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="A már létrehozott költségvetések listáját megjelenítő Screenthost." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

Győződjön meg róla, hogy a **Költségvetés létrehozása** ablakban látható hatókör helyes. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy adott költségekre, például az előfizetésben lévő erőforráscsoportokkal, illetve egy szolgáltatással, például a virtuális gépekkel járó költségekre vonatkozóan hozzon létre költségvetést. A költségelemzések során használt szűrőket alkalmazhatja a költségvetésekre is.

A hatókör és a szűrők azonosítása után írja be a költségvetés nevét. Ezt követően válassza ki a havi, negyedéves vagy éves alaphelyzetbe állítási időtartamot a költségvetéshez. Az alaphelyzetbe állítási időszak meghatározza a költségvetés által elemzett időintervallumot. A költségvetés által kiértékelt költség minden új periódus elején nulláról kezdődik. A negyedéves költségvetés ugyanúgy működik, mint a havi költségvetés. A különbség az, hogy a negyedévre vonatkozó költségvetési összeg egyenlően oszlik meg a negyedév három hónapja között. Az éves költségvetési összeg a naptári év mind a 12 hónapja között oszlik meg egyenlően.

Ha használatalapú, MSDN- vagy Visual Studio-előfizetéssel rendelkezik, a számlázási időszak nem feltétlenül esik egybe a naptári hónapokkal. Az ilyen típusú előfizetésekhez és erőforráscsoportokhoz olyan költségvetést is létrehozhat, amely a számlázási időszakhoz vagy a naptári hónapokhoz igazodik. Ha a számlázási időszakhoz igazított költségvetést szeretne létrehozni, a **Számlázási hónap**, **Számlázási negyedév** vagy **Számlázási év** alaphelyzetbe állítási időtartamot válassza. Ha a naptári hónaphoz igazított költségvetést szeretne létrehozni, a **Havi**, **Negyedéves** vagy **Éves** alaphelyzetbe állítási időtartamot válassza.

Ezután határozza meg a lejárati dátumot, amikortól a költségvetés érvénytelenné válik, és leáll a költségek kiértékelése.

A költségvetésben eddig kiválasztott mezők alapján egy grafikon jelenik meg, amely segítséget nyújt a költségvetéshez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban potenciálisan felmerülő legmagasabb becsült költségen alapul. Igény esetén módosíthatja a költségvetési összeget.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Képernyőfelvétel a havi költségadatok költségvetésének létrehozásáról." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

A költségvetési összeg konfigurálása után a **tovább** gombra kattintva konfigurálja a költségvetési riasztásokat a tényleges költségek és az előre jelzett költségvetési riasztások számára.

## <a name="configure-actual-costs-budget-alerts"></a>A tényleges költségek költségvetési riasztások konfigurálása

A költségvetésekhez meg kell adnia legalább egy költségküszöbértéket (a költségvetés %-ában) és egy hozzá tartozó e-mail-címet. Igény szerint akár öt küszöbértéket és öt e-mail-címet is megadhat egyetlen költségvetéshez. Ha elér egy költségvetési küszöbértéket, az e-mail-értesítések általában az értékelést követő egy órán belül megérkeznek. A tényleges költségekkel kapcsolatos költségvetési riasztások a konfigurált költségvetési küszöbértékekkel kapcsolatban felmerülő tényleges költségekhez jönnek létre.

## <a name="configure-forecasted-budget-alerts"></a>Előre jelzett költségvetési riasztások konfigurálása

Az előre jelzett riasztások speciális értesítést biztosítanak arról, hogy a kiadások várhatóan túllépik a költségvetést. A riasztások az [előre jelzett költségeket](quick-acm-cost-analysis.md#understand-forecast)használják. A riasztások akkor jönnek létre, ha az előrejelzett költségszámítás meghaladja a beállított küszöbértéket. Megadhat egy előre jelzett küszöbértéket (a költségvetés%-ában). Ha egy előrejelzett költségvetési küszöbérték teljesül, az értesítések küldése általában egy órán belül történik a kiértékelés során.

A tényleges és előre jelzett költségadatok beállítása közötti váltáshoz használja a mezőt a `Type` riasztás konfigurálásakor a következő képen látható módon.

Ha e-maileket szeretne kapni, vegye fel az azure-noreply@microsoft.com címet a jóváhagyott küldők listájára, hogy az e-mailek ne kerüljenek a levélszemét mappájába. Az értesítésekkel kapcsolatban a [költségriasztások használatát](./cost-mgt-alerts-monitor-usage-spending.md) ismertető cikkben tekinthet meg további információt.

A következő példában egy e-mail-riasztás jön létre, amikor eléri a költségvetés 90%-át. Ha a Budgets API-val hoz létre költségvetést, szerepköröket is rendelhet adott személyekhez, hogy riasztásokat kaphassanak. A szerepkörök személyekhez rendelése nem támogatott az Azure Portalon. További információ a Budgets API-ról: [Budgets API](/rest/api/consumption/budgets). Ha más nyelven szeretné elküldeni az e-mailes riasztásokat, tekintse meg a következő témakört: [Támogatott területi beállítások a költségvetési riasztások e-mailjeihez](manage-automation.md#supported-locales-for-budget-alert-emails).

A riasztási korlátok a megadott költségvetési küszöbérték 0,01%-a 1000%-át támogatják.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="A riasztási feltételeket megjelenítő képernyőkép." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Miután létrehozta a költségvetést, a rendszer megjeleníti annak költségelemzését. A [költségek és kiadások elemzésének](./quick-acm-cost-analysis.md) egyik első lépése a költségvetés és a kiadási tendenciák összehasonlítása.

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Képernyőkép a Cost Analysis szolgáltatásban bemutatott költségekkel rendelkező költségvetésről." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

Az előző példában létrehozott egy költségvetést egy előfizetéshez. Költségvetést erőforráscsoportokhoz is létrehozhat. Ha erőforráscsoporthoz szeretne költségvetést létrehozni, lépjen a **Költségkezelés + számlázás** &gt; **Előfizetések** területre &gt; válasszon ki egy előfizetést > **Erőforráscsoportok** > válasszon ki egy erőforráscsoportot > **Költségvetések** > végül a **Hozzáadás** lehetőséggel adjon hozzá egy költségvetést.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Költségvetés létrehozása az egyesített Azure- és AWS-költségekhez

Az Azure és az AWS egy csoportba foglalásához rendeljen hozzá egy felügyeleti csoportot az összekötőhöz a konszolidált és összekapcsolt fiókokkal együtt. Rendelje hozzá Azure-előfizetését ugyanahhoz a felügyeleti csoporthoz. Ezután hozzon létre egy költségvetést az egyesített költségek számára.

1. A Cost Managementben válassza a **Költségvetések** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Hatókör módosítása** lehetőséget, majd válassza ki a felügyeleti csoportot.
1. Végezze el a költségvetés létrehozásának további lépéseit.

## <a name="costs-in-budget-evaluations"></a>Költségek a költségvetések kiértékelésében

A költségvetési költségek kiértékelései már a fenntartott példányok és vásárlások adatait is tartalmazzák. Ha a díjakat fizetnie kell, riasztásokat kaphat, amikor a díjakat a rendszer beépíti a kiértékelésekbe. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) annak ellenőrzéséhez, hogy a költségvetési küszöbértékek megfelelően vannak-e konfigurálva az új költségekhez. Az Azure-ban számlázott díjak nem változnak. A költségvetések kiértékelése a költségek teljesebb köre alapján történik. Ha a díjakat nem kell fizetnie, a költségvetés működése változatlan marad.

Ha úgy szeretné szűrni az új költségeket, hogy a költségvetések kiértékelése csak az első fél Azure-használati díjai alapján történjen, adja hozzá a következő szűrőket a költségvetéshez:

- Közzétevő típusa: Azure
- Díj típusa: Használat

A költségvetési költségek kiértékelése a tényleges költségek alapján történik. Nem tartalmazzák az amortizációt. A költségvetésekben elérhető szűrési lehetőségekkel kapcsolatban a [Csoportosítási és szűrési lehetőségek ismertetése](group-filter.md) című szakaszban tekinthet meg további információt.

## <a name="trigger-an-action-group"></a>Műveletcsoport aktiválása

Amikor költségvetést hoz létre vagy módosít egy előfizetés vagy erőforráscsoport hatókörére vonatkozóan, konfigurálhatja úgy, hogy meghívjanak egy műveletcsoportot. A műveletcsoport különböző műveleteket hajthat végre a költségvetési küszöbérték elérésekor. 

A műveletcsoportok jelenleg csak az előfizetés és erőforráscsoport hatókör esetén támogatottak. A műveleti csoportok létrehozásával kapcsolatos további információkért lásd: [alapszintű műveleti csoport beállításainak konfigurálása](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

A költségvetés-alapú automatizálás műveletcsoportok segítségével történő használatáról a [Költségek kezelése az Azure Budgetsszel](../manage/cost-management-budget-scenario.md) című cikkben tekinthet meg további információt.

A műveleti csoportok létrehozásához vagy frissítéséhez válassza a **csoport kezelése műveletet** a költségvetés létrehozásakor vagy szerkesztésekor.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Képernyőkép, amely bemutatja, hogyan lehet költségvetést létrehozni a felügyeleti műveleti csoportok megjelenítéséhez." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Ezután válassza a **Műveletcsoport hozzáadása** elemet a műveletcsoport létrehozásához.

A költségvetés műveletcsoportokkal való integrációja csak olyan műveletcsoportok esetén működik, amelyeken le van tiltva a gyakori riasztási séma. A séma letiltásával kapcsolatban a [gyakori riasztási séma engedélyezését](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) ismertető szakaszban tekinthet meg további információt.

## <a name="create-and-edit-budgets-with-powershell"></a>Költségvetés létrehozása és szerkesztése a PowerShell-lel

Ha Ön nagyvállalati ügyfél, az Azure PowerShell modul használatával programozott módon hozhat létre és szerkeszthet költségvetést. Az Azure PowerShell legújabb verziójának letöltéséhez futtassa az alábbi parancsot:

```azurepowershell-interactive
install-module -name Az
```

Az alábbi példaparancsok költségvetést hoznak létre.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Költségvetés létrehozása Azure Resource Manager-sablonnal

Azure Resource Manager-sablonok használatával költségvetést hozhat létre. A sablon használatát lásd: [Költségvetés létrehozása Azure Resource Manager-sablonnal](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha korábban létrehozott egy költségvetést, de már nincs rá szüksége, tekintse meg a részleteit, majd törölje.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetés létrehozása és szerkesztése a PowerShell-lel
> * Költségvetés létrehozása Azure Resource Manager-sablonnal

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhatja létre a Cost Management-adatok ismétlődő exportálását.

> [!div class="nextstepaction"]
> [Exportált adatok létrehozása és kezelése](tutorial-export-acm-data.md)