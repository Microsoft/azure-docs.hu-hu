---
title: Példa API Management-házirend – az üzenet törzse alapján küldött kérelem útvonala
titleSuffix: Azure API Management
description: Azure API Management-szabályzat mintája – bemutatja, hogyan irányíthatja át a kérelmeket a szerveik méretétől függően.
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
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076470"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Kérés átirányítása a törzs méretétől függően

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan irányíthatja át a kérelmeket a törzsük méretétől függően. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)