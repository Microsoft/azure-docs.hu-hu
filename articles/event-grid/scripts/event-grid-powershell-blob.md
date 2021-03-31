---
title: Azure PowerShell – előfizetés a blob Storage-fiókra
description: Ez a cikk egy példa Azure PowerShell parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő Event Grid eseményekre egy Blob Storage-fiók esetében.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 15daa9843eb721ebf9caa2ae49e75fadbd0a4be8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460796"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Feliratkozás egy Blob Storage-fiók eseményeire a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy Blob Storage-fiók eseményeihez.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](/powershell/azure/get-started-azureps).
