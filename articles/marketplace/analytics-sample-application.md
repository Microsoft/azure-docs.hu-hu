---
title: Példa a kereskedelmi piactér elemzési adatokhoz való hozzáférésre
description: A minta alkalmazás használatával saját kereskedelmi Marketplace Analytics-alkalmazást hozhat létre.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583859"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Példa a kereskedelmi piactér elemzési adatokhoz való hozzáférésre

A minta alkalmazások C# és JAVA nyelven jönnek létre, és elérhetők a [githubon](https://github.com/partneranalytics).

- [C#-minta alkalmazása](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [JAVA-minta alkalmazás](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Dönthet úgy, hogy inspirációt készít a minta alkalmazásból, és bármilyen nyelven felépítheti saját alkalmazását.

A minta alkalmazás a következő célokat éri el:

- Létrehoz egy Azure Active Directory (Azure AD) tokent.
- Lekéri az elérhető adatkészleteket.
- Felhasználó által definiált lekérdezések létrehozása.
- Felhasználó által definiált és rendszerlekérdezéseket kap.
- Egy jelentést ütemezhet.

A minta alkalmazás nem fedi le az API-k más funkciókhoz való meghívásának módszerét. A többi API meghívásának folyamata azonban ugyanaz marad, mint a fent leírt módon.

## <a name="how-to-run-the-application"></a>Az alkalmazás futtatása

1. A tárház klónozása egy helyi rendszerbe ezzel a paranccsal:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > További útmutatásért tekintse meg a `ProgrammaticExportSampleAppISV/README.md` fájlt a GitHub- [tárházban](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git).

1. Az alkalmazás gyors futtatásához frissítse az ügyfél-azonosítót és az ügyfél titkos kulcsát a **appsettings.Development.js**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="A appsettings.Development.jsfájl részleteit mutatja be.":::

Az alkalmazás futtatása elindít egy helyi webkiszolgálót, és egy oldal megnyílik ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Az ütemezett jelentés oldalát mutatja be.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Ez a lap API-hívásokat végez a helyi gépen futó webkiszolgálón, amely viszont a tényleges programozott hozzáférési API-hívásokat fogja végezni.

## <a name="code-snippets"></a>Kódrészletek

A programozott hozzáférési API-hívásokhoz tartozó C#-kód alapszintű szerkezete a következő:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Az API-hívások képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k](analytics-available-apis.md)
