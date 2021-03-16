---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467204"
---
Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez lépjen a Azure Portal Storage-erőforrására, és válassza a **Storage Explorer** lapot. Navigáljon a tárolóhoz, kattintson a jobb gombbal, majd válassza a **közös hozzáférési aláírás beolvasása** elemet. Fontos, hogy az SAS-t lekérje a tárolóhoz, nem pedig magához a Storage-fiókhoz. Győződjön meg arról, hogy az **olvasási**, **írási**, **törlési** és **listázási** engedély be van jelölve, majd kattintson a **Létrehozás** gombra. Ezután másolja az **URL-cím** szakaszban lévő értéket egy ideiglenes helyre. A következő formátumban kell lennie: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
