---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 18a4ad144870352c22ca7460786fdf3dbcaabeda
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498658"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service végpontnak érvényes HTTPS URI-nak kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Átvitel közben az adatokat illetéktelenek is módosíthatják. Léteznek protokollok, amelyek titkosítást biztosítanak a visszaélések és az illetéktelen módosítási problémák megoldása érdekében. Annak érdekében, hogy a robotok csak titkosított csatornákon keresztül kommunikálnak, állítsa a végpontot érvényes HTTPS URI-re. Ez biztosítja, hogy a HTTPS protokoll az átvitel során használt adatokat titkosítsa, és gyakran követelmény a jogszabályi vagy iparági szabványoknak való megfeleléshez. Látogasson el ide: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |audit, deny, disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service felhasználó által kezelt kulccsal kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service automatikusan titkosítja az erőforrást az adatok védelme, valamint a szervezeti biztonsági és megfelelőségi követelmények teljesítése érdekében. Alapértelmezés szerint a Rendszer a Microsoft által felügyelt titkosítási kulcsokat használja. A kulcsok kezelésével vagy az előfizetéshez való hozzáférés szabályozásával kapcsolatos nagyobb rugalmasság érdekében válassza az ügyfél által kezelt kulcsokat, más néven a saját kulcs használata (BYOK) lehetőséget. További információ a titkosítás Azure Bot Service: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |audit, deny, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
