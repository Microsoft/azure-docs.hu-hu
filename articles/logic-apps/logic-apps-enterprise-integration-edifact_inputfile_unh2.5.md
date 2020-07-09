---
title: UNH 2,5-szegmensek EDIFACT-üzenetekben
description: A EDIFACT-üzenetek feloldása a Azure Logic Apps 2.5-ös UNH rendelkező szegmensekkel Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792539"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>EDIFACT-dokumentumok kezelése UNH 2.5-es szegmensekkel Azure Logic Apps

Ha egy UNH 2.5-ös szegmens létezik egy EDIFACT-dokumentumban, a rendszer a szegmenst használja a séma kereséséhez. Ebben a példában a EDIFACT-üzenetben a UNH mező a következő `EAN008` :

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Az üzenet kezeléséhez kövesse az alábbi lépéseket:

1. Frissítse a sémát.

1. Keresse meg a szerződés beállításait.

## <a name="update-the-schema"></a>A séma frissítése

Az üzenet feldolgozásához olyan sémát kell központilag telepíteni, amely rendelkezik a UNH 2.5-ös gyökértartomány-névvel. A minta UNH mezőjében például a séma gyökerének neve szerepel `EFACT_D03B_ORDERS_EAN008` . Mindegyikhez `D03B_ORDERS` eltérő unh 2.5-es szegmenssel kell telepítenie egy különálló sémát.

## <a name="add-schema-to-edifact-agreement"></a>Séma hozzáadása a EDIFACT-szerződéshez

### <a name="edifact-decode"></a>EDIFACT dekódolása

A bejövő üzenet dekódolásához állítsa be a sémát a EDIFACT-szerződés fogadási beállításaiban:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az integrációs fiókját.

1. Adja hozzá a sémát az integrációs fiókhoz.

1. Konfigurálja a sémát a EDIFACT-szerződés fogadási beállításai között.

1. Válassza ki a EDIFACT szerződést, és válassza a **Szerkesztés JSON-ként**lehetőséget. Adja hozzá a UNH 2.5 értéket a fogadási megállapodás `schemaReferences` szakaszához:

   ![UNH 2.5 hozzáadása a szerződés fogadásához](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT kódolása

A bejövő üzenet kódolásához konfigurálja a sémát a EDIFACT-szerződés küldési beállításai között.

1. A [Azure Portal](https://portal.azure.com)nyissa meg az integrációs fiókját.

1. Adja hozzá a sémát az integrációs fiókhoz.

1. Konfigurálja a sémát a EDIFACT-szerződés küldési beállításai között.

1. Válassza ki a EDIFACT szerződést, és kattintson **a Szerkesztés JSON-ként**elemre.  UNH 2.5 érték hozzáadása a szerződés küldése **schemaReferences**

1. Válassza ki a EDIFACT szerződést, és válassza a **Szerkesztés JSON-ként**lehetőséget. Adja hozzá a UNH 2.5 értéket a küldési szerződés `schemaReferences` szakaszhoz:

   ![A UNH 2.5 hozzáadása a szerződés elküldéséhez](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>További lépések

* További információ az [integrációs fiókokra vonatkozó szerződésekről](../logic-apps/logic-apps-enterprise-integration-agreements.md)