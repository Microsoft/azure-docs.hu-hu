---
title: Példa API Management-szabályzat – kérelem engedélyezése külső hitelesítő használatával
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogy a külső engedélyező kérések engedélyezése hogyan engedélyezzen egyéni vagy örökölt hitelesítési/engedélyezési logikát.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076232"
---
# <a name="authorize-requests-using-external-authorizer"></a>Kérelmek hitelesítése külső hitelesítő használatával

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan védheti meg az API-hozzáférést egy külső engedélyező egyéni hitelesítési/engedélyezési logikával. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Hozzáférési korlátozási szabályzatok](../api-management-access-restriction-policies.md)
+ [Szabályzatminták](../policy-reference.md)