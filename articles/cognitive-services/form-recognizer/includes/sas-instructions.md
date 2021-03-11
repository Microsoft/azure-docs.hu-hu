---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623255"
---
Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez lépjen a Azure Portal Storage-erőforrására, és válassza a **Storage Explorer** lapot. Navigáljon a tárolóhoz, kattintson a jobb gombbal, majd válassza a **közös hozzáférési aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási**, **írási**, **törlési** és **listázási** engedély be van jelölve, majd kattintson a **Létrehozás** gombra. Ezután másolja az **URL-cím** szakaszban lévő értéket egy ideiglenes helyre. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
