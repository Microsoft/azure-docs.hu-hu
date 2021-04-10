---
title: A veszélyforrások elleni reagálás automatizálása az Azure Sentinelben | Microsoft Docs
description: Ez a cikk az Azure Sentinel automatizálását mutatja be, és bemutatja, hogyan használható a fenyegetés megelőzése és a reagálás a forgatókönyvek használatával.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609360"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>A veszélyforrások megválaszolásának automatizálása az Azure Sentinelben

Ez a cikk ismerteti, hogy mi az Azure Sentinel-forgatókönyvek, és hogyan használhatók a biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) műveletek megvalósításához, jobb eredmények eléréséhez az idő és az erőforrások megtakarítása mellett.

## <a name="what-is-a-playbook"></a>Mi az a forgatókönyv?

A SIEM/SOC csapatokat jellemzően a biztonsági riasztásokkal és az incidensekkel együtt rendszeresen, olyan köteteken használják, amelyeken a rendelkezésre álló személyek túlterheltek. Ez az eredmény túl gyakran előfordul olyan helyzetekben, amikor sok riasztást figyelmen kívül hagynak, és sok incidenst nem vizsgálnak meg, így a szervezet sebezhetővé válik a nem észlelt támadásokkal szemben.

A riasztások és az incidensek többsége sok esetben olyan ismétlődő mintázatoknak felel meg, amelyek a Szervizelési műveletek meghatározott és meghatározott készletei által kezelhetők.

A forgatókönyvek ezen szervizelési műveletek gyűjteményei, amelyek rutinként futtathatók az Azure Sentinel használatával. A forgatókönyvek segítségével automatizálhatja és koordinálhatja a fenyegetésekre adott választ; manuálisan is futtatható vagy beállítható úgy, hogy adott riasztásokra vagy incidensekre reagálva automatikusan fusson, amikor egy elemzési szabály vagy egy Automation-szabály aktiválja őket.

A forgatókönyvek létrehozása és alkalmazása az előfizetés szintjén történik, de a forgatókönyvek lap (az új **Automation** **panelen) megjeleníti a kiválasztott** előfizetések összes elérhető forgatókönyvét.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps alapvető fogalmak

Az Azure Sentinelben elérhető forgatókönyvek az [Azure Logic apps](../logic-apps/logic-apps-overview.md)-ben létrehozott munkafolyamatokon alapulnak, amelyek segítségével a vállalaton belül a feladatok és munkafolyamatok ütemezhetők, automatizálható és koordinálható. Ez azt jelenti, hogy a forgatókönyvek kihasználhatják Logic Apps beépített sablonjainak minden erejét és testreszabható megoldását.

> [!NOTE]
> Mivel Azure Logic Apps egy különálló erőforrás, további díjak is vonatkozhatnak. További részletekért látogasson el a [Azure Logic apps](https://azure.microsoft.com/pricing/details/logic-apps/) díjszabási oldalára.

A Azure Logic Apps összekötők használatával kommunikál más rendszerekkel és szolgáltatásokkal. Az alábbi rövid magyarázatot ad az összekötők és néhány fontos attribútumról:

- **Felügyelt összekötő:** Műveletek és triggerek összessége, amelyek egy adott termékre vagy szolgáltatásra irányuló API-hívásokat mutatnak be. Azure Logic Apps több száz összekötőt kínál a Microsofttal és a nem Microsoft-szolgáltatásokkal való kommunikációhoz.
  - [Az összes Logic Apps összekötő és a dokumentációjuk](/connectors/connector-reference/)

- **Egyéni összekötő:** Érdemes lehet olyan szolgáltatásokkal kommunikálni, amelyek nem érhetők el előre elkészített összekötőként. Az egyéni összekötők ezt az igényt úgy kezelik, hogy lehetővé teszik egy összekötő létrehozását (és akár megosztását), valamint a saját eseményindítók és műveletek definiálását.
  - [Saját egyéni Logic Apps összekötők létrehozása](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure Sentinel-összekötő:** Az Azure Sentinel használatával kommunikáló forgatókönyvek létrehozásához használja az Azure Sentinel-összekötőt.
  - [Az Azure Sentinel Connector dokumentációja](/connectors/azuresentinel/)

- **Trigger:** Egy összekötő-összetevő, amely elindítja a forgatókönyveket. Meghatározza azt a sémát, amelyet a forgatókönyv az indításkor vár. Az Azure Sentinel-összekötő jelenleg két eseményindítóval rendelkezik:
  - [Riasztási trigger](/connectors/azuresentinel/#triggers): a forgatókönyv bemenetként fogadja a riasztást.
  - [Incidensek triggere](/connectors/azuresentinel/#triggers): a forgatókönyv az incidenst a bemenetként fogadja, valamint a benne foglalt riasztásokat és entitásokat.

    > [!IMPORTANT]
    >
    > - Az **incidensek trigger** funkciója a forgatókönyvekhez jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

- **Műveletek:** A műveletek az indítás után megjelenő lépések. Ezeket egymás után, párhuzamosan, vagy összetett feltételek mátrixában lehet rendezni.

- **Dinamikus mezők:** Az eseményindítók és műveletek kimeneti sémája által meghatározott ideiglenes mezők, amelyek a tényleges kimenet alapján vannak feltöltve, amelyek a következő műveletekben használhatók.

### <a name="permissions-required"></a>A szükséges engedélyek

 Annak érdekében, hogy a SecOps csapata a biztonsági előkészítési, automatizálási és reagálási (fellépési) műveletekhez Logic Apps használjon, azaz a forgatókönyvek létrehozásához és futtatásához – az Azure Sentinelben – Azure-szerepköröket rendelhet hozzá a Security Operations csapatának adott tagjaihoz vagy a teljes csapathoz. Az alábbiakban a különböző elérhető szerepköröket, valamint a hozzájuk rendelt feladatokat ismertetjük:

#### <a name="azure-roles-for-logic-apps"></a>Logic Apps Azure-szerepkörök

- A **Logic app közreműködője** lehetővé teszi a logikai alkalmazások kezelését és a forgatókönyvek futtatását, de nem tudja módosítani őket (a **tulajdonosi** szerepkörre van szükség).
- A **Logic app operátor** lehetővé teszi a Logic apps olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissítheti őket.

#### <a name="azure-roles-for-sentinel"></a>A Sentinelhez készült Azure-szerepkörök

- Az **Azure Sentinel közreműködő** szerepkör lehetővé teszi a forgatókönyvek egy elemzési szabályhoz való csatolását.
- Az **Azure Sentinel válaszadó** szerepkör lehetővé teszi, hogy a forgatókönyveket manuálisan futtassa.
- Az **Azure Sentinel Automation közreműködője** lehetővé teszi, hogy az automatizálási szabályok futtassák a forgatókönyveket. Más célra nem használható.

#### <a name="learn-more"></a>Tudjon meg többet

- [További információ a Azure Logic apps Azure-szerepköreiről](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [További információk az Azure Sentinel Azure-szerepköreiről](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Forgatókönyvek létrehozásának lépései

- [Az automatizálási forgatókönyv meghatározása](#use-cases-for-playbooks).

- Hozza [létre az Azure logikai alkalmazást](tutorial-respond-threats-playbook.md).

- [Tesztelje a logikai alkalmazást](#run-a-playbook-manually-on-an-alert).

- Csatolja a forgatókönyvet egy [Automation-szabályhoz](#incident-creation-automated-response) vagy egy [elemzési szabályhoz](#alert-creation-automated-response), vagy [futtassa manuálisan, ha szükséges](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Forgatókönyvek használata forgatókönyvek esetén

A Azure Logic Apps platform több száz műveletet és eseményindítót kínál, így szinte bármilyen automatizálási forgatókönyvet létrehozhat. Az Azure Sentinel a következő SOC-forgatókönyvek megkezdését javasolja:

#### <a name="enrichment"></a>Bővítés

**Gyűjtsön adatokat, és csatolja az incidenshez az intelligensebb döntések meghozatala érdekében.**

Például:

Az Azure Sentinel-incidenst egy olyan elemzési szabály riasztása hozta létre, amely IP-címek entitásokat állít elő.

Az incidens egy automatizálási szabályt indít el, amely a következő lépésekkel futtatja a forgatókönyveket:

- Indítás [új Azure Sentinel-incidens létrehozásakor](/connectors/azuresentinel/#triggers). Az incidensben ábrázolt entitásokat az incidens-trigger dinamikus mezőiben tárolja a rendszer.

- Az egyes IP-címek esetében a további adatok lekéréséhez a külső veszélyforrások felderítése szolgáltatót, például a [vírus összegét](https://www.virustotal.com/)kell lekérdezni.

- Adja hozzá a visszaadott értékeket és az elemzéseket az incidens megjegyzéséhez.

#### <a name="bi-directional-sync"></a>Kétirányú szinkronizálás

**A forgatókönyvek használatával az Azure Sentinel-incidenseket más Ticketing-rendszerekkel is szinkronizálhatja.**

Például:

Hozzon létre egy Automation-szabályt az összes incidens létrehozásához, és csatoljon egy olyan forgatókönyvet, amely megnyit egy jegyet a ServiceNow-ben:

- Indítás [új Azure Sentinel-incidens létrehozásakor](/connectors/azuresentinel/#triggers).

- Hozzon létre egy új jegyet a [ServiceNow](/connectors/service-now/)-ben.

- Vegye fel a jegybe az incidens nevét, a fontos mezőket, valamint egy URL-címet az Azure Sentinel incidensnek az egyszerű kimutatáshoz.

#### <a name="orchestration"></a>Vezénylés

**Az incidensek várólistájának jobb vezérléséhez használja a SOC chat platformot.**

Például:

Az Azure Sentinel-incidenst egy olyan elemzési szabály riasztása hozta létre, amely felhasználóneveket és IP-címek entitásokat állít elő.

Az incidens egy automatizálási szabályt indít el, amely a következő lépésekkel futtatja a forgatókönyveket:

- Indítás [új Azure Sentinel-incidens létrehozásakor](/connectors/azuresentinel/#triggers).

- Küldjön üzenetet a biztonsági operatív csatornának a [Microsoft Teams](/connectors/teams/) vagy a [Slack](/connectors/slack/) szolgáltatásban, és győződjön meg arról, hogy a biztonsági elemzők tisztában vannak az incidenssel.

- Küldje el a riasztásban szereplő összes információt e-mailben a vezető hálózati rendszergazdai és biztonsági rendszergazdának. Az e-mail-üzenet tartalmazni fogja a felhasználói választógombok **blokkolását** és **mellőzését** .

- Várjon, amíg a rendszer választ kap a rendszergazdáktól, majd folytassa a futtatást.

- Ha a rendszergazdák a **Letiltás** lehetőséget választotta, küldjön egy parancsot a tűzfalnak a riasztás IP-címének letiltásához, egy másikat pedig az Azure ad-hoz a felhasználó letiltásához.

#### <a name="response"></a>Reagálás

**Azonnali reagálás a fenyegetésekre, minimális emberi függőségekkel.**

Két példa:

**1. példa:** Válaszoljon egy olyan elemzési szabályra, amely feltört felhasználót jelez [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)által felderített módon:

   - Indítás [új Azure Sentinel-incidens létrehozásakor](/connectors/azuresentinel/#triggers).

   - Az incidensben feltörtként gyanús összes felhasználói entitás esetében:

     - Küldjön egy Teams-üzenetet a felhasználónak, és erősítse meg, hogy a felhasználó felvette a gyanús műveletet.

     - Ellenőrizze, Azure AD Identity Protection-e, hogy [a felhasználó állapota sérült](/connectors/azureadip/#confirm-a-risky-user-as-compromised)-e. Azure AD Identity Protection a felhasználót **kockázatos** címkével fogja felcímkézni, és alkalmazza a már konfigurált kényszerítési házirendet – például arra, hogy a felhasználó a következő bejelentkezéskor az MFA használatát igényli.

        > [!NOTE]
        > A forgatókönyv nem indít el kényszerítési műveletet a felhasználón, és nem kezdeményezi a kényszerítési házirend konfigurálását. Csak azt közli Azure AD Identity Protection, hogy szükség szerint alkalmazza a már meghatározott házirendeket. Minden kényszerítés teljes mértékben a Azure AD Identity Protectionban definiált megfelelő házirendekre függ.

**2. példa:** Válaszoljon egy olyan elemzési szabályra, amely egy feltört gépet jelez, amelyet a [Microsoft Defender a végpontnak](/windows/security/threat-protection/)észlelt:

   - Indítás [új Azure Sentinel-incidens létrehozásakor](/connectors/azuresentinel/#triggers).

   - Az **entitások – gazdagépek beolvasása** művelettel az Azure sentinelben elemezheti az incidens entitásokban található gyanús gépeket.

   - Adjon ki egy parancsot a Microsoft Defender számára a végpont számára, hogy [elkülönítse a számítógépeket](/connectors/wdatp/#actions---isolate-machine) a riasztásban.

## <a name="how-to-run-a-playbook"></a>Forgatókönyv futtatása

A forgatókönyvek **manuálisan** vagy **automatikusan** is futtathatók.

A manuális Futtatás azt jelenti, hogy ha riasztást kap, dönthet úgy, hogy a kiválasztott riasztásra adott válaszként futtat egy igény szerinti forgatókönyvet. Ez a funkció jelenleg csak riasztások esetén támogatott, incidensek esetén nem.

A futtatásuk automatikusan azt jelenti, hogy automatikus válaszként állítja be őket egy elemzési szabályban (riasztások esetén), vagy egy Automation-szabályban (incidensek esetében) műveletként. [További információ az Automation-szabályokról](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Automatikus válasz beállítása

A biztonsági műveleti csapatok jelentősen csökkenthetik a számítási feladatokat azáltal, hogy teljes mértékben automatizálják az incidensek és a riasztások ismétlődő típusaira adott rutin válaszokat, így többek között az egyedi incidensekre és riasztásokra koncentrálhat, elemezheti a mintákat, a veszélyforrások elleni vadászatot és egyebeket.

Az automatikus válasz beállítása azt jelenti, hogy minden alkalommal, amikor egy elemzési szabály aktiválódik, egy riasztás létrehozása mellett a szabály egy forgatókönyvt fog futtatni, amely a szabály által létrehozott riasztás bemenetként jelenik meg.

Ha a riasztás incidenst hoz létre, akkor az incidens egy olyan automatizálási szabályt indít el, amely egy forgatókönyv futtatására szolgál, amely a riasztás által létrehozott incidens bemenetként fog megjelenni.

#### <a name="alert-creation-automated-response"></a>Riasztás-létrehozási automatikus válasz

A riasztások létrehozásakor és a riasztások bemenetként való fogadásakor elindított forgatókönyvek esetében (az első lépés az, amikor egy Azure Sentinel-riasztás aktiválódik), csatolja a forgatókönyvt egy elemzési szabályhoz:

1. Szerkessze a riasztást létrehozó [elemzési szabályt](tutorial-detect-threats-custom.md) , amely számára automatikus választ szeretne megadni.

1. Az **automatikus válasz** lapon a **riasztás automatizálása** területen válassza ki azokat a forgatókönyveket vagy forgatókönyveket, amelyeket az elemzési szabály a riasztások létrehozásakor aktivál majd.

#### <a name="incident-creation-automated-response"></a>Incidens-létrehozási automatizált válasz

Az incidensek létrehozásával és az incidensek a bemenetként való fogadásával indított forgatókönyvek esetében (az első lépés: "Ha egy Azure Sentinel-incidens aktiválódik"), hozzon létre egy Automation-szabályt, és Definiáljon egy **futtatási** forgatókönyv-műveletet. Ezt kétféleképpen teheti meg:

- Szerkessze az az incidenst létrehozó elemzési szabályt, amelyhez automatikus választ szeretne adni. Az **automatikus válasz** lapon az **incidens automatizálása** területen hozzon létre egy Automation-szabályt. Ez egy automatikus választ hoz létre ehhez az elemzési szabályhoz.

- Az **Automation** panel **Automation-szabályok** lapján hozzon létre egy új Automation-szabályt, és határozza meg a megfelelő feltételeket és a kívánt műveleteket. Ez az Automation-szabály minden olyan elemzési szabályra érvényes lesz, amely megfelel a megadott feltételeknek.

    > [!NOTE]
    > A forgatókönyv automatizálási szabályokból való futtatásához az Azure Sentinel egy kifejezetten erre a szolgáltatásra vonatkozó fiókot használ. A fiók használata (a felhasználói fiókkal ellentétben) növeli a szolgáltatás biztonsági szintjét, és lehetővé teszi, hogy az Automation-szabályok API támogassa a CI/CD használati eseteket.
    >
    > Ennek a fióknak explicit engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a forgatókönyv található. Ezen a ponton bármely automatizálási szabály futtathatja az adott erőforráscsoport bármely forgatókönyvét.
    >
    > Ha egy automatizálási szabályhoz hozzáadja a **futtatási forgatókönyv futtatása** műveletet, megjelenik a forgatókönyvek legördülő listája. Azok a forgatókönyvek, amelyekhez az Azure Sentinel nem rendelkezik engedéllyel, nem érhetők el ("szürkén jelenik meg"). Az Azure Sentinel számára engedélyt adhat a helyszínen, ha kiválasztja a forgatókönyv- **engedélyek kezelése** hivatkozást.
    >
    > Több-bérlős ([világítótorony](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) forgatókönyv esetén meg kell határoznia az engedélyeket azon a bérlőn, amelyen a forgatókönyv él, még akkor is, ha a forgatókönyvet hívó Automation-szabály egy másik bérlőn található. Ehhez **tulajdonosi** engedélyekkel kell rendelkeznie a forgatókönyv erőforráscsoporthoz.

Az [Automation-szabályok létrehozásával kapcsolatos teljes útmutatást](tutorial-respond-threats-playbook.md#respond-to-incidents)itt találhatja meg.

### <a name="run-a-playbook-manually-on-an-alert"></a>Forgatókönyv manuális futtatása riasztáson

A manuális indítás a következő lapokon érhető el az Azure Sentinel portálról:

- Az **incidensek** nézetben válasszon ki egy adott incidenst, nyissa meg a **riasztások** lapot, és válassza ki a riasztást.

- A **vizsgálat** területen válasszon egy adott riasztást.

1. A kiválasztott riasztáshoz kattintson a forgatókönyvek **megtekintése** elemre. Ekkor megjelenik az összes olyan forgatókönyv listája, amely a következővel kezdődik: egy **Azure Sentinel-riasztás aktiválásakor** , és hogy van-e hozzáférése.

1. Egy adott forgatókönyv sorában kattintson a **Futtatás** elemre az aktiváláshoz.

1. A **futtatások lapon** megtekintheti a riasztáson futtatott forgatókönyvek összes időpontjának listáját. Eltarthat néhány másodpercig, amíg az összes éppen befejezett Futtatás megjelenik ebben a listában.

1. Ha egy adott futtatásra kattint, megnyílik a teljes futtatási napló Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Forgatókönyv manuális futtatása egy incidensen

Még nem támogatott. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>A forgatókönyvek kezelése

A forgatókönyvek lapon megjelenik azon forgatókönyvek listája, amelyekhez Ön hozzáférhet, és **amelyeket az Azure** -ban aktuálisan megjelenített előfizetések szűrnek. Az előfizetések szűrő a globális oldal fejlécének **címtár + előfizetés** menüjéből érhető el.

Ha egy forgatókönyv nevére kattint, az a forgatókönyv főoldalára irányítja Logic Apps. Az **állapot** oszlopban látható, hogy engedélyezve van vagy le van tiltva.

A **trigger típusa** az Logic apps triggert jelöli, amely elindítja ezt a forgatókönyvet.

| Trigger típusa | A forgatókönyvben szereplő összetevők típusát jelzi |
|-|-|
| **Azure Sentinel-incidens/riasztás** | A forgatókönyv a Sentinel-eseményindítók egyikével (riasztás, incidens) indul el. |
| **Az Azure Sentinel-művelet használata** | A forgatókönyv egy nem Sentinel-triggerrel lett elindítva, de Azure Sentinel-műveletet használ |
| **Egyéb** | A forgatókönyv nem tartalmaz Sentinel-összetevőket |
| **Nincs inicializálva** | A forgatókönyv létre lett hozva, de nem tartalmaz összetevőket (triggereket vagy műveleteket). |
|

A forgatókönyv logikai alkalmazásának oldalán további információk jelennek meg a forgatókönyvről, beleértve a futtatási idő naplóját is, valamint az eredményt (sikeres vagy sikertelen). Ha rendelkezik a megfelelő engedélyekkel, megadhatja a Logic Apps designert, és közvetlenül is szerkesztheti a forgatókönyvet.

### <a name="api-connections"></a>API-kapcsolatok

Az API-kapcsolatok a Logic Apps más szolgáltatásokhoz való csatlakoztatására szolgálnak. Minden alkalommal, amikor új hitelesítés történik egy Logic Apps összekötőn, létrejön egy új API- **kapcsolatú** erőforrás, amely a szolgáltatáshoz való hozzáférés konfigurálásakor megadott információkat tartalmazza.

Az összes API-kapcsolat megjelenítéséhez írja be az *API-kapcsolatok* kifejezést a Azure Portal fejlécének keresési mezőjébe. Jegyezze fel a fontos oszlopokat:

- Megjelenítendő név – a "felhasználóbarát" név, amelyet a kapcsolódáshoz adnak meg, valahányszor létrehoz egyet.
- Állapot – a kapcsolat állapotát jelzi: hiba, csatlakoztatott.
- Erőforráscsoport – az API-kapcsolatok a forgatókönyv (Logic Apps) erőforrásának erőforráscsoporthoz jönnek létre.

Az API-kapcsolatok megtekintésének egy másik módja, ha a **minden erőforrás** panelre lép, és az *API-kapcsolat* alapján szűri. Így egyszerre több kapcsolat kijelölését, címkézését és törlését is lehetővé teszi.

Meglévő kapcsolatok engedélyezésének módosításához adja meg a kapcsolódási erőforrást, és válassza az API- **Kapcsolatok szerkesztése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: a fenyegetési válaszok automatizálására szolgáló forgatókönyvek használata az Azure Sentinelben](tutorial-respond-threats-playbook.md)
