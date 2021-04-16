---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f5b70c5da99fb4f8db32b234c032c13bd4585a0d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510734"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A biztonságos alkalmazások meghatározásához szükséges adaptív alkalmazásvezérlőket engedélyezni kell a gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Az alkalmazásvezérlők engedélyezésével meghatározhatja a gépeken futó, ismerten biztonságos alkalmazások listáját, és riasztást küld, ha más alkalmazások is futnak. Ez segít a gépek kártevők elleni megsokosodásában. A szabályok konfigurálási és karbantartásának egyszerűsítése érdekében a Security Center gépi tanulással elemzi az egyes gépeken futó alkalmazásokat, és javaslatot ad az ismerten biztonságos alkalmazások listájára. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
