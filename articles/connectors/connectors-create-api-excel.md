---
title: Az Excel online-ban kezelheti az adatkezelési, munkalapokat és táblázatokat
description: A munkalapokon és a táblákban lévő, az Excel online-ban vagy az Excel online-ban található OneDrive Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400723"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Az Excel online-beli adatkezelési Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Excel Online for Business](/connectors/excelonlinebusiness/) Connector vagy az [Excel Online for OneDrive](/connectors/excelonline/) -összekötő használatával automatizált feladatokat és munkafolyamatokat hozhat létre az Excel Online for Business vagy a OneDrive adatai alapján. Ez az összekötő olyan műveleteket biztosít, amelyek segítségével dolgozhat az adataival, és kezelheti a számolótáblákat, például:

* Hozzon létre új munkalapokat és táblákat.
* Munkalapok, táblák és sorok beolvasása és kezelése.
* Egyetlen sor és egy fő oszlop hozzáadása.

Ezután a műveletek kimeneteit más szolgáltatásokkal kapcsolatos műveletekkel is használhatja. Ha például olyan műveletet használ, amely hetente hoz létre munkalapokat, egy másik műveletet is használhat, amely megerősítő e-mailt küld az Office 365 Outlook Connector használatával.

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Az [Excel Online for Business](/connectors/excelonlinebusiness/) és az [Excel Online for OneDrive](/connectors/excelonline/) -összekötők Azure Logic apps és eltérnek a [PowerApps készült Excel-összekötőtől](/connectors/excel/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Munkahelyi vagy iskolai fiók](https://www.office.com/) a munkahelyi fiókhoz vagy a személyes Microsoft-fiók

  Az Excel-adatok vesszővel tagolt (CSV) fájlként is létezhetnek egy tárolási mappában, például a OneDrive-ben. 
  Ugyanezt a CSV-fájlt is használhatja a [sima fájl összekötővel](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni az Excel online-adatait. Ez az összekötő csak műveleteket biztosít, ezért a logikai alkalmazás indításához válasszon egy különálló eseményindítót, például az **Ismétlődés** eseményindítóját.

## <a name="add-excel-action"></a>Excel-művelet hozzáadása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben, ha még nincs megnyitva.

1. Az trigger alatt válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be a "Excel" kifejezést a szűrőként. A műveletek listában válassza ki a kívánt műveletet.

   > [!NOTE]
   > A Logic app Designer nem tud betölteni 100 vagy több oszlopot tartalmazó táblákat. Ha lehetséges, csökkentse a kiválasztott tábla oszlopainak számát, hogy a tervező betöltse a táblát.

1. Ha a rendszer kéri, jelentkezzen be a munkahelyi vagy iskolai fiókjába.

   A hitelesítő adataival engedélyezheti a logikai alkalmazásnak, hogy kapcsolatot hozzon létre az Excel online-ban, és hozzáférhessen az adataihoz.

1. Folytassa a kijelölt művelethez szükséges részletek megadását és a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljaiban leírtak szerint tekintse meg a következő összekötő-hivatkozási lapokat:

* [Vállalati Excel online](/connectors/excelonlinebusiness/)
* [Excel online OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
