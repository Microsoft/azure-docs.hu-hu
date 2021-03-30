---
title: A következő művelet késleltetése a munkafolyamatokban
description: Várjon, amíg a következő műveletet a Logic app-munkafolyamatokban a késleltetés vagy a késleltetés használatával hajtsa végre Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74787336"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>A következő művelet futásának késleltetése Azure Logic Apps

Ha azt szeretné, hogy a logikai alkalmazás a következő művelet futtatása előtt várjon egy ideig, akkor a logikai alkalmazás munkafolyamatában lévő művelet előtt adja hozzá a beépített **késleltetés-ütemezett** műveletet. Másik lehetőségként hozzáadhatja a beépített **késleltetést, amíg az ütemezési** művelet megvárja a következő művelet futtatása előtt megadott dátumot és időpontot. A beépített ütemezett műveletekkel és eseményindítókkal kapcsolatos további információkért lásd: [ismétlődő automatizált, feladatok és munkafolyamatok ütemezett és futtatása Azure Logic Appssal](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Késleltetés**: várjon a megadott számú időegységre (például másodperc, perc, óra, nap, hét vagy hónap) a következő művelet futtatása előtt.

* **Késleltetés eddig**: Várjon, amíg a következő művelet futtatása előtt a megadott dátumra és időpontra nem kerül sor.

Íme néhány példa arra, hogyan használhatja ezeket a műveleteket:

* Várjon, amíg egy hétköznapon el nem küldi az állapot-frissítést e-mailben.

* Késleltetheti a munkafolyamatot, amíg egy HTTP-hívás be nem fejeződik az adatok folytatása és beolvasása előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* A [Logic apps](../logic-apps/logic-apps-overview.md)alapszintű ismerete. A művelet használata előtt a logikai alkalmazásnak először egy triggerrel kell kezdődnie. A késleltetési művelet hozzáadása előtt bármely olyan triggert használhat, amelyet szeretne, és további műveleteket is hozzáadhat. Ez a témakör egy Office 365 Outlook-triggert használ. Ha most ismerkedik a Logic apps szolgáltatással, Ismerje meg, [hogyan hozhatja létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Késleltetési művelet hozzáadása

1. A Logic app Designerben abban a lépésben, ahová hozzá szeretné adni a késleltetés műveletet, válassza az **új lépés** lehetőséget.

   A lépések közötti késleltetési művelet hozzáadásához mozgassa a mutatót a lépéseket összekötő nyíl fölé. Válassza ki a megjelenő pluszjelet (+), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be a "késleltetés" kifejezést a szűrőként. A műveletek listából válassza a következő műveletet: **delay**

   !["Késleltetés" művelet hozzáadása](./media/connectors-native-delay/add-delay-action.png)

1. Itt adhatja meg, hogy mennyi ideig kell várni a következő művelet futtatása előtt.

   ![A késleltetés időtartamának megadása](./media/connectors-native-delay/delay-time-intervals.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Description |
   |----------|-----------|----------|------|-------------|
   | Darabszám | count | Yes | Egész szám | A késleltetni kívánt időegységek száma |
   | Unit (Egység) | egység | Igen | Sztring | Az idő mértékegysége, például:,,,, `Second` `Minute` `Hour` `Day` `Week` , vagy `Month` |
   ||||||

1. Adja meg a munkafolyamatban futtatni kívánt egyéb műveleteket.

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>A késleltetés hozzáadása a művelethez

1. A Logic app Designerben abban a lépésben, ahová hozzá szeretné adni a késleltetés műveletet, válassza az **új lépés** lehetőséget.

   A lépések közötti késleltetési művelet hozzáadásához mozgassa a mutatót a lépéseket összekötő nyíl fölé. Válassza ki a megjelenő pluszjelet (+), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be a "késleltetés" kifejezést a szűrőként. A műveletek listából válassza ki ezt a műveletet: **késleltetés, amíg**

   !["Késleltetési idő" művelet hozzáadása](./media/connectors-native-delay/add-delay-until-action.png)

1. Adja meg annak a befejezési dátumát és időpontját, amikor folytatni szeretné a munkafolyamatot.

   ![Időbélyeg megadása a késleltetés befejezéséhez](./media/connectors-native-delay/delay-until-timestamp.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Description |
   |----------|-----------|----------|------|-------------|
   | Időbélyeg | időbélyeg | Igen | Sztring | A munkafolyamat ezen formátummal való folytatásának befejezési dátuma és időpontja: <p>ÉÉÉÉ-hh-NNTóó: PP: ssZ <p>Tehát ha például a 2017-es szeptember 18-án, 2:00 ÓRAKOR, a "2017-09-18T14:00:00Z" parancsot kell megadnia. <p>**Megjegyzés:** Ebben az időformátumban az [ISO 8601 dátum és idő specifikációjának](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) kell megfelelnie az [UTC dátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)és idő formátumban, de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Időzóna nélkül fel kell vennie a "Z" betűt a végén szóköz nélkül. Ez a "Z" a megfelelő [tengeri időpontra](https://en.wikipedia.org/wiki/Nautical_time)hivatkozik. |
   ||||||

1. Adja meg a munkafolyamatban futtatni kívánt egyéb műveleteket.

1. Ha elkészült, mentse a logikai alkalmazást.

## <a name="next-steps"></a>Következő lépések

* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)