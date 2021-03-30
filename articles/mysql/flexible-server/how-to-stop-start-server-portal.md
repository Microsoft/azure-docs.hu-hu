---
title: Leállítás/indítás – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet leállítani/elindítani a műveleteket a Azure Database for MySQL az Azure Portalon keresztül.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241922"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL rugalmas kiszolgáló leállítása/elindítása (előzetes verzió)

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti a rugalmas kiszolgáló leállításának és indításának menetét.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for MySQL rugalmas kiszolgálóval kell rendelkeznie.

## <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a leállítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Állítsa le a rugalmas kiszolgálót."::: 

3.  A kiszolgáló leállításának megerősítéséhez kattintson az **Igen** gombra.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Erősítse meg a rugalmas kiszolgáló leállítását."::: 

> [!NOTE]
> A kiszolgáló leállítása után a többi felügyeleti művelet nem érhető el a rugalmas kiszolgáló számára.

## <a name="start-a-stopped-server"></a>Leállított kiszolgáló indítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az elindítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Start** gombra az eszköztáron.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Indítsa el a rugalmas kiszolgálót.":::  

> [!NOTE]
> A kiszolgáló elindítása után az összes felügyeleti művelet elérhető lesz a rugalmas kiszolgáló számára.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).

