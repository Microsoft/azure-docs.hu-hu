---
ms.assetid: ''
title: Az Azure Key Vault helyreállítható törlés |} A Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/19/2019
ms.openlocfilehash: f222b37e8ca6efcfe28146ee948511d887f547a4
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339142"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Az Azure Key Vault helyreállítható törlés áttekintése

A Key Vault helyreállítható törlési funkció lehetővé teszi, hogy a törölt tárolók és a tároló objektumok, más néven helyreállítható törlés. Pontosabban hogy oldja meg a következő esetekben:

- A key vault helyreállítható törlés támogatása
- Key vault-objektumokon (például a helyreállítható törlés támogatása kulcsok, titkos kulcsokat, a tanúsítványok)

## <a name="supporting-interfaces"></a>Adapterek támogatása

A helyreállítható törlési funkció érhető el először a [REST](/rest/api/keyvault/), [CLI](key-vault-soft-delete-cli.md), [PowerShell](key-vault-soft-delete-powershell.md) és [.NET /C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) felületek.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure Key vault-Kulcstartók követett erőforrások Azure Resource Manager által felügyelt rendszer. Az Azure Resource Manager megad egy jól definiált működés törlésre, amely megköveteli, hogy egy sikeres törlési művelet nem érhető el többé erőforrás kell eredményez. A helyreállítható törlési funkció címeket a helyreállítás a törölt objektum, a törlés véletlen vagy szándékos volt-e.

1. Általános esetben egy felhasználó véletlenül törölték a key vault vagy a key vault-objektum; Ha, amely a key vault vagy a key vault objektum egy előre meghatározott ideig állíthatók nem voltak, a felhasználó előfordulhat, hogy a törlés visszavonásához és az adatok helyreállítása.

2. Egy másik esetben egy engedélyezetlen felhasználó megkísérli key vault és a egy a key vault-objektumot, például egy tároló tartalmazza, az üzletmenet megszakadása miatt belül kulcs törlése. A törlés, a key vault vagy a key vault objektum elválasztja az alapul szolgáló adatok tényleges törlésének használható biztonsági intézkedésként a szerint, például az adatok törlését egy másik jogosultságok korlátozása révén megbízható szerepkört. Ezt a módszert hatékonyan szükséges kvórum egy műveletet, amelyek egyébként egy azonnali adatvesztést.

### <a name="soft-delete-behavior"></a>Helyreállítható törlés viselkedés

Ezzel a funkcióval a törlési műveletet a key vault-objektum vagy a key vault helyreállítható törlés, hatékonyan az egy adott megőrzési időszak (90 nap), a az erőforrásokat tartalmazó miközben a megjelenését, hogy az objektum van-e törölve. A szolgáltatás további lehetővé teszi a helyreállítás, a törölt objektum, lényegében a törlésének visszavonása. 

Helyreállítható törlés egy nem kötelező a Key Vault-viselkedés, és **alapértelmezés szerint nincs engedélyezve** ebben a kiadásban. Ez lehet bekapcsolni keresztül [CLI](key-vault-soft-delete-cli.md) vagy [Powershell](key-vault-soft-delete-powershell.md).

### <a name="purge-protection"></a>Védelem végleges törlése 

Ha véglegesen törölni a védelem be van kapcsolva egy tárolót, vagy egy objektum törölt állapotban nem törölhető, amíg a 90 napos megőrzési időszak letelte. Ezek a tárolók és objektumok továbbra is helyreállíthatók legyenek, ügyfelek biztosítva azt, hogy az adatmegőrzési után történik. 

