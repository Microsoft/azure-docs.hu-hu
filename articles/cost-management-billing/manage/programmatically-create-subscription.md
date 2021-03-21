---
title: Programozott Azure-előfizetések létrehozása
description: Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9ec0ffeb930fd9285f34ad9ba9e6aa606b15b5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593888"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programozott Azure-előfizetések létrehozása

Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.

Különböző REST API-k használatával az alábbi Azure-szerződéstípusokhoz hozhat létre előfizetést:

- Nagyvállalati Szerződés (EA)
- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft-partnerszerződés (MPA)

A REST API-kkal nem lehet programozott módon további előfizetéseket létrehozni más szerződési típusokhoz.

Az előfizetések létrehozásához szükséges követelmények és részletek eltérnek a különféle szerződések és API-verziók esetében. Lásd az alábbi, adott helyzetre vonatkozó cikkeket:

Legutóbbi API-k:

- [EA-előfizetések létrehozása](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-előfizetések létrehozása](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-előfizetések létrehozása](programmatically-create-subscription-microsoft-partner-agreement.md)

Ezek a cikkek azt is mutatják be, hogyan lehet előfizetéseket létrehozni egy Azure Resource Manager sablonnal (ARM-sablon). Egy ARM-sablon segíti az előfizetés-létrehozási folyamat automatizálását.

Ha továbbra is az [előzetes verziójú API-kat](programmatically-create-subscription-preview.md)használja, továbbra is létrehozhat előfizetéseket. 

## <a name="next-steps"></a>Következő lépések

* Előfizetés létrehozása után elérhetővé teheti ezt a képességet más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
