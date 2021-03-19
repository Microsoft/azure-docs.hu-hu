---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040248"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -fiók 

Ahhoz, hogy a OneDrive-fiókját a Logic Apps használatával használhassa, engedélyezni kell Logic Apps, hogy a Azure Portalhoz kapcsolódjon a OneDrive-fiókjához.

Az alábbi lépéseket követve engedélyezheti Logic Apps számára a OneDrive-fiókhoz való kapcsolódást:  

1. Jelentkezzen be az Azure Portalra. 

1. Az **Azure-szolgáltatások** területen válassza a **Logic apps** lehetőséget. Ezután válassza ki a logikai alkalmazás nevét a listából.

1. A logikai alkalmazás menüjében válassza a **Logic app Designer** elemet a **fejlesztői eszközök** területen.

1. A Logic Apps Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *OneDrive* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:

   ![Képernyőkép a Logic Apps Designerről, amely a hozzáadni kívánt OneDrive API-műveletek listáját jeleníti meg.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Ha korábban még nem hozott létre kapcsolatot a OneDrive-hez, kövesse a OneDrive hitelesítő adataival való bejelentkezéshez szükséges utasításokat:  

   ![Képernyőkép a Logic Apps Designerről, amely a OneDrive API bejelentkezési kérését jeleníti meg.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Válassza a **Bejelentkezés** lehetőséget, és adja meg felhasználónevét és jelszavát. **Bejelentkezés** kiválasztása: 

   ![Képernyőkép Microsoft-fiók bejelentkezési oldalról, OneDrive API-hitelesítéshez.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Ezekkel a hitelesítő adatokkal engedélyezhető, hogy a logikai alkalmazás hozzáférhessen a OneDrive-fiókban lévő adatokhoz. 

4. Válassza az **Igen** lehetőséget, ha engedélyezni szeretné a logikai alkalmazás számára a OneDrive-fiók használatát:  

   ![Képernyőkép a Logic Apps Microsoft-fiók engedélyezéséről, amely az engedélyezett műveleteket mutatja.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. A kapcsolatok mostantól a lépésben szerepelnek. Válassza a mentés lehetőséget, majd folytassa a logikai alkalmazás létrehozását. 

   ![Képernyőkép a Logic Apps Designerről, amely a OneDrive API-kapcsolattal rendelkező Action Editort jeleníti meg.](./media/connectors-create-api-onedrive/onedrive-5.png)
