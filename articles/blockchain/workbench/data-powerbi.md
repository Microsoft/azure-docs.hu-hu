---
title: Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban
description: Megtudhatja, hogy hogyan töltheti be és tekintheti meg az Azure Blockchain Workbench SQL-adatbázisának adatait a Microsoft Power BI-ban.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000147"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Azure Blockchain Workbench-adatok használata a Microsoft Power BI-ban

A Microsoft Power BI lehetővé teszi, hogy az SQL DB-adatbázisokból könnyedén hozzon elő hatékony jelentéseket a Power BI Desktop, majd tegye közzé azokat a alkalmazásban [https://www.powerbi.com](https://www.powerbi.com) .

Ebből a cikkből részletesen megismerheti, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához a PowerBI Desktop alkalmazásból, illetve hogyan hozhat létre és tehet közzé jelentéseket a powerbi.com-on.

## <a name="prerequisites"></a>Előfeltételek

* [Power bi Desktop](https://powerbi.microsoft.com/desktop/)letöltése.

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Power BI csatlakoztatása az Azure Blockchain Workbench-beli adatkapcsolathoz

1.  Nyissa meg a Power BI Desktopot.
2.  Válassza az **Adatok beolvasása** lehetőséget.

    ![Adatok lekérése](./media/data-powerbi/get-data.png)
3.  Válassza az **SQL Server** lehetőséget az adatforrástípusok listájából.

4.  Adja meg a kiszolgáló és az adatbázis nevét a megjelenő párbeszédpanelen. Adja meg, hogy importálni akarja-e az adatokat, vagy **DirectQuery**-lekérdezést szeretne végrehajtani. Válassza az **OK** lehetőséget.

    ![SQL Server kiválasztása](./media/data-powerbi/select-sql.png)

5.  Adja meg az adatbázis hitelesítő adatait az Azure Blockchain Workbench eléréséhez. Válassza az **Adatbázis** lehetőséget, és adja meg a hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin**, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis beállításai](./media/data-powerbi/db-settings.png)

6.  Az adatbázishoz való csatlakozás után a **Kezelő** párbeszédpanel megjeleníti az adatbázisban elérhető táblákat és nézeteket. A nézetek jelentéskészítéshez lettek tervezve, és mind **vw** előtaggal kezdődnek.

    ![Képernyőfelvétel a Power BI asztalról a navigátor párbeszédpanel és a vwContractAction kiválasztásával.](./media/data-powerbi/navigator.png)

7.  Jelölje ki a belefoglalni kívánt nézeteket. Demonstrációs célokra a **vwContractAction** tartalmazza, amely részletesen ismerteti a szerződésben vállalt műveleteket.

    ![Nézetek kiválasztása](./media/data-powerbi/select-views.png)

Ezután létrehozhatja és közzéteheti a jelentéseket a Power BI-ban megszokott munkafolyamatot követve.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)