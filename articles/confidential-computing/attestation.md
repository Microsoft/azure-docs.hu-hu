---
title: Enklávék igazolása az Azure-ban
description: Megtudhatja, hogyan használhatja az igazolást annak ellenőrzésére, hogy biztonságos-e a megbízható számítástechnikai környezet
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551351"
---
# <a name="attesting-sgx-enclaves"></a>SGX ENKLÁVÉHOZ enklávék igazolása

Az Azure-beli bizalmas számítástechnikai szolgáltatás olyan Intel SGX ENKLÁVÉHOZ-alapú virtuális gépeket kínál, amelyek elkülönítik a kódok vagy adatok egy részét. Ha ezekkel az [enklávékkal](confidential-computing-enclaves.md)dolgozik, érdemes lehet ellenőrizni és érvényesíteni, hogy a megbízható környezet biztonságos-e. Ez az ellenőrzés az igazolási folyamat. 

## <a name="overview"></a>Áttekintés 

Az igazolás lehetővé teszi, hogy a függő entitás nagyobb megbízhatóságot biztosítson, hogy a szoftverük (1) egy enklávéban fut, és (2) az enklávé naprakész és biztonságos. Az enklávé például arra kéri a mögöttes hardvert, hogy olyan hitelesítő adatokat állítson elő, amely igazolja, hogy az enklávé létezik a platformon. A jelentést ezután egy második enklávé is megadhatja, amely ellenőrzi, hogy a jelentés ugyanazon a platformon lett létrehozva.

![tanúsító kód az enklávéban](media/attestation/attestation.png)



Az igazolást olyan biztonságos igazolási szolgáltatás használatával kell megvalósítani, amely kompatibilis a rendszerszoftverrel és a szilíciummal. Néhány példa a használható szolgáltatásokra

- [Microsoft Azure igazolás (előzetes verzió)](../attestation/overview.md) vagy
- [Az Intel tanúsítvány-és kiépítési szolgáltatásai](https://software.intel.com/sgx/attestation-services)


mindkettő kompatibilis az Azure bizalmas számítástechnikai Intel SGX ENKLÁVÉHOZ infrastruktúrával. 

## <a name="next-steps"></a>Következő lépések
Próbálja ki az [enklávé-kompatibilis alkalmazások Microsoft Azure igazolási mintáit](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).