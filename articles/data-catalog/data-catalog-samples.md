---
title: Azure Data Catalog fejlesztői minták
description: Ez a cikk áttekintést nyújt a Data Catalog REST API elérhető fejlesztői mintákról.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674767"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure Data Catalog fejlesztői minták

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ismerkedjen meg Azure Data Catalog alkalmazások fejlesztésével a Data Catalog REST API használatával. A Data Catalog REST API egy REST-alapú API, amely programozott hozzáférést biztosít Data Catalog erőforrásokhoz az adategységek programozott módon történő regisztrálásához, feliratozásához és kereséséhez.

## <a name="samples-available-on-githubcom"></a>GitHub.com elérhető minták

* [Ismerkedés az Azure Data Catalog szolgáltatással](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   Az első lépéseket bemutató minta bemutatja, hogyan végezheti el a hitelesítést az Azure AD-vel az adategységek regisztrálásához, kereséséhez és törléséhez az Data Catalog REST API használatával.
   
* [A Azure Data Catalog használatának első lépései az egyszerű szolgáltatásnév használatával](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   Ez a minta bemutatja, hogyan regisztrálhat, kereshet és törölhet egy adategységet a Data Catalog REST API használatával. Ez a példa az egyszerű szolgáltatás hitelesítését használja.

* [Azure Data Catalog importálási/exportálási eszköze](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   Ez a minta azt mutatja be, hogyan lehet a Data Catalog REST API használatával beolvasni az eszközöket a Azure Data Catalogból, és szerializálni őket egy fájlba. Azt is bemutatja, hogyan hozhat létre JSON-ként szerializált eszközöket, és hogyan küldheti azokat a katalógusba. Támogatja a katalógus egy részhalmazának exportálását egy keresési lekérdezés használatával.

* [Csoportos regisztráció és Megjegyzés Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   Ez a minta bemutatja, hogyan regisztrálhat az adategységeket egy Excel-munkafüzetből Data Catalog REST API és az Open XML használatával.
  
* [Szószedet-kifejezések tömeges importálása Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   Ez a minta bemutatja, hogyan importálhat Szószedet-kifejezéseket CSV-fájlokból az ADC-szószedetbe.

* [Kapcsolatok tömeges importálása Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   Ez a példa azt mutatja be, hogyan lehet programozott módon importálni a kapcsolati adatokat egy CSV-fájlból egy adatkatalógusba.

* [Kapcsolatok közzététele Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   Ebből a példából megtudhatja, hogyan teheti közzé programozási módon a kapcsolati adatokat egy adatkatalógusban.
   
## <a name="next-steps"></a>Következő lépések
[Azure Data Catalog REST API-hivatkozás](/rest/api/datacatalog/)
