---
title: Vész-helyreállítás integrációs fiókokhoz
description: Az integrációs fiókok és a B2B-összetevők beállítása a régiók közötti vész-helyreállítási Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09fb738a499d6016c3aead0d33436f034bc5d339
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728402"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Régiók közötti vész-helyreállítás beállítása a Azure Logic Apps integrációs fiókjaihoz

A B2B munkaterhelések olyan pénz-tranzakciókat tartalmaznak, mint a megrendelések és a számlák. A katasztrófák során kritikus fontosságú, hogy a vállalatok gyorsan visszaállítsák a partnereinkkel megállapodott üzleti szintű SLA-kat. Ez a cikk bemutatja, hogyan hozhat létre egy üzletmenet-folytonossági tervet a B2B munkaterhelésekhez. 

* Vész-helyreállítási készültség 
* Feladatátvétel a másodlagos régióba katasztrófa esetén 
* Visszatérés az elsődleges régióba egy katasztrófa utáni esemény után

## <a name="disaster-recovery-readiness"></a>Vész-helyreállítási készültség  

1. Azonosítson egy másodlagos régiót, és hozzon létre egy [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a másodlagos régióban.

2. Vegyen fel partnereket, sémákat és szerződéseket azokhoz a szükséges üzenet-folyamatokhoz, amelyekben a futtatási állapotot replikálni kell a másodlagos régió integrációs fiókjába.

   > [!TIP]
   > Győződjön meg arról, hogy konzisztens az integrációs fiók összetevője a régiók közötti elnevezési konvencióban. 

3. A futtatási állapot elsődleges régióból való lekéréséhez hozzon létre egy logikai alkalmazást a másodlagos régióban. 

   A logikai alkalmazásnak *triggerrel* és *művelettel* kell rendelkeznie. 
   Az triggernek csatlakoznia kell az elsődleges régió integrációs fiókjához, és a műveletnek csatlakoznia kell a másodlagos régió integrációs fiókjához. 
   Az időintervallum alapján az trigger lekérdezi az elsődleges régió futási állapotát, és lekéri az új rekordokat, ha vannak ilyenek. A művelet frissíti őket a másodlagos régió integrációs fiókjába. 
   Ez segítséget nyújt a növekményes futtatókörnyezet állapotának az elsődleges régióból a másodlagos régióba való beszerzéséhez.

4. Logic Apps integrációs fiókban az üzletmenet folytonossága a B2B protokollok – X12, AS2 és EDIFACT alapján történő támogatásra lett tervezve. A részletes lépések megkereséséhez válassza ki a megfelelő hivatkozásokat.

5. A javaslat az összes elsődleges régió-erőforrás üzembe helyezése egy másodlagos régióban is. 

   Az elsődleges régió erőforrásai közé tartozik az üzenetküldéshez, az Azure API Managementhoz és a Azure Logic apps Azure App Service szolgáltatáshoz használt Azure SQL Database vagy Azure Cosmos DB, Azure Service Bus és Azure Event Hubs.   

6. Hozzon létre kapcsolatot egy elsődleges régióból egy másodlagos régióba. A futtatási állapot elsődleges régióból való lekéréséhez hozzon létre egy logikai alkalmazást egy másodlagos régióban. 

   A logikai alkalmazásnak triggerrel és művelettel kell rendelkeznie. 
   Az triggernek csatlakoznia kell egy elsődleges régió integrációs fiókjához. 
   A műveletnek csatlakoznia kell egy másodlagos régió integrációs fiókjához. 
   Az időintervallum alapján az trigger lekérdezi az elsődleges régió futási állapotát, és lekéri az új rekordokat, ha vannak ilyenek. 
   A művelet frissíti őket egy másodlagos régió integrációs fiókjába. 
   Ez a folyamat segítséget nyújt a növekményes futtatókörnyezet állapotának lekéréséhez az elsődleges régióból a másodlagos régióba.

Logic Apps integrációs fiókban az üzletmenet folytonossága a X12, az AS2 és a EDIFACT B2B-protokollok alapján nyújt támogatást. A X12 és az AS2 használatának részletes lépéseiért lásd a jelen cikk [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) és [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) című témakörét.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Feladatátvétel egy másodlagos régióba katasztrófa esetén

Katasztrófa esetén, ha az elsődleges régió nem érhető el az üzletmenet folytonossága érdekében, a másodlagos régió felé irányuló közvetlen forgalom. Egy másodlagos régió segíti a vállalkozásokat a funkciók gyors helyreállításában, hogy azok megfeleljenek a partnerek által elfogadott RPO/RTO. Emellett az egyik régióból egy másik régióba történő feladatátvételre irányuló erőfeszítéseket is csökkentheti. 

Az elsődleges régióból a másodlagos régióba való másolás során várható késés történt. Ha el szeretné kerülni, hogy a rendszer elküldje a duplikált vezérlőelemeket a partnereknek a katasztrófa-események során, a másodlagos régión belüli szerződésekben a [PowerShell-parancsmagok](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn)használatával növelje a vezérlőelem-számokat.

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Visszatérés egy elsődleges régióba, a katasztrófa utáni esemény után

Ha elérhetővé kívánja tenni az elsődleges régiót, kövesse az alábbi lépéseket:

1. A másodlagos régióban lévő partnerektől érkező üzenetek fogadásának leállítása.  

2. A [PowerShell-parancsmagok](/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn)használatával növelje az összes elsődleges régióra vonatkozó szerződés generált vezérlőelem-számát.  

3. Közvetlen forgalom a másodlagos régióból az elsődleges régióba.

4. Győződjön meg arról, hogy a másodlagos régióban létrehozott logikai alkalmazás az elsődleges régióból való kihúzási állapotot engedélyezte.

## <a name="x12"></a>X12 

Az EDI-X12 dokumentumok üzleti folytonossága az ellenőrzési számokon alapul:

> [!TIP]
> A logikai alkalmazások létrehozásához használhatja a [X12 gyors üzembe helyezési sablonját](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) is. Az elsődleges és a másodlagos integrációs fiókok létrehozása a sablon használatának előfeltételei. A sablon segítségével két logikai alkalmazást hozhat létre, amelyek közül az egyik a kapott vezérlőelemek száma, a másik a generált vezérlőelemek száma. A rendszer a logikai alkalmazásokban hozza létre a megfelelő eseményindítókat és műveleteket, és csatlakoztatja az eseményindítót az elsődleges integrációs fiókhoz és a műveletet a másodlagos integrációs fiókhoz.

**Előfeltételek**

A bejövő üzenetek vész-helyreállításának engedélyezéséhez válassza a X12-szerződés fogadási beállításai között található ismétlődő ellenőrzési beállításokat.

![Ismétlődő ellenőrzési beállítások kiválasztása](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. [Logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) létrehozása másodlagos régióban.    

2. Keressen rá a **X12** elemre, és válassza **a X12 elemet – ha módosítani kívánja a vezérlőelem számát**.   

   ![X12 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Az trigger arra kéri, hogy hozzon létre kapcsolatot egy integrációs fiókkal. 
   Az triggert egy elsődleges régió integrációs fiókjához kell csatlakoztatni.

3. Adja meg a kapcsolatok nevét, válassza ki az *elsődleges régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget.   

   ![Képernyőkép, amely megjeleníti a kapcsolódási név megadásának helyét, és kiválasztja az elsődleges régió integrációs fiókját. ](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A **vezérlési szám szinkronizálásának megkezdéséhez szükséges dátum és** idő beállítás nem kötelező. A **gyakoriság** beállítható **nap**, **óra**, **perc** vagy **másodperc** értékre egy intervallummal.   

   ![Képernyőkép, amely a vezérlési szám szinkronizálási beállításának megkezdéséhez szükséges DateTime értéket mutatja.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Válassza az **Új lépés** > **Művelet hozzáadása** lehetőséget.

   ![Képernyőkép, amely az új lépés gombot és a művelet hozzáadása lehetőséget jeleníti meg.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Keressen rá a **X12**, és válassza a **X12** lehetőséget.   

   ![Vezérlőelem-számok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Ha egy műveletet másodlagos régió integrációs fiókjához szeretne csatlakoztatni, válassza a **kapcsolat módosítása**  >  **új kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adja meg a kapcsolatok nevét, válassza ki a *másodlagos régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget. 

   ![Képernyőkép a másodlagos régió integrációs fiókjának nevének hozzáadásáról.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Váltson a nyers bemenetekre a jobb felső sarokban lévő ikonra kattintva.

   ![Képernyőkép, amely megjeleníti a nyersre váltáshoz használandó ikont.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Válassza ki a dinamikus tartalom-választó törzsét, és mentse a logikai alkalmazást.

   ![Dinamikus tartalom mezői](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Az adott időintervallum alapján az trigger lekérdezi az elsődleges régió vezérlési szám tábláját, és lekéri az új rekordokat. 
   A művelet frissíti a rekordokat a másodlagos régió integrációs fiókjában. 
   Ha nincsenek frissítések, az trigger állapota **kihagyva** jelenik meg.   

   ![Vezérlő száma tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján a növekményes futtatókörnyezet állapota egy elsődleges régióból egy másodlagos régióba replikálódik. Katasztrófa esetén, ha az elsődleges régió nem érhető el, az üzletmenet folytonossága érdekében a másodlagos régió felé irányuló közvetlen forgalom. 

## <a name="edifact"></a>EDIFACT 

Az EDI-EDIFACT dokumentumok üzleti folytonossága a vezérlőelemek számán alapul.

**Előfeltételek**

A bejövő üzenetek vész-helyreállításának engedélyezéséhez válassza a EDIFACT-szerződés fogadási beállításai között található ismétlődő ellenőrzési beállításokat.

![A EDIFACT-szerződés fogadási beállításaiban található duplikált ellenőrzési beállításokat bemutató képernyőkép.](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. [Logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) létrehozása másodlagos régióban.    

2. Keressen rá a **EDIFACT** elemre, és válassza **a EDIFACT elemet – ha módosítani kívánja a vezérlőelem számát**.

   ![EDIFACT keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Az trigger arra kéri, hogy hozzon létre kapcsolatot egy integrációs fiókkal. 
   Az triggert egy elsődleges régió integrációs fiókjához kell csatlakoztatni. 

3. Adja meg a kapcsolatok nevét, válassza ki az *elsődleges régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget.    

   ![Elsődleges régió integrációs fiókjának neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A **vezérlési szám szinkronizálásának megkezdéséhez szükséges dátum és** idő beállítás nem kötelező. A **gyakoriság** beállítható **nap**, **óra**, **perc** vagy **másodperc** értékre egy intervallummal.    

   ![A DateTime és a Frequency beállításait megjelenítő képernyőkép.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Válassza az **Új lépés** > **Művelet hozzáadása** lehetőséget.    

   ![Képernyőfelvétel: válassza ki a művelet hozzáadása lehetőséget.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Keressen rá a **EDIFACT**, és válassza a **EDIFACT** lehetőséget.   

   ![Képernyőkép, amely megjeleníti a vezérlőelem-számok hozzáadásának és frissítésének helyét.](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Ha egy műveletet másodlagos régió integrációs fiókjához szeretne csatlakoztatni, válassza a **kapcsolat módosítása**  >  **új kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adja meg a kapcsolatok nevét, válassza ki a *másodlagos régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget.

   ![Hozzon létre egy másodlagos régió integrációs fiókjának nevét.](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Váltson a nyers bemenetekre a jobb felső sarokban lévő ikonra kattintva.

   ![Képernyőkép, amely kiemeli az ikont, ha a nyers bemenetekre szeretne váltani.](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Válassza ki a dinamikus tartalom-választó törzsét, és mentse a logikai alkalmazást.   

   ![Képernyőfelvétel: a dinamikus tartalom választója, ahol kiválaszthatja a törzs lehetőséget.](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Az adott időintervallum alapján az trigger lekérdezi az elsődleges régió vezérlési szám tábláját, és lekéri az új rekordokat.
   A művelet frissíti a rekordokat a másodlagos régió integrációs fiókjába. 
   Ha nincsenek frissítések, az trigger állapota **kihagyva** jelenik meg.

   ![Vezérlő száma tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján a növekményes futtatókörnyezet állapota egy elsődleges régióból egy másodlagos régióba replikálódik. Katasztrófa esetén, ha az elsődleges régió nem érhető el, az üzletmenet folytonossága érdekében a másodlagos régió felé irányuló közvetlen forgalom. 

## <a name="as2"></a>AS2 

Az AS2 protokollt használó dokumentumok üzletmenet-folytonossága az üzenet azonosítója és a MIC érték alapján történik.

> [!TIP]
> A logikai alkalmazások létrehozásához az [AS2 gyors üzembe helyezési sablonját](https://github.com/Azure/azure-quickstart-templates/pull/3302) is használhatja. Az elsődleges és a másodlagos integrációs fiókok létrehozása a sablon használatának előfeltételei. A sablon segítséget nyújt egy triggerrel és egy művelettel rendelkező logikai alkalmazás létrehozásához. A logikai alkalmazás létrehoz egy kapcsolódást egy triggerből egy elsődleges integrációs fiókhoz, és egy műveletet egy másodlagos integrációs fiókhoz.

1. Hozzon létre egy [logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) a másodlagos régióban.  

2. Keressen rá az **AS2**-ra, és válassza **az AS2 – a MIC-érték** létrehozásakor lehetőséget.   

   ![AS2 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Egy trigger arra kéri, hogy hozzon létre kapcsolatot egy integrációs fiókkal. 
   Az triggert egy elsődleges régió integrációs fiókjához kell csatlakoztatni. 
   
3. Adja meg a kapcsolatok nevét, válassza ki az *elsődleges régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép, amely azt mutatja be, hogy hol kell megadni a kapcsolódási nevet egy MIC-érték létrehozásakor.](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A **MIC-érték szinkronizálásának kezdési dátuma** nem kötelező. A **gyakoriság** beállítható **nap**, **óra**, **perc** vagy **másodperc** értékre egy intervallummal.   

   ![DateTime és gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Válassza az **Új lépés** > **Művelet hozzáadása** lehetőséget.  

   ![Új lépés, művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Keressen az **AS2**-on, és válassza az **AS2 – MIC-tartalom hozzáadása vagy frissítése** elemet.  

   ![MIC-Hozzáadás vagy-frissítés](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Ha egy műveletet egy másodlagos integrációs fiókhoz szeretne csatlakoztatni, válassza a **kapcsolat módosítása**  >  **új kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adja meg a kapcsolatok nevét, válassza ki a *másodlagos régió integrációs fiókját* a listából, és válassza a **Létrehozás** lehetőséget.

   ![Másodlagos régió integrációs fiókjának neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Váltson a nyers bemenetekre a jobb felső sarokban lévő ikonra kattintva.

   ![Váltás nyers bemenetekre](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Válassza ki a dinamikus tartalom-választó törzsét, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Az időintervallum alapján az trigger lekérdezi az elsődleges régió táblázatát, és lekéri az új rekordokat. A művelet frissíti őket a másodlagos régió integrációs fiókjába. 
   Ha nincsenek frissítések, az trigger állapota **kihagyva** jelenik meg.  

   ![Elsődleges régió tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Az időtartam alapján a növekményes futtatókörnyezet állapota az elsődleges régióból a másodlagos régióba replikálódik. Katasztrófa esetén, ha az elsődleges régió nem érhető el, az üzletmenet folytonossága érdekében a másodlagos régió felé irányuló közvetlen forgalom. 

## <a name="next-steps"></a>Következő lépések

[B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)
