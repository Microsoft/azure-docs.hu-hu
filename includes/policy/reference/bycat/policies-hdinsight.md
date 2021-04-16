---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60278160646de1b80d224cabc6d781872a5624d9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498714"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight fürtöt be kell injektálni egy virtuális hálózatba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |A Azure HDInsight fürtök egy virtuális hálózatba való be injektálása fejlett HDInsight hálózati és biztonsági funkciókat biztosít, és lehetővé teszi a hálózati biztonsági konfiguráció vezérlését. |Naplózás, Letiltva, Megtagadás |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Azure HDInsight fürtöknek ügyfél által kezelt kulcsokat kell használniuk az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Az ügyfél által kezelt kulcsokkal kezelheti a titkosítást a Azure HDInsight többi részében. Alapértelmezés szerint az ügyféladatok szolgáltatás által kezelt kulcsokkal vannak titkosítva, de az ügyfél által kezelt kulcsokra általában szükség van a jogszabályi megfelelőségi szabványoknak való megfeleléshez. Az ügyfél által kezelt kulcsok lehetővé teszik az adatok titkosítását egy olyan Azure Key Vault, amelyet Ön hozott létre és birtokolt. Teljes mértékben Ön felelős a kulcs életciklusa felett, beleértve a rotációt és a felügyeletet is. További információ: [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Naplózás, Megtagadás, Letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight fürtöknek titkosítást kell használniuk a gazdagépen az adatok titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |A titkosítás engedélyezése a gazdagépen segít megvédeni és védeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi követelményeknek. Ha engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva vannak, az adatok pedig titkosítva vannak a Storage szolgáltatás felé. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight fürtöknek titkosítást kell használniuk az átvitel során a fürtcsomópontok Azure HDInsight titkosításához](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Az adatok a fürtcsomópontok közötti átvitel Azure HDInsight illetéktelenek. Az átvitel során a titkosítás engedélyezése az átvitel során a nem rendeltetésszerű használat és az illetéktelen módosítás problémáira is kihat. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
