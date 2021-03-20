---
title: Példa API Management-szabályzat – közös hozzáférésű aláírás előállítása
titleSuffix: Azure API Management
description: Azure API Management házirend-minta – bemutatja, hogyan hozhatja meg a közös hozzáférésű aláírásokat kifejezések használatával, és továbbítsa a kérést az Azure Storage-ba az újraírható URI-házirenddel.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078425"
---
# <a name="generate-shared-access-signature"></a>Közös hozzáférésű aláírás előállítása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan hozhatja meg a [közös hozzáférésű aláírást](../../storage/common/storage-sas-overview.md) kifejezések használatával, és hogyan továbbíthatja a kérést az Azure Storage-ba az újraírható URI-házirenddel. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)