---
title: Leállítás/indítás – Azure Portal – Azure Database for PostgreSQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet leállítani/elindítani a műveleteket a Azure Database for PostgreSQL az Azure Portalon keresztül.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935890"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL rugalmas kiszolgáló leállítása/elindítása (előzetes verzió)

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló jelenleg előzetes verzióban érhető el.

Ez a cikk részletes útmutatást nyújt egy rugalmas kiszolgáló leállításához és elindításához.

## <a name="pre-requisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for PostgreSQL rugalmas kiszolgálóval kell rendelkeznie.

## <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a leállítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.

> [!NOTE]
> A kiszolgáló leállítása után más felügyeleti műveletek nem érhetők el a rugalmas kiszolgáló számára.

Vegye figyelembe, hogy a leállított kiszolgálók a hét napja után automatikusan újraindulnak. A függőben lévő karbantartási frissítések a kiszolgáló következő indításakor lépnek életbe.

## <a name="start-a-stopped-server"></a>Leállított kiszolgáló indítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az elindítani kívánt rugalmas kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Start** gombra az eszköztáron.

> [!NOTE]
> A kiszolgáló elindítása után az összes felügyeleti művelet elérhető lesz a rugalmas kiszolgáló számára.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Database for PostgreSQL rugalmas kiszolgáló számítási és tárolási lehetőségeiről](./concepts-compute-storage.md).
