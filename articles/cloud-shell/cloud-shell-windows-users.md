---
title: Azure Cloud Shell a Windows-felhasználók számára | Microsoft Docs
description: Útmutató a Linux rendszereket nem ismerő felhasználók számára
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508946"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell a Cloud Shellben Windows-felhasználóknak

Május 2018-én a módosítások a PowerShellben Azure Cloud Shell-ben [jelent](https://azure.microsoft.com/blog/pscloudshellrefresh/) meg.
A Azure Cloud Shell PowerShell-felülete mostantól a [PowerShell Core 6](https://github.com/powershell/powershell) rendszert futtatja egy Linux-környezetben.
Ennek a változásnak az a része, hogy az Cloud Shell PowerShell-felülete némileg eltér a várt Windows PowerShell-környezethez képest.

## <a name="file-system-case-sensitivity"></a>Fájlrendszerbeli kis-és nagybetűk megkülönböztetése

A fájlrendszer megkülönbözteti a kis-és nagybetűket a Windowsban, míg a Linux rendszeren a kis-és nagybetűk megkülönböztetésére is.
Korábban `file.txt` és `FILE.txt` ugyanezt a fájlt tekintették, de mostantól különböző fájloknak számítanak.
A fájlrendszerben a megfelelő burkolatot kell használni `tab-completing` .
A PowerShell-specifikus élmények, például a `tab-completing` parancsmagok neve, paramétere és értéke nem megkülönbözteti a kis-és nagybetűket.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell-aliasok vs Linux-segédprogramok

Egyes meglévő PowerShell-aliasok nevei megegyeznek a beépített Linux-parancsokkal, például:,, `cat` `ls` `sort` `sleep` stb. A PowerShell Core 6-ban a beépített Linux-parancsokkal ütköző aliasok el lettek távolítva.
Alább láthatók azok a közös aliasok, amelyek el lettek távolítva, valamint egyenértékű parancsaik:  

|Alias eltávolítva   |Egyenértékű parancs   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>$HOME fenntartása

A korábbi felhasználók csak parancsfájlokat és egyéb fájlokat maradhatnak a felhőalapú meghajtóban.
Most a felhasználó $HOME könyvtára is megmarad a munkamenetek között.

## <a name="powershell-profile"></a>PowerShell-profil

Alapértelmezés szerint a rendszer nem hozza létre a felhasználó PowerShell-profilját.
A profil létrehozásához hozzon létre egy `PowerShell` könyvtárat a alatt `$HOME/.config` .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

A alkalmazásban `$HOME/.config/PowerShell` a profil fájljait ( `profile.ps1` és/vagy) hozhatja létre `Microsoft.PowerShell_profile.ps1` .

## <a name="whats-new-in-powershell-core-6"></a>A PowerShell Core 6 újdonságai

A PowerShell Core 6 újdonságával kapcsolatos további információkért tekintse át a [PowerShell-docs](/powershell/scripting/whats-new/what-s-new-in-powershell-70) és a [első lépések a PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogbejegyzésben.