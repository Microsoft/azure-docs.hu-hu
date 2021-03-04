---
title: Munkaterület létrehozása a kiszűrése-védelem engedélyezésével
description: Ebből a cikkből megtudhatja, hogyan hozhat létre munkaterületet az Azure szinapszis Analytics szolgáltatásban tárolt kiszűrése-védelemmel
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: a3dc0b9e83af5a29172189f75e5a2174dc4d393d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096122"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Munkaterület létrehozása a kiszűrése-védelem engedélyezésével
Ez a cikk azt ismerteti, hogyan hozhat létre olyan munkaterületet, amelyen engedélyezve van az kiszűrése Protection, és hogyan kezelheti a jóváhagyott Azure AD-bérlőket ehhez a munkaterülethez.

>[!Note]
>A felügyelt virtuális hálózat és a kiszűrése-védelem munkaterület-konfigurációja nem módosítható a munkaterület létrehozása után.

## <a name="prerequisites"></a>Előfeltételek
- Engedélyek egy munkaterület-erőforrás létrehozásához az Azure-ban.
- A szinapszis munkaterületre vonatkozó engedélyek a felügyelt privát végpontok létrehozásához.
- A hálózati erőforrás-szolgáltatóhoz regisztrált előfizetések. [Részletek](../../azure-resource-manager/management/resource-providers-and-types.md)

Kövesse a rövid útmutató [: szinapszis-munkaterület létrehozása](../quickstart-create-workspace.md) című témakörben ismertetett lépéseket a munkaterület létrehozásának megkezdéséhez. A munkaterület létrehozása előtt az alábbi információk segítségével adja hozzá az adatok kiszűrése-védelmét a munkaterülethez.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Kiszűrése-védelem hozzáadása a munkaterület létrehozásakor
1. A hálózatkezelés lapon jelölje be a "felügyelt virtuális hálózat engedélyezése" jelölőnégyzetet.
1. Válassza az "igen" lehetőséget a "kimenő adatforgalom engedélyezése csak a jóváhagyott célokhoz" beállításnál.
1. Válassza ki a munkaterülethez jóváhagyott Azure AD-bérlőket.
1. Tekintse át a konfigurációt, és hozza létre a munkaterületet.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Képernyőfelvétel: a Create szinapszis-munkaterület &quot;a felügyelt virtuális hálózat engedélyezése&quot; beállításának megjelenítése.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Jóváhagyott Azure Active Directory bérlők kezelése a munkaterülethez
1. A munkaterület Azure Portal válassza a "jóváhagyott Azure AD-bérlők" lehetőséget. Itt jelenik meg a munkaterület jóváhagyott Azure AD-bérlők listája. A munkaterület bérlője alapértelmezés szerint szerepel, és nem szerepel a listáján.
1. A "+ Hozzáadás" paranccsal új bérlőket vehet fel a jóváhagyott listára.
1. Ha el szeretne távolítani egy Azure AD-bérlőt a jóváhagyott listából, válassza ki a bérlőt, és válassza a "Törlés", majd a "Mentés" lehetőséget.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Kiszűrése-védelemmel ellátott munkaterület létrehozása":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Csatlakozás az Azure-erőforrásokhoz jóváhagyott Azure AD-bérlők között

Felügyelt privát végpontokat hozhat létre az Azure AD-bérlők azon Azure-erőforrásaihoz való kapcsolódáshoz, amelyeket a munkaterület jóváhagy. Kövesse a útmutatóban felsorolt lépéseket a [felügyelt privát végpontok létrehozásához](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>A munkaterületén kívüli bérlők erőforrásai nem rendelkezhetnek az SQL-készletekhez való kapcsolódáshoz szükséges tűzfalszabályok blokkolásával. A munkaterület felügyelt virtuális hálózatában lévő erőforrások, például a Spark-fürtök, felügyelt magánhálózati kapcsolatokon keresztül kapcsolódhatnak a tűzfallal védett erőforrásokhoz.

## <a name="known-limitations"></a>Ismert korlátozások
A felhasználók megadhatnak egy környezeti konfigurációs fájlt a Python-csomagok nyilvános adattárakból (például PyPI) való telepítéséhez. A védett munkaterületek kiszűrése a kimenő adattárak kapcsolatai le vannak tiltva. Ennek eredményeképpen a nyilvános adattárakból (például PyPI) telepített Python-könyvtár nem támogatott. 

Másik lehetőségként a felhasználók feltölthetik a munkaterület-csomagokat, vagy létrehozhatnak egy privát csatornát az elsődleges Azure Data Lake Storage fiókjában. További információért látogasson el a [csomagkezelő az Azure szinapszis Analytics szolgáltatásban](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) című témakörben. 
  
## <a name="next-steps"></a>Következő lépések

További információ a [kiszűrése-védelemről a szinapszis-munkaterületeken](./workspace-data-exfiltration-protection.md)

További információ a [felügyelt munkaterületről Virtual Network](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
