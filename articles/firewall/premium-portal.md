---
title: Azure Firewall Premium Preview a Azure Portal
description: A Azure Portal Azure Firewall Premium előzetes verziójának megismerése.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549698"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure Firewall Premium Preview a Azure Portal

> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A Azure Firewall Premium Preview egy új generációs tűzfal, amely a szigorúan bizalmas és szabályozott környezetekhez szükséges képességekkel rendelkezik. A következő funkciókat tartalmazza:

- **TLS-vizsgálat** – visszafejti a kimenő forgalmat, feldolgozza az adatokat, majd titkosítja az adatokat, és elküldi azt a célhelyre.
- **IDP** – a hálózati behatolás-észlelési és-megelőzési rendszer (IDP) lehetővé teszi a kártékony tevékenységek hálózati tevékenységének figyelését, a tevékenység adatainak naplózását, a jelentés megadását, és opcionálisan megkísérli a blokkolását.
- **URL-szűrés** – kiterjeszti Azure Firewall FQDN-szűrési képességét egy teljes URL-cím megfontolni. Például a `www.contoso.com/a/c` helyett `www.contoso.com` .
- **Webes kategóriák** – a rendszergazdák engedélyezhetik vagy megtagadhatják a webhely-kategóriákhoz való hozzáférést, például a szerencsejáték-webhelyeket, a közösségi média webhelyeket és egyebeket.

További információ: [Azure Firewall Premium-funkciók](premium-features.md).

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

A Azure Firewall Premium Preview üzembe helyezése hasonló a standard Azure Firewall üzembe helyezéséhez:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="portál üzembe helyezése":::

A **Tűzfalszabályok** esetében válassza a **prémium (előzetes verzió)** és a **Tűzfalszabályok** lehetőséget, válasszon ki egy meglévő prémium szabályzatot, vagy hozzon létre egy újat.

## <a name="configure-the-premium-policy"></a>A prémium szabályzat konfigurálása

A prémium szintű tűzfalszabályok konfigurálása hasonló a szabványos tűzfal-házirend konfigurálásához. A prémium szintű szabályzattal a prémium szintű szolgáltatásokat konfigurálhatja:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Prémium szintű házirend üzembe helyezése":::

### <a name="rule-configuration"></a>Szabály konfigurációja

Ha a prémium szabályzatban konfigurálja az alkalmazási szabályokat, a prémium szintű funkciókat is konfigurálhatja:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Prémium szabály":::

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tekinteni a Azure Firewall Premium előzetes verziójának funkcióit, tekintse meg a [Azure Firewall Premium Preview üzembe helyezése és konfigurálása](premium-deploy.md)című témakört.