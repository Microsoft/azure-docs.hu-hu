---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70049049"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Ebben a cikkben egy olyan háttér-szolgáltatást hoz létre, amely egy adott eszköz közvetlen metódusának meghívására ütemez egy feladatot, és az egyes feladatok állapotát figyeli. Ezeknek a műveleteknek a végrehajtásához a szolgáltatásnak szüksége van a **beállításjegyzék olvasási** és **beállításjegyzék-írási** engedélyeire. Alapértelmezés szerint minden IoT hub egy **registryReadWrite** nevű megosztott hozzáférési házirenddel jön létre, amely megadja ezeket az engedélyeket.
