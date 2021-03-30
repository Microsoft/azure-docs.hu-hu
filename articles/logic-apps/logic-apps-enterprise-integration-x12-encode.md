---
title: X12-üzenetek kódolása
description: Ellenőrizze az EDIt és alakítsa át az XML-kódolású üzeneteket a Azure Logic Appsban lévő X12-üzenet kódolóval Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792347"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-üzenetek kódolása az Azure Logic Appsben az Enterprise Integration Packkel

Az X12-üzenetek kódolása összekötővel ellenőrizheti az EDI-t és a partnerspecifikus tulajdonságokat, XML-kódolású üzeneteket konvertálhat át EDI-tranzakciókészletekké az üzenetváltásban, valamint műszaki nyugtázást és/vagy funkcionális nyugtázást igényelhet.
Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő triggerhez a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy már definiált és az Azure-előfizetéshez társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) . Rendelkeznie kell egy integrációs fiókkal a kódolás X12 üzenet-összekötő használatához.
* Legalább két olyan [partner](logic-apps-enterprise-integration-partners.md) , amely már definiálva van az integrációs fiókban
* Az integrációs fiókban már definiált [X12-szerződés](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>X12-üzenetek kódolása

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. Az X12 kódolása nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelem eseményindítóhoz. A Logic app Designerben adjon hozzá egy triggert, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be a "x12" kifejezést a szűrőhöz. Válassza ki **a X12-kódolást, hogy X12 az üzenetet a szerződés neve** vagy a **X12-kódolás a X12 üzenet identitások alapján**.
   
    ![A "x12" kifejezés keresése](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Ha korábban nem hozott létre kapcsolatot az integrációs fiókkal, a rendszer felszólítja, hogy hozza létre a kapcsolatot most. Nevezze el a kapcsolatot, majd válassza ki a csatlakoztatni kívánt integrációs fiókot. 
   
    ![integrációs fiók kapcsolatai](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    A csillaggal rendelkező tulajdonságok megadása kötelező.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolatok neve * |Adja meg a kapcsolatok nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen található. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinéznie. A kapcsolódás létrehozásához kattintson a **Létrehozás** gombra.

    ![integrációs fiók-csatlakozás létrehozva](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Ezzel létrejön a kapcsolatok.

    ![integrációs fiók kapcsolatának részletei](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>X12-üzenetek kódolása a szerződés neve szerint

Ha úgy döntött, hogy a X12-üzeneteket a szerződés neve szerint kódolja, nyissa meg a **X12-szerződés nevét** , adja meg vagy válassza ki a meglévő X12-szerződést. Adja meg a kódolni kívánt XML-üzenetet.

![Adja meg a X12-szerződés nevét és az XML-üzenetet a kódoláshoz](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12-üzenetek kódolása identitások szerint

Ha úgy dönt, hogy identitások alapján kódolja a X12-üzeneteket, adja meg a küldő azonosítóját, a küldő minősítőjét, a fogadó azonosítóját, valamint a fogadói minősítőt a X12-szerződésben konfigurált módon. Válassza ki a kódolni kívánt XML-üzenetet.
   
![Adja meg a küldő és a fogadó identitását, és válassza ki a kódolni kívánt XML-üzenetet.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Az X12-kódolás részletei

A X12 kódolású összekötő a következő feladatokat hajtja végre:

* A szerződés feloldása a feladó és a fogadó környezeti tulajdonságainak egyeztetésével.
* Szerializálja az EDI-adatcserét, és átalakítja az XML-kódolású üzeneteket az adatcsere EDI-tranzakciós csoportjaiba.
* A tranzakciónapló fejlécének és a pótkocsi szegmensének alkalmazása
* Létrehoz egy Interchange Control számot, egy csoport vezérlőelem-számot és egy tranzakciónapló-vezérlő számot az egyes kimenő adatcserékhez
* A hasznos adatokban szereplő elválasztók cseréje
* Az EDI és a Partner-specifikus tulajdonságok ellenőrzése
  * A tranzakció-set adatelemek sémájának érvényesítése az üzenet sémája alapján
  * A tranzakció-set adatelemeken végrehajtott EDI-ellenőrzés.
  * Kiterjesztett ellenőrzés a tranzakció-set adatelemeken
* Technikai és/vagy működési Visszaigazolás kérése (ha be van állítva).
  * A technikai nyugtázás a fejléc-ellenőrzés eredményeképpen jön létre. A technikai nyugtázás jelentést készít a Interchange fejléc és a trailer feldolgozásának állapotáról a címzett
  * A rendszer a törzs érvényesítésének eredményeképpen létrehoz egy funkcionális visszaigazolást. A funkcionális nyugtázási jelentések minden hibát észleltek a fogadott dokumentum feldolgozása során

## <a name="view-the-swagger"></a>A hencegés megtekintése
Tekintse meg a [hencegés részleteit](/connectors/x12/). 

## <a name="next-steps"></a>Következő lépések
[További információ a Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack") 

