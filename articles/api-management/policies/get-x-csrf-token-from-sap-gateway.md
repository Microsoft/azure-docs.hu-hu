---
title: Azure API Management-szabályzat – példa – az X-CSRF minta implementálása | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan valósítható meg a sok API által használt X-CSRF minta. Ez a példa kifejezetten az SAP-átjáróra vonatkozik.
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
ms.openlocfilehash: bc6f60397c2d432dd6547a8be34280fcf3acd05d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078408"
---
# <a name="implement-x-csrf-pattern"></a>Az X-CSRF minta implementálása

Ez a cikk egy Azure API Management Policy-mintát mutat be, amely bemutatja, hogyan valósítható meg a sok API által használt X-CSRF minta. Ez a példa kifejezetten az SAP-átjáróra vonatkozik. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)