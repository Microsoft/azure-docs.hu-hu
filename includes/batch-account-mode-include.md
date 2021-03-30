---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68323890"
---
> [!NOTE]
> Batch-fiók létrehozásakor kétféle *készletfoglalási* mód közül választhat: **felhasználói előfizetés** és **Batch szolgáltatás**. A legtöbb esetben az alapértelmezett „Batch szolgáltatás” mód használata javasolt, ahol a készleteket a rendszer a háttérben foglalja le Azure által felügyelt előfizetésekben. A szintén választható „Felhasználói előfizetés” mód esetében a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. A „Felhasználói előfizetés” módot akkor kell használnia, ha az [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/) szolgáltatással szeretne Batch-készleteket létrehozni. Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, az előfizetését az Azure Batchben is regisztrálnia kell, és egy Azure Key Vaulttal is társítania kell.