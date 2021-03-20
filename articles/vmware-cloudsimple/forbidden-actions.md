---
title: Tiltott műveletek a emelt szintű hozzáférés során
description: A VMware Engine visszaállítja a módosításokat, hogy a szolgáltatás zavartalan maradjon, ha a VMware Engine a következő tiltott műveletek bármelyikét észleli.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915433"
---
# <a name="forbidden-actions-during-elevated-access"></a>Tiltott műveletek a emelt szintű hozzáférés során

A jogosultságszint-emelési időszak során bizonyos műveletek tiltottak. Ha a VMware Engine a következő tiltott műveletek bármelyikét észleli, a VMware Engine visszaállít a módosításokat, hogy biztosítsa, hogy a szolgáltatás zavartalan maradjon.

## <a name="cluster-actions"></a>Fürt műveletei

- Fürt eltávolítása a vCenter-ből.
- VSphere magas rendelkezésre állásának (HA) módosítása a fürtön.
- Gazdagép hozzáadása a fürthöz a vCenter-ből.
- Gazdagép eltávolítása a fürtből a vCenter-ből.

## <a name="host-actions"></a>Gazdagép műveletei

- Adattárolók eltávolítása ESXi-gazdagépen.
- A vCenter-ügynök eltávolítása a gazdagépről.
- A gazdagép konfigurációjának módosítása.
- A gazdagép profiljainak módosítása.
- Gazdagép elhelyezése karbantartási módba.

## <a name="network-actions"></a>Hálózati műveletek

- Az alapértelmezett elosztott virtuális kapcsoló (DVS) törlése egy privát felhőben.
- Gazdagép eltávolítása az alapértelmezett DVS-ből.
- Bármely DVS-beállítás importálása.
- A DVS beállításainak újrakonfigurálása.
- Bármely DVS frissítése.
- A felügyeleti Portcsoport törlése.
- A felügyeleti Portcsoport szerkesztése.

## <a name="roles-and-permissions-actions"></a>Szerepkörök és engedélyek műveletei

- Globális szerepkör létrehozása.
- Bármely felügyeleti objektumra vonatkozó engedély módosítása vagy törlése.
- Az alapértelmezett szerepkörök módosítása vagy eltávolítása.
- Növelje a szerepkör jogosultságait a Felhőbeli tulajdonosi szerepkörnél magasabbra.

## <a name="other-actions"></a>Egyéb műveletek

- Az alapértelmezett licencek eltávolítása:
  - vCenter Server
  - ESXi-csomópontok
  - NSX – T
  - HCX
- A felügyeleti erőforráskészlet módosítása vagy törlése.
- Klónozási felügyeleti virtuális gépek.


## <a name="next-steps"></a>Következő lépések
[CloudSimple-karbantartás és-frissítések](cloudsimple-maintenance-updates.md) 
