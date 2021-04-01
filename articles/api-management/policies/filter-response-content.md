---
title: Azure API Management-szabályzat mintája – válasz tartalmának szűrése | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan szűrhetők az adatelemek a válasz adattartalma alapján a kérelemhez társított termék alapján.
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
ms.openlocfilehash: 1f2794c2d72dd460f0b3edf5fb7ec4035746c6e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078442"
---
# <a name="filter-response-content"></a>Válasz tartalmának szűrése

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan szűrhetők a kéréshez társított termék alapján a válasz adatelemeinek szűrése. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **kimenő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)