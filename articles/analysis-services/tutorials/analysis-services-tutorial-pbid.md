---
title: Oktatóanyag – Azure Analysis Services összekötése a Power BI Desktoptal | Microsoft Docs
author: minewiskan
description: Ebből az oktatóanyagból megtudhatja, hogyan szerezhet be Analysis Services kiszolgáló nevét a Azure Portal, majd a Power BI Desktop használatával csatlakozhat a kiszolgálóhoz.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 46805427e6d00e858cf869eef68bf68549f57fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018117"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Oktatóanyag: Csatlakozás a Power BI Desktoppal

Ebben az oktatóanyagban a Power BI Desktopot fogja használni arra, hogy csatlakozzon az adventureworks minta-modelladatbázishoz a kiszolgálóján. A végrehajtandó feladat azt szimulálja, ahogyan egy felhasználó jellemzően csatlakozik a modellhez és létrehoz egy egyszerű jelentést a modelladatok alapján.

> [!div class="checklist"]
> * Kiszolgálónév lekérdezése a portálról
> * Csatlakozás a Power BI Desktop használatával
> * Egyszerű jelentés létrehozása

## <a name="prerequisites"></a>Előfeltételek

- [Hozzáadta az adventureworks modelladatbázist](../analysis-services-create-sample-model.md) a kiszolgálójához.
- [*Olvasási*](../analysis-services-server-admins.md) engedéllyel rendelkezik az adventureworks minta-modelladatbázishoz.
- [Telepítette a legújabb Power BI Desktopot](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Ebben az oktatóanyagban bemutatjuk a portálon, hogy csak a kiszolgálónevet kapja meg. A felhasználóknak általában a kiszolgáló rendszergazdája adja meg a kiszolgáló nevét.

Jelentkezzen be a [portálra](https://portal.azure.com/).

## <a name="get-server-name"></a>Kiszolgálónév lekérése
Ahhoz, hogy a Power BI Desktopból csatlakozni tudjon a kiszolgálójához, szüksége lesz a kiszolgáló nevére. A kiszolgálónevet a portálról kérheti le.

Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
   ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktopban

1. Power bi Desktop kattintson az   >  **Azure**-  >  **Azure Analysis Services adatbázis** beolvasása elemre.

   ![Csatlakozás az Adatok lekérése alatt](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. A **Kiszolgáló** mezőbe illessze be a kiszolgáló nevét, az **Adatbázis** mezőben adja meg az **adventureworks** nevet, majd kattintson az **OK** gombra.

   ![Kiszolgálónév és modelladatbázis megadása](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Amikor a rendszer erre kéri, adja meg a hitelesítő adatait. A megadott fióknak legalább olvasási engedéllyel kell rendelkeznie az adventureworks minta-modelladatbázishoz.

    Az adventureworks modell egy üres jelentéssel, Jelentés nézetben nyílik meg a Power BI Desktopban. A **Mezők** listában minden nem rejtett modellobjektum megjelenik. A csatlakozás állapota a jobb alsó sarokban látható.

4. A **VIZUALIZÁCIÓK** között válassza ki a **Fürtözött sávdiagramot**, majd kattintson a **Formázás** (festékhenger ikon) lehetőségre, és kapcsolja be az **Adatcímkéket**. 

   ![Vizualizációk](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Az   >  **Internet Sales** tábla mezők területen válassza az **internetes eladások összesen** és a **Margók** lehetőséget. A **Product Category** (Termékkategória) táblázatban jelölje ki a **Product Category Name** (Termékkategória neve) mezőt.

   ![Jelentés befejezése](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Szánjon néhány percet az adventureworks mintamodell megismerésére különböző vizualizációk létrehozásával és adatok és metrikák alapján végzett szeleteléssel. Ha elégedett a jelentésével, ne felejtse el kimenteni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha többé nincs szüksége rá, akkor ne mentse a jelentést, vagy törölje a fájlt, ha már kimentette.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban a Power BI Desktop használatát sajátította el egy kiszolgálón lévő adatmodellhez való csatlakozásra és egy egyszerű jelentés létrehozására. Ha nem ismeri az adatmodell létrehozását, tekintse meg az [Adventure Works Internet Sales táblázatos adatmodellezési oktatóanyagot](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) a SQL Server Analysis Services dokumentációjában.