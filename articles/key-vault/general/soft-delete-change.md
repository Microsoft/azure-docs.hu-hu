---
title: A soft-delete engedélyezése az összes Key Vault-objektumon – Azure Key Vault | Microsoft Docs
description: Ebből a dokumentumból a soft-delete parancsot használhatja az összes kulcstartóhoz, valamint alkalmazás- és adminisztrációs módosításokat is el lehet látni az ütközési hibák elkerülése érdekében.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 7e1b2ee95864affa6e5e72e1f8354767dc95bdb1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753325"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A soft-delete minden kulcstartón engedélyezve lesz

> [!WARNING]
> A nem megfelelő változás: a nem végleges törlési lehetőség hamarosan elamorzodhat. Azure Key Vault felhasználóknak és rendszergazdáknak azonnal engedélyezniük kell a kulcstartóikban a soft-delete parancsot.
>
> A Azure Key Vault HSM esetén a soft-delete alapértelmezés szerint engedélyezve van, és nem tiltható le.

Ha egy titkos adatokat a rendszer a kulcstartóból a soft-delete védelem nélkül töröl, a titkos kulcs véglegesen törlődik. A felhasználók jelenleg leiratkozhatja a kulcstartó létrehozása során a soft-delete parancsot. A Microsoft azonban hamarosan engedélyezi a soft-delete védelmet az összes kulcstartón, hogy megvédje a titkos kulcsokat a felhasználók véletlen vagy rosszindulatú törléseitől. A felhasználók a továbbiakban nem tudják kikapcsolni vagy kikapcsolni a soft-delete parancsot.

:::image type="content" source="../media/softdeletediagram.png" alt-text="A kulcstartók a soft-delete védelem és a soft-delete védelem nélküli törlését bemutató ábra.":::

A soft-delete funkcióval kapcsolatos részletes információkért lásd a Azure Key Vault [törlés áttekintését.](soft-delete-overview.md)

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Működik az alkalmazásom a soft-delete funkció engedélyezése esetén?

> [!Important] 
> Alaposan tekintse át az alábbi információkat, mielőtt bekapcsolja a kulcstartókhoz a soft-delete parancsot.

A kulcstartók nevei globálisan egyediek. A kulcstartóban tárolt titkos kulcsok neve is egyedi. Nem használhatja újra a kulcstartó vagy a kulcstartó-objektum nevét, amely a soft-deleted állapotban létezik. 

Ha például az alkalmazás programozott módon létrehoz egy "A tároló" nevű kulcstartót, majd később törli az A tárolót, a rendszer áthelyezi a kulcstartót a soft-deleted állapotba. Az alkalmazás addig nem tud újra létrehozni egy másik kulcstartót "A" nevű kulcstartóval, amíg a kulcstartót ki nem törli a soft-deleted állapotból. 

Ha az alkalmazás létrehoz egy nevű kulcsot az "A" tárolóban, majd később törli azt, az alkalmazás nem tud létrehozni egy új, nevű kulcsot az "A tárolóban", amíg az objektumot ki nem törli `test key` a `test key` `test key` soft-deleted állapotból. 

Ha törölni próbál egy Key Vault-objektumot, majd újra létrehozza ugyanazokkal a névvel anélkül, hogy először törölné a törölt állapotból, ütközési hibákat okozhat. Ezek a hibák az alkalmazások vagy az automatizálás meghiúsulhat. A következő szükséges alkalmazás- és adminisztrációs módosítások előtt forduljon a fejlesztői csapathoz. 

### <a name="application-changes"></a>Alkalmazásváltozások

Ha az alkalmazás feltételezi, hogy a soft-delete nincs engedélyezve, és azt várja, hogy a törölt titkos kulcsok vagy kulcstartók nevei azonnal újra felhasználhatók, a következő módosításokat kell eszközlünk az alkalmazás logikájában.

1. Törölje az eredeti kulcstartót vagy titkos kulcsot.
1. Véglegesen törölt kulcstartó vagy titkos kulcs végleges törlése.
1. Várja meg, amíg a véglegesen véglegesen véglegesen befejeződik. Az azonnali újrakészítés ütközést eredményezhet.
1. Hozza létre újra a kulcstartót ugyanazokkal a névvel.
1. Ha a létrehozási művelet továbbra is névütközési hibát ad vissza, próbálja meg újra létrehozni a kulcstartót. Azure DNS legrosszabb esetben a rekordok frissítése akár 10 percet is igénybe vehet.

### <a name="administration-changes"></a>Az adminisztráció változásai

A titkos kulcsok végleges törléséhez hozzáférést kérő rendszerbiztonsági tagnak több hozzáférési szabályzati engedélyt kell adni a titkos kulcsok és a kulcstartó törléséhez.

Tiltsa le a kulcstartókra vonatkozó Azure-szabályzatokat, amelyek kötelezővé temetik a soft-delete kikapcsolását. Előfordulhat, hogy ezt a problémát eszkalálnia kell egy rendszergazdának, aki az Ön környezetére alkalmazott Azure-szabályzatokat szabályozza. Ha ez a szabályzat nincs letiltva, előfordulhat, hogy nem tud új kulcstartókat létrehozni az alkalmazott szabályzat hatókörében.

