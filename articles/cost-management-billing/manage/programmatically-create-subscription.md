---
title: Azure-előfizetések létrehozása programozott módon
description: Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493888"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programozott Azure-előfizetések létrehozása

Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.

Különböző REST API-k használatával az alábbi Azure-szerződéstípusokhoz hozhat létre előfizetést:

- Nagyvállalati Szerződés (EA)
- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft-partnerszerződés (MPA)

A REST API-kkal nem lehet programozott módon további előfizetéseket létrehozni más szerződéstípusokhoz.

Az előfizetések létrehozásához szükséges követelmények és részletek eltérnek a különféle szerződések és API-verziók esetében. Lásd az alábbi, adott helyzetre vonatkozó cikkeket:

Legutóbbi API-k:

- [EA-előfizetések létrehozása](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-előfizetések létrehozása](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-előfizetések létrehozása](programmatically-create-subscription-microsoft-partner-agreement.md)

Ha még [előzetes verziójú API-kat](programmatically-create-subscription-preview.md) használ, továbbra is létrehozhat velük előfizetéseket. 

Ezenkívül [ARM-sablonnal is létrehozhat előfizetéseket](create-subscription-template.md). Az ARM-sablonok segítségével REST API-kkal automatizálható az előfizetés-létrehozási folyamat. 

## <a name="next-steps"></a>Következő lépések

* Előfizetés létrehozása után elérhetővé teheti ezt a képességet más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
