---
ms.openlocfilehash: 3f4430631a664f81f53f9df1f46ebc27c635de36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860203"
---
### <a name="prerequisites"></a>Előfeltételek
* A [Dropbox](https://www.Dropbox.com/) fiók 

A Dropbox-fiókjában, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást, a Dropbox-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazást, a Dropbox-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. A Logic app Designerben Dropbox vagy a kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Dropbox* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![Dropbox 1. lépés.](./media/connectors-create-api-dropbox/dropbox-1.png)
2. Ha még nem hozott létre a Dropboxba előtt kapcsolatokat, első kéri, adja meg a dropbox szolgáltatásbeli hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a Dropbox-fiókjában adatok elérésére:  
   ![Dropbox 2. lépés.](./media/connectors-create-api-dropbox/dropbox-2.png)
3. Adja meg a Dropbox felhasználónevet és jelszót, hogy a logikai alkalmazás engedélyezése:  
   ![Dropbox 3. lépés.](./media/connectors-create-api-dropbox/dropbox-3.png)   
4. A logikai alkalmazás használatához a Dropbox-fiókjának engedélyezéséhez:  
   ![Dropbox 4. lépés.](./media/connectors-create-api-dropbox/dropbox-4.png)
5. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Dropbox 5. lépés.](./media/connectors-create-api-dropbox/dropbox-5.png)   

