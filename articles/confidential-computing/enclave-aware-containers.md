---
title: Enklávé Aware-tárolók az Azure-ban
description: enklávé Ready Application-tárolók támogatása az Azure Kubernetes Service-ben (ak)
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 0c9e7f98a9df7bfea6b0c6dd6d3c2cf1f6090ab0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932635"
---
# <a name="enclave-aware-containers"></a>Enklávé-Aware tárolók

Az enklávé egy védett memória-régió, amely titkosságot biztosít az adatok és a programkódok végrehajtásához. Ez egy megbízható végrehajtási környezet (TEE) egy példánya, amelyet a hardver biztosít. Az AK-beli bizalmas számítástechnikai csomópontok az [Intel Software Guard Extensions (SGX enklávéhoz)](https://software.intel.com/sgx) használatával elszigetelt enklávé-környezeteket hoznak létre az egyes tároló alkalmazások közötti csomópontokban.

Csakúgy, mint az Intel SGX ENKLÁVÉHOZ Virtual Machines, a enklávékban való futtatásra fejlesztett tároló alkalmazások két összetevővel rendelkeznek:

- Nem megbízható összetevő (a gazdagép neve) és
- Egy megbízható összetevő (az enklávé néven).

![Enklávé-Aware tároló architektúrája](./media/enclave-aware-containers/enclaveawarecontainer.png)

Az enklávé Aware containers Application Architecture segítségével a lehető leghatékonyabban vezérelheti a megvalósítást, miközben a kód helyigényét az enklávéban alacsonyan tartja. Az enklávéban futó kód minimalizálása segít csökkenteni a támadási felületi területeket.   

## <a name="enablers"></a>Adottságok

### <a name="open-enclave-sdk"></a>Az Enclave SDK megnyitása
Az Open enklávé SDK egy hardveres, nyílt forráskódú kódtár, amely a hardveres megbízható végrehajtási környezeteket használó C, C++ alkalmazások fejlesztésére szolgál. Az aktuális implementáció támogatást nyújt az Intel SGX ENKLÁVÉHOZ, valamint az [op-Tee operációs rendszer az ARM-TrustZone](https://optee.readthedocs.io/en/latest/general/about.html)való előzetes támogatásához.

Ismerkedés az Open enklávé-alapú [Container alkalmazással](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)

### <a name="intel-sgx-sdk"></a>Intel SGX ENKLÁVÉHOZ SDK
Az Intel karbantartja a szoftverfejlesztői készletet a Linux-és Windows-tárolók számítási feladataihoz használt SGX ENKLÁVÉHOZ-alkalmazások létrehozásához. A Windows-tárolók jelenleg nem támogatottak az AK bizalmas számítástechnikai csomópontjain.

Ismerkedés az Intel SGX ENKLÁVÉHOZ- [alapú alkalmazásokkal](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>Bizalmas konzorciumi keretrendszer (CCF)
A bizalmas konzorciumi keretrendszer (CCF) egy nyílt forráskódú keretrendszer, amely a több féltől származó számítási és adatmennyiségre összpontosító, biztonságos, nagy rendelkezésre állású és teljesítményű alkalmazások új kategóriájának kiépítését teszi elérhetővé. A CCF lehetővé teszi a nagyvállalati követelményeknek megfelelő, nagy kapacitású, bizalmas hálózatok bevezetését, amelyekkel felgyorsítható a konzorcium-alapú blockchain és a többtényezős számítási technológia termelésének és nagyvállalati bevezetése.

Ismerkedjen meg az Azure bizalmas számítástechnikai és [a CCF használatába](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Bizalmas származtatású ONNX Runtime

A nyílt forráskódú enklávé-alapú ONNX-futtatókörnyezet biztonságos csatornát hoz létre az ügyfél és a következtetés szolgáltatás között, így biztosítva, hogy sem a kérés, sem a válasz nem hagyhatja el a biztonságos enklávét. 

Ez a megoldás lehetővé teszi, hogy meglévő ML betanított modellt használjon, és bizalmasan fusson, miközben megbízhatósági kapcsolatot biztosít az ügyfél és a kiszolgáló között az igazolásokkal és ellenőrzésekkel. 

Ismerkedjen meg a ML Model lifttel, és váltson át a [ONNX runtimere](https://aka.ms/confidentialinference)

### <a name="edgeless-rt"></a>Edge nélküli RT

Az Edge nélküli RT egy nyílt forráskódú projekt, amely az Open enklávé SDK-ra épül. Támogatja a Go és a további C++ funkciókat. Ismerkedjen meg egy egyszerű, bizalmas go-alkalmazással az ismerős VS Code [-környezet használatával](https://github.com/edgelesssys/edgelessrt). Az egyazon nélküli alkalmazások esetében kövesse az [alábbi utasításokat](https://github.com/edgelesssys/edgelessrt/blob/master/docs/ERTAzureAKSDeployment.md)


## <a name="container-based-sample-implementations"></a>Tároló-alapú minták implementációi

[Azure Samples for enklávé Aware-tárolók az AK-ban](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[DC virtuális gép](./virtual-machine-solutions.md) 
 [Bizalmas tárolók](./confidential-containers.md)