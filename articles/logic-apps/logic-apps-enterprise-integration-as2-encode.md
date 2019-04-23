---
title: Kódolás AS2-üzenetek – Azure Logic Apps |} A Microsoft Docs
description: Az Azure Logic Apps és az Enterprise Integration Pack ÜZENETEKKÉNT kódolása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 455056feaa1b13022be3ab3c15880a87c50dedd9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003547"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Az Azure Logic Apps és az Enterprise Integration Pack AS2-üzenetek kódolása

Biztonságának és megbízhatóságának közben továbbítására üzenetek létrehozásához használja a kódolás AS2-üzenet connector. Ez az összekötő a digitális aláírás, titkosítás és a nyugtázás a keresztül üzenet törlése értesítések (MDN), ami szintén támogatja a letagadhatatlanság biztosít.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. A kódolás AS2-üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [AS2-egyezményt](logic-apps-enterprise-integration-as2.md) , amely már definiálva van az integrációs fiók

## <a name="encode-as2-messages"></a>AS2-üzenetek kódolása

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. A kódolás AS2-üzenet connector eseményindítók, nem rendelkezik, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3.  A keresőmezőbe adja meg a szűrőnek "AS2". Válassza ki **AS2 - kódolás AS2-üzenet**.
   
    ![Keressen a "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne. 
   
    ![integrációs fiók felé irányuló kapcsolat](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Connection Name * |Adja meg a kapcsolat bármilyen nevet. |
    | Integration Account * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

5.  Ha elkészült, a kapcsolat adatait példához hasonlóan kell kinéznie. A kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**.
   
    ![integráció a kapcsolat részletei](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. A kapcsolat létrejötte után ebben a példában látható módon, adja meg az adatokat a **AS2-a**, **AS2-azonosítók a** az a szerződés szerint és **törzs**, amely az üzenet hasznos adattartalmából.
   
    ![Adja meg a kötelező mezők](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2-kódoló részletei

A kódolás AS2-összekötő az alábbi feladatokat hajtja végre: 

* Alkalmazza az AS2/HTTP-fejlécek
* Tünetek kimenő üzenetek (Ha be van állítva)
* Kimenő üzenetek titkosítja (Ha be van állítva)
* Az üzenet tömöríti (Ha be van állítva)
* A MIME-fejlécben fájlnév továbbítására (Ha be van állítva)


  > [!NOTE]
  > Ha tanúsítványok kezelése az Azure Key Vault használ, győződjön meg arról, hogy konfigurálnia a kulcsokat, hogy lehetővé tegye a **titkosítása** műveletet.
  > Ellenkező esetben az AS2-kódolást sikertelen lesz.
  >
  > ![Keyvault visszafejti](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Ez a minta kipróbálása

Próbálja meg egy teljesen működőképes logic app és a minta AS2 forgatókönyv üzembe helyezéséhez, tekintse meg a [AS2 logikaialkalmazás-sablon és a forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/as2/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag") 

