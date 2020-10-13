---
title: Hibaelhárítás – a Windows nem tudta befejezni a rendszer konfigurálását
titlesuffix: Azure Virtual Machines
description: Ez a cikk az Azure-beli virtuális gépek indítását megakadályozó hibák megoldásának lépéseit ismerteti.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: bde091b4a4559c3574ee122d74574d1f9477f3fd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977048"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Hibaelhárítás – a Windows nem tudta befejezni a rendszer konfigurálását

Ez a cikk az Azure-beli virtuális gépek (VM) rendszerindítását megakadályozó problémák megoldásának lépéseit ismerteti.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen a telepítés Windows-hiba jelenik meg, miközben a Windows telepítő elindítja a szolgáltatásokat. A hiba a következő üzenetet jeleníti meg:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Az 1. ábra egy Windows-telepítési hibaüzenetet jelenít meg a következő üzenettel: "a Windows nem tudja befejezni a rendszer konfigurálását. A konfiguráció folytatásának megkísérlése érdekében indítsa újra a számítógépet. A telepítő elindítja a szolgáltatásokat.](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Ok

Ezt a hibát az okozza, hogy az operációs rendszer (OS) nem tudja befejezni a [Sysprep-folyamatot](/windows-hardware/manufacture/desktop/sysprep-process-overview). Ez a hiba akkor fordul elő, amikor egy általánosított virtuális gép kezdeti rendszerindítását kísérli meg. Ha ezzel a problémával találkozik, hozza létre újra az általánosított rendszerképet, mert a rendszerkép nem telepíthető állapotú, és nem állítható helyre.

## <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az [Azure-útmutatást a rendszerkép előkészítéséhez/rögzítéséhez](../windows/upload-generalized-managed.md) és egy új általánosított rendszerkép előkészítéséhez.