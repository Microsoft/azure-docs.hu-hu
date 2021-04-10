---
title: Azure-hálózat – az utazás késési statisztikája | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utakra vonatkozó késések statisztikáit.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: bc2d7bb7ba17a4a47fecf2144157f79f5367fca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059169"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-alapú hálózati időszeletelési késések statisztikája

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések gyűjtése a ThousandEyes-ügynököktől történik, amelyek világszerte Azure Cloud-régiókban futnak, amelyek folyamatosan küldenek hálózati mintavételt egymás között 1 percenként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="december-2020-round-trip-latency-figures"></a>December 2020 időarányos késési számok

Az Azure-régiók közötti, az elmúlt 30 napban (2020. december 31-én végződő) havi átlagos menettérti időpontok alább láthatók. A [ThousandEyes](https://thousandeyes.com)a következő méréseket látja el.

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).
