---
title: Integrációs fiókhoz tartozó összetevők metaadatainak kezelése
description: Az Azure Logic Apps integrációs fiókjainak hozzáadása vagy beolvasása az Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792472"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Az integrációs fiókok összetevői metaadatainak kezelése az Azure Logic Apps és az Enterprise Integration Pack segítségével

Az integrációs fiókokban megadhatja az összetevők egyéni metaadatait, és lekérheti ezeket a metaadatokat a futtatás közben a logikai alkalmazás általi használathoz. Például megadhatja az összetevők metaadatait, például a partnereket, a szerződéseket, a sémákat és a térképeket – az összes tároló-metaadatot a kulcs-érték párokkal. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Olyan alapszintű [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely rendelkezik olyan összetevőkkel, amelyekhez metaadatokat kíván hozzáadni, például: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Megállapodás](logic-apps-enterprise-integration-agreements.md)
  * [Séma](logic-apps-enterprise-integration-schemas.md)
  * [Térkép](logic-apps-enterprise-integration-maps.md)

* Az integrációs fiókhoz és a használni kívánt összetevő-metaadatokhoz kapcsolódó logikai alkalmazás. Ha a logikai alkalmazás még nincs összekapcsolva, ismerkedjen meg a [Logic apps integrációs fiókokkal való összekapcsolásával](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Ha még nem rendelkezik logikai alkalmazással, Ismerje meg, [hogyan hozhat létre logikai alkalmazásokat](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Adja meg az összetevő-metaadatok kezeléséhez használni kívánt triggert és műveleteket. Vagy ha csak ki szeretné próbálni a dolgokat, adjon hozzá egy triggert, például a **kérést** vagy a **http** -t a logikai alkalmazáshoz.

## <a name="add-metadata-to-artifacts"></a>Metaadatok hozzáadása az összetevőkhöz

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a> az Azure-fiókja hitelesítő adataival. Keresse meg és nyissa meg az integrációs fiókját.

1. Válassza ki azt az összetevőt, amelyhez metaadatokat szeretne felvenni, majd válassza a **Szerkesztés** lehetőséget. Adja meg az adott összetevő metaadat-részleteit, például:

   ![Metaadatok megadása](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Ha elkészült, válassza az **OK** gombot.

1. Ha meg szeretné tekinteni a metaadatokat az integrációs fiókhoz tartozó JavaScript Object Notation (JSON) definíciójában, válassza a **Szerkesztés JSON-ként** lehetőséget a JSON-szerkesztő megnyitásához: 

   ![JSON a partner metaadatainak](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Összetevők metaadatainak beolvasása

1. A Azure Portal nyissa meg a kívánt integrációs fiókhoz csatolt logikai alkalmazást. 

1. Ha a Logic app Designerben hozzáadja a metaadatok beolvasásához szükséges lépést a munkafolyamat trigger vagy utolsó művelete alatt, válassza az **új lépés**  >  **művelet hozzáadása lehetőséget**. 

1. A keresőmezőbe írja be az "integrációs fiók" kifejezést. A keresőmező alatt válassza az **összes** lehetőséget. A műveletek listából válassza a következő műveletet: **integrációs fiók összetevő keresése – integrációs fiók**

   ![Az "integrációs fiók összetevő-keresés" kiválasztása](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Adja meg ezt az információt a keresett összetevőhöz:

   | Tulajdonság | Kötelező | Érték | Leírás | 
   |----------|---------|-------|-------------| 
   | **Összetevő típusa** | Yes | **Séma**, **Térkép**, **partner**, **Szerződés** vagy egyéni típus | A kívánt összetevő típusa | 
   | **Összetevő neve** | Yes | <*összetevő – név*> | A kívánt összetevő neve | 
   ||| 

   Tegyük fel például, hogy egy kereskedelmi partneri összetevő metaadatait szeretné lekérni:

   ![Válassza ki az összetevő típusát, és adja meg az összetevő nevét](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adja hozzá azt a műveletet, amelyet a metaadatok kezelésére kíván használni, például:

   1. Az **integrációs fiók összetevője keresési** művelet területen válassza a **következő lépés** lehetőséget, majd válassza a **művelet hozzáadása** lehetőséget. 

   1. A keresőmezőbe írja be a "http" kifejezést. A keresőmezőbe válassza a **beépített modulok** elemet, majd válassza a következő műveletet: **http-http**

      ![HTTP-művelet hozzáadása](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Adja meg a kezelni kívánt összetevő-metaadatok adatait. 

      Tegyük fel például, hogy szeretné lekérni a `routingUrl` témakör korábbi részében hozzáadott metaadatokat. A következő tulajdonságértékek adhatók meg: 

      | Tulajdonság | Kötelező | Érték | Leírás | 
      |----------|----------|-------|-------------| 
      | **Metódus** | Yes | <*művelet – Futtatás*> | Az összetevőn futtatandó HTTP-művelet. Ez a HTTP-művelet például a **Get** metódust használja. | 
      | **URI** | Yes | <*metaadatok – hely*> | Ha a `routingUrl` metaadatok értékét a beolvasott összetevőből szeretné elérni, használhat egy kifejezést, például: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Fejlécek** | No | <*fejléc – értékek*> | Minden olyan fejléc kimenete a triggerből, amelyet át szeretne adni a HTTP-műveletbe. Ha például át szeretné adni az trigger `headers` tulajdonságának értékét: használhat kifejezéseket, például: <p>`@triggeroutputs()['headers']` | 
      | **Törzs** | No | <*törzs – tartalom*> | Minden más olyan tartalom, amelyet át szeretne adni a HTTP-művelet `body` tulajdonságán. Ez a példa a leletek `properties` értékeit a http-műveletnek adja át: <p>1. kattintson a **Body (törzs** ) tulajdonságra, hogy a dinamikus tartalom lista megjelenjen. Ha nem jelenik meg a tulajdonságok, válassza a **továbbiak** lehetőséget. <br>2. a dinamikus tartalom listából válassza a **Tulajdonságok** lehetőséget az **integrációs fiók** összetevőinek keresése területen. | 
      |||| 

      Például:

      ![Értékek és kifejezések megadása HTTP-művelethez](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. A HTTP-művelethez megadott információk megtekintéséhez tekintse meg a logikai alkalmazás JSON-definícióját. A Logic app Designer eszköztárán válassza a **kód nézet** lehetőséget, hogy az alkalmazás JSON-definíciója megjelenjen, például:

      ![Logikai alkalmazás JSON-definíciója](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      A Logic app Designerre való váltás után a már használt kifejezések megoldódott, például:

      ![Megoldott kifejezések a Logic app Designerben](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Következő lépések

* [További információ a szerződésekről](logic-apps-enterprise-integration-agreements.md)
