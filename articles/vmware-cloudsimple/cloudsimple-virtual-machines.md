---
title: Virtuális gépek – áttekintés
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg a CloudSimple virtuális gépeket és azok előnyeit. A VMware virtuális gépeket a Azure Portal kezelheti.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141976"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple Virtual Machines – áttekintés

A CloudSimple lehetővé teszi a VMware virtuális gépek (VM-EK) kezelését a Azure Portal.  A vSphere-fürtről származó fürt vagy erőforráskészlet az Azure-on keresztül felügyelhető az előfizetéshez való hozzárendeléssel.

Ha CloudSimple virtuális gépet szeretne létrehozni az Azure-ból, egy virtuálisgép-sablonnak léteznie kell a saját Felhőbeli vCenter.  A sablon az operációs rendszer és az alkalmazások testreszabására szolgál.  A sablon virtuális gépe megerősíthető, hogy megfeleljen a vállalati biztonsági házirendeknek.  A sablon segítségével virtuális gépeket hozhat létre, majd önkiszolgáló modell használatával felhasználhatja azokat a Azure Portal.

## <a name="benefits"></a>Előnyök

A Azure Portal CloudSimple virtuális gépei olyan önkiszolgáló mechanizmust biztosítanak, amellyel a felhasználók VMware virtuális gépeket hozhatnak létre és kezelhetnek.

* CloudSimple virtuális gép létrehozása a saját Felhőbeli vCenter
* Virtuális gép tulajdonságainak kezelése
  * Lemezek hozzáadása/eltávolítása
  * Hálózati adapterek hozzáadása/eltávolítása
* A CloudSimple virtuális gép energiagazdálkodási műveletei
  * Bekapcsolás és kikapcsolás
  * Virtuális gép alaphelyzetbe állítása
* Virtuális gép törlése

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan használhatja [a VMWare virtuális gépeket az Azure](quickstart-create-vmware-virtual-machine.md) -ban
* Ismerje meg, hogyan [képezhető le Azure-előfizetése](azure-subscription-mapping.md)
