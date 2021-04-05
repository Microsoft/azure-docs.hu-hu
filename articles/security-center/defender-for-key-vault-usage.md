---
title: Válaszadás a Key Vaulthoz készült Azure Defender-riasztásokra
description: Ismerje meg, hogy milyen lépésekkel lehet reagálni az Azure Defender és a Key Vault riasztására.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96122203"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Válaszadás Key Vaulthoz készült Azure Defender-riasztásokra
Ha értesítést kap a Key Vault Azure Defendertől, javasoljuk, hogy vizsgálja meg a riasztást, és válaszoljon az alább leírtak szerint. Az Azure Defender for Key Vault megvédi az alkalmazásokat és a hitelesítő adatokat, így még ha ismeri is a riasztást kiváltó alkalmazást vagy felhasználót, fontos, hogy minden riasztás körülményeit ellenőrizze.  

Az Azure Defender által Key Vault összes riasztása a következő elemeket tartalmazza:

- Objektumazonosító
- A gyanús erőforrás egyszerű felhasználóneve vagy IP-címe

> [!TIP]
> A bekövetkezett hozzáférés *típusától* függően előfordulhat, hogy egyes mezők nem lesznek elérhetők. Ha például egy alkalmazás fért hozzá a Key Vaulthoz, nem fogja látni a társított egyszerű felhasználónevet. Ha a forgalom az Azure-on kívülről érkezett, nem fogja látni az objektumazonosítót.

## <a name="step-1-contact"></a>1. lépés Kapcsolattartó

1. Ellenőrizze, hogy a forgalom az Azure-bérlőn belülről származik-e. Ha a Key Vault-tűzfal engedélyezve van, valószínű, hogy hozzáférést adott a riasztást kiváltó felhasználóhoz vagy alkalmazáshoz.
1. Ha nem tudja ellenőrizni a forgalom forrását, folytassa a [2. lépéssel. Azonnali mérséklés](#step-2-immediate-mitigation).
1. Ha azonosítani tudja a bérlő forgalmának forrását, vegye fel a kapcsolatot az alkalmazás felhasználóval vagy tulajdonosával. 

> [!CAUTION]
> Az Azure Defender for Key Vault úgy lett kialakítva, hogy segítse a lopott hitelesítő adatok által okozott gyanús tevékenységek azonosítását. **Ne** zárja be a riasztást egyszerűen azért, mert felismeri a felhasználót vagy az alkalmazást. Vegye fel a kapcsolatot az alkalmazás vagy a felhasználó tulajdonosával, és ellenőrizze, hogy a tevékenység jogos-e. Ha szükséges, létrehozhat egy letiltási szabályt a zaj kiküszöbölése érdekében. További információ: [riasztások mellőzése az Azure Defendertől](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>2. lépés Azonnali mérséklés 
Ha nem ismeri fel a felhasználót vagy az alkalmazást, vagy ha úgy gondolja, hogy a hozzáférés nem lett engedélyezve:

- Ha a forgalom ismeretlen IP-címről származik:
    1. Engedélyezze a Azure Key Vault tűzfalat a [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/general/network-security.md)című témakörben leírtak szerint.
    1. Konfigurálja a tűzfalat megbízható erőforrásokkal és virtuális hálózatokkal.

- Ha a riasztás forrása jogosulatlan alkalmazás vagy gyanús felhasználó volt:
    1. Nyissa meg a Key Vault hozzáférési szabályzatának beállításait.
    1. Távolítsa el a megfelelő rendszerbiztonsági tag-t, vagy korlátozza a rendszerbiztonsági tag által elvégezhető műveleteket.  

- Ha a riasztás forrása Azure Active Directory szerepkörrel rendelkezik a bérlőben:
    1. Forduljon a rendszergazdához.
    1. Döntse el, hogy szükség van-e a Azure Active Directory engedélyek csökkentésére vagy visszavonására.

## <a name="step-3-identify-impact"></a>3. lépés A hatás azonosítása 
A hatás enyhítése után vizsgálja meg a Key vaultban érintett titkos kulcsokat:
1. Nyissa meg a "biztonság" lapot a Azure Key Vaulton, és tekintse meg az aktivált riasztást.
1. Válassza ki az aktivált riasztást.
    Tekintse át az elért titkok listáját és az időbélyeget.
1. Ha a Key Vault diagnosztikai naplói engedélyezve vannak, tekintse át az előző műveleteket a megfelelő hívó IP-címéhez, a felhasználói résztvevőhöz vagy az objektum-AZONOSÍTÓhoz.  

## <a name="step-4-take-action"></a>4. lépés: Művelet elvégzése 
Ha lefordította a gyanús felhasználó vagy alkalmazás által elért titkok, kulcsok és tanúsítványok listáját, ezeket az objektumokat azonnal el kell forgatni.

1. Az érintett titkokat le kell tiltani vagy törölni kell a kulcstartóból.
1. Ha a hitelesítő adatokat egy adott alkalmazáshoz használta:
    1. Vegye fel a kapcsolatot az alkalmazás rendszergazdájával, és kérje meg őket, hogy ellenőrizze a környezetét a feltört hitelesítő adatok bármilyen felhasználására, mivel azok biztonsága sérül.
    1. Ha a feltört hitelesítő adatokat használták, az alkalmazás tulajdonosának azonosítania kell az elért adatokat, és csökkentenie kell a hatását.


## <a name="next-steps"></a>Következő lépések

Ez az oldal azt ismerteti, hogyan lehet reagálni az Azure Defender és a Key Vault közötti riasztásokra. A kapcsolódó információk a következő lapokon találhatók:

- [A Key Vault Azure Defender bemutatása](defender-for-key-vault-introduction.md)
- [Riasztások letiltása az Azure Defenderből](alerts-suppression-rules.md)
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)