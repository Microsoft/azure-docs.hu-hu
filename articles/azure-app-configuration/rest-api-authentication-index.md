---
title: Azure app Configuration REST API – hitelesítés
description: Az Azure app Configuration REST API használatával történő hitelesítésre szolgáló lapok
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932693"
---
# <a name="authentication"></a>Hitelesítés

Az összes HTTP-kérelmet hitelesíteni kell. A következő hitelesítési sémák támogatottak.

## <a name="hmac"></a>HMAC

A [HMAC-hitelesítés](./rest-api-authentication-hmac.md) véletlenszerűen generált titkot használ a kérelmek hasznos adatainak aláírásához. A hitelesítési módszert használó kérések hitelesítésének részletei a [HMAC-engedélyezés](./rest-api-authorization-hmac.md) szakaszban találhatók.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure ad)-hitelesítés](../active-directory/authentication/overview-authentication.md) a kérelmek hitelesítéséhez a Azure Active Directorytól kapott tulajdonosi jogkivonatot használ. Az ezen hitelesítési módszert használó kérelmek [engedélyezésének részletei az Azure ad-hitelesítés](./rest-api-authorization-azure-ad.md) szakaszban találhatók.