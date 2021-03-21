---
title: A helyreállítható Törlés engedélyezése az összes Key Vault-objektumon – Azure Key Vault | Microsoft Docs
description: Ez a dokumentum fogadja el az összes kulcstartó helyreállítható törlését, valamint az alkalmazás-és adminisztrációs módosítások elvégzését az ütközési hibák elkerülése érdekében.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572867"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A Soft-delete minden kulcstartón engedélyezve lesz.

> [!WARNING]
> Megszakítási változás: a rendszer hamarosan letilthatja a törlés lehetőségét. Azure Key Vault felhasználóknak és rendszergazdáknak azonnal engedélyeznie kell a helyreállítható törlést a kulcstartón.
>
> Azure Key Vault felügyelt HSM esetén a Soft delete alapértelmezés szerint engedélyezve van, és nem tiltható le.

Ha törölnek egy titkos kulcsot a kulcstartóból, és nem törli a védelmet, a titkos kulcs véglegesen törlődik. A Key Vault létrehozásakor a felhasználók jelenleg nem törölhetik a helyreállítható törlést. A Microsoft azonban hamarosan lehetővé teszi a helyreállítható törlési védelmet az összes kulcstartón, hogy a titkos kulcsokat a felhasználók véletlen vagy rosszindulatú törlésével védjék. A felhasználók többé nem fogják tudni letiltani vagy kikapcsolni a helyreállítható törlést.

:::image type="content" source="../media/softdeletediagram.png" alt-text="A Key Vault törlését bemutató ábra, amely azt mutatja be, hogy a rendszer törli-e a törlési védelmet, és nem törölhető védelemmel.":::

