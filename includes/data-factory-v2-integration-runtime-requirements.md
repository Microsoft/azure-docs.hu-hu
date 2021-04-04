---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023182"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár egy helyszíni hálózaton, egy Azure-beli virtuális hálózaton vagy egy Amazon Virtual Private-felhőben található, a csatlakozáshoz konfigurálnia kell egy saját üzemeltetésű [integrációs](../articles/data-factory/create-self-hosted-integration-runtime.md) modult.

Ha az adattár felügyelt felhőalapú adatszolgáltatás, akkor használhatja az Azure Integration Runtime szolgáltatást is. Ha a hozzáférés a tűzfalszabályok által jóváhagyott IP-címekre korlátozódik, [Azure Integration Runtime IP-címeket](../articles/data-factory/azure-integration-runtime-ip-addresses.md) adhat hozzá az engedélyezési listához. 

A Data Factory által támogatott hálózati biztonsági mechanizmusokkal és lehetőségekkel kapcsolatos további információkért lásd az [adatelérési stratégiákat](../articles/data-factory/data-access-strategies.md).
