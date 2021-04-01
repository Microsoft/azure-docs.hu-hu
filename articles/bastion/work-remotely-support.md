---
title: 'Távoli használat a Bastion használatával: Azure Bastion'
description: Ez az oldal azt ismerteti, hogyan használhatja az Azure Bastion-t a COVID-19 világjárvány miatti távoli működés érdekében.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077949"
---
# <a name="working-remotely-using-azure-bastion"></a>Távoli munka az Azure Bastion használatával

Az Azure Bastion kulcsfontosságú szerepet játszik a távoli munkafolyamatok támogatásában azáltal, hogy lehetővé teszi, hogy az internetkapcsolattal rendelkező felhasználók hozzáférjenek az Azure Virtual Machines szolgáltatáshoz. Különösen lehetővé teszi a rendszergazdák számára, hogy az Azure-on futó alkalmazásaikat bármikor és bárhonnan, a világ bármely pontjáról kezelhesse.

>[!NOTE]
>Ez a cikk azt ismerteti, hogyan használhatja az Azure-t, az Azure-t, a Microsoft-hálózatot és az Azure partner-ökoszisztémát a COVID-19-válság miatt felmerülő hálózati problémák megoldásához.
>

## <a name="securely-access-virtual-machines"></a>Virtuális gépek biztonságos elérése

Pontosabban, az Azure Bastion biztonságos és zökkenőmentes RDP/SSH-kapcsolatot biztosít az Azure-beli virtuális hálózaton belüli virtuális gépekhez, a nyilvános IP-cím használata nélkül, közvetlenül a Azure Portal. Az Azure-beli megerősített architektúrával és főbb funkciókkal kapcsolatos további információkért tekintse meg a [Mi az az Azure Bastion](bastion-overview.md).

Az Azure Bastion virtuális hálózatokon van üzembe helyezve, ami azt jelenti, hogy a vállalatok egy Azure-ban konfigurálják és kezelhetik a virtuális gépekhez egy Azure-beli virtuális hálózaton belüli távoli felhasználói hozzáférést. Az Azure Bastion létrehozásával és kezelésével kapcsolatos útmutatásért tekintse meg a [megerősített gazdagép létrehozása](./tutorial-create-host-portal.md)című témakört.

## <a name="next-steps"></a>Következő lépések

* Konfigurálja az Azure Bastion-t a [Azure Portal](./tutorial-create-host-portal.md), a [PowerShell](bastion-create-host-powershell.md)vagy az Azure CLI használatával.

* További információért olvassa el a [megerősített gyakori kérdések](bastion-faq.md) című témakört.