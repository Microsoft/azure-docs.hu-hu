---
title: Az Azure Kubernetes Service (ak) nyilvános előzetes verziójának bizalmas számítástechnikai csomópontjai
description: Bizalmas számítástechnikai csomópontok az AK-on
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564174"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Bizalmas számítástechnikai csomópontok az Azure Kubernetes szolgáltatásban (nyilvános előzetes verzió)

Az [Azure bizalmas számítástechnikai](overview.md) szolgáltatása lehetővé teszi a bizalmas adatok védelmét a használat közben. A mögöttes infrastruktúrák más alkalmazásokból, rendszergazdákból és felhőalapú szolgáltatóktól származó adatokat védik a hardveres megbízható végrehajtási tároló környezetével.

## <a name="overview"></a>Áttekintés

Az Azure Kubernetes Service (ak) támogatja az Intel SGX ENKLÁVÉHOZ által működtetett [DCsv2 bizalmas számítástechnikai csomópontok](confidential-computing-enclaves.md) hozzáadását. A futtatott csomópontok a hardveres megbízható végrehajtási környezetben (TEE) belül is futtathatnak bizalmas munkaterheléseket azáltal, hogy lehetővé teszik a felhasználói szintű kód számára a memória privát régiói számára való kiosztását. Ezeket a saját memória-régiókat enklávéknak nevezzük. Az enklávék úgy vannak kialakítva, hogy a magasabb szintű jogosultságokkal rendelkező folyamatokból származó kódok és adatok védelme érdekében. A SGX ENKLÁVÉHOZ végrehajtási modellje eltávolítja a vendég operációs rendszer, a gazdagép operációs rendszere és a hypervisor közbenső rétegeit. A *Container-alapú hardverek által elkülönített végrehajtási* modell lehetővé teszi, hogy az alkalmazások közvetlenül a CPU-val fussanak, miközben a speciális memória-blokkot titkosítják. A bizalmas számítástechnikai csomópontok segítséget nyújtanak az AK-ra kiterjedő tároló-alkalmazások általános biztonsági állapotának kialakításában, valamint a védelem részletes tárolási stratégiájának kiegészítéseként. 

![a SGX enklávéhoz csomópont áttekintése](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AK bizalmas csomópontok funkciói

- Hardveres és feldolgozható szintű tárolók elkülönítése SGX ENKLÁVÉHOZ megbízható végrehajtási környezettel (póló) 
- Különböző (bizalmas és nem bizalmas csomópont-készletek)
- A titkosított oldal gyorsítótára (EPC) memória-alapú Pod-ütemezése
- Előre telepített SGX ENKLÁVÉHOZ DCAP-illesztőprogram
- Előre telepített Intel FSGS-javítás
- Támogatja a CPU-felhasználáson alapuló horizontális Pod automatikus skálázást és a fürt automatikus skálázását
- Folyamaton kívüli igazolás segítője az AK daemonset elemet
- Linux-tárolók támogatása az Ubuntu 18,04 Gen 2 VM Worker-csomópontokon

## <a name="aks-provided-daemon-sets-addon"></a>AK megadott démon-készletek (Addon)

#### <a name="sgx-device-plugin"></a>SGX ENKLÁVÉHOZ-eszköz beépülő modul <a id="sgx-plugin"></a>

Az SGX ENKLÁVÉHOZ-eszköz beépülő modulja implementálja az Kubernetes-eszköz beépülő modulját az EPC memória számára. Ez a beépülő modul a Kubernetes-ben egy további erőforrástípust tesz elérhetővé az EPC memóriában. A felhasználók az erőforrásra vonatkozó korlátozásokat ugyanúgy meghatározhatják, mint a többi erőforrást. Az ütemezési függvényen kívül az eszköz beépülő modul segítséget nyújt a SGX ENKLÁVÉHOZ-eszközillesztők számára a bizalmas számítási feladatokhoz tartozó tárolók kiosztásához. Az EPC memória alapú üzembe helyezési ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` ) minta megvalósítását [itt](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) találja

#### <a name="sgx-quote-helper-service"></a>SGX ENKLÁVÉHOZ-ajánlat segítő szolgáltatása <a id="sgx-quote"></a>

A távoli igazolást végrehajtó enklávé-alkalmazásoknak ÁRAJÁNLATot kell létrehozniuk. Az ajánlat titkosítási igazolást nyújt az alkalmazás identitásáról és állapotáról, valamint azt a környezetet, amelyben az enklávé fut. Az IDÉZŐJELek létrehozása a SGX ENKLÁVÉHOZ platform szoftver-összetevőinek (PSW/DCAP) részét képező, az Inteltől származó bizonyos megbízható szoftver-összetevőkre támaszkodik. Ez a PSW olyan démon-készletként van csomagolva, amely egy csomóponton fut. Kihasználható az enklávé-alkalmazások igazolási AJÁNLATának kérelmezése során. Az AK által biztosított szolgáltatás használatával jobban megtarthatja a PSW és a gazdagépen található egyéb SW-összetevők közötti kompatibilitást. [További](confidential-nodes-out-of-proc-attestation.md) információ a használatáról és a szolgáltatás részleteiről.

## <a name="programming--application-models"></a>Programozási & alkalmazás modelljei

### <a name="confidential-containers"></a>Bizalmas tárolók

A [bizalmas tárolók](confidential-containers.md) meglévő programokat és a leggyakoribb **programozási nyelvi** futtatókörnyezetet (Python, node, Java stb.) futtatják, a meglévő függvénytár-függőségekkel együtt, forráskód-módosítás vagy újrafordítás nélkül. Ez a modell az Azure-partnerek által & nyílt forráskódú projekteken keresztül engedélyezett, a titkosság leggyorsabb modellje. A biztonságos enklávékban való futtatásra készen álló tároló-lemezképek bizalmas tárolóként vannak kinevezve.

### <a name="enclave-aware-containers"></a>Enklávé-Aware tárolók

Az AK támogatja a bizalmas csomópontokon történő futtatásra programozott és az SDK-k és keretrendszerek által elérhetővé tett **speciális utasításkészlet** használatát. Ez az alkalmazási modell a legkevesebb megbízható számítástechnikai Alappal (TCB) biztosítja az alkalmazások felügyeletét. [További információ](enclave-aware-containers.md) : enklávé Aware containers.

## <a name="next-steps"></a>Következő lépések

[AK-fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal](./confidential-nodes-aks-get-started.md)

[Gyors alapszintű, bizalmas tárolók mintái](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions