---
title: EDIFACT-üzenetek kódolása
description: Az EDI érvényesítése és az XML-kód előállítása a EDIFACT-üzenetkezelővel Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106525"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps EDIFACT-üzeneteinek kódolása Enterprise Integration Pack

Az EDIFACT-üzenet kódolása összekötővel ellenőrizheti az EDI-t és a partnerspecifikus tulajdonságokat, létrehozhat egy XML-dokumentumot az egyes tranzakciótípusokhoz, és igényelhet műszaki nyugtázást, funkcionális nyugtázást vagy mindkettőt.
Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő triggerhez a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Az alábbi elemek szükségesek:

* Egy Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy már definiált és az Azure-előfizetéshez társított [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) . Rendelkeznie kell egy integrációs fiókkal a kódolás EDIFACT üzenet-összekötő használatához. 
* Legalább két olyan [partner](logic-apps-enterprise-integration-partners.md) , amely már definiálva van az integrációs fiókban
* Az integrációs fiókban már definiált [EDIFACT-szerződés](logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>EDIFACT-üzenetek kódolása

> [!IMPORTANT]
> Az EDIFACT-összekötő csak UTF-8 karaktereket támogat.
> Ha a kimenet váratlan karaktereket tartalmaz, ellenőrizze, hogy a EDIFACT-üzenetei az UTF-8 karakterkészletet használják-e.

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. Az EDIFACT kódolása nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelem eseményindítóhoz. A Logic app Designerben adjon hozzá egy triggert, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be szűrőként a "EDIFACT" kifejezést. Válassza a **EDIFACT-üzenet kódolása a szerződés neve szerint** vagy **a kódolás EDIFACT üzenet alapján identitások szerint** lehetőséget.
   
    ![EDIFACT keresése](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Ha korábban nem hozott létre kapcsolatot az integrációs fiókkal, a rendszer felszólítja, hogy hozza létre a kapcsolatot most. Nevezze el a kapcsolatot, majd válassza ki a csatlakoztatni kívánt integrációs fiókot.

    ![integrációs fiókhoz való csatlakozás létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    A csillaggal rendelkező tulajdonságok megadása kötelező.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolatok neve * |Adja meg a kapcsolatok nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen található. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinéznie. A kapcsolódás létrehozásához kattintson a **Létrehozás** gombra.

    ![integrációs fiók kapcsolatának részletei](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Ezzel létrejön a kapcsolatok.

    ![integrációs fiók-csatlakozás létrehozva](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>EDIFACT-üzenet kódolása a szerződés neve szerint

Ha úgy döntött, hogy a EDIFACT-üzeneteket a szerződés neve szerint kódolja, nyissa meg a **EDIFACT-szerződés** nevét, adja meg vagy válassza ki a EDIFACT-szerződés nevét. Adja meg a kódolni kívánt XML-üzenetet.

![Adja meg a EDIFACT-szerződés nevét és az XML-üzenetet a kódoláshoz](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>EDIFACT-üzenet kódolása identitások szerint

Ha úgy dönt, hogy identitások alapján kódolja a EDIFACT-üzeneteket, adja meg a küldő azonosítóját, a küldő minősítőjét, a fogadó azonosítóját, valamint a fogadói minősítőt a EDIFACT-szerződésben konfigurált módon. Válassza ki a kódolni kívánt XML-üzenetet.

![Adja meg a küldő és a fogadó identitását, és válassza ki a kódolni kívánt XML-üzenetet.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Az EDIFACT-kódolás részletei

A kódolás EDIFACT-összekötő a következő feladatokat hajtja végre: 

* A szerződés feloldása a küldő minősítő & azonosító és a fogadó minősítő és azonosítójának egyeztetésével
* Szerializálja az EDI-adatcserét, és átalakítja az XML-kódolású üzeneteket az adatcsere EDI-tranzakciós csoportjaiba.
* A tranzakciónapló fejlécének és a pótkocsi szegmensének alkalmazása
* Létrehoz egy Interchange Control számot, egy csoport vezérlőelem-számot és egy tranzakciónapló-vezérlő számot az egyes kimenő adatcserékhez
* A hasznos adatokban szereplő elválasztók cseréje
* Az EDI és a Partner-specifikus tulajdonságok ellenőrzése
  * A Transaction-set adatelemek sémájának érvényesítése az üzenet sémája alapján.
  * A tranzakció-set adatelemeken végrehajtott EDI-ellenőrzés.
  * Kiterjesztett ellenőrzés a tranzakció-set adatelemeken
* Létrehoz egy XML-dokumentumot az egyes tranzakciótípusok számára.
* Technikai és/vagy működési Visszaigazolás kérése (ha be van állítva).
  * Technikai visszaigazolás esetén a CONTRL üzenet egy adatcsere fogadását jelzi.
  * Funkcionális visszaigazolásként a CONTRL üzenet a kapott adatcsere, csoport vagy üzenet elfogadását vagy elutasítását jelzi a hibák és a nem támogatott funkciók listájával.

## <a name="view-swagger-file"></a>Hencegő fájl megtekintése
A EDIFACT-összekötőhöz tartozó felvágási részletek megtekintéséhez lásd: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Következő lépések
[További információ a Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack") 