Védelem végleges törlése egy nem kötelező a Key Vault-viselkedés, és **alapértelmezés szerint nincs engedélyezve**. Ez lehet bekapcsolni keresztül [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) vagy [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

### <a name="permitted-purge"></a>Engedélyezett végleges törlése

Végleges törlés, a végleges törlése, a key vault POST műveletnek a proxy erőforráson keresztül lehetséges, és speciális jogosultságra van szükség. Általában csak az előfizetés tulajdonosa fogja tudni kulcstartó végleges törlése. A POST műveletet aktivál, hogy a tároló azonnali és helyreállíthatatlan törlését. 

Kivételek a következők:
- Ha az Azure-előfizetés van megjelölve *undeletable*. Csak a szolgáltatás ebben az esetben előfordulhat, hogy végezze el a tényleges törlés, és úgy valósítja meg az ütemezett folyamatként. 
- Ha a---kiürítés-védelem engedélyezése jelző engedélyezve van a tárolóban, magát. Ebben az esetben a Key Vault várakozik 90 nap során az eredeti titkos objektum lett megjelölve törlésre véglegesen törli az objektumot.

### <a name="key-vault-recovery"></a>A Key vault helyreállítási

Kulcstartó törlése után a szolgáltatás az előfizetést, a helyreállítás megfelelő metaadatok hozzáadása a proxy erőforrást hoz létre. A proxy erőforrás tárolt objektum, a törölt kulcstartó ugyanazon a helyen rendelkezésre áll. 

### <a name="key-vault-object-recovery"></a>A Key vault objektum-helyreállítást

Egy kulcstartó objektumot, például egy kulcs törlése után a szolgáltatás fogja elhelyezni az objektum törölt állapotban, így bármely adatbeolvasási műveletekkel elérhetetlenné. Az ebben az állapotban a key vault-objektum is csak akkor jelenik meg, helyreállított vagy kényszerített/véglegesen törölve. 

Egy időben a Key Vault ütemeznek az alapul szolgáló adatokat, a törölt kulcstartó vagy a végrehajtás egy előre meghatározott adatmegőrzési időszak után a key vault objektum megfelelő törlését. A tárolóhoz tartozó DNS-rekordot is megmarad a megőrzési időtartam idejére.

### <a name="soft-delete-retention-period"></a>Helyreállítható törlés megőrzési ideje

Helyreállíthatóan törölt erőforrások megadott idő, 90 napig maradnak meg. A helyreállítható törlés megőrzési intervallumban a következők érvényesek:

- Előfordulhat, hogy az összes kulcstartó és a helyreállítható Törlés állapotban az előfizetés, valamint a hozzáférés törlésének és a helyreállítási információk őket a key vault-objektumokon listázása.
    - Csak a speciális engedélyekkel rendelkező felhasználók listázhatja a törölt tárolók. Azt javasoljuk, hogy a felhasználók ezeket a törölt kezelése tárolók különleges engedélyekkel rendelkező egyéni szerepkör létrehozása.
- Key vault ugyanazzal a névvel nem hozható létre ugyanazon a helyen; Ennek megfelelően a kulcstartó objektum nem hozható létre egy adott tároló Ha, hogy a key vault tartalmaz egy objektumot, ugyanazzal a névvel, és amely egy törölt állapotban van 
- Csak a kifejezetten a kiemelt jogosultságú felhasználó előfordulhat, hogy a key vault vagy a key vault-objektum egy helyreállítás parancsot a megfelelő proxyt erőforrás visszaállítása.
    - A felhasználó az egyéni szerepkör tagja a jogosultsággal, hozzon létre egy kulcstartót a erőforráscsoportba tartozó visszaállításához a tárolóban.
- Csak a kifejezetten a kiemelt jogosultságú felhasználó előfordulhat, hogy kényszerített a key vault vagy a key vault objektum törlése egy törlési parancsot a megfelelő proxy erőforráson.

Egy kulcstartó vagy egy objektumot a key vault helyreállítható, hacsak a a szolgáltatás a megőrzési időtartam végén a helyreállíthatóan törölt kulcstartó vagy a key vault-objektum és a tartalom végleges törlésére hajtja végre. Előfordulhat, hogy nem ütemezhető törölni az erőforrást.

### <a name="billing-implications"></a>Számlázás – következmények

A (key vault vagy egy kulcs vagy titkos kulcs) objektum törölt állapotban van, amikor általában csak két művelet lehetséges: "kiürítése" és "tudja". Minden egyéb művelet sikertelen lesz. Annak ellenére, hogy az objektum létezik, ezért nincs művelet is elvégezhető, és ezért nem találtunk akkor jön létre, így nincs számlázási. Létezik azonban a rendszer következő kivételeket:

- "kiürítése" és "tudja" műveletek is beleszámít a normál key vault-műveletek és számítjuk fel.
- Ha az objektum egy HSM-kulcsot, a kulcs áttelepítése HSM által védett kulcs verziója a havi díjak díj fog alkalmazni, ha egy kulcs verziója már használták az elmúlt 30 napban. Mert az objektum nincs művelet is elvégezhető, szemben a törölt állapotban van, díjmentesen érvényes lesz.

## <a name="next-steps"></a>További lépések

A következő két útmutatók a helyreállítható törlés használata elsődleges felhasználási kínálnak.

- [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](key-vault-soft-delete-powershell.md) 
- [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](key-vault-soft-delete-cli.md)

