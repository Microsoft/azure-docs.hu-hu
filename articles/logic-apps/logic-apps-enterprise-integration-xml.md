---
title: XML-üzenetek és egyszerű fájlok
description: XML-üzenetek feldolgozása, ellenőrzése és átalakítása Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792151"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-üzenetek és Azure Logic Apps Enterprise Integration Pack

A [Azure Logic apps](logic-apps-overview.md)a Enterprise Integration Pack által küldött és fogadott XML-üzeneteket dolgozhatja fel. Ha BizTalk Server használta, az Enterprise Integration Pack hasonló képességeket biztosít az üzenetek átalakításához és ellenőrzéséhez, az egyszerű fájlokhoz való munka és az XPath használatával, hogy egy üzenetből gazdagítsa vagy kinyerje az adott tulajdonságokat. Ha most ismerkedik a területtel, ezek a funkciók kibővítik, hogyan dolgozza fel az üzeneteket a logikai alkalmazás munkafolyamatában. Ha például vállalatközi (B2B) forgatókönyvet használ, és adott XML-sémákkal dolgozik, akkor a Enterprise Integration Pack segítségével növelheti, hogy a vállalat hogyan dolgozza fel ezeket az üzeneteket.

A Enterprise Integration Pack például a következő funkciókat tartalmazza:

* [XML-érvényesítés](logic-apps-enterprise-integration-xml-validation.md): egy bejövő vagy kimenő XML-üzenet érvényesítése egy adott sémán.

* [XML-transzformáció](logic-apps-enterprise-integration-transform.md): az XML-üzenetek konvertálása vagy testreszabása a követelmények vagy a partnerek követelményei alapján Maps használatával.

* A [lapos fájl kódolása és a lapos fájl dekódolása](logic-apps-enterprise-integration-flatfile.md): egy egyszerű fájl kódolása vagy visszafejtése.

  Az SAP például a IDOC-fájlokat egyszerű fájlformátumban fogadja el és küldi el. Számos integrációs platform XML-üzeneteket hoz létre, beleértve a Logic Apps. Így létrehozhat egy logikai alkalmazást, amely a sima fájl kódolóját használja az XML-fájlok konvertálása egyszerű fájlokba.

* [XPath](workflow-definition-language-functions-reference.md#xpath): az üzenet gazdagítása és a megadott tulajdonságok kinyerése. Ezután a kibontott tulajdonságok segítségével átirányíthatja az üzenetet egy célhelyre vagy egy köztes végpontra.

## <a name="sample"></a>Sample

[Helyezzen üzembe egy teljesen működőképes Logic app-alkalmazást](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-mintát) a Azure Logic apps XML-szolgáltatásainak használatával.

## <a name="next-steps"></a>Következő lépések

További információ a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
