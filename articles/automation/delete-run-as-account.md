---
title: Azure Automation futtató fiók törlése
description: Ez a cikk azt ismerteti, hogyan törölhető egy futtató fiók a PowerShell-lel vagy a Azure Portalból.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055893"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók törlése

A Azure Automation futtató fiókok hitelesítést biztosítanak a Azure Resource Manager vagy a klasszikus Azure üzemi modell erőforrásainak automatizálási runbookok és egyéb Automation-funkciók használatával történő kezeléséhez. Ez a cikk egy futtató vagy klasszikus futtató fiók törlését ismerteti. A művelet során a rendszer megőrzi az Automation-fiókot. A futtató fiók törlését követően újra létrehozhatja azt a Azure Portal vagy a megadott PowerShell-parancsfájl használatával.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Futtató fiók vagy klasszikus futtató fiók törlése

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

3. A Futtató fiókok tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván.

4. A kiválasztott fiók Tulajdonságok paneljén kattintson a **Törlés** elemre.

   ![Futtató fiók törlése](media/delete-run-as-account/automation-account-delete-run-as.png)

5. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="next-steps"></a>Következő lépések

A futtató vagy klasszikus futtató fiók újbóli létrehozásával kapcsolatban lásd a [futtató fiókok létrehozása](create-run-as-account.md)című témakört.