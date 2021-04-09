---
title: Bizalmas számítástechnikai csomópontok az Azure Kubernetes szolgáltatásban (ak)
description: Bizalmas számítástechnikai csomópontok az AK-on
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 203185d9f6def2204906b8722f1969b14eee8787
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933151"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Bizalmas számítástechnikai csomópontok az Azure Kubernetes szolgáltatásban

Az [Azure bizalmas számítástechnikai](overview.md) szolgáltatása lehetővé teszi a bizalmas adatok védelmét a használat közben. A mögöttes bizalmas számítástechnikai infrastruktúra megvédi ezeket az adatokat más alkalmazásoktól, rendszergazdáktól és felhőalapú szolgáltatóktól egy hardveres megbízható végrehajtási tároló környezetével. A bizalmas számítástechnikai csomópontok hozzáadásával megcélozhatja, hogy a Container Application egy elkülönített, hardveresen védett és tanúsítható környezetben fusson.

## <a name="overview"></a>Áttekintés

Az Azure Kubernetes Service (ak) támogatja az Intel SGX ENKLÁVÉHOZ által működtetett [DCsv2 bizalmas számítástechnikai csomópontok](confidential-computing-enclaves.md) hozzáadását. Ezek a csomópontok lehetővé teszik az érzékeny számítási feladatok futtatását egy hardveres megbízható végrehajtási környezetben (TEE) belül. A TEE lehetővé teszi, hogy a tárolók felhasználói szintű kódja kiossza a memóriában lévő privát régiókat, hogy a kódot közvetlenül a CPU használatával hajtsa végre. A közvetlenül a CPU-val végrehajtandó saját memória-régiókat enklávéknak nevezzük. Az enklávék segítenek az adatok titkosságának, az adatok integritásának és a kód integritásának az ugyanazon csomópontokon futó egyéb folyamatoktól való védelmében. Az Intel SGX ENKLÁVÉHOZ végrehajtási modellje eltávolítja a vendég operációs rendszer, a gazdagép operációs rendszere és a hypervisor közbenső rétegeit is, így csökkentve a támadási felületet. A csomópontok *tárolók közötti, elkülönített végrehajtási* modellje lehetővé teszi, hogy az alkalmazások közvetlenül is végrehajtsák a processzort, miközben a védett memória speciális blokkját tárolóban tárolják. A bizalmas tárolókkal rendelkező bizalmas számítástechnikai csomópontok nagyban kiegészítik a megbízható biztonsági tervezést és a védelmi részletes adattárolói stratégiát.

![a SGX enklávéhoz csomópont áttekintése](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AK bizalmas csomópontok funkciói

- Hardveres és feldolgozható szintű tárolók elkülönítése az Intel SGX ENKLÁVÉHOZ megbízható végrehajtási környezete (póló) alapján 
- Különböző (bizalmas és nem bizalmas csomópont-készletek)
- A titkosított oldal gyorsítótára (EPC) memória-alapú Pod-ütemezése (kiegészítő funkció szükséges)
- Az Intel SGX ENKLÁVÉHOZ DCAP illesztőprogramja előre telepítve
- CPU-használaton alapuló horizontális hüvely automatikus skálázása és a fürt automatikus skálázása
- Linux-tárolók támogatása az Ubuntu 18,04 Gen 2 VM Worker-csomópontokon

## <a name="confidential-computing-add-on-for-aks"></a>Bizalmas számítástechnikai bővítmény az AK-hoz
A bővítmény funkció lehetővé teszi az AK-ra való további képességet, ha a fürtön bizalmas számítástechnikai csomópont-készleteket futtat. Ez a bővítmény engedélyezi az alábbi funkciókat.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Azure-eszköz beépülő modul Intel SGX ENKLÁVÉHOZ <a id="sgx-plugin"></a>

Az eszköz beépülő modulja a Kubernetes-eszköz beépülő modul felületét valósítja meg a titkosított oldal-gyorsítótár (EPC) memóriája számára, és elérhetővé teszi az eszközillesztőket a csomópontokból. Gyakorlatilag ez a beépülő modul a Kubernetes-ben egy másik erőforrástípusként teszi elérhetővé az EPC memóriát. A felhasználók az erőforrásra vonatkozó korlátozásokat ugyanúgy meghatározhatják, mint a többi erőforrást. Az ütemezési függvényen kívül az eszköz beépülő modul segítséget nyújt az Intel SGX ENKLÁVÉHOZ-illesztőprogramokhoz való hozzárendeléséhez a bizalmas számítási feladatokhoz tartozó tárolók számára. Ennek a beépülő modulnak a fejlesztője elkerülheti az Intel SGX ENKLÁVÉHOZ illesztőprogram-köteteknek a telepítési fájlokban való csatlakoztatását. Az EPC memória alapú üzembe helyezési ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` ) minta megvalósítását [itt](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) találja


## <a name="programming-models"></a>Programozási modellek

### <a name="confidential-containers"></a>Bizalmas tárolók

A [bizalmas tárolók](confidential-containers.md) segítségével a leggyakoribb **programozási nyelvek** futtatókörnyezetei (Python, node, Java stb.) által kezelt meglévő, nem módosított tároló-alkalmazásokat futtathatják bizalmasan. Ennek a csomagolási modellnek nincs szüksége forráskód-módosításra vagy újrafordításra. Ez a leggyorsabban a titkossági módszer, amely a standard Docker-tárolók Open-Source-projektekkel vagy Azure-partneri megoldásokkal való csomagolásával érhető el. Ebben a csomagolási és végrehajtási modellben a Container alkalmazás összes része betöltődik a megbízható határba (enklávé). Ez a modell jól működik a piacon elérhető polcos tároló-alkalmazások és az általános célú csomópontokon jelenleg futó egyéni alkalmazások esetében.

### <a name="enclave-aware-containers"></a>Enklávé-Aware tárolók
Az AK-beli bizalmas számítástechnikai csomópontok olyan tárolókat is támogatnak, amelyek egy enklávéban futnak, hogy használják a CPU-ból elérhető **speciális utasításkészlet-készletet** . Ez a programozási modell lehetővé teszi a végrehajtási folyamat szigorúbb vezérlését, és speciális SDK-k és keretrendszerek használatát igényli. Ez a programozási modell a legkevesebb megbízható számítástechnikai Alappal (TCB) rendelkező Application flow vezérlését biztosítja. Az enklávéban a tárolók fejlesztése nem megbízható és megbízható részeket biztosít a tároló alkalmazás számára, így lehetővé teszi a normál memória-és titkosított lapozófájl-(EPC-) memória kezelését, ahol az enklávé fut. [További információ](enclave-aware-containers.md) : enklávé Aware containers.

## <a name="next-steps"></a>Következő lépések

[AK-fürt üzembe helyezése bizalmas számítástechnikai csomópontokkal](./confidential-nodes-aks-get-started.md)

[Gyors alapszintű, bizalmas tárolók mintái](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](../virtual-machines/dcv2-series.md)

[Részletes védelem a bizalmas tárolókkal kapcsolatos Webinar-munkamenettel](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
