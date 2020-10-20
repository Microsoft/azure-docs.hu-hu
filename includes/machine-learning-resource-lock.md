---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204462"
---
Az Azure lehetővé teszi az erőforrások _zárolásának_ elhelyezését, így azok nem törölhetők vagy csak olvashatók. __Az erőforrások zárolása nem várt eredményekhez vezethet.__ Néhány olyan művelet, amely úgy tűnik, hogy nem módosítja az erőforrást, valójában a zárolás által blokkolt műveletekre van szükség. 

Ha például egy törlési zárolást szeretne alkalmazni a munkaterület erőforráscsoport számára, akkor az Azure ML számítási fürtök méretezési műveletei meggátolják a skálázást.

Az erőforrások zárolásával kapcsolatos további információkért lásd: [erőforrások zárolása a váratlan változások megelőzése érdekében](../articles/azure-resource-manager/management/lock-resources.md).