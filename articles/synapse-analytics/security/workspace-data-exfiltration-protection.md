---
title: Az Azure szinapszis Analytics-munkaterületek kiszűrése-védelme
description: Ez a cikk az Azure szinapszis Analytics kiszűrése-védelmét ismerteti
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598048"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Az Azure szinapszis Analytics-munkaterületek kiszűrése-védelme
Ez a cikk az Azure szinapszis Analytics kiszűrése-védelmét ismerteti

## <a name="securing-data-egress-from-synapse-workspaces"></a>A szinapszis-munkaterületekről érkező adatforgalom biztonságossá tétele
Az Azure szinapszis Analytics-munkaterületek támogatják a munkaterületek kiszűrése-védelmének engedélyezését. A kiszűrése-védelemmel megvédheti az Azure-erőforrásokhoz hozzáférő rosszindulatú bennfenteseket, és hogy kiszivárogtassák a bizalmas adatokat a szervezet hatókörén kívüli helyekre. A munkaterület létrehozásakor dönthet úgy, hogy a munkaterületet felügyelt virtuális hálózattal konfigurálja, és további védelmet biztosít a kiszűrése szemben. Ha egy munkaterületet [felügyelt virtuális hálózattal](./synapse-workspace-managed-vnet.md)hoztak létre, akkor az Adatintegráció és a Spark-erőforrások a felügyelt virtuális hálózaton vannak telepítve. A munkaterület dedikált SQL-készletei és kiszolgáló nélküli SQL-készletei több-bérlős funkciókkal rendelkeznek, ezért a felügyelt virtuális hálózaton kívül kell létezni. A kiszűrése-védelemmel ellátott munkaterületek esetében a felügyelt virtuális hálózatban lévő erőforrások mindig a [felügyelt privát végpontokon](./synapse-workspace-managed-private-endpoints.md) keresztül kommunikálnak, a szinapszis SQL-erőforrásai pedig csak az engedélyezett Azure-erőforrásokhoz (a munkaterületről jóváhagyott felügyelt privát végponti kapcsolatok céljaihoz) csatlakozhatnak. 

> [!Note]
> A felügyelt virtuális hálózat és a kiszűrése-védelem munkaterület-konfigurációja nem módosítható a munkaterület létrehozása után.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>A szinapszis-munkaterület kimenő adatforgalmának kezelése jóváhagyott célokkal
Miután a munkaterület engedélyezve lett a kiszűrése Protection szolgáltatással, a munkaterület-erőforrás tulajdonosai kezelhetik a jóváhagyott Azure AD-bérlők listáját a munkaterületen. A munkaterületen a [megfelelő engedélyekkel](./synapse-workspace-access-control-overview.md) rendelkező felhasználók a szinapszis Studio használatával hozhatnak létre felügyelt magánhálózati végponti kapcsolódási kérelmeket a munkaterület jóváhagyott Azure ad-bérlői erőforrásaihoz. A felügyelt magánhálózati végpontok létrehozása le lesz tiltva, ha a felhasználó egy nem jóváhagyott bérlőben lévő erőforráshoz kísérel meg létrehozni egy magánhálózati végponti kapcsolódást.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Minta munkaterület, amelyen engedélyezve van az kiszűrése-védelem
Tegyük fel, hogy példát mutatunk be a szinapszis-munkaterületek kiszűrése védelmére. A contoso Azure-erőforrásokkal rendelkezik az A bérlőn és A B bérlőn, és szükség van ezekre az erőforrásokra a biztonságos kapcsolódáshoz. Egy szinapszis-munkaterület lett létrehozva az A bérlőn, a B bérlő pedig jóváhagyott Azure AD-bérlőként lett hozzáadva. A diagramon az A bérlő és a B bérlő által jóváhagyott Azure Storage-fiókokhoz tartozó privát végponti kapcsolatok láthatók, amelyeket a Storage-fiók tulajdonosai jóváhagytak. A diagram emellett a letiltott privát végpontok létrehozását is megjeleníti. A privát végpont létrehozása le lett tiltva, mert egy Azure Storage-fiókot céloz meg a fabrikam Azure AD-bérlőben, amely nem jóváhagyott Azure AD-bérlő a contoso munkaterülethez.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Ez az ábra bemutatja, hogyan valósul meg a kiszűrése-védelem a szinapszis-munkaterületek számára":::

>[!IMPORTANT]
>
> - A munkaterületén kívüli bérlők erőforrásai nem rendelkezhetnek az SQL-készletekhez való kapcsolódáshoz szükséges tűzfalszabályok blokkolásával. A munkaterület felügyelt virtuális hálózatában lévő erőforrások, például a Spark-fürtök, felügyelt magánhálózati kapcsolatokon keresztül kapcsolódhatnak a tűzfallal védett erőforrásokhoz.
>
> - A metaadattár le van tiltva olyan szinapszis-munkaterületeken, amelyeken engedélyezve van az kiszűrése Protection által kezelt Virtual Network. Ezekben a munkaterületeken a Spark SQL nem használható.
> >

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [hozhat létre olyan munkaterületet, amelyen engedélyezve van az adatkiszűrése-védelem](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

További információ a [felügyelt munkaterületről Virtual Network](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
