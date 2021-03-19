---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87102720"
---
## <a name="prerequisites"></a>Előfeltételek

* [OneDrive vállalati](https://OneDrive.com) fiókhoz 

Ahhoz, hogy a OneDrive for Business-fiókot a Logic Apps használatával használhassa, engedélyezni kell a Logic Apps, hogy a Azure Portalban kapcsolódjon a OneDrive for Business-fiókhoz.

A következő lépésekkel engedélyezheti Logic Apps számára a OneDrive for Business-fiókhoz való kapcsolódást:  

1. Jelentkezzen be az Azure Portalra. 

1. Az **Azure-szolgáltatások** területen válassza a **Logic apps** lehetőséget. Ezután válassza ki a logikai alkalmazás nevét a listából.

1. A logikai alkalmazás menüjében válassza a **Logic app Designer** elemet a **fejlesztői eszközök** területen.

1. A Logic Apps Designerben válassza a **Microsoft felügyelt API** -k megjelenítése lehetőséget a legördülő listában, majd írja be a *OneDrive for Business* kifejezést a keresőmezőbe. Válassza ki a használni kívánt triggert vagy műveletet:  

   ![Képernyőkép a Logic Apps Designerről, amely a OneDrive for Business API-műveletekkel kapcsolatos ismétlődési eseményindítót jeleníti meg.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Ha korábban még nem hozott létre kapcsolatot a OneDrive vállalatnál, kövesse a OneDrive vállalati hitelesítő adatok megadására vonatkozó utasításokat. Ezekkel a hitelesítő adatokkal engedélyezheti a logikai alkalmazásnak, hogy hozzáférjen a OneDrive a vállalati fiók adataihoz:  

   ![Képernyőkép a Logic Apps Designerről, amely a OneDrive for Business szolgáltatáshoz való bejelentkezési kérést jeleníti meg.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Adja meg a OneDrive for Business felhasználónevét és jelszavát a logikai alkalmazás engedélyezéséhez:  

   ![Képernyőkép a OneDrive for Business bejelentkezési oldaláról, amely a bejelentkezési kérést mutatja.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. A kapcsolatok mostantól a lépésben szerepelnek. Válassza a mentés lehetőséget, majd folytassa a logikai alkalmazás létrehozását. 

   ![Képernyőkép a Logic Apps Designerről, amely az OneDrive for Business-kapcsolatok listáját jeleníti meg.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
