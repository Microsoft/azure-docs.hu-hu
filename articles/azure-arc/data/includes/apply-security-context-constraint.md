---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687590"
---
Ez a szakasz a biztonsági környezeti megkötés (SCC) alkalmazásának módját ismerteti. Az előzetes kiadás esetében ezek a biztonsági korlátozások ellazítására használhatók. 

1. Töltse le az egyéni biztonsági környezet korlátozását (SCC). Használja a következők egyikét: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Nyers](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      A következő parancs letölti az ív-adathalmazt. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC létrehozása

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Alkalmazza a SCC-t a szolgáltatási fiókra.

   > [!NOTE]
   > Használja ugyanazt a névteret itt és az `azdata arc dc create` alábbi parancsban. Példa: `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
