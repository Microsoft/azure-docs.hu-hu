---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77474234"
---
A tárolóra irányuló lekérdezések számlázása az Azure-erőforrás díjszabási szintjénél történik `ApiKey` .

Az Azure Cognitive Services-tárolók nem rendelkeznek licenccel a mérési/számlázási végponthoz való csatlakozás nélkül. Engedélyeznie kell a tárolókat, hogy mindig a számlázási végponttal kommunikáljanak a számlázási adatokkal. Cognitive Services tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak.

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

A tárolónak a számlázási argumentum értékeit kell futtatnia. Ezek az értékek lehetővé teszik a tárolónak a számlázási végponthoz való kapcsolódást. A tároló 10 – 15 percen belül jelentést készít a használatról. Ha a tároló nem csatlakozik az Azure-hoz az engedélyezett időtartományon belül, a tároló továbbra is futni fog, de addig nem szolgál lekérdezéseket, amíg a számlázási végpont vissza nem áll. A rendszer 10 – 15 percen belül 10 alkalommal kísérli meg a kapcsolódást. Ha a 10 próbálkozáson belül nem tud csatlakozni a számlázási végponthoz, a tároló leállítja a kérelmek kiszolgálását.

### <a name="billing-arguments"></a>Számlázási argumentumok

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` A <span class="docon docon-navigate-external x-hidden-focus"></span> </a> parancs elindítja a tárolót, ha mind a három következő beállítás érvényes értékkel rendelkezik:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | A számlázási információk nyomon követéséhez használt Cognitive Services erőforrás API-kulcsa.<br/>Ennek a beállításnak az értékét a ben megadott kiépített erőforráshoz tartozó API-kulcsra kell beállítani `Billing` . |
| `Billing` | A számlázási információk nyomon követéséhez használt Cognitive Services erőforrás végpontja.<br/>Ennek a beállításnak az értékét egy kiépített Azure-erőforrás végpont-URI-azonosítójának kell beállítania.|
| `Eula` | Azt jelzi, hogy elfogadta a tároló licencét.<br/>Ennek a beállításnak az értékét az **elfogadás**értékre kell beállítani. |