Ha a szervezetre jogi megfelelőségi követelmények vonatkoznak, és nem engedélyezi, hogy a törölt kulcstartók és titkos kulcsok hosszabb ideig helyreállítható állapotban maradjanak, módosítania kell a helyreállítható törlés megőrzési idejét, hogy megfeleljen a szervezet szabványainak. A megőrzési időtartam 7 és 90 nap között lehet.

## <a name="procedures"></a>Eljárások

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>A kulcstartók naplózásával ellenőrizze, hogy engedélyezve van-e a soft-delete

1. Jelentkezzen be az Azure Portalra.
1. Keressen rá a **Azure Policy.**
1. Válassza **a Definíciók lehetőséget.**
1. A **Category (Kategória)** Key Vault **a** szűrőben.
1. Válassza ki **Key Vault szabályzatot, amely engedélyezi a soft-delete** szabályzatot.
1. Válassza a **Hozzárendelés** elemet.
1. Állítsa be a hatókört az előfizetéséhez.
1. Győződjön meg arról, hogy a házirend hatása Audit **(Naplózás) beállításra van állítva.**
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. A környezet teljes vizsgálatának befejezése akár 24 órát is igénybe vehet.
1. A **Azure Policy** válassza a Megfelelőség **lehetőséget.**
1. Válassza ki az alkalmazott szabályzatot.

Mostantól szűrheti és láthatja, hogy mely kulcstartók esetében engedélyezett a soft-delete (megfelelő erőforrások), és mely kulcstartók esetében nem engedélyezett a soft-delete (nem megfelelő erőforrások).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>A soft-delete bekapcsolás meglévő kulcstartóhoz

1. Jelentkezzen be az Azure Portalra.
1. Keresse meg a kulcstartót.
1. A **Beállítások alatt válassza** a Tulajdonságok **lehetőséget.**
1. A **Helyreállító törlés alatt** válassza a Tároló és annak objektumai helyreállításának **engedélyezése** lehetőséget.
1. Állítsa be a megőrzési megőrzési adatokat a soft-delete számára.
1. Kattintson a **Mentés** gombra.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Véglegesen véglegesen kiürítő hozzáférési szabályzat engedélyeinek megadása egy rendszerbiztonsági tagnak

1. Jelentkezzen be az Azure Portalra.
1. Keresse meg a kulcstartót.
1. A **Beállítások alatt válassza a** Hozzáférési **szabályzatok lehetőséget.**
1. Válassza ki azt a szolgáltatásnév, amely számára hozzáférést szeretne.
1. Haladjon végig az egyes legördülő menükben a **Kulcs,** **Titkos** kulcs és Tanúsítvány engedélyek **alatt,** amíg meg nem látja a Kiemelt **műveletek elemet.** Válassza a **Véglegesen kiürítés** engedélyt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-change-affect-me"></a>Érint ez a változás?

Ha már be van kapcsolva a soft-delete, vagy ha nem törli, majd nem hozza létre újra az azonos nevű Key Vault-objektumokat, valószínűleg nem fogja észre venni a kulcstartó viselkedésének változását.

Ha olyan alkalmazása van, amely gyakran töröl és hoz létre újra kulcstartó-objektumokat ugyanazokkal az elnevezési konvencióval, az elvárt viselkedés fenntartása érdekében módosításokat kell eszközlünk az alkalmazáslogikában. Lásd a cikk [Alkalmazásváltozások](#application-changes) című szakaszát.

### <a name="how-do-i-benefit-from-this-change"></a>Hogyan ezt a változást?

A törlés elleni védelem egy újabb védelmi réteget biztosít a szervezet számára a véletlen vagy rosszindulatú törlés ellen. Kulcstartó-rendszergazdaként korlátozhatja a helyreállításhoz és a véglegesen kiüríthető engedélyekhez való hozzáférést.

Ha egy felhasználó véletlenül töröl egy kulcstartót vagy titkos kulcsot, hozzáférési engedélyeket adhat neki a titkos kulcs helyreállításához anélkül, hogy ezzel a kockázattal jár, hogy véglegesen törli a titkos kulcsot vagy a kulcstartót. Ez az önkiszolgáló folyamat minimálisra csökkenti a környezetben való állásidőt, és garantálja a titkos kulcsok rendelkezésre állását.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hogyan, hogy van-e teendőm?

A kulcstartók naplózása című szakasz lépéseit követve [ellenőrizze,](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) hogy a soft delete engedélyezve van-e ebben a cikkben. Ez a módosítás hatással van minden olyan kulcstartóra, amelynél nincs bekapcsolva a soft-delete.

### <a name="what-action-do-i-need-to-take"></a>Milyen műveletet kell tennem?

Miután megerősítette, hogy nem kell változtatnia az alkalmazáslogikán, kapcsolja be az összes kulcstartón a soft-delete (törlés) gombot.

### <a name="when-do-i-need-to-take-action"></a>Mikor kell műveletet tennem?

Annak érdekében, hogy az alkalmazások ne legyenek hatással az alkalmazásokra, a lehető leghamarabb kapcsolja be a kulcstartókban a törlést.

## <a name="next-steps"></a>Következő lépések

- A változással kapcsolatos kérdéseit a következő elérhetőségen velünk kapcsolatba lépheti: [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Olvassa el [a Soft-delete áttekintését.](soft-delete-overview.md)
