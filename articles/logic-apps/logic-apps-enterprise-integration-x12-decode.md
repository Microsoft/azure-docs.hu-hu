---
title: X12-üzenetek dekódolása
description: Az EDI érvényesítése és a X12-üzenet dekóderrel való Azure Logic Appsának előállítása Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792367"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-üzenetek dekódolása az Azure Logic Appsben az Enterprise Integration Packkel

Az X12-üzenet dekódolása összekötővel ellenőrizheti az envelope-ot egy kereskedelmi partnerrel kötött megállapodás alapján, ellenőrizheti az EDI-t és a partnerspecifikus tulajdonságokat, feloszthatja az üzenetváltásokat tranzakciókészletekre vagy megőrizheti a teljes üzenetváltásokat, és nyugtázásokat hozhat létre a feldolgozott tranzakciókhoz. Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő triggerhez a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy már definiált és az Azure-előfizetéshez társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) . A dekódolási X12 üzenet-összekötő használatához integrációs fiókkal kell rendelkeznie.
* Legalább két olyan [partner](logic-apps-enterprise-integration-partners.md) , amely már definiálva van az integrációs fiókban
* Az integrációs fiókban már definiált [X12-szerződés](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>X12-üzenetek dekódolása

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. Az X12 dekódolása nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelem eseményindítóhoz. A Logic app Designerben adjon hozzá egy triggert, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be a "x12" kifejezést a szűrőhöz. Válassza a **X12-X12-üzenet dekódolása** lehetőséget.
   
    ![A "x12" kifejezés keresése](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Ha korábban nem hozott létre kapcsolatot az integrációs fiókkal, a rendszer felszólítja, hogy hozza létre a kapcsolatot most. Nevezze el a kapcsolatot, majd válassza ki a csatlakoztatni kívánt integrációs fiókot. 

    ![Integrációs fiók kapcsolati adatainak megadása](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    A csillaggal rendelkező tulajdonságok megadása kötelező.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolatok neve * |Adja meg a kapcsolatok nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen található. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinéznie. A kapcsolódás létrehozásához kattintson a **Létrehozás** gombra.
   
    ![integrációs fiók kapcsolatának részletei](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Miután létrejött a kapcsolódás, ahogy az ebben a példában is látható, válassza ki a dekódoláshoz használandó X12-fájl üzenetet.

    ![integrációs fiók-csatlakozás létrehozva](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Például:

    ![X12-üzenet kiválasztása a dekódoláshoz](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > A tényleges üzenet tartalma vagy az üzenet tömbje, amely jó vagy rossz, Base64 kódolású. Ezért olyan kifejezést kell megadnia, amely feldolgozza ezt a tartalmat.
   > Íme egy példa, amely a tartalmat XML formátumban dolgozza fel, vagy a tervezőben a Expression Builder használatával megadható.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Tartalom – példa](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Az X12-dekódolás részletei

A X12 dekódolási összekötő a következő feladatokat hajtja végre:

* Ellenőrzi a borítékot a kereskedelmi partneri szerződés alapján
* Az EDI és a Partner-specifikus tulajdonságok ellenőrzése
  * EDI-alapú szerkezeti ellenőrzés és bővített séma ellenőrzése
  * Az adatcsere-boríték szerkezetének érvényesítése.
  * A keret sémájának ellenőrzése a vezérlő sémán keresztül.
  * A Transaction-set adatelemek sémájának érvényesítése az üzenet sémája alapján.
  * A tranzakció-set adatelemeken végrehajtott EDI-érvényesítés 
* Ellenőrzi, hogy az adatcsere, a csoport és a tranzakciónapló-vezérlők száma nem duplikált-e
  * Ellenőrzi az adatcsere-vezérlési számot a korábban fogadott adatváltozásokkal szemben.
  * Ellenőrzi a csoport vezérlőelem számát a csomóponton lévő más csoportok vezérlőelem-számokkal.
  * Ellenőrzi a tranzakciónapló-vezérlő számát az adott csoportban lévő más tranzakciónapló-vezérlők számával.
* Feldarabolja a csomópontot a tranzakciós készletekbe, vagy megőrzi a teljes adatcsomópontot:
  * Csomópont felosztása tranzakciónaplóként – a tranzakciók felfüggesztése a következő hiba miatt: felosztás tranzakciós készletekre, és az egyes tranzakciótípusok elemzése. 
  A X12-dekódolási művelet csak azokat a tranzakciónaplókat jeleníti meg, amelyek nem tudják érvényesíteni az ellenőrzést `badMessages` , és a fennmaradó tranzakciókat a következőre küldi: `goodMessages` .
  * Csomópont felosztása tranzakciótípusokként – adatcsere felfüggesztése a következő hiba miatt: a rendszer elosztja a csomópontot a tranzakciónaplók között, és elemzi az egyes tranzakciós készleteket. 
  Ha a csomópont egy vagy több tranzakciójának ellenőrzése sikertelen, a X12 dekódolása művelet a csomóponton lévő összes tranzakciós készletet kiírja a következőre: `badMessages` .
  * Adatcsere megőrzése – tranzakciók felfüggesztése hiba esetén: őrizze meg a cserét, és dolgozza fel a teljes batchd-adatcserét. 
  A X12-dekódolási művelet csak azokat a tranzakciónaplókat jeleníti meg, amelyek nem tudják érvényesíteni az ellenőrzést `badMessages` , és a fennmaradó tranzakciókat a következőre küldi: `goodMessages` .
  * Adatcsere megőrzése – az adatcsere felfüggesztése hiba esetén: őrizze meg a cserét, és dolgozza fel a teljes batchd-adatcserét. 
  Ha a csomópont egy vagy több tranzakciójának ellenőrzése sikertelen, a X12 dekódolása művelet a csomóponton lévő összes tranzakciós készletet kiírja a következőre: `badMessages` . 
* Technikai és/vagy funkcionális nyugtát állít elő (ha konfigurálva van).
  * A technikai nyugtázás a fejléc-ellenőrzés eredményeképpen jön létre. A technikai visszaigazolás a csomópontok fejlécének és a pótkocsinak a címzett általi feldolgozásának állapotát jelenti.
  * A rendszer a törzs érvényesítésének eredményeképpen létrehoz egy funkcionális visszaigazolást. A funkcionális nyugtázási jelentések minden hibát észleltek a fogadott dokumentum feldolgozása során

## <a name="view-the-swagger"></a>A hencegés megtekintése
Tekintse meg a [hencegés részleteit](/connectors/x12/). 

## <a name="next-steps"></a>Következő lépések
[További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack") 

