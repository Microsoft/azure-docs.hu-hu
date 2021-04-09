---
title: Azure AD Identity Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan továbbíthatja a naplókat és a riasztásokat Azure AD Identity Protection az Azure Sentinelbe az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632241"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Adatok összekapcsolása Azure Active Directory (Azure AD) Identity Protectionből

A naplók a [Azure ad Identity Protectionból](../active-directory/identity-protection/overview-identity-protection.md) az Azure sentinelbe továbbítva továbbítják a riasztásokat az Azure Sentinel szolgáltatásba az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Azure Active Directory Identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázati észlelések és a sebezhetőségek számára, és lehetővé teszi a kockázatok azonnali javítását, és szabályzatok beállítását a jövőbeli események automatikus szervizeléséhez. A szolgáltatás a Microsoft által a fogyasztói identitások védelmét szolgáló tapasztalatra épül, és egy nap alatt több mint 13 000 000 000-es bejelentkezésből származó, hatalmas pontosságot nyer. 

## <a name="prerequisites"></a>Előfeltételek

- [Prémium szintű Azure ad P2-előfizetéssel](https://azure.microsoft.com/pricing/details/active-directory/)kell rendelkeznie.
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználónak kell lennie.

## <a name="connect-to-azure-ad-identity-protection"></a>Kapcsolódás Azure AD Identity Protectionhoz

Ha prémium szintű Azure AD P2-előfizetéssel rendelkezik, Azure AD Identity Protection szerepel. Ha a [szabályzatok engedélyezve vannak](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) és riasztásokat generálnak, a riasztási adatátvitel könnyen továbbítható az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure ad Identity Protection** csempére.

1. A **Kapcsolódás** lehetőségre kattintva megkezdheti a streaming Azure ad Identity Protection események beküldését az Azure sentinelbe.

1. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure AD Identity Protection riasztásokhoz, keresse meg a **SecurityAlert**.

Ha tesztelni kívánja az összekötőt, [szimulálhatja az észleléseket](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) , hogy az Azure sentinelbe továbbítható minta-riasztásokat készítsen.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure AD Identity Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
