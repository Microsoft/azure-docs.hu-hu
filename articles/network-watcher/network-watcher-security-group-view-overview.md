---
title: Bevezetés az Azure-beli biztonsági szabályok Network Watcher | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher – Hatályos biztonsági szabályok nézet funkcióról
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778605"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Bevezetés az Azure Network Watcher

A hálózati biztonsági csoportok alhálózati szinten vagy hálózati adapterek szintjén vannak társítva. Ha alhálózati szinten van társítva, akkor az alhálózaton található összes virtuálisgép-példányra vonatkozik. Az hatályos biztonsági szabályok nézet visszaadja az összes konfigurált NSG-t és szabályt, amelyek egy virtuális gép hálózati adapterének és alhálózatának szintjén vannak társítva, és betekintést nyújtanak a konfigurációba. Emellett a rendszer a virtuális gép minden egyes NIC-hez visszaadja az hatályos biztonsági szabályokat. Az Effective security rules (Hatályos biztonsági szabályok) nézet használatával felmérheti a virtuális gépek hálózati biztonsági réseit, például a nyitott portokat. Azt is ellenőrizheti, hogy a hálózati biztonsági csoport a várt módon működik-e a konfigurált és a jóváhagyott biztonsági [szabályok összehasonlítása alapján.](network-watcher-nsg-auditing-powershell.md)

A biztonsági megfelelőségre és a naplózásra egy további kiterjesztett alkalmazásra is szükség van. A biztonsági szabályok egy előíró készletét definiálhatja a szervezet biztonsági szabályozásának modelljeként. Az időszakos megfelelőségi naplózás programozott módon valósítható meg az előíró szabályok és a hálózatban az egyes virtuális gépekre vonatkozó hatályos szabályok összehasonlításával.

A portálon az egyes hálózati adapterek szabályai jelennek meg, és bejövő és kimenő szerint vannak csoportosítva. Ez egyszerű áttekintést nyújt a virtuális gépekre alkalmazott szabályokról. Egy letöltési gomb biztosítja az összes biztonsági szabály egyszerű letöltését a laptól függetlenül egy CSV-fájlba.

![biztonsági csoport nézet][1]

Kiválaszthat szabályokat, és megnyílik egy új panel, amely megjeleníti a hálózati biztonsági csoportot, valamint a forrás- és célelőtagokat. Ezen a panelen közvetlenül a Hálózati biztonsági csoport erőforráshoz navigálhat.

![Lebontása][2]

### <a name="next-steps"></a>Következő lépések

Az effective *security groups* (Hatályos biztonsági csoportok) funkciót az alább felsorolt egyéb módszerekkel is használhatja:
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Azure CLI](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

A Hálózati biztonsági csoport beállításainak naplózása a PowerShell használatával cikkből megtudhatja, hogyan naplólhatja [a hálózati biztonsági csoport beállításait.](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png