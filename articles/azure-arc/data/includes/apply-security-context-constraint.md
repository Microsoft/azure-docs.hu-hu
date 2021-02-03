---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494012"
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

   > [!NOTE]
   > A RedHat OpenShift 4,5-es vagy újabb szervizcsomagja a következőt módosítja: hogyan alkalmazza a SCC szolgáltatást a szolgáltatási fiókra.
   > Használja ugyanazt a névteret itt és az `azdata arc dc create` alábbi parancsban. Példa: `arc` . 
   > 
   > Ha a 4,5-es vagy újabb RedHat-OpenShift használja, futtassa a következőket: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
