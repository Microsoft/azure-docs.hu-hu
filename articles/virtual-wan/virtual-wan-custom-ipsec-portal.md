---
title: 'Egyéni IPsec-házirend konfigurálása az Azure Virtual WAN-hoz: portál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat Egyéni IPsec-házirendet az Azure Virtual WAN-hoz a portál használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851173"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Egyéni IPsec-házirend konfigurálása a virtuális WAN-hoz a portál használatával

Egyéni IPsec-házirendet konfigurálhat egy virtuális WAN VPN-kapcsolathoz a Azure Portal. Az egyéni szabályzatok akkor hasznosak, ha azt szeretné, hogy mindkét oldal (a helyszíni és az Azure VPN Gateway) ugyanazokat a beállításokat használja az IKE 1. és a 2. fázisához.

## <a name="working-with-custom-policies"></a>Egyéni szabályzatok használata

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Házirend konfigurálása

1. **Keresse meg a virtuális hubot**. Egy böngészőből lépjen az [Azure Portalra](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be az Azure-fiókjával. Navigáljon a virtuális WAN-erőforráshoz, és keresse meg azt a virtuális hubot, amelyhez a VPN-hely csatlakozik.
2. **Válassza ki a VPN-helyet**. A hub – Áttekintés lapon kattintson a **VPN (hely – hely)** elemre, és válassza ki azt a VPN-helyet, amelyhez egyéni IPSec-házirendet kíván beállítani.

   ![Válassza](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **A VPN-kapcsolat szerkesztése**. A **helyi menüben** válassza a **VPN-kapcsolat szerkesztése** **lehetőséget.**

   ![szerkesztés](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurálja a beállításokat**. A **VPN-kapcsolat szerkesztése** lapon módosítsa az IPSec-beállítást az alapértelmezett értékről az egyénire, és szabja testre az IPSec-házirendet. A beállítások mentéséhez kattintson a **Mentés** gombra.

   ![konfigurálás és mentés](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.