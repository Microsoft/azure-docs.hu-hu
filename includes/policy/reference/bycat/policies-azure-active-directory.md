---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866631"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services felügyelt tartományok csak a TLS 1.2 módot használják](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Csak TLS 1.2 módot használjon a felügyelt tartományokhoz. Alapértelmezés szerint a Azure AD Domain Services lehetővé teszi az olyan cipherek használatát, mint az NTLM v1 és a TLS v1. Előfordulhat, hogy ezekre a ciphrekre bizonyos örökölt alkalmazásokhoz szükség van, de gyengenek minősülnek, és letilthatók, ha nincs rájuk szükség. Ha a csak TLS 1.2 mód engedélyezve van, a TLS 1.2-t nem használó kérést igénylést igénylést betöltő ügyfelek sikertelenek lesznek. További információ: [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Naplózás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
