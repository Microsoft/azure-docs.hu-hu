---
title: Oktatóanyag – Mintamodell hozzáadása- Azure Analysis Services | Microsoft dokumentumok
description: Az oktatóanyagnak ez a leckéje a mintamodellek hozzáadását ismerteti az Azure Analysis Servicesben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a72236843c13e139f33a669b54f108e91679c8c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74326581"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Oktatóanyag : Mintamodell hozzáadása a portálról

Az oktatóanyag elvégzése során egyszerű táblázatos Adventure Works-modelladatbázist adhat hozzá kiszolgálójához. A mintamodell az Adventure Works Internet Sales (1200) minta-adatmodell kiegészített változata. A mintamodellek a modellkezelés, az eszközökhöz és ügyfélalkalmazásokhoz való kapcsolódás és a modelladatok lekérdezése tesztelésekor hasznosak. Ez az oktatóanyag az [Azure Portalt](https://portal.azure.com) és az [SQL Server Management Studiót](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) használja a következőkhöz: 

> [!div class="checklist"]
> * Kiegészített táblázatos adatmodell-minta hozzáadása kiszolgálóhoz 
> * Csatlakozás a modellhez az SSMS használatával

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Egy Azure Analysis Services-kiszolgáló. További tudnivalók: [Kiszolgáló létrehozása – Portal](analysis-services-create-server.md).
- Kiszolgáló-rendszergazdai engedélyek
- [SQL Server Management Stúdió](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [portálra.](https://portal.azure.com/)

## <a name="add-a-sample-model"></a>Mintamodell hozzáadása

1. A kiszolgáló **Áttekintés** lapján kattintson az **Új modell** lehetőségre.

    ![Mintamodell létrehozása](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Az **Új modell:** > **Válasszon adatforrást,** ellenőrizze, hogy a **Mintaadatok** ki van-e jelölve, majd kattintson a **Hozzáadás**gombra.

    ![Mintaadatok kijelölése](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Az **Áttekintés** lapon ellenőrizheti, hogy az `adventureworks` mintamodell hozzáadása megtörtént.

    ![Mintaadatok kijelölése](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A mintamodell gyorsítótár-memória erőforrásokat használ. Ha a mintamodellt nem használja teszteléshez, akkor ajánlott eltávolítani a kiszolgálóról.

Egy modell a következő lépések alapján törölhető egy kiszolgálóról az SSMS használatával.

1. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.

2. A **Csatlakozás kiszolgálóhoz** panelen illessze be a kiszolgáló nevét, majd a **Hitelesítés** módjaként válassza az **Active Directory – univerzális, MFA-támogatással** lehetőséget, adja meg felhasználónevét, végül kattintson a **Csatlakozás** gombra.

    ![Bejelentkezés](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. Az **Object Explorerben** kattintson a jobb gombbal az `adventureworks` mintaadatbázisra, majd kattintson a **Törlés** elemre.

    ![Mintaadatbázis törlése](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>További lépések 

Ebben az oktatóanyagban azt tanulhatta meg, hogyan adhat hozzá egyszerű mintamodellt kiszolgálójához. Most, hogy már rendelkezik modelladatbázissal, csatlakozhat hozzá az SQL Server Management Studióból, és hozzáadhat felhasználói szerepköröket. További tudnivalókat talál a következő oktatóanyagban.

> [!div class="nextstepaction"]
> [Oktatóanyag: A kiszolgáló rendszergazdájának és felhasználói szerepköreinek konfigurálása](tutorials/analysis-services-tutorial-roles.md)


