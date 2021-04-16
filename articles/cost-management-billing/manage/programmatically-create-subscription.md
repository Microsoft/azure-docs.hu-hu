---
title: Azure-előfizetések létrehozása programozott módon
description: Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379009"
---
# <a name="create-azure-subscriptions-programmatically"></a>Azure-előfizetések létrehozása programozott módon

Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.

Különböző REST API-k használatával az alábbi Azure-szerződéstípusokhoz hozhat létre előfizetést:

- Nagyvállalati Szerződés (EA)
- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft-partnerszerződés (MPA)

REST API-okkal nem hozhat létre további előfizetéseket más szerződéstípusokhoz programozott módon.

Az előfizetések létrehozásához szükséges követelmények és részletek eltérnek a különféle szerződések és API-verziók esetében. Lásd az alábbi, adott helyzetre vonatkozó cikkeket:

Legutóbbi API-k:

- [EA-előfizetések létrehozása](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-előfizetések létrehozása](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-előfizetések létrehozása](programmatically-create-subscription-microsoft-partner-agreement.md)

Ezek a cikkek azt is bemutatja, hogyan hozhat létre előfizetéseket Azure Resource Manager sablonnal (ARM-sablonnal). Az ARM-sablonokkal automatizálható az előfizetés létrehozásának folyamata.

Ha továbbra is előzetes verziójú [API-kat használ,](programmatically-create-subscription-preview.md)továbbra is létrehozhat velük előfizetéseket. 

## <a name="next-steps"></a>Következő lépések

* Előfizetés létrehozása után elérhetővé teheti ezt a képességet más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
