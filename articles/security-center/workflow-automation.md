---
title: Munkafolyamat-automatizálás a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és automatizálhat munkafolyamatokat Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096173"
---
# <a name="automate-responses-to-security-center-triggers"></a>Security Center eseményindítóra adott válaszok automatizálása

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek megválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érintett felek értesítését, a módosítási felügyeleti folyamat indítását és az adott szervizelési lépések alkalmazását. A biztonsági szakértők azt ajánlják, hogy az eljárások több lépésének automatizálására legyen lehetőség. Az Automation csökkenti a terhelést. Emellett javíthatja a biztonságot, mivel biztosítja, hogy a folyamat lépései gyorsan, következetesen és az előre meghatározott követelményeknek megfelelően történjenek.

Ez a cikk a Azure Security Center munkafolyamat-automatizálási szolgáltatását ismerteti. Ez a funkció a biztonsági riasztások, a javaslatok és a jogszabályi megfelelőség változásainak Logic Appsét indítja el. Előfordulhat például, hogy egy adott felhasználót szeretne e-mailben elküldeni a Security Center riasztás esetén. Azt is megtudhatja, hogyan hozhat létre Logic Apps a [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|**Biztonsági rendszergazdai szerepkör** vagy **tulajdonos** az erőforráscsoporthoz<br>A cél erőforráshoz is írási engedéllyel kell rendelkeznie<br><br>Azure Logic Apps munkafolyamatok használatához a következő Logic Apps szerepköröket/engedélyeket is meg kell adni:<br> - A [Logic app-operátor](../role-based-access-control/built-in-roles.md#logic-app-operator) engedélyei kötelezőek vagy logikai alkalmazások olvasási/aktiválási hozzáférése (ez a szerepkör nem tud logikai alkalmazásokat létrehozni vagy szerkeszteni, csak a meglévőket *futtathatja* )<br> - A logikai alkalmazás létrehozásához és módosításához a [Logic app közreműködői](../role-based-access-control/built-in-roles.md#logic-app-contributor) engedélyei szükségesek<br>Ha logikai alkalmazás-összekötőket szeretne használni, további hitelesítő adatokra lehet szüksége a saját szolgáltatásaiba való bejelentkezéshez (például az Outlook/csapatok/Slack-példányok esetében)|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Logikai alkalmazás létrehozása és az automatikus futtatásának meghatározása 

1. A Security Center oldalsávján válassza a **munkafolyamat-automatizálás** elemet.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Munkafolyamat-automatizálások listája":::

    Ebből a lapról új Automation-szabályokat hozhat létre, valamint engedélyezheti, letilthatja vagy törölheti a meglévőket.

1. Új munkafolyamat definiálásához kattintson a **munkafolyamat-automatizálás hozzáadása** lehetőségre. 

    Megjelenik egy ablaktábla az új automatizálási beállításokkal. Itt megadhatja a következőket:
    1. Az automatizálás neve és leírása.
    1. Az automatikus munkafolyamatot kezdeményező eseményindítók. Előfordulhat például, hogy azt szeretné, hogy a logikai alkalmazás akkor fusson, amikor az "SQL" kifejezést tartalmazó biztonsági riasztást hoz létre.

        > [!NOTE]
        > Ha az trigger olyan javaslat, amely "alárendelt – ajánlásokat" tartalmaz, például az **SQL-adatbázisokra vonatkozó sebezhetőségi felmérési eredményeket kell szervizelni**, a logikai alkalmazás nem aktiválódik minden új biztonsági keresésnél. csak a szülő javaslat állapotának megváltozásakor.

    1. Az a logikai alkalmazás, amely az aktiválási feltételek teljesülése esetén fog futni. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Munkafolyamat-automatizálások ablaktábla hozzáadása":::

1. A műveletek szakaszban kattintson az **új létrehozása** lehetőségre a logikai alkalmazás létrehozási folyamatának megkezdéséhez.

    Azure Logic Apps.

    [![Új logikai alkalmazás létrehozása](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Adja meg a nevet, az erőforráscsoportot és a helyet, majd kattintson a **Létrehozás** gombra.

1. Az új logikai alkalmazásban a biztonsági kategóriából választhat a beépített, előre definiált sablonok közül. Megadhat egyéni folyamatokat is, amelyek a folyamat indításakor történnek.

    > [!TIP]
    > Esetenként logikai alkalmazásokban a paraméterek a karakterlánc részeként szerepelnek az összekötőben, és nem a saját mezőiben. A paraméterek kinyeréséről a következő témakörben talál példát: a [Logic app-paraméterek használatának #14e Azure Security Center munkafolyamat-automatizálások létrehozásakor](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    A Logic app Designer a következő Security Center eseményindítókat támogatja:

    - **Azure Security Center javaslat létrehozásakor vagy elindításakor** – ha a logikai alkalmazás egy elavult vagy lecserélt javaslatra támaszkodik, az automatizálás leáll, és frissítenie kell az aktiválást. A javaslatok változásainak nyomon követéséhez lásd: [Azure Security Center kibocsátási megjegyzések](release-notes.md).

    - **Azure Security Center riasztás létrehozásakor vagy indításakor** – testreszabhatja az indítást úgy, hogy az csak az Önt érdeklő súlyossági szinttel rendelkező riasztásokhoz kapcsolódik.
    
    - **Ha egy Security Center szabályozási megfelelőségi felmérést hoznak létre vagy indítanak** el, a megfelelőségi felmérések frissítései alapján automatizálják az automatizálást.

    > [!NOTE]
    > Ha az örökölt triggert használja a Azure Security Center riasztásra adott válasz indításakor, a rendszer nem indítja el a logikai alkalmazásokat a munkafolyamat-automatizálási szolgáltatással. Ehelyett használja a fent említett eseményindítók egyikét. 

    [![Minta logikai alkalmazás](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. A logikai alkalmazás meghatározása után térjen vissza a munkafolyamat-automatizálási definíciós panelre ("Munkafolyamat-automatizálás hozzáadása"). Kattintson a **frissítés** lehetőségre, hogy az új logikai alkalmazás elérhető legyen a kiválasztáshoz.

    ![Frissítés](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Válassza ki a logikai alkalmazást, és mentse az automatizálást. Vegye figyelembe, hogy a logikai alkalmazás legördülő lista csak a fent említett Security Center-összekötőket mutatja Logic Apps.


## <a name="manually-trigger-a-logic-app"></a>Logikai alkalmazás manuális elindítása

Logic Apps manuálisan is futtathatja a biztonsági riasztások és javaslatok megtekintésekor.

Logikai alkalmazás manuális futtatásához nyisson meg egy riasztást vagy javaslatot, és kattintson a **logikai alkalmazás elindítása** lehetőségre:

[![Logikai alkalmazás manuális elindítása](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>A munkafolyamatok automatizálásának konfigurálása a megadott szabályzatok használatával

A szervezet figyelési és incidens-reagálási folyamatainak automatizálása nagy mértékben növelheti a biztonsági incidensek kivizsgálásához és enyhítéséhez szükséges időt.

Az Automation-konfigurációk szervezeten belüli üzembe helyezéséhez használja az alább ismertetett Azure Policy "DeployIfNotExist" szabályzatokat a munkafolyamat-automatizálási eljárások létrehozásához és konfigurálásához.

Ismerkedés a [munkafolyamat-automatizálási sablonokkal](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

A szabályzatok megvalósítása:

1. Az alábbi táblázatból válassza ki az alkalmazni kívánt szabályzatot:

    |Cél  |Szabályzat  |Házirend-azonosító  |
    |---------|---------|---------|
    |Biztonsági riasztások munkafolyamat-automatizálása|[Azure Security Center-riasztásokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Azure Security Center-javaslatokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Munkafolyamatok automatizálása szabályozási megfelelőségi változásokhoz|[Munkafolyamat-automatizálás üzembe helyezése Azure Security Center szabályozási megfelelőséghez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Ezeket a Azure Policy keresésével is megtalálhatja:
    > 1. Nyissa meg Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Hozzáférés Azure Policy":::
    > 2. A Azure Policy menüben válassza a **definíciók** lehetőséget, és keressen rájuk név alapján. 

1. A megfelelő Azure Policy lapon válassza a **hozzárendelés** lehetőséget.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="A Azure Policy kiosztása":::

1. Nyissa meg az egyes lapokat, és adja meg a kívánt paramétereket:
    1. Az **alapvető beállítások** lapon állítsa be a házirend hatókörét. A központosított felügyelet használatához rendelje hozzá a szabályzatot a munkafolyamat-automatizálási konfigurációt használó előfizetéseket tartalmazó felügyeleti csoporthoz. 
    1. A **Parameters (paraméterek** ) lapon állítsa be az erőforráscsoport és az adattípus részleteit. 
        > [!TIP]
        > Minden paraméterhez tartozik egy elemleírás, amely ismerteti az Ön számára elérhető lehetőségeket.
        >
        > Azure Policy paraméterek lapja (1) hozzáférést biztosít a hasonló konfigurációs beállításokhoz, mint Security Center munkafolyamat-automatizálási lapja (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="A paraméterek összehasonlítása a munkafolyamat-automatizálásban Azure Policy" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Ha a hozzárendelést meglévő előfizetésekre szeretné alkalmazni, nyissa meg a **szervizelés** lapot, és válassza a Szervizelési feladat létrehozása lehetőséget.

1. Tekintse át az összefoglalás lapot, és válassza a **Létrehozás** lehetőséget.


## <a name="data-types-schemas"></a>Adattípusok sémái

A logikai alkalmazás-példánynak átadott biztonsági riasztások vagy javaslatok eseményeinek nyers esemény-sémáit a munkafolyamat- [automatizálási adattípusok sémái](https://aka.ms/ASCAutomationSchemas)között tekintheti meg. Ez olyan esetekben lehet hasznos, amikor nem használja a fent említett Security Center beépített logikai alkalmazás-összekötőit, de ehelyett a Logic app általános HTTP-összekötőjét használja – az Event JSON-sémával manuálisan elemezheti azt, ahogy jónak látja.


## <a name="faq-for-workflow-automation"></a>A munkafolyamatok automatizálásával kapcsolatos gyakori kérdések

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Támogatja a Munkafolyamat-automatizálás az üzletmenet folytonosságát vagy a vész-helyreállítási (BCDR) forgatókönyveket?

Ha a környezetet olyan BCDR-forgatókönyvek esetében készíti elő, amelyekben a cél erőforrás leállás vagy más katasztrófa esetén fordul elő, akkor a szervezet felelőssége az adatvesztés megelőzése azáltal, hogy az Azure Event Hubs, Log Analytics munkaterület és a Logic app irányelvek alapján hozza létre a biztonsági mentéseket.

Javasoljuk, hogy minden aktív automatizáláshoz hozzon létre egy azonos (letiltott) automatizálást, és tárolja azt egy másik helyen. Leállás esetén engedélyezheti ezeket a biztonsági mentési automatizálásokat, és megtarthatja a normál működést.

További információ az [üzletmenet folytonosságáról és a Azure Logic apps vész-helyreállításáról](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan hozhat létre Logic Apps, hogyan automatizálhatja a végrehajtást a Security Centerban, és hogyan futtathatja őket manuálisan.

Kapcsolódó anyagok esetében lásd: 

- [A Microsoft Learn modul, amely bemutatja, hogyan használható a Munkafolyamat-automatizálás a biztonsági válaszok automatizálására](/learn/modules/resolve-threats-with-azure-security-center/)
- [Biztonsági javaslatok az Azure Security Centerben](security-center-recommendations.md)
- [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
- [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Az Azure Logic Apps összekötői](../connectors/apis-list.md)
- [Munkafolyamat-automatizálási adattípussémák](https://aka.ms/ASCAutomationSchemas)