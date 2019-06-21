---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: ad543ca2cd92895b9042ba795591523f00feb70a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202703"
---
### <a name="prerequisites"></a>Előfeltételek
* Egy Wunderlist-fiók  

Wunderlist-fiókja, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Wunderlist-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a Wunderlist-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. A Logic app Designerben Wunderlist, a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Wunderlist* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![](./media/connectors-create-api-wunderlist/wunderlist-0.png)
2. Ha még nem hozott létre a Wunderlistben, mielőtt kapcsolatokat, első kéri a Wunderlist hitelesítő adatokat. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a Wunderlist-fiókhoz tartozó adatok eléréséhez:   
   ![](./media/connectors-create-api-wunderlist/wunderlist-1.png)  
3. Adja meg a hitelesítő adatokat, majd válassza ki a bejelentkezési gomb  
   ![](./media/connectors-create-api-wunderlist/wunderlist-2.png)  
4. Akkor lesz majd megtudja, mi a logikai alkalmazás engedéllyel rendelkezik majd a Wunderlist-fiókját. Ha elfogadja, jelölje be a gomb jelzi a szerződését. 
   ![](./media/connectors-create-api-wunderlist/wunderlist-4.png)  
5. Végül válassza ki a **engedélyezés** gomb  
   ![](./media/connectors-create-api-wunderlist/wunderlist-5.png)  

