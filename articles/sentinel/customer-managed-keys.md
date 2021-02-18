---
title: Ügyfél által felügyelt kulcsok beállítása az Azure Sentinelben | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az ügyfél által felügyelt kulcsokat (CMK) az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: a1c2754fcae5768c6b87d6280fc882acd46d9a0a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585330"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Az Azure Sentinel ügyfél által felügyelt kulcsának beállítása

Ez a cikk háttér-információkat és lépéseket tartalmaz az ügyfél által felügyelt kulcs (CMK) Azure Sentinelhez való konfigurálásához. A CMK lehetővé teszi, hogy az összes mentett vagy az Azure Sentinel számára továbbított adatokat titkosítsa az összes kapcsolódó tárolási erőforráson egy, az Ön által létrehozott vagy birtokolt Azure Key Vault kulccsal.

> [!NOTE]
> - Az Azure Sentinel CMK funkció csak az **új ügyfelek** számára érhető el.
>
> - A képességhez való hozzáférést az Azure-szolgáltatások regisztrációja szabályozza. A hozzáférést a kapcsolatfelvételsel kérheti le azuresentinelCMK@microsoft.com . A függőben lévő kérések jóváhagyása a rendelkezésre álló kapacitás alapján történik.
>
> - A CMK képesség csak az 1 TB-ot vagy annál többet küldő ügyfelek számára érhető el. Ha az Azure-előfizetése CMK kiépítésére alkalmazza a Microsoftot, a további díjszabásról is tájékozódhat. További információ a [log Analytics díjszabásáról](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>A CMK működése 

Az Azure Sentinel megoldás számos tárolási erőforrást használ a naplók gyűjtéséhez és szolgáltatásaihoz, beleértve a Log Analytics és másokat is. Az Azure Sentinel CMK konfigurációjának részeként konfigurálnia kell a CMK beállításait a kapcsolódó tárolási erőforrásokon is. A Log Analyticson kívüli tárolási erőforrásokban mentett adatfájlok is titkosítva lesznek.

További információ a [CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Ha engedélyezi a CMK az Azure Sentinelben, a CMK nem támogató nyilvános előzetes funkciók nem lesznek engedélyezve.

## <a name="enable-cmk"></a>CMK engedélyezése 

A CMK kiépítéséhez kövesse az alábbi lépéseket: 

1.  Hozzon létre egy Azure Key Vault és tárolja a kulcsot.

2.  Engedélyezze a CMK a Log Analytics munkaterületen.

3.  Cosmos DB regisztrálása.

4.  Hozzáférési szabályzat hozzáadása a Azure Key Vault-példányhoz.

5.  CMK engedélyezése az Azure Sentinelben.

6.  Az Azure Sentinel engedélyezése.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>1. lépés: Azure Key Vault létrehozása és a kulcs tárolása

1.  [Hozzon létre Azure Key Vault erőforrást](/azure-stack/user/azure-stack-key-vault-manage-portal), majd hozza létre vagy importálja az adattitkosításhoz használandó kulcsot.
    > [!NOTE]
    >  A kulcs és a hozzáférés biztosításához a Azure Key Vault helyreállítható kell konfigurálni.

1.  [Helyreállítási beállítások bekapcsolása:](../key-vault/general/key-vault-recovery.md)

    -   Győződjön meg arról, hogy a [Soft delete](../key-vault/general/soft-delete-overview.md) be van kapcsolva.

    -   A [védelem kiürítésének](../key-vault/general/soft-delete-overview.md#purge-protection) bekapcsolásával megvédheti a titkos kulcs/tár kényszerített törlését a Soft delete után is.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2. lépés: a CMK engedélyezése a Log Analytics munkaterületen

Kövesse a [Azure monitor ügyfél által felügyelt kulcs konfigurációjának](../azure-monitor/logs/customer-managed-keys.md) utasításait, és hozzon létre egy CMK-munkaterületet, amelyet az Azure Sentinel-munkaterületként fog használni az alábbi lépésekben.

### <a name="step-3-register-for-cosmos-db"></a>3. lépés: a Cosmos DB regisztrálása

Az Azure Sentinel a Cosmos DB további tárolási erőforrásként működik. Ügyeljen arra, hogy regisztrálja Cosmos DB.

Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató [regisztrálásához](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) kövesse az Cosmos db útmutatást.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4. lépés: hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz

Győződjön meg arról, hogy a Azure Key Vault-példányhoz Cosmos DB hozzáférést ad hozzá. A Cosmos DB útmutatást követve [adjon hozzá egy hozzáférési szabályzatot a Azure Key Vault-példányhoz](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) Azure Cosmos db rendszerbiztonsági tag használatával.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>5. lépés: a CMK engedélyezése az Azure Sentinelben

Az Azure Sentinel CMK funkció csak az Azure-termékcsoport közvetlen hozzáférésének kézhezvételét követően érhető el az új ügyfelek számára. A Microsoft partnereitől kérheti az Azure Sentinel-csapat jóváhagyását, hogy lehetővé tegye a CMK használatát a megoldásban.

A jóváhagyás után a rendszer a következő információk megadását kéri a CMK funkció engedélyezéséhez.

-  Munkaterület-azonosító, amelyen engedélyezni szeretné a CMK

-  Key Vault URL-cím: másolja a kulcs azonosítóját a legutolsó perjelre:  
    

    ![kulcs azonosítója](./media/customer-managed-keys/key-identifier.png)

    Az Azure Sentinel csapata engedélyezi az Azure Sentinel CMK szolgáltatást a megadott munkaterületen.

-  A szolgáltatás használatához jóváhagyott Azure Sentinel-termék csapatának ellenőrzése. Ezt a továbblépés előtt kell megadnia.

### <a name="step-6-enable-azure-sentinel"></a>6. lépés: az Azure Sentinel engedélyezése


Nyissa meg a Azure Portal, és engedélyezze az Azure Sentinel szolgáltatást azon a munkaterületen, amelyen be szeretné állítani a CMK. További információ: [Azure Sentinel](quickstart-onboard.md)bevezetése.

## <a name="key-encryption-key-revocation-or-deletion"></a>Kulcs titkosítási kulcsának visszavonása vagy törlése


Abban az esetben, ha egy felhasználó visszavonja a kulcs titkosítási kulcsát, akár törléssel, akár az Azure Sentinel elérésének eltávolításával, egy órán belül az Azure Sentinel tiszteletben tartja a változást, és úgy viselkedik, mintha az adott információ már nem érhető el. Ezen a ponton az állandó tárolási erőforrásokat (például az adatfeldolgozást, az állandó konfigurációs változásokat és az incidensek létrehozását) használó műveletek nem lesznek megtiltva. A korábban tárolt adatértékek nem lesznek törölve, de nem lesznek elérhetők. A nem hozzáférhető adatokra az adatmegőrzési szabályzat vonatkozik, és a szabályzatnak megfelelően törlődik.

Az egyetlen művelet, amely a titkosítási kulcs visszavonása vagy törlése után lehetséges, fiók törlése.

Ha a hozzáférés vissza lett állítva a visszavonás után, az Azure Sentinel egy órán belül visszaállítja az adatelérést.

Ha többet szeretne megtudni arról, hogy ez hogyan működik Azure Monitorban, tekintse meg a [Azure monitor CMK visszavonása](../azure-monitor/logs/customer-managed-keys.md#key-revocation)című témakört.

## <a name="key-encryption-key-rotation"></a>Kulcs titkosítási kulcsának elforgatása


Az Azure Sentinel és a Log Analytics támogatja a kulcs rotációját. Amikor a felhasználó elvégzi a Key Vault a kulcsok elforgatását, az Azure Sentinel egy órán belül támogatja az új kulcsot.

Key Vault a kulcs elforgatását a kulcs új verziójának létrehozásával végezheti el:

![kulcs elforgatása](./media/customer-managed-keys/key-rotation.png)

A kulcs előző verzióját 24 óra elteltével letilthatja, vagy azt követően, hogy a Azure Key Vault naplók már nem jelenítenek meg semmilyen tevékenységet, amely az előző verziót használja.

Ha ugyanazt a kulcsot használja az Azure Sentinel-ben és a Log Analytics-ben, akkor szükség van a kulcs elforgatására, explicit módon frissítenie kell a fürterőforrás Log Analytics az új Azure Key Vault kulcs verzióját. További információ: [Azure monitor CMK forgatása](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan állíthat be egy ügyfél által felügyelt kulcsot az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.