---
title: EDIFACT-üzenetek dekódolása
description: Az EDI érvényesítése és a visszaigazolások előállítása a Azure Logic Apps EDIFACT-üzeneteinek dekóderével Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000011"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Azure Logic Apps EDIFACT-üzeneteinek dekódolása a Enterprise Integration Pack

Az EDIFACT-üzenet dekódolása összekötővel ellenőrizheti az EDI-t és a partnerspecifikus tulajdonságokat, feloszthatja az üzenetváltásokat tranzakciókészletekre, vagy megőrizheti a teljes üzenetváltásokat, és nyugtázásokat hozhat létre a feldolgozott tranzakciókhoz. Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő triggerhez a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy már definiált és az Azure-előfizetéshez társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) . A dekódolási EDIFACT üzenet-összekötő használatához integrációs fiókkal kell rendelkeznie. 
* Legalább két olyan [partner](logic-apps-enterprise-integration-partners.md) , amely már definiálva van az integrációs fiókban
* Az integrációs fiókban már definiált [EDIFACT-szerződés](logic-apps-enterprise-integration-edifact.md)

## <a name="decode-edifact-messages"></a>EDIFACT-üzenetek dekódolása

> [!IMPORTANT]
> Az EDIFACT-összekötő csak UTF-8 karaktereket támogat.
> Ha a kimenet váratlan karaktereket tartalmaz, ellenőrizze, hogy a EDIFACT-üzenetei az UTF-8 karakterkészletet használják-e. 

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. Az EDIFACT dekódolása nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelem eseményindítóhoz. A Logic app Designerben adjon hozzá egy triggert, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3. A keresőmezőbe írja be szűrőként a "EDIFACT" kifejezést. Válassza a **dekódolás EDIFACT üzenetet**.
   
    ![EDIFACT keresése](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Ha korábban nem hozott létre kapcsolatot az integrációs fiókkal, a rendszer felszólítja, hogy hozza létre a kapcsolatot most. Nevezze el a kapcsolatot, majd válassza ki a csatlakoztatni kívánt integrációs fiókot.
   
    ![integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    A csillaggal rendelkező tulajdonságok megadása kötelező.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolatok neve * |Adja meg a kapcsolatok nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen található. |

4. Amikor elkészült a csatlakozás létrehozásával, válassza a **Létrehozás** lehetőséget. A kapcsolat részleteinek ehhez a példához hasonlóan kell kinéznie:

    ![integrációs fiók részletei](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Miután létrejött a kapcsolódás, ahogy az ebben a példában is látható, válassza ki a dekódoláshoz használandó EDIFACT-fájl üzenetet.

    ![integrációs fiók-csatlakozás létrehozva](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Például:

    ![EDIFACT-üzenet kiválasztása a dekódoláshoz](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT-dekódoló részletei

A dekódolási EDIFACT-összekötő a következő feladatokat hajtja végre: 

* Ellenőrzi a borítékot a kereskedelmi partneri szerződés alapján.
* A megegyezést a küldő minősítő & azonosítójának és a fogadó minősítő & azonosítójának egyeztetésével oldja fel.
* Több tranzakcióra osztja szét a cserét, ha a csomópont több tranzakciót is tartalmaz a szerződés fogadási beállítások konfigurációja alapján.
* Kibontja a csomópontot.
* Az EDI és a Partner-specifikus tulajdonságok ellenőrzése, beleértve a következőket:
  * Az adatcsere-boríték szerkezetének érvényesítése
  * A boríték sémájának ellenőrzése a vezérlő sémája alapján
  * A tranzakció-set adatelemek sémájának érvényesítése az üzenet sémája alapján
  * A tranzakció-set adatelemeken végrehajtott EDI-érvényesítés
* Ellenőrzi, hogy az adatcsere, a csoport és a tranzakciónapló-vezérlők száma nem ismétlődik-e (ha be van állítva) 
  * Ellenőrzi az adatcsere-vezérlési számot a korábban fogadott adatváltozásokkal szemben. 
  * Ellenőrzi a csoport vezérlőelem számát a csomóponton lévő más csoportok vezérlőelem-számokkal. 
  * Ellenőrzi a tranzakciónapló-vezérlő számát az adott csoportban lévő más tranzakciónapló-vezérlők számával.
* Feldarabolja a csomópontot a tranzakciós készletekbe, vagy megőrzi a teljes adatcsomópontot:
  * Csomópont felosztása tranzakciónaplóként – a tranzakciók felfüggesztése a következő hiba miatt: felosztás tranzakciós készletekre, és az egyes tranzakciótípusok elemzése. 
  A EDIFACT-dekódolási művelet csak azokat a tranzakciónaplókat jeleníti meg, amelyek nem tudják érvényesíteni az ellenőrzést `badMessages` , és a fennmaradó tranzakciókat a következőre küldi: `goodMessages` .
  * Csomópont felosztása tranzakciótípusokként – adatcsere felfüggesztése a következő hiba miatt: a rendszer elosztja a csomópontot a tranzakciónaplók között, és elemzi az egyes tranzakciós készleteket. 
  Ha a csomópont egy vagy több tranzakciójának ellenőrzése sikertelen, a EDIFACT dekódolása művelet a csomóponton lévő összes tranzakciós készletet kiírja a következőre: `badMessages` .
  * Adatcsere megőrzése – tranzakciók felfüggesztése hiba esetén: őrizze meg a cserét, és dolgozza fel a teljes batchd-adatcserét. 
  A EDIFACT-dekódolási művelet csak azokat a tranzakciónaplókat jeleníti meg, amelyek nem tudják érvényesíteni az ellenőrzést `badMessages` , és a fennmaradó tranzakciókat a következőre küldi: `goodMessages` .
  * Adatcsere megőrzése – az adatcsere felfüggesztése hiba esetén: őrizze meg a cserét, és dolgozza fel a teljes batchd-adatcserét. 
  Ha a csomópont egy vagy több tranzakciójának ellenőrzése sikertelen, a EDIFACT dekódolása művelet a csomóponton lévő összes tranzakciós készletet kiírja a következőre: `badMessages` .
* Létrehoz egy technikai (vezérlő) és/vagy funkcionális visszaigazolást (ha be van állítva).
  * A technikai nyugtázás vagy a CONTRL ACK a teljes fogadott adatcsere szintaktikai vizsgálatának eredményét jelenti.
  * A funkcionális nyugták elfogadják vagy elutasítja a fogadott adatcserét vagy csoportot

## <a name="view-swagger-file"></a>Hencegő fájl megtekintése
A EDIFACT-összekötőhöz tartozó felvágási részletek megtekintéséhez lásd: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Következő lépések
[További információ a Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack") 

