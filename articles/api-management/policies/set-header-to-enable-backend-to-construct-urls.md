---
title: Azure API Management-házirend minta – továbbított fejléc hozzáadása | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan adhat hozzá továbbított fejlécet a bejövő kérelemben, hogy a háttér-API megfelelő URL-címeket lehessen létrehozni.
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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078612"
---
# <a name="add-a-forwarded-header"></a>Továbbított fejléc hozzáadása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan adhat hozzá továbbított fejlécet a bejövő kérelemben, hogy a háttérrendszer API-ját megfelelő URL-címeket lehessen létrehozni. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="code"></a>Code

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)