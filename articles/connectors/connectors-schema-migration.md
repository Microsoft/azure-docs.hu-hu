---
title: Alkalmazások migrálása a legújabb sémára
description: A Logic app-munkafolyamatok JSON-definícióinak migrálása a legutóbbi munkafolyamat-definíció nyelvi sémájának verziójára
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666788"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Logikai alkalmazások migrálása a séma legújabb verziójára

A meglévő logikai alkalmazások a legújabb sémába való áthelyezéséhez kövesse az alábbi lépéseket: 

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

2. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Az eszköztáron válassza a **séma frissítése**lehetőséget.

   > [!NOTE]
   > Ha a **séma frissítése**lehetőséget választja, Azure Logic apps automatikusan futtatja az áttelepítési lépéseket, és megadja a kód kimenetét. Ezt a kimenetet használhatja a logikai alkalmazás definíciójának frissítéséhez. Ügyeljen azonban arra, hogy kövesse az ajánlott eljárásokat a következő **gyakorlati tanácsok** című szakaszban leírtak szerint.

   ![Séma frissítése](./media/connectors-schema-migration/update-schema.png)

   Megjelenik a séma frissítése lap, amely egy olyan dokumentumra mutató hivatkozást mutat be, amely leírja az új séma fejlesztéseit.

## <a name="best-practices"></a>Ajánlott eljárások

Íme néhány ajánlott eljárás a Logic apps legújabb séma-verzióra való áttelepítéséhez:

* Másolja az áttelepített szkriptet egy új logikai alkalmazásba. Ne írja felül a régi verziót, amíg el nem végzi a tesztelést, és ellenőrizze, hogy az áttelepített alkalmazás a várt módon működik-e.

* Az éles üzembe helyezés **előtt** tesztelje a logikai alkalmazást.

* Az áttelepítés befejezése után kezdje el frissíteni a logikai alkalmazásokat a [felügyelt API](../connectors/apis-list.md) -k használatára, ahol lehetséges. Kezdje például a Dropbox v2 használatát mindenhol, hogy a DropBox v1-et használja.

## <a name="next-steps"></a>Következő lépések

* Útmutató [a logikai alkalmazások manuális áttelepítéséhez](../logic-apps/logic-apps-schema-2015-08-01.md)
