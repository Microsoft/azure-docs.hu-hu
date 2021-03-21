---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521533"
---
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd válassza a **Kapcsolódás** lehetőséget. A legördülő listából válassza a **Bastion** lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Megerősített":::

1. Miután a legördülő menüből kiválasztotta a Bastion-t, megjelenik egy oldalsó sáv, amely három lapból áll: RDP, SSH és Bastion. Mivel a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, alapértelmezés szerint a megerősített lap aktív. Válassza a **bástya használata** lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Válassza a Bastion használata lehetőséget":::

1. A **kapcsolat az Azure Bastion használatával** lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd válassza a **kapcsolat** lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Kapcsolódás":::

1. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Csatlakozás a 443-es port használatával":::
