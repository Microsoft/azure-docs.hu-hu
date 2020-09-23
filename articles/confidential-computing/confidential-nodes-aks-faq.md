---
title: Gyakori kérdések az Azure Kubernetes szolgáltatásban (ak) található bizalmas csomópontok támogatásáról
description: Válaszok az Azure Kubernetes szolgáltatással (ak) & Azure bizalmas számítástechnikai (ACC) csomópontok támogatásával kapcsolatos gyakori kérdésekre.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: b4b6c04a1e35031387a006b6350ebf6cbe502fee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995887"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Gyakori kérdések az Azure Kubernetes szolgáltatás (ak) bizalmas számítástechnikai csomópontjairól

Ez a cikk az Intel SGX ENKLÁVÉHOZ-alapú, az Azure Kubernetes Service (ak) szolgáltatáson alapuló bizalmas számítástechnikai csomópontjaival kapcsolatos gyakori kérdéseket tárgyalja. Ha további kérdése van, küldjön e-mailt acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Milyen szolgáltatói szerződés (SLA) és Azure-támogatás érhető el az előzetes verzióban? 

A termék előzetes verziója nem biztosít SLA-t az [itt](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)megadott módon. A terméktámogatást azonban Azure-támogatással biztosítjuk.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Mi az igazolás, és hogyan végezhető el a enklávékban futó alkalmazások igazolása? 

Az igazolás az a folyamat, amely azt mutatja be és ellenőrzi, hogy egy szoftver megfelelően lett-e létrehozva az adott hardveres platformon. Emellett igazolja, hogy a bizonyítéka a biztonságos platformon futó biztosítékok biztosítására szolgál, és nem lett illetéktelenül módosítva. [További információk](attestation.md) arról, hogyan történik az igazolás az enklávé-alkalmazásokhoz.

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Használhatom a meglévő tárolóban lévő alkalmazásokat, és az Azure bizalmas számítástechnikai szolgáltatásával is futtathatom az AK-n? 

Igen, tekintse át a [bizalmas tárolók oldalt](confidential-containers.md) a platform-adottságokkal kapcsolatos további részletekért.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Milyen Intel SGX ENKLÁVÉHOZ-illesztőprogram verziója van telepítve az AK-rendszerképben? 

Jelenleg az Azure bizalmas számítástechnikai DCSv2 virtuális gépek az Intel SGX ENKLÁVÉHOZ DCAP 1,33-es szervizcsomaggal telepíthetők. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Megnyitok egy Azure-támogatási jegyet, ha problémákat tapasztalok? 

Igen. Az előzetes verzióban az Azure-támogatás is elérhető. Nincs szolgáltatói szerződés csatolva, mert a termék előzetes verzióként érhető el.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Beadhatom a Post install szkripteket/az illesztőprogramokat az AK által kiépített csomópontokhoz? 

Nem. Az [AK-motor alapú bizalmas számítástechnikai csomópontok](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) támogatják a bizalmas számítástechnikai csomópontokat, amelyek lehetővé teszik az egyéni telepítéseket.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Érdemes Docker-alapú rendszerképet használni az enklávé-alkalmazások használatának megkezdéséhez? 

A különböző Segítők (ISV-és OSS-projektek) lehetővé teszik a bizalmas tárolók engedélyezésének módját. További részletekért tekintse át a [bizalmas tárolók oldalt](confidential-containers.md) , valamint a megvalósításokra vonatkozó egyéni referenciákat.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Futtathatok ACC-csomópontokat más standard AK-beli SKU-kal (különböző Node Pool-fürtöt)? 

Igen, különböző csomópont-készleteket futtathat ugyanazon az AK-fürtön belül, beleértve a ACC-csomópontokat is. Ha az enklávé-alkalmazásokat egy adott csomópont-készleten szeretné megcélozni, érdemes lehet csomópont-választókat hozzáadni vagy EPC-korlátokat alkalmazni. További részletekért tekintse meg a [bizalmas csomópontok](confidential-nodes-aks-get-started.md)gyors üzembe helyezéséről szóló témakört.

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>A Windows-csomópontok és a Windows-tárolók is futtathatók a ACC használatával? 

Jelenleg nem. Vegye fel velünk a kapcsolatot, ha Windows-csomópontokkal vagy-tárolókkal kell rendelkeznie. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Mi a teendő, ha a tároló mérete meghaladja az elérhető EPC-memóriát? 

Az EPC-memória az alkalmazás azon részére vonatkozik, amely az enklávéban való végrehajtásra van programozva. A tároló teljes mérete nem a megfelelő módszer a maximálisan elérhető EPC-memória összehasonlítására. Valójában a DCSv2 gépek a SGX ENKLÁVÉHOZ-mel lehetővé teszik a 32 GB-os maximális virtuálisgép-memória használatát, ahol az alkalmazás nem megbízható részét fogja használni. Ha azonban a tároló több mint rendelkezésre álló EPC-memóriát használ fel, akkor az enklávéban futó program részének teljesítménye is hatással lehet.

A munkavégző csomópontok EPC-memóriájának jobb kezelése érdekében vegye figyelembe az EPC memória-alapú korlátait a Kubernetes-on keresztül. Kövesse az alábbi példát hivatkozásként

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Mi történik, ha az enklávé több, mint a maximálisan elérhető EPC-memóriát használja? 

Az összes rendelkezésre álló EPC-memória meg van osztva az enklávé-alkalmazások között ugyanazon a virtuális gépeken vagy munkavégző csomópontokon. Ha az alkalmazás az EPC memóriát az elérhetőnél nagyobb mértékben használja, az alkalmazás teljesítménye befolyásolhatja a teljesítményt. Ezért javasoljuk, hogy az alkalmazáson belüli tolerancia beállítása a telepítési YAML fájlban, hogy jobban kezelje a rendelkezésre álló EPC memóriát a feldolgozó csomópontjain a fenti példákban látható módon. Azt is megteheti, hogy a munkavégző csomópont készletének virtuálisgép-méreteit vagy további csomópontok hozzáadását választja. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Miért nem tudok Forks () és exec futtatni több folyamat futtatását az én enklávé-alkalmazásban? 

Jelenleg az Azure bizalmas számítástechnikai DCsv2 SKU-beli virtuális gépek egyetlen címtartomány használatát támogatják a program enklávéban történő végrehajtásához. Az egyetlen folyamat a magas biztonságot célzó jelenlegi korlátozás. A bizalmas tárolók segítői azonban más implementációkkal is rendelkezhetnek, amelyekkel elháríthatja ezt a korlátozást.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Automatikusan telepít további daemonsets a SGX ENKLÁVÉHOZ-illesztőprogramok elérhetővé tétele érdekében? 

Igen. A daemonset elemet neve SGX enklávéhoz-Device-plugin és SGX enklávéhoz-quote-Helper. További részletekért tekintse meg a [megfelelő felhasználási célokat](confidential-nodes-aks-overview.md).  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Mi a virtuális gép SKU-jának kiválasztása a bizalmas számítástechnikai csomópontok számára? 

DCSv2 SKU-ket. A [DCSv2 SKU](../virtual-machines/dcv2-series.md) -ket a [támogatott régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)lehet elérni.

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Továbbra is ütemezhetem és futtathatom a nem enklávé tárolókat a bizalmas számítástechnikai csomópontokon? 

Igen. A virtuális gépek is rendelkeznek egy normál memóriával, amely képes a standard szintű tároló munkaterhelések futtatására. Mielőtt döntene az üzembe helyezési modellekről, vegye figyelembe az alkalmazások biztonsági és veszélyforrási modelljét.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Kiépíthetek az AK-t DCSv2 Node-készletekkel a Azure Portalon keresztül? 

Igen. Az Azure CLI Alternatív megoldásként is használható az [itt](confidential-nodes-aks-get-started.md)leírt módon.

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Milyen Ubuntu-verziót és VM-generációt támogat? 

18,04 a 2. gen-on. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Megváltoztathatjuk a jelenlegi Intel SGX ENKLÁVÉHOZ DCAP Diver verziót az AK-on? 

Nem. Az egyéni telepítések elvégzéséhez javasoljuk, hogy válassza az [AK-motor bizalmas számítástechnikai munkavégző csomópontok](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) telepítése lehetőséget. 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>A Kubernetes melyik verzióját támogatja és javasolja? 

A Kubernetes 1,16-es vagy újabb verziójának támogatása és ajánlása 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Mik a termék előzetes verziójának aktuális korlátozásai vagy technikai korlátai? 

- Csak az Ubuntu 18,04 Gen 2 VM-csomópontot támogatja 
- Nem támogatott Windows-csomópontok vagy Windows-tárolók támogatása
- Az EPC memória-alapú vízszintes Pod automatikus skálázása nem támogatott. A CPU és a normál memória alapú skálázás támogatott.
- A bizalmas alkalmazásokhoz tartozó fejlesztői szóközök jelenleg nem támogatottak

## <a name="next-steps"></a>Következő lépések
A bizalmas tárolókkal kapcsolatos további részletekért tekintse meg a [bizalmas tárolók oldalt](confidential-containers.md) .