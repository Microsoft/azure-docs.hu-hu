---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: eaee3be3627d764c064e96a7cc31232834f430eb
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429209"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A bot Service-végpontnak érvényes HTTPS URI-nak kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Átvitel közben az adatokat illetéktelenek is módosíthatják. Léteznek protokollok, amelyek titkosítást biztosítanak a visszaélések és az illetéktelen módosítási problémák megoldása érdekében. Annak biztosítása érdekében, hogy a robotok csak titkosított csatornákon keresztül kommunikáljanak, állítsa a végpontot érvényes HTTPS URI-ra. Ez biztosítja, hogy a HTTPS protokoll az adatátvitel titkosítására szolgáljon, és ez gyakran követelmény a szabályozási vagy iparági szabványoknak való megfeleléshez is. Látogasson el ide: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |naplózás, megtagadás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[A bot Service-t az ügyfél által felügyelt kulccsal kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service automatikusan titkosítja az erőforrást az adatai védelme érdekében, és megfelel a szervezeti biztonsági és megfelelőségi kötelezettségeknek. Alapértelmezés szerint a rendszer a Microsoft által felügyelt titkosítási kulcsokat használja. A kulcsok kezelésének és az előfizetéshez való hozzáférés szabályozásának nagyobb rugalmassága érdekében válassza az ügyfél által felügyelt kulcsok, más néven a saját kulcs használata (BYOK) lehetőséget. További információ a Azure Bot Service titkosításról: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
