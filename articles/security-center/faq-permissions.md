---
title: Azure Security Center GYIK – az engedélyekkel kapcsolatos kérdések
description: Ez a gyakori kérdések a Azure Security Centerban található engedélyekkel kapcsolatos kérdésekre adnak választ, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87824613"
---
# <a name="permissions"></a>Engedélyek

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hogyan működnek az engedélyek Azure Security Center?

Azure Security Center az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)használja, amely [beépített szerepköröket](../role-based-access-control/built-in-roles.md) biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban.

Security Center megvizsgálja az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. A Security Centerban csak akkor jelenik meg az erőforrásokhoz kapcsolódó információ, ha a tulajdonos, közreműködő vagy olvasó szerepkörét rendelte hozzá ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik.

A Security Center szerepkörökkel és engedélyezett műveletekkel kapcsolatos további tudnivalókért tekintse meg [a Azure Security Center engedélyeit](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Kik módosíthatják a biztonsági házirendeket?

A biztonsági szabályzatok módosításához biztonsági rendszergazdának vagy az előfizetés tulajdonosának vagy közreműködőnek kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).