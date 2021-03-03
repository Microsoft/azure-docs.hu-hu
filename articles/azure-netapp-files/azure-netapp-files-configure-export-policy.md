---
title: Az NFS-kötet exportálási házirendjének konfigurálása – Azure NetApp Files
description: Leírja, hogyan konfigurálható az exportálási házirend az NFS-kötetekhez való hozzáférés vezérléséhez Azure NetApp Files használatával
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740119"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportálási szabályzat konfigurálása NFS-kötethez

Az exportálási házirend konfigurálásával szabályozhatja a Azure NetApp Files kötethez való hozzáférést. Azure NetApp Files exportálási házirend az NFS protokollt (NFSv3 és NFSv 4.1) és a kettős protokollt (NFSv3 és SMB) használó köteteket támogatja. 

Legfeljebb öt exportálási szabályzatot hozhat létre.

## <a name="configure-the-policy"></a>A szabályzat konfigurálása 

1.  A **kötetek** lapon válassza ki azt a kötetet, amelyhez konfigurálni szeretné az exportálási szabályzatot, majd válassza a **házirend exportálása** lehetőséget. Az exportálási házirendet a kötet létrehozása során is konfigurálhatja.

2.  Exportálási házirend létrehozásához a következő információkat kell megadni:   
    * **Index**: a szabályhoz tartozó index számának megadása.  
      
      Egy exportálási szabályzat legfeljebb öt szabályból állhat. A szabályok kiértékelése az indexszámok alapján felállított listában elfoglalt helyük alapján történik. Az alacsonyabb indexszámmal rendelkező szabályok lesznek először kiértékelve. Az 1-es indexszámmal rendelkező szabály például előbb lesz kiértékelve, mint a 2-es indexszámú szabály. 

    * **Engedélyezett ügyfelek**: az értéket a következő formátumok egyikében adhatja meg:  
      * IPv4-címek. Például: `10.1.12.24`
      * Az alhálózati maszkkal rendelkező IPv4-címek száma bitek számával megadva. Például: `10.1.12.10/4`
      * Vesszővel tagolt IP-címek. Egyetlen szabályban több gazdagép IP-címe is megadható, vesszővel elválasztva. Például: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Hozzáférés**: válasszon a következő hozzáférési típusok közül:  
      * Nincs hozzáférés 
      * Olvasás és írás
      * Csak olvasási engedély

    * **Írásvédett** és írható **/** olvasható: Ha Kerberos-titkosítást használ a nfsv 4.1 használatával, kövesse az [Nfsv 4.1 Kerberos-titkosítás konfigurálása](configure-kerberos-encryption.md)című témakör útmutatását.  A Kerberos teljesítményére gyakorolt hatásért lásd: a [Kerberos teljesítményére gyakorolt hatás a nfsv 4.1-es köteteken](performance-impact-kerberos.md). 

      ![Kerberos biztonsági beállítások](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Gyökérszintű hozzáférés**: megadhatja, hogy a `root` fiók hozzáférhet-e a kötethez.  Alapértelmezés szerint a gyökérszintű hozzáférés be van **kapcsolva**, és a `root` fiók hozzáfér a kötethez.

      ![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Következő lépések 
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)
