---
title: 'Oktatóanyag: a forgatókönyvek automatizálási szabályokkal való használata az Azure Sentinelben'
description: Ez az oktatóanyag segítséget nyújt a forgatókönyvek és az Azure Sentinel automatizálási szabályainak használatához az incidensek megválaszolásának automatizálása és a biztonsági fenyegetések elhárítása érdekében.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600634"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Oktatóanyag: a forgatókönyvek automatizálási szabályokkal való használata az Azure Sentinelben

Ez az oktatóanyag bemutatja, hogyan használhatók a forgatókönyvek az automatizálási szabályokkal együtt az incidensek megoldásának automatizálása és az Azure Sentinel által észlelt biztonsági fenyegetések elhárítása érdekében. Az oktatóanyag befejezése után a következőket teheti:

> [!div class="checklist"]
>
> * Automation-szabály létrehozása
> * Forgatókönyv létrehozása
> * Műveletek hozzáadása egy forgatókönyvhöz
> * Forgatókönyv csatolása egy Automation-szabályhoz vagy egy elemzési szabályhoz a fenyegetési válasz automatizálása érdekében

## <a name="what-are-automation-rules-and-playbooks"></a>Mik azok az Automation-szabályok és a forgatókönyvek?

Az Automation-szabályok segítenek az incidensek osztályozásában az Azure Sentinelben. Felhasználhatja az incidensek automatikus hozzárendelését a megfelelő munkatárshoz, a zajos incidensek vagy az ismert téves riasztások, a súlyosságuk módosítására és a címkék hozzáadására. Emellett a mechanizmus, amellyel az incidensekre adott válaszként futtathat forgatókönyveket.

A forgatókönyvek olyan eljárások gyűjteményei, amelyek az Azure Sentinelből egy riasztásra vagy incidensre reagálva futtathatók. A forgatókönyvek segítségével automatizálhatja és összehangolhatja a választ, és beállíthatja, hogy a rendszer automatikusan fusson, ha adott riasztások vagy incidensek jönnek létre, egy elemzési szabályhoz vagy egy automatizálási szabályhoz való csatlakozással. Igény szerint manuálisan is futtatható.

Az Azure Sentinelben a forgatókönyvek az [Azure Logic apps](../logic-apps/logic-apps-overview.md)-ben létrehozott munkafolyamatokon alapulnak, ami azt jelenti, hogy a Logic apps összes energiagazdálkodási, testreszabható és beépített sablonjait kapja. Az egyes forgatókönyvek azért jönnek létre az adott előfizetéshez, amelyhez tartozik, de a forgatókönyvek megjelenítésével a kiválasztott előfizetésekben elérhető összes **forgatókönyv megjelenik.**

> [!NOTE]
> Mivel a forgatókönyvek a Azure Logic Apps használatát teszik elérhetővé, további díjakat is igényelhet. További részletekért látogasson el a [Azure Logic apps](https://azure.microsoft.com/pricing/details/logic-apps/) díjszabási oldalára.

Ha például le szeretné állítani a potenciálisan sérült felhasználókat a hálózatról való mozgásra és az információk ellopására, létrehozhat egy automatizált, többtényezős választ a feltört felhasználókat észlelő szabályok által generált incidensekre. Első lépésként hozzon létre egy forgatókönyvet, amely a következő műveleteket végzi el:

1. Ha egy Automation-szabály meghív egy incidenst, a forgatókönyv egy jegyet nyit meg a [ServiceNow](/connectors/service-now/) vagy bármely más IT-jegyben.

1. Üzenetet küld a [Microsoft Teams](/connectors/teams/) vagy [Slack](/connectors/slack/) biztonsági operatív csatornájának, hogy a biztonsági elemzők tisztában legyenek az incidenssel.

1. Emellett az incidensben lévő összes információt egy e-mail-üzenetben küldi el a vezető hálózati rendszergazdai és biztonsági rendszergazdának. Az e-mail-üzenet tartalmazni fogja a felhasználói választógombok **blokkolását** és **mellőzését** .

1. A forgatókönyv megvárja, amíg a rendszer választ kap a rendszergazdáktól, majd folytatja a következő lépésekkel.

1. Ha a rendszergazdák a **Letiltás** lehetőséget választják, egy parancsot küld az Azure ad-nek, hogy letiltsa a felhasználót, és az egyiket a tűzfalra, hogy blokkolja az IP-címet.

1. Ha a rendszergazdák úgy döntenek, hogy **figyelmen kívül hagyják**, a forgatókönyv bezárja az eseményt az Azure sentinelben, a ServiceNow pedig a jegyet.

A forgatókönyv elindításához létre kell hoznia egy Automation-szabályt, amely az incidensek létrehozásakor fut. Ez a szabály a következő lépéseket hajtja végre:

1. A szabály az incidens állapotát **aktívra** módosítja.

1. Az incidenst az ilyen típusú incidens kezelésével foglalkozó elemzőhöz rendeli.

1. Hozzáadja a "feltört felhasználó" címkét.

1. Végül meghívja az imént létrehozott forgatókönyvet. ([Ehhez a lépéshez speciális engedélyek szükségesek](automate-responses-with-playbooks.md#incident-creation-automated-response).)

A forgatókönyvek az incidensekre adott válaszként automatikusan futtathatók olyan automatizálási szabályok létrehozásával, amelyek a fenti példában látható módon meghívják a forgatókönyveket. A riasztásokra adott válaszként automatikusan is futtathatók, ha közli, hogy az elemzési szabály automatikusan futtat egy vagy több forgatókönyvet a riasztás létrehozásakor. 

Azt is megteheti, hogy a kiválasztott riasztásra adott válaszként manuálisan szeretné futtatni a forgatókönyveket.

Teljesebb és részletes bevezetést kaphat a veszélyforrások [automatizálására az Automation-szabályokkal](automate-incident-handling-with-automation-rules.md) [és az Azure](automate-responses-with-playbooks.md) Sentinel használatával.

> [!IMPORTANT]
>
> - Az **Automation-szabályok** és az **incidensek triggerének** használata a forgatókönyvekhez jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="create-a-playbook"></a>Forgatókönyv létrehozása

Kövesse az alábbi lépéseket egy új forgatókönyv létrehozásához az Azure Sentinelben:

### <a name="prepare-the-playbook-and-logic-app"></a>A forgatókönyv és a logikai alkalmazás előkészítése

1. Az **Azure Sentinel** navigációs menüjében válassza az **automatizálás** lehetőséget.

1. A felső menüben válassza a **Létrehozás** és **új forgatókönyv hozzáadása** lehetőséget.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Új forgatókönyv hozzáadása":::

    Megnyílik egy új böngésző lap, és megnyithatja a **logikai alkalmazás létrehozása** varázslót.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Logikai alkalmazás létrehozása":::

1. Adja meg az **előfizetését** és az **erőforráscsoportot**, és adjon meg egy nevet a **logikai alkalmazás neve** alatt.

1. A **régió** mezőben válassza ki azt az Azure-régiót, ahol a logikai alkalmazás adatait tárolni szeretné.

1. Ha a forgatókönyv tevékenységét diagnosztikai célokra szeretné figyelni, jelölje be a **log Analytics engedélyezése** jelölőnégyzetet, és adja meg a **log Analytics munkaterület** nevét.

1. Ha címkéket szeretne alkalmazni a forgatókönyvre, kattintson a **Tovább gombra: címkék >** (nem kapcsolódik az Automation-szabályok által alkalmazott címkékhez. [További információ a címkékről](../azure-resource-manager/management/tag-resources.md). Ellenkező esetben kattintson a **felülvizsgálat + létrehozás** gombra. Erősítse meg a megadott adatokat, majd kattintson a **Létrehozás** gombra.

1. A forgatókönyv létrehozása és üzembe helyezése közben (ez eltarthat néhány percig) a **Microsoft. EmptyWorkflow** nevű képernyő jelenik meg. Amikor megjelenik az "üzembe helyezés befejezése" üzenet, kattintson az **erőforrás** megnyitása lehetőségre.

1. Ekkor megnyílik az új forgatókönyv [Logic apps Designer](../logic-apps/logic-apps-overview.md), ahol megkezdheti a munkafolyamat megtervezését. Egy rövid bevezető videóval és néhány gyakran használt logikai alkalmazás-eseményindítóval és-sablonnal láthat egy képernyőt. [További](../logic-apps/logic-apps-create-logic-apps-from-templates.md) információ a forgatókönyvek létrehozásáról a Logic apps.

1. Válassza ki az **üres logikai alkalmazás** sablonját.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Logic Apps Designer sablon gyűjteménye":::

### <a name="choose-the-trigger"></a>Válassza ki az triggert

Minden forgatókönyvnek egy triggerrel kell kezdődnie. Az trigger meghatározza azt a műveletet, amely elindítja a forgatókönyvet, valamint azt a sémát, amelyet a forgatókönyv el fog fogadni.

1. Keresse meg az Azure Sentinelt a keresősáv alatt. Válassza az **Azure Sentinel** lehetőséget, ha megjelenik az eredmények között.

1. Az eredményül kapott **Eseményindítók** lapon megjelenik az Azure Sentinel által kínált két eseményindító:
    - Ha egy Azure Sentinel-riasztásra adott válasz aktiválódik
    - Ha az Azure Sentinel incidenslétrehozási szabálya aktiválódik

   Válassza ki azt az triggert, amely megfelel a létrehozandó forgatókönyv típusának.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Válasszon egy triggert a forgatókönyvhöz":::

### <a name="add-actions"></a>Műveletek hozzáadása

Most megadhatja, hogy mi történjen a forgatókönyv meghívásakor. Az **új lépés** kiválasztásával műveleteket, logikai feltételeket, hurkokat vagy váltási eseti feltételeket adhat hozzá. Ez a kijelölés egy új keretet nyit meg a tervezőben, ahol kiválaszthat egy rendszer vagy egy alkalmazást, amellyel kapcsolatba lehet lépni, vagy feltételt állíthat be. Adja meg a rendszer vagy alkalmazás nevét a keret tetején található keresősáv alatt, majd válasszon az elérhető eredmények közül.

A fenti lépések mindegyikében a bármelyik mezőre kattintva egy panel jelenik meg két menüvel: **dinamikus tartalom** és **kifejezés**. A **dinamikus tartalom** menüben hozzáadhat hivatkozásokat az adott forgatókönyvhöz továbbított riasztás vagy incidens attribútumaihoz, beleértve az összes érintett entitás értékeit és attribútumait is. A **kifejezés** menüben a függvények nagy könyvtára közül választhat, hogy további logikát adjon a lépésekhez.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Logikaialkalmazás-tervező":::

Ez a képernyőkép azokat a műveleteket és kikötéseket mutatja be, amelyeket a jelen dokumentum elején, a példánál ismertetett forgatókönyv létrehozásával fog felvenni. Az egyetlen különbség, hogy az itt látható forgatókönyvben az **incidens** trigger helyett a **riasztási triggert** használjuk. Ez azt jelenti, hogy ezt a forgatókönyvet közvetlenül egy elemzési szabályból hívja meg, nem pedig egy Automation-szabályból. Az alábbiakban a forgatókönyvek meghívásának mindkét módját ismertetjük.

## <a name="automate-threat-responses"></a>Fenyegetési válaszok automatizálása

Létrehozta a forgatókönyvét, és definiálta az triggert, megadta a feltételeket, és előírta a végrehajtandó műveleteket, valamint az általa előállított kimeneteket. Most meg kell határoznia azokat a feltételeket, amelyeken futni fog, és beállítja az automatizálási mechanizmust, amely a feltételek teljesülése esetén fog futni.

### <a name="respond-to-incidents"></a>Reagálás incidensekre

Az **incidensek** megválaszolásához egy olyan [automatizálási szabályt](automate-incident-handling-with-automation-rules.md) kell létrehoznia, amely az incidens létrehozásakor fog futni, és ez a forgatókönyv a forgatókönyvet fogja hívni.

Automatizálási szabály létrehozása:

1. Az Azure Sentinel navigációs menüjének **automatizálás** paneljén válassza a **Létrehozás** lehetőséget a felső menüben, majd **vegyen fel új szabályt**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Új szabály hozzáadása":::

1. Megnyílik az **új automatizálási szabály létrehozása** panel. Adja meg a szabály nevét.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Automation-szabály létrehozása":::

1. Ha azt szeretné, hogy az Automation-szabály csak bizonyos elemzési szabályokon legyen érvényes, adja meg, hogy mely eszközökre van szükség az **IF Analytics-szabály nevének** feltételének módosításával.

1. Adja meg az Automation-szabály aktiválásának függőben lévő egyéb feltételeket. Kattintson a **feltétel hozzáadása** elemre, és válassza a feltételek lehetőséget a legördülő listából. A feltételek listáját a riasztás részletei és az entitás-azonosító mezők töltik fel.

1. Válassza ki azokat a műveleteket, amelyeket az Automation-szabálynak figyelembe kell vennie. Az elérhető műveletek közé tartozik a **tulajdonos hozzárendelésének**, az **Állapot módosítása**, a **Súlyosság módosítása**, a **Címkék hozzáadása** és a forgatókönyv **futtatása**. Annyi műveletet adhat hozzá, amennyit csak szeretne.

1. Ha egy **futtatási forgatókönyv futtatása** műveletet ad hozzá, a rendszer kérni fogja, hogy válasszon az elérhető forgatókönyvek legördülő listájából. Csak az **incidens triggerrel** kezdődő forgatókönyvek futtathatók az Automation-szabályokból, így csak a listában fognak megjelenni.

    > [!IMPORTANT]
    > Az Azure Sentinelnek explicit engedélyekkel kell rendelkeznie a forgatókönyvek automatizálási szabályokból való futtatásához. Ha egy forgatókönyv "szürkén jelenik meg" a legördülő listában, az azt jelenti, hogy a Sentinel nem rendelkezik engedéllyel a forgatókönyvhöz tartozó erőforráscsoporthoz. Az engedélyek hozzárendeléséhez kattintson az **ötletekbõl-engedélyek kezelése** hivatkozásra.
    > A megnyíló **kezelés engedélyek** paneljén jelölje be a futtatni kívánt forgatókönyveket tartalmazó erőforráscsoportok jelölőnégyzetét, majd kattintson az **alkalmaz** gombra.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Engedélyek kezelése":::
    > - Önnek rendelkeznie kell **tulajdonosi** engedélyekkel minden olyan erőforráscsoport esetében, amelyhez Azure Sentinel-engedélyeket kíván biztosítani, és a **logikai alkalmazás közreműködői** szerepkört minden olyan erőforráscsoport esetében meg kell adnia, amely a futtatni kívánt forgatókönyveket tartalmazza.
    > - Több-bérlős telepítés esetén, ha a futtatni kívánt forgatókönyv egy másik bérlőn található, meg kell adnia az Azure Sentinel engedélyt a forgatókönyvnek a forgatókönyv bérlőben való futtatásához.
    >    1. A forgatókönyvek bérlője Azure Sentinel navigációs menüjében válassza a **Beállítások** lehetőséget.
    >    1. A **Beállítások** panelen válassza a **Beállítások** lapot, majd a forgatókönyv- **engedélyek** Expander elemet.
    >    1. Kattintson az **engedélyek konfigurálása** gombra a fent említett **kezelés engedélyek** panel megnyitásához, és folytassa az itt leírtak szerint.

1. Állítsa be az Automation-szabály lejárati dátumát, ha azt szeretné, hogy egy legyen.

1. Adjon meg egy számot **, amely meghatározza, hogy az** automatizálási szabályok milyen sorrendben fussanak.

1. Kattintson az **Alkalmaz** gombra. Ennyi az egész!

Ismerje meg az Automation-szabályok létrehozásának [egyéb módszereit](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) .

### <a name="respond-to-alerts"></a>Reagálás riasztásokra

Egy adott riasztáshoz egy **elemzési szabály** létrehozásával vagy egy meglévő, a riasztás generálásakor futtatott, illetve egy már létező, az [elemzési szabály varázsló](tutorial-detect-threats-custom.md)automatikus válaszként való kiválasztásával reagálhat a **riasztásokra** .

1. Az Azure Sentinel navigációs menüjének **elemzés** paneljén válassza ki azt az elemzési szabályt, amelynek a válaszát automatizálni szeretné, majd a részletek ablaktáblán kattintson a **Szerkesztés** elemre.

1. Az **elemzési szabály varázsló meglévő szabály szerkesztése** lapján válassza az **automatikus válasz** lapot.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Automatikus válasz lap":::

1. Válassza ki a forgatókönyvét a legördülő listából. Több forgatókönyvet is kiválaszthat, de csak a **riasztási triggert** használó forgatókönyvek lesznek elérhetők.

1. A **felülvizsgálat és létrehozás** lapon válassza a **Mentés** lehetőséget.

## <a name="run-a-playbook-on-demand"></a>Forgatókönyvek igény szerinti futtatása

Igény szerint futtathatja a forgatókönyveket is.

 > [!NOTE]
 > Igény szerint csak a **riasztási triggert** használó forgatókönyveket lehet futtatni.

Igény szerinti forgatókönyv futtatása:

1. Az **incidensek** lapon válasszon ki egy incidenst, és kattintson a **teljes részletek megtekintése** elemre.

2. A **riasztások** lapon kattintson arra a riasztásra, amelyen futtatni szeretné a forgatókönyvét, és görgessen végig a jobb oldalon, majd kattintson a forgatókönyvek **megtekintése** lehetőségre, és válassza ki az előfizetésben elérhető forgatókönyvek listájából **futtatandó** forgatókönyvet.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan használhatók az Azure Sentinelben lévő forgatókönyvek és automatizálási szabályok a fenyegetések megválaszolására. 
- Ismerje meg, hogyan lehet [proaktív módon vadászni a fenyegetésekről az](hunting.md) Azure Sentinel használatával.
