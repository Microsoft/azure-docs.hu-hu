---
title: A Linux teljesítmény-teljesítménymutatóinak Azure NetApp Filesa | Microsoft Docs
description: A Linux rendszerhez készült Azure NetApp Files teljesítmény-teljesítménymutatókat ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82614589"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>A Linux rendszerre vonatkozó teljesítménytesztek Azure NetApp Files

Ez a cikk a Linux rendszerhez készült Azure NetApp Files teljesítmény-teljesítménymutatókat ismerteti.

## <a name="linux-scale-out"></a>Linux-felskálázás

Ez a szakasz a Linux munkaterhelés teljesítményére és a számítási feladatok IOPS vonatkozó teljesítménymutatókat ismerteti.

### <a name="linux-workload-throughput"></a>A Linux munkaterhelés átviteli sebessége  

Az alábbi gráf a 64-Bináris prefixum (KiB) szekvenciális számítási feladatot és egy 1 TiB-os munkakészletet jelöl. Azt mutatja, hogy egyetlen Azure NetApp Files kötet képes kezelni a ~ 1 600 MiB/s Pure szekvenciális írások és a ~ 4 500 MiB/s Pure szekvenciális olvasások között.  

A gráf egyszerre 10%-os csökkenést ábrázol, a tiszta olvasástól a tiszta írásig. Azt mutatja be, hogy mit várhat, ha eltérő olvasási/írási arányt használ (100%: 0%, 90%: 10%, 80%: 20% stb.).

![A Linux munkaterhelés átviteli sebessége](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux munkaterhelés IOPS  

A következő gráf egy 4 Bináris prefixum (KiB) véletlenszerű számítási feladatot és egy 1 TiB-os munkakészletet képvisel. A gráf azt mutatja, hogy egy Azure NetApp Files kötet képes kezelni a ~ 130 000 Pure Random writes és a ~ 460 000 Pure véletlenszerű olvasások között.  

Ez a gráf egyszerre 10%-os csökkenést ábrázol, a tiszta olvasástól a tiszta írásig. Azt mutatja be, hogy mit várhat, ha eltérő olvasási/írási arányt használ (100%: 0%, 90%: 10%, 80%: 20% stb.).

![Linux munkaterhelés IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux-felskálázás  

A Linux 5,3 kernel lehetővé teszi az együgyféles kibővíthető hálózatkezelést az NFS-hez `nconnect` . Az ebben a szakaszban található diagramok az ügyféloldali csatlakoztatási lehetőség NFSv3-vel való ellenőrzésének eredményeit mutatják be. A szolgáltatás a SUSE (SLES12SP4-től kezdve) és az Ubuntu (a 19,10-es kiadástól kezdve) verzióban érhető el. A fogalom a többcsatornás SMB és az Oracle Direct NFS esetében is hasonló.

A gráfok összehasonlítják a `nconnect` nem csatlakoztatott csatlakoztatott kötetek előnyeit. A gráfokban a FIO az USA-west2 Azure-régióban egyetlen D32s_v3 példányból generálta a munkaterhelést.

### <a name="linux-read-throughput"></a>Linux olvasási sebesség  

Az alábbi ábrák a ~ 3 500 MiB/s olvasását mutatja be `nconnect` , nagyjából 2,3 x nem `nconnect` .

![Linux olvasási sebesség](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux írási sebesség  

A következő diagramok szekvenciális írásokat jelenítenek meg. Ezek azt jelzik, hogy a `nconnect` szekvenciális írásoknak nincs érzékelhető előnyük. 1 500 a MiB/s nagyjából a szekvenciális írási mennyiség felső korlátja, valamint a D32s_v3-példány kimenő korlátja.

![Linux írási sebesség](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux-olvasási IOPS  

A következő diagramok véletlenszerűen olvasnak a ~ 200 000 olvasási IOPS `nconnect` , nagyjából 3x nem `nconnect` .

![Linux-olvasási IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux-írási IOPS  

Az alábbi grafikonok véletlenszerű írásokat mutatnak a ~ 135 000 írási IOPS `nconnect` , amely nagyjából 3x-ot nem `nconnect` .

![Linux-írási IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Következő lépések

- [Azure NetApp Files: a lehető legtöbbet hozza ki a felhőalapú tárterületről](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
