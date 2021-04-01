---
title: Minta API Management-szabályzat – az IP-cím szűrése Application Gateway használatakor
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan szűrheti a kérések IP-címét a Application Gateway használatakor.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076113"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>A kérés IP-címének szűrése Application Gateway használatakor

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan történik a szűrés a kérés IP-címén, ha a API Management példány egy Application Gateway vagy más közvetítőn keresztül érhető el. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Hozzáférési korlátozási szabályzatok](../api-management-access-restriction-policies.md)
+ [Szabályzatminták](../policy-reference.md)