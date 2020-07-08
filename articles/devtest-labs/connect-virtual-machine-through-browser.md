---
title: Kapcsolódás a virtuális gépekhez egy böngészőben – Azure | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a virtuális gépekhez egy böngészőben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b1e50a15673199a4bf95956ae66e6b58d6d41d0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483737"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Kapcsolódás a virtuális gépekhez egy böngészőben 

A DevTest Labs integrálható az [Azure Bastion](https://docs.microsoft.com/azure/bastion/)szolgáltatással, amely lehetővé teszi a virtuális gépekhez való kapcsolódást egy böngészőben. A funkció a DevTest Labs szolgáltatásban való engedélyezésével kapcsolatos további információkért lásd: [a böngészőalapú kapcsolatok engedélyezése a labor virtuális gépeken](enable-browser-connection-lab-virtual-machines.md).

Ha a *böngésző kapcsolódása* engedélyezve van, a labor-felhasználók egy böngészőben érhetik el a virtuális gépeket.  

## <a name="create-a-lab-virtual-machine"></a>Tesztkörnyezet virtuális gép létrehozása

Először létre kell hoznia a labor virtuális gépet egy olyan virtuális hálózaton belül, amelyen a Bastion konfigurálva van. Válassza ki a létrehozott második **alhálózatot** , ne pedig a AzureBastionSubnet. A virtuális hálózatot a **Speciális beállítások** lapon választhatja ki a virtuális gépek létrehozása során.

![Virtuális gép létrehozása](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuális gép elindítása böngészőben

A virtuális gép létrehozása után elindíthatja azt egy böngészőben, ha a *böngésző csatlakoztatva* gombra kattint, és beírja a számítógép felhasználónevét és jelszavát.  

![Indítás böngészőben](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Következő lépések

[Virtuális gép hozzáadása laborhoz Azure DevTest Labs](devtest-lab-add-vm.md)
