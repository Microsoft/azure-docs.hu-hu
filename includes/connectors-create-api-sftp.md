---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202766"
---
### <a name="prerequisites"></a>Előfeltételek
* Egy [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) fiók  

SFTP-fiókjához, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás az SFTP-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás az SFTP-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A logic app Designerben az sftp protokoll használatára, egy kapcsolat létrehozásához válassza **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *SFTP* kifejezést a keresőmezőbe. Válassza ki a **SFTP - amikor egy fájl hozzáadásakor és módosításakor** eseményindító:  
   ![1. az SFTP online kapcsolat kép](./media/connectors-create-api-sftp/sftp-1.png)  
2. Ha még nem hozott létre az SFTP előtt kapcsolatokat, első kéri a SFTP hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a SFTP-fiókhoz tartozó adatok eléréséhez:  
   ![Az SFTP online kapcsolat kép 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:   
   ![Az SFTP online kapcsolat kép 3](./media/connectors-create-api-sftp/sftp-3.png) 