A Soft-delete funkció részletes ismertetését lásd: [Azure Key Vault-törlés – áttekintés](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Használható az alkalmazásom a helyreállítható törlési funkcióval?

> [!Important] 
> A kulcstartók törlésének bekapcsolása előtt figyelmesen tekintse át az alábbi információkat.

A Key Vault-nevek globálisan egyediek. A Key vaultban tárolt titkos kulcsok nevei szintén egyediek. Nem fogja tudni újra felhasználni egy olyan kulcstartó vagy kulcstartó-objektum nevét, amely már létezik a helyreállított állapotban. 

Ha például az alkalmazás programozott módon létrehoz egy "Vault A" nevű kulcstartót, és később törli az "A tárolót", akkor a Key vaultot a rendszer áthelyezi a helyreállított állapotba. Az alkalmazás nem tudja újból létrehozni a "Vault A" nevű kulcstartót, amíg a kulcstároló nem törlődik a helyreállított állapotból. 

Továbbá, ha az alkalmazás létrehoz egy nevű kulcsot `test key` a "Vault a" néven, és később törli ezt a kulcsot, az alkalmazás nem tudja létrehozni az "a tároló" nevű új kulcsot, `test key` amíg az objektum nem törlődik a helyreállított `test key` állapotból. 

Egy Key Vault-objektum törlésére történt kísérlet, és a rendszer újból létrehozza azt ugyanazzal a névvel, és nem törli azt a helyreállított állapotból. az ütközési hibákhoz vezethet. A hibák miatt előfordulhat, hogy az alkalmazások vagy az Automation meghibásodik. A következő szükséges alkalmazás-és adminisztrációs módosítások elvégzése előtt tekintse meg a fejlesztői csapatát. 

### <a name="application-changes"></a>Alkalmazás módosításai

Ha az alkalmazás azt feltételezi, hogy a Soft-delete nincs engedélyezve, és azt várja, hogy a törölt titkos vagy kulcstároló-nevek azonnal újra elérhetők legyenek, a következő módosításokat kell elvégeznie az alkalmazás logikájában.

1. Törölje az eredeti kulcstartót vagy titkos kulcsot.
1. Törölje a Key vaultot vagy a titkos kulcsot a helyreállított állapotból.
1. Várjon, amíg a kiürítés befejeződik. Az azonnali ismételt létrehozás ütközést okozhat.
1. Hozza létre újra a kulcstárolót ugyanazzal a névvel.
1. Ha a létrehozási művelet továbbra is névütközés-hibát eredményez, próbálkozzon újra a kulcstartó újbóli létrehozásával. Azure DNS rekordok frissítése akár 10 percet is igénybe vehet a legrosszabb esetben.

### <a name="administration-changes"></a>Adminisztrációs változások

A titkos kulcsok végleges törléséhez hozzáférést igénylő rendszerbiztonsági tageknek több hozzáférési házirendre van szükségük a titkok és a kulcstartó törléséhez.

Tiltsa le az Azure-szabályzatokat a kulcstartók számára, amelyek feladata, hogy a helyreállított törlés ki legyen kapcsolva. Előfordulhat, hogy ezt a problémát egy olyan rendszergazdának kell megadnia, aki a környezetre alkalmazott Azure-szabályzatokat vezérli. Ha a házirend nincs letiltva, elveszítheti az új kulcstartók létrehozását az alkalmazott házirend hatókörében.

Ha a szervezete jogi megfelelőségi követelmények hatálya alá tartozik, és nem teszi lehetővé, hogy a törölt kulcstartók és titkok hosszabb ideig maradjanak helyreállítható állapotban, akkor a szervezet szabványainak megfelelően módosítania kell a helyreállítható törlés megőrzési időtartamát. A megőrzési időtartamot az utolsó 7 és 90 nap között állíthatja be.

## <a name="procedures"></a>Eljárások

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>A kulcstárolók naplózásával ellenőrizze, hogy engedélyezve van-e a Soft delete

1. Jelentkezzen be az Azure Portalra.
1. **Azure Policy** keresése.
1. Válassza a **definíciók** lehetőséget.
1. A **Kategória** területen válassza a **Key Vault** elemet a szűrőben.
1. Válassza ki, hogy a **Key Vault törölje a törlésre engedélyezett** szabályzatot.
1. Válassza a **Hozzárendelés** elemet.
1. Állítsa be a hatókört az előfizetésre.
1. Győződjön meg arról, hogy a házirend hatása **naplózásra** van beállítva.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. A környezet teljes vizsgálata akár 24 órát is igénybe vehet.
1. A **Azure Policy** ablaktáblán válassza a **megfelelőség** lehetőséget.
1. Válassza ki az alkalmazott szabályzatot.

Mostantól szűrheti és megtekintheti, hogy mely kulcstartók rendelkeznek a helyreállítható törléssel (megfelelő erőforrásokkal), és hogy mely kulcstartók esetében nem engedélyezett a helyreállítható törlés (nem megfelelő erőforrások).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Helyreállítható törlés bekapcsolása meglévő kulcstartóhoz

1. Jelentkezzen be az Azure Portalra.
1. Keresse meg a Key vaultot.
1. A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.
1. A helyreállítható **Törlés** területen jelölje be a tár **helyreállításának engedélyezése** jelölőnégyzetet.
1. Állítsa be a megőrzési időszakot a Soft delete értékre.
1. Kattintson a **Mentés** gombra.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Engedélyek kiürítésének engedélyezése a rendszerbiztonsági tag számára

1. Jelentkezzen be az Azure Portalra.
1. Keresse meg a Key vaultot.
1. A **Beállítások** területen válassza a **hozzáférési szabályzatok** lehetőséget.
1. Válassza ki azt a szolgáltatásnevet, amelyhez hozzáférést szeretne biztosítani.
1. A **kulcs**-, a **titkos** és a **tanúsítvány-engedélyek** területen a legördülő menün keresztül mozoghat, amíg meg nem jelenik a **Kiemelt művelet**. Válassza ki a **kiürítési** engedélyt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-change-affect-me"></a>Befolyásolja a változás a változást?

Ha már bekapcsolta a Soft-delete funkciót, vagy ha nem törli és nem hozza létre újra a Key Vault-objektumokat ugyanazzal a névvel, akkor valószínűleg nem fogja észrevenni a kulcstartó viselkedésében bekövetkezett változást.

Ha olyan alkalmazással rendelkezik, amely gyakran ugyanazokat az elnevezési konvenciókat törli és újra létrehozza, akkor a várt működés érdekében módosítania kell az alkalmazás logikáját. Tekintse meg a jelen cikk [alkalmazás módosításait](#application-changes) ismertető szakaszát.

### <a name="how-do-i-benefit-from-this-change"></a>Hogyan kihasználni ezt a változást?

A Soft-delete Protection a szervezet számára egy másik védelmi réteget biztosít a véletlen vagy rosszindulatú törléssel szemben. Key Vault-rendszergazdaként korlátozhatja a hozzáférést az engedélyek és a kiürítési engedélyek esetében is.

Ha egy felhasználó véletlenül töröl egy kulcstartót vagy titkos kulcsot, a titkos kulcs vagy kulcstartó végleges törlésének kockázata nélkül megadhatja számukra a titkos kulcs helyreállításához szükséges hozzáférési engedélyeket. Ez a saját kiszolgálási folyamat a környezet leállását és a titkok rendelkezésre állását is csökkentheti.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hogyan megtudni, ha lépéseket kell tenniük?

Ebben a cikkben a [Key Vaults naplózása](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) című szakaszban ismertetett lépéseket követve ellenőrizheti, hogy engedélyezve van-e a Soft delete funkció. Ez a módosítás hatással van minden olyan kulcstartóra, amely nem rendelkezik a helyreállítható törléssel.

### <a name="what-action-do-i-need-to-take"></a>Milyen műveleteket kell elvégeznie?

Miután meggyőződött róla, hogy nem kell módosítania az alkalmazás logikáját, kapcsolja be a Soft-delete szolgáltatást az összes kulcstartón.

### <a name="when-do-i-need-to-take-action"></a>Mikor kell lépéseket végrehajtani?

Annak érdekében, hogy az alkalmazások ne legyenek hatással, kapcsolja be a helyreállítható törlést a kulcstartón a lehető leghamarabb.

## <a name="next-steps"></a>Következő lépések

- Ha kérdése van, lépjen kapcsolatba velünk a változással kapcsolatban [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Olvassa el a [Soft-delete áttekintést](soft-delete-overview.md).
