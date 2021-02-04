---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 578cd451b53634bc63876e8ca2d25e3251eb8cc2
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556464"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Az Azure HDInsight-fürtöknek az ügyfél által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Az ügyfél által felügyelt kulcsok (CMK-EK) segítségével kezelheti az Azure HDInsight-fürtökön tárolt adatok titkosítását. Alapértelmezés szerint a rendszer a szolgáltatás által felügyelt kulcsokkal titkosítja az ügyféladatokat, de a CMKs általában a szabályozási megfelelőségi előírások teljesítéséhez szükségesek. A CMKs lehetővé teszi az adattitkosítást egy Ön által létrehozott és birtokolt Azure Key Vault kulccsal. A kulcs életciklusának teljes körű ellenőrzése és felelőssége, beleértve a rotációt és a felügyeletet is. További információ a CMK-titkosításról: [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) .  |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Az Azure HDInsight-fürtöknek titkosítást kell használniuk a gazdagépen a REST-adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |A titkosítás engedélyezése a gazdagépen segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Az Azure HDInsight-fürtöknek az Azure HDInsight-fürtcsomópontok közötti kommunikáció titkosításához az átvitel során titkosítást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Az Azure HDInsight-fürtcsomópontok közötti adatátvitel során az adatforgalom illetéktelen lehet. Ha engedélyezi a titkosítást az átvitel során, a visszaélések és a hamisítás során felmerülő problémák is megtalálhatók. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
