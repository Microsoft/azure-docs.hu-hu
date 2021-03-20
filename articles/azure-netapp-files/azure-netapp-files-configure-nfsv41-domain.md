---
title: A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan konfigurálható az NFS-ügyfél a NFSv 4.1 és a Azure NetApp Files használatával.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072152"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz

A Nfsv4 névleképezője bevezeti a hitelesítési tartomány fogalmát. Azure NetApp Files jelenleg a szolgáltatásból az NFS-ügyfél felé irányuló, csak a legfelső szintű felhasználó-hozzárendelést támogatja. Ha a NFSv 4.1 funkciót Azure NetApp Files használatával szeretné használni, frissítenie kell az NFS-ügyfelet.

## <a name="default-behavior-of-usergroup-mapping"></a>A felhasználó/csoport leképezésének alapértelmezett viselkedése

A gyökér-leképezés alapértelmezett értéke a `nobody` felhasználó, mert a nfsv4 névleképezője tartomány alapértelmezés szerint be van állítva `localdomain` . Ha a Azure NetApp Files NFSv 4.1-es kötetet root-ként csatlakoztatja, a fájlok engedélyei a következőképpen jelennek meg:  

![A felhasználó/csoport leképezésének alapértelmezett viselkedése a NFSv 4.1 esetében](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Ahogy a fenti példában is látható, a felhasználónak kell `file1` lennie `root` , de alapértelmezés szerint le van képezve `nobody` .  Ez a cikk bemutatja, hogyan állíthatja be a felhasználót úgy, hogy `file1` `root` a beállítást a értékre módosítja `idmap Domain` `defaultv4iddomain.com` .  

## <a name="steps"></a>Lépések 

1. Szerkessze a `/etc/idmapd.conf` fájlt az NFS-ügyfélen.   
    Szüntesse meg a sor megjegyzését `#Domain` (azaz távolítsa el a `#` sort), és módosítsa az értéket a következőre: `localdomain` `defaultv4iddomain.com` . 

    Kezdeti konfiguráció: 
    
    ![A NFSv 4.1 kezdeti konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Frissített konfiguráció:
    
    ![Frissített konfiguráció a NFSv 4.1-es verziójához](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Válassza le a jelenleg csatlakoztatott NFS-köteteket.
3. Frissítse a `/etc/idmapd.conf` fájlt.
4. Indítsa újra a `rpcbind` szolgáltatást a gazdagépen ( `service rpcbind restart` ), vagy egyszerűen indítsa újra a gazdagépet.
5. Csatlakoztassa az NFS-köteteket igény szerint.   

    Lásd: [kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Az alábbi példa az eredményül kapott felhasználó/csoport módosítását mutatja be: 

![Képernyőkép, amely az eredményül kapott felhasználó/csoport módosítására mutat példát.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

A példa azt mutatja, hogy a felhasználó/csoport mostantól a verzióról a verzióra módosult `nobody` `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Más (nem gyökérszintű) felhasználók és csoportok viselkedése

Azure NetApp Files támogatja a helyi felhasználókat (a gazdagépen helyileg létrehozott felhasználókat), akik NFSv 4.1-köteteken található fájlokhoz vagy mappákhoz vannak társítva. A szolgáltatás azonban jelenleg nem támogatja a felhasználók/csoportok több csomóponton történő leképezését. Ezért az egyik gazdagépen létrehozott felhasználók nem képezhetők le alapértelmezés szerint a másik gazdagépen létrehozott felhasználók számára. 

A következő példában `Host1` három meglévő tesztelési felhasználói fiók van ( `testuser01` , `testuser02` , `testuser03` ): 

![Képernyőkép, amely azt mutatja, hogy a Host1 három meglévő tesztelési felhasználói fiókkal rendelkezik.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`Vegye figyelembe, hogy a teszt felhasználói fiókok nem lettek létrehozva, de a kötet mindkét gazdagépre csatlakoztatva van:

![Eredményül kapott konfiguráció a NFSv 4.1 számára](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Következő lépés 

[Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

