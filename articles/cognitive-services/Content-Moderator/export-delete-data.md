---
title: Felhasználói adatContent Moderator exportálása vagy törlése –
titleSuffix: Azure Cognitive Services
description: Teljes hozzáférése van az adataihoz. Megtudhatja, hogyan tekintheti meg, exportálhatja vagy törölheti az adatait Content Moderatorban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "73744786"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Felhasználói adatexportálás vagy-törlés Content Moderator

A Content Moderator felhasználói adatokat gyűjt a szolgáltatás üzemeltetéséhez, de az ügyfelek teljes hozzáféréssel rendelkeznek az adatok megtekintésére, exportálására és törlésére a [felülvizsgálati eszközzel](https://contentmoderator.cognitive.microsoft.com/) , valamint a [moderálási és felülvizsgálati API](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference)-k használatával.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

A Content Moderator felhasználói adatainak exportálásával és törlésével kapcsolatos további információkért tekintse meg a következő táblázatot.

| Adatok | Exportálási művelet | Művelet törlése |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | N/A | Törlés a Azure Portal használatával (Azure-előfizetések). Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |
| Egyéni egyeztetésre szolgáló lemezképek | Hívja meg a [Képazonosítók beolvasása API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)-t. A lemezképek egyirányú tulajdonosi kivonatoló formátumban tárolódnak, és nem lehet kinyerni a tényleges képeket. | Hívja meg az [összes rendszerkép törlése API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)-t. Vagy törölje a Content Moderator erőforrást a Azure Portal használatával. |
| Az egyéni egyeztetés feltételei | Cal a [minden feltétel beolvasása API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Hívja meg az [összes feltétel törlése API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)-t. Vagy törölje a Content Moderator erőforrást a Azure Portal használatával. |
| Címkék | N/A | Használja az egyes címkékhez elérhető **delete** ikont a felhasználói felület áttekintése címke beállításai lapon. Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |
| Felülvizsgálatok | A [Get felülvizsgálati API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) meghívása | Használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon.
| Felhasználók | N/A | Használja az egyes felhasználók számára elérhető **delete** ikont a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. Vagy használja a **csapat törlése** gombot a [felhasználói felületi](https://contentmoderator.cognitive.microsoft.com/) csoport beállításainak áttekintése lapon. |

