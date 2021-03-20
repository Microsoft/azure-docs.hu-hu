---
title: Azure Maps hitelesítési adatainak megtekintése
description: A Azure Portal használatával megtekintheti Azure Maps hitelesítési adatait.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87126455"
---
A Azure Portalban megtekintheti a Azure Maps fiók hitelesítésének részleteit. A fiókjában a **Beállítások** menüben válassza a **hitelesítés** lehetőséget.

![Hitelesítési részletek](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure Maps fiók létrehozása után a Azure Maps `x-ms-client-id` érték szerepel az Azure Portal-hitelesítés részletei lapon. Ez az érték azt a fiókot jelöli, amelyet a rendszer a REST API kérelmekhez fog használni. Ezt az értéket az alkalmazás-konfigurációban kell tárolni, és a HTTP-kérések megkezdése előtt be kell olvasni az Azure AD-hitelesítés Azure Maps használatával történő használatakor.
