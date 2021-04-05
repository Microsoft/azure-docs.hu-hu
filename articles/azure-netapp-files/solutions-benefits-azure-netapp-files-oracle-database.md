---
title: A Azure NetApp Files és a Oracle Database használatának előnyei | Microsoft Docs
description: Leírja a technológiát, és teljesítménybeli összehasonlítást biztosít az Oracle Direct NFS (dNFS) és a hagyományos NFS-ügyfél között. A dNFS és a Azure NetApp Files használatának előnyeit mutatja be.
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932498"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Az Azure NetApp Files Oracle Database-szel való használatának előnyei

Az Oracle Direct NFS (dNFS) lehetővé teszi, hogy a teljesítmény nagyobb legyen, mint az operációs rendszer saját NFS-illesztőprogramja. Ez a cikk ismerteti a technológiát, és teljesítménybeli összehasonlítást biztosít a dNFS és a hagyományos NFS-ügyfél (kernel NFS) között. Emellett a dNFS előnyeit és könnyű használatát is megjeleníti Azure NetApp Files használatával.  

## <a name="how-oracle-direct-nfs-works"></a>Az Oracle Direct NFS működése

Az alábbi összefoglalás azt ismerteti, hogyan működik az Oracle Direct NFS magas szinten:

* Az Oracle Direct NFS megkerüli az operációs rendszer pufferének gyorsítótárát. Az adatokat a rendszer csak egyszer gyorsítótárazza a felhasználói térben, kiküszöbölve a memória-másolatok terhelését.  

* A hagyományos NFS-ügyfél egyetlen hálózati folyamatot használ, ahogy az az alábbi ábrán látható:    

    ![Hagyományos NFS-ügyfél egyetlen hálózati folyamat használatával](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Az Oracle Direct NFS tovább javítja a teljesítményt a hálózati forgalom több hálózati folyamaton keresztüli terheléselosztásával. A tesztelt és az alább látható módon 650 különböző hálózati kapcsolatok lettek dinamikusan létrehozva a Oracle Database:  

    ![Az Oracle Direct NFS teljesítményének javítása](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

A [közvetlen NFS-hez készült Oracle-GYIK](http://www.orafaq.com/wiki/Direct_NFS) azt mutatja, hogy az Oracle dNFS egy optimalizált NFS-ügyfél. Gyors és méretezhető hozzáférést biztosít a NAS-tárolóeszközökön található NFS-tárolóhoz (a TCP/IP protokollon keresztül érhető el). a dNFS az adatbázis-kernelhez van építve, amely az ASM, amelyet elsősorban a DAS vagy a SAN Storage használ. Ennek megfelelően *az útmutató a dNFS használata a NAS-tároló megvalósításakor és az ASM használata a Tárolóhálózati tároló megvalósításakor.*

az dNFS az alapértelmezett beállítás az Oracle 18c.

a dNFS Oracle Database 11g kezdődően érhető el. Az alábbi ábra összehasonlítja a dNFS natív NFS-sel. A dNFS használatakor egy Azure-beli virtuális gépen futó Oracle-adatbázis nagyobb I/O-t tud vezetni a natív NFS-ügyféllel.

![Az Oracle és a Azure NetApp Files dNFS összehasonlítása natív NFS-sel](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Engedélyezheti vagy letilthatja a dNFS két parancs futtatásával és az adatbázis újraindításával.

Engedélyezés:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

A letiltáshoz:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files az Oracle Direct NFS-sel együtt

Az Oracle-dNFS teljesítménye javítható a Azure NetApp Files szolgáltatással. A szolgáltatás teljes körű irányítást biztosít az alkalmazás teljesítményére. Rendkívül nagy igényű alkalmazásoknak is eleget tesz. Az Azure NetApp Files Oracle-dNFS kombinációja nagy mértékben kihasználja a számítási feladatokat.

## <a name="next-steps"></a>Következő lépések

- [Megoldásarchitektúrák az Azure NetApp Filesszal](azure-netapp-files-solution-architectures.md)
- [Az Azure-beli Oracle-alkalmazások és-megoldások áttekintése](../virtual-machines/workloads/oracle/oracle-overview.md)