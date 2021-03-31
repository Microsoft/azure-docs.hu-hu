---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649053"
---
A App Service platform-összetevői, beleértve az Azure-beli virtuális gépeket, a tárhelyet, a hálózati kapcsolatokat, a webes keretrendszereket, a felügyeleti és integrációs funkciókat, aktívan biztosítva és megerősítve. App Service a folyamatos megfelelőség ellenőrzése folyamatosan történik, hogy megbizonyosodjon róla, hogy:

- Az alkalmazás erőforrásai a többi ügyfél [Azure-erőforrásaitól védve vannak](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) .
- A virtuálisgép [-példányok és a futásidejű szoftverek rendszeresen frissülnek](../articles/app-service/overview-patch-os-runtime.md) az újonnan felderített biztonsági rések kezeléséhez. 
- Az alkalmazás és az egyéb Azure-erőforrások (például a [SQL Database](https://azure.microsoft.com/services/sql-database/)) közötti titkok (például a kapcsolati karakterláncok) az Azure-ban maradnak, és nem kereszteznek semmilyen hálózati határt. A titkos kulcsokat a rendszer mindig titkosítja tároláskor.
- A App Service kapcsolati funkciói (például a [hibrid kapcsolat](../articles/app-service/app-service-hybrid-connections.md)) közötti kommunikáció titkosítva van. 
- A távoli felügyeleti eszközökkel (például Azure PowerShellokkal, Azure CLI-vel, Azure SDK-kkal, REST API-kkal) létesített kapcsolatok mindegyike titkosítva van.
- a 24 órás veszélyforrások kezelése megvédi az infrastruktúrát és a platformot a kártevők, az elosztott szolgáltatásmegtagadási (DDoS), a személyes környezet (MITM) és más fenyegetések ellen.

További információ az infrastruktúra és a platform biztonságáról az Azure-ban: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/overview/trusted-cloud/).