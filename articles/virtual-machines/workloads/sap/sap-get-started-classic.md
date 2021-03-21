---
title: SAP használata Linux rendszerű virtuális gépeken | Microsoft Docs
description: Ismerje meg az SAP használatát Linux virtuális gépeken (VM) a Microsoft Azure megoldásban
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-sap
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: bf0ae8bdd800f5fa43df73eca312504f56fc009a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675386"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Az SAP használata az Azure-beli Linux rendszerű virtuális gépeken
A felhőalapú számítástechnika széles körben használt fogalom, amely egyre fontosabbá válik az informatikai iparágban, a kisvállalatok esetében ugyanúgy, mint a nagy, nemzetközi vállalatok számára. A Microsoft Azure a Microsoft új lehetőségek széles skáláját nyújtó felhőszolgáltatás-platformja. Az ügyfelek most már gyorsan építhetnek ki és vonhatnak ki az üzemből felhőszolgáltatásként rendelkezésre bocsájtott alkalmazásokat, ami azt jelenti, hogy többé nem érvényesülnek a technikai vagy költségvetési korlátozások. A vállalatoknak nem kell időt és forrásokat befektetni a hardverinfrastruktúrába: ehelyett az alkalmazásra, az üzleti folyamatokra és az ügyfeleknek és felhasználóknak nyújtott értékre koncentrálhatnak.

A Microsoft Azure Virtual Machines szolgáltatással a Microsoft átfogó infrastruktúra-szolgáltatási (IaaS) platformot kínál. Az Azure virtuális gépek támogatják az SAP NetWeaver-alapú alkalmazásokat (IaaS). Az alábbi tanulmányok azt írják le, hogyan tervezhet és implementálhat SAP NetWeaver-alapú alkalmazásokat az Azure-beli Windows rendszerű virtuális gépeken. Emellett SAP NetWeaver-alapú alkalmazásokat is alkalmazhat [Windows rendszerű virtuális gépeken](./get-started.md?toc=/azure/virtual-machines/windows/classic/toc.json).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>SAP NetWeaver az Azure SUSE Linux Virtual Machines
Title: SAP NetWeaver tesztelése Microsoft Azure SUSE Linux rendszerű virtuális gépeken

Összefoglalás: az SAP NetWeaver Azure Linux rendszerű virtuális gépeken való futtatásának hivatalos SAP-támogatása ebben az időpontban. Ennek ellenére előfordulhat, hogy az ügyfelek valamilyen tesztelést végeznek, vagy fontolóra veszik az SAP-bemutató vagy a betanítási rendszerek futtatását az Azure Linux virtuális gépeken, ha nincs szükség az SAP-támogatással való kapcsolatfelvételre. Ez a cikk segítséget nyújt az Azure SUSE Linux rendszerű virtuális gépek beállításához az SAP futtatásához, és néhány alapvető tudnivalót biztosít a lehetséges buktatók elkerüléséhez.

Frissítve: december 2015

[Ez a cikk itt található](./sap-deployment-checklist.md?toc=/azure/virtual-machines/linux/toc.json)
