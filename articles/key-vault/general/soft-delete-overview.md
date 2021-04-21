---
title: Azure Key Vault törlési | Microsoft Docs
description: A helyreállítható törlés Azure Key Vault lehetővé teszi a törölt kulcstartók és kulcstartó-objektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753253"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: a helyreállítható törlés áttekintése

> [!IMPORTANT]
> A kulcstartókban azonnal engedélyeznie kell a soft-delete parancsot. A soft-delete szolgáltatás lemondásának lehetősége hamarosan elavult. Teljes részleteket [itt talál](soft-delete-change.md)

> [!IMPORTANT]
> A törölt tárolók a törlési beállításokat aktiválják a Key Vault szolgáltatásokkal (például Azure RBAC-szerepkörök hozzárendelései, Event Grid előfizetések). A helyreállítható módon törölt Key Vault integrált szolgáltatások beállításait manuálisan újra létre kell hozni. 

Key Vault helyreállított törlési funkciója lehetővé teszi a törölt tárolók és törölt kulcstartó-objektumok (például kulcsok, titkos kulcsok, tanúsítványok) helyreállítását, más néven a helyreállított törlést. Pontosabban a következő helyzetekkel foglalkozunk: Ez a biztonsági megoldás a következő védelmi megoldásokat biztosítja:

- A titkos kulcs, kulcs, tanúsítvány vagy kulcstartó törlése után a titkos kulcs egy 7–90 naptári napos, konfigurálható időszakig helyreállítható marad. Ha nincs megadva konfiguráció, az alapértelmezett helyreállítási időszak 90 nap lesz. Ez elegendő időt biztosít a felhasználóknak a titkos adatok véletlen törlésének jelzésére és a válaszra.
- A titkos adatok végleges törléséhez két műveletet kell tenni. Először a felhasználónak törölnie kell az objektumot, amely a soft-deleted állapotba helyezi azt. Másodszor, a felhasználónak törölnie kell az objektumot a soft-deleted állapotban. A véglegesen kiürítés művelethez további hozzáférési szabályzati engedélyekre van szükség. Ezek a további védelmi intézkedések csökkentik annak kockázatát, hogy egy felhasználó véletlenül vagy rosszindulatúan töröl egy titkos kódot vagy egy kulcstartót.  
- A szolgáltatásnévnek egy további "végleges törlési" hozzáférési szabályzati engedéllyel kell rendelkeznie ahhoz, hogy a titkos adatokat törölhént állapotban véglegesen törölhén legyen. A véglegesen kiürítés hozzáférési szabályzatának engedélye alapértelmezés szerint nem adható meg egyetlen szolgáltatásnévnek sem, beleértve a kulcstartót és az előfizetések tulajdonosait is, és szándékosan be kell állítani. Azáltal, hogy emelt szintű hozzáférési házirend-engedélyre van szükség a soft-deleted titkos adatok törléséhez, csökkenti annak valószínűségét, hogy véletlenül töröl egy titkos adatokat.

## <a name="supporting-interfaces"></a>Támogató felületek

A soft-delete funkció az [REST API,](/rest/api/keyvault/)az [Azure CLI,](./key-vault-recovery.md) [a Azure PowerShell, a](./key-vault-recovery.md) [.NET/C#](/dotnet/api/microsoft.azure.keyvault) felületeken, valamint az [ARM-sablonokon keresztül érhető el.](/azure/templates/microsoft.keyvault/2019-09-01/vaults)

## <a name="scenarios"></a>Forgatókönyvek

Az Azure Key Vaultok nyomon követhető erőforrások, amelyek kezelése a Azure Resource Manager. Azure Resource Manager egy jól meghatározott törlési viselkedést is megad, amelyhez a sikeres DELETE műveletnek azt kell eredményeznie, hogy az erőforrás többé nem lesz elérhető. A helyreállító törlés funkció a törölt objektum helyreállításával foglalkozik, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.

1. A tipikus forgatókönyvben előfordulhat, hogy a felhasználó véletlenül törölt egy kulcstartót vagy egy Key Vault-objektumot; Ha a Kulcstartó vagy a Key Vault-objektum egy előre meghatározott ideig helyreállítható lenne, a felhasználó visszavonhatja a törlést, és helyreállíthatja az adatait.

2. Egy másik forgatókönyvben egy engedélyezetlen felhasználó megpróbálhat törölni egy kulcstartót vagy kulcstartó-objektumot, például egy kulcstartón belüli kulcsot az üzlet megzavarása érdekében. A Kulcstartó vagy a Key Vault-objektum törlésének a mögöttes adatok tényleges törlésétől való elkülönítése biztonsági intézkedésként használható például azáltal, hogy egy másik, megbízható szerepkörre korlátozza az adattörlésre vonatkozó engedélyeket. Ez a megközelítés gyakorlatilag kvórumot igényel egy olyan művelethez, amely azonnali adatvesztést eredményezhet.

### <a name="soft-delete-behavior"></a>Helyreállítható törlés működése

Ha a soft-delete engedélyezve van, a törölt erőforrásként megjelölt erőforrások egy adott ideig (alapértelmezés szerint 90 napig) megmaradnak. A szolgáltatás emellett egy mechanizmust is biztosít a törölt objektum helyreállításához, amely lényegében visszavonja a törlést.

Új kulcstartó létrehozásakor a soft-delete alapértelmezés szerint be van kapcsolva. Kulcstartót az [Azure CLI](./key-vault-recovery.md) vagy a következő használatával [hozhat létre Azure PowerShell.](./key-vault-recovery.md) Ha a kulcstartón engedélyezte a soft-delete parancsot, az nem tiltható le

Az alapértelmezett megőrzési időtartam 90 nap, de a kulcstartó létrehozása során a megőrzési szabályzat időköze beállítható 7–90 napra a Azure Portal. A véglegesen kiürítés elleni védelem adatmegőrzési szabályzata ugyanazt az időközt használja. A beállítás után a megőrzési szabályzat időköze nem módosítható.

Nem használhatja újra a kulcstartó nevét, amely a megőrzési időszak végéig nem lett törölve.

### <a name="purge-protection"></a>Védelem végleges végleges kiürítése

A véglegesen kiürítés elleni védelem Key Vault nem kötelező, és alapértelmezés szerint **nincs engedélyezve.** A végleges törlés elleni védelem csak akkor engedélyezhető, ha a soft-delete engedélyezve van.  A parancssori felület vagy a PowerShell [használatával](./key-vault-recovery.md?tabs=azure-cli) [lehet bekapcsolni.](./key-vault-recovery.md?tabs=azure-powershell)

Ha a végleges törlés elleni védelem be van stb., a törölt állapotban van egy tároló vagy objektum nem törölhető, amíg a megőrzési időszak le nem telt. A helyreállíthatóan törölt tárolók és objektumok továbbra is helyreállíthatóak, így biztosítva a megőrzési szabályzat betartását.

Az alapértelmezett megőrzési időtartam 90 nap, de a megőrzési szabályzat időköze beállítható 7 és 90 nap között a Azure Portal. Miután beállította és mentette a megőrzési szabályzat időközét, az a tárolóra nem módosítható.

### <a name="permitted-purge"></a>Engedélyezett véglegesen kiürítés

A kulcstartó végleges törlése, végleges törlése a proxyerőforrás POST műveletével lehetséges, és speciális jogosultságokat igényel. Általában csak az előfizetés tulajdonosa tudja véglegesen kiürítani a kulcstartót. A POST művelet aktiválja a tároló azonnali és visszavonhatatlan törlését. 

A kivételek a következőek:
- Ha az Azure-előfizetés nem *deeletableként lett megjelölve.* Ebben az esetben csak a szolgáltatás végezheti el a tényleges törlést, és ezt ütemezett folyamatként végzi el. 
- Ha a `--enable-purge-protection flag` engedélyezve van a tárolón. Ebben az esetben a Key Vault 90 napot vár az eredeti titkos objektum törlésre való jelölése után az objektum végleges törléséhez.

További lépések: How [to use Key Vault soft-delete with CLI: Purging a key vault](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) (Kulcstartó végleges törlése) vagy How to use Key Vault soft-delete with PowerShell: Purging a key vault (Kulcstartó törlése a parancssori felületről) vagy How to use Key Vault soft-delete with PowerShell: Purging a key vault (Kulcstartó végleges törlése): How to use Key Vault soft-delete with PowerShell: Purging a key vault (Kulcstartó törlése a parancssori felületről): How to use Key Vault [soft-delete with PowerShell: Purging a key vault](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)(

### <a name="key-vault-recovery"></a>Kulcstartó helyreállítása

Kulcstartó törlésekor a szolgáltatás létrehoz egy proxyerőforrást az előfizetés alatt, és hozzáadja a megfelelő metaadatokat a helyreállításhoz. A proxyerőforrás egy tárolt objektum, amely ugyanazon a helyen érhető el, mint a törölt kulcstartó. 

### <a name="key-vault-object-recovery"></a>Key Vault-objektumok helyreállítása

Egy Key Vault-objektum, például kulcs törlésekor a szolgáltatás törölt állapotba fogja az objektumot, így az nem lesz elérhető az összes lekérési művelet számára. Ebben az állapotban a Key Vault-objektum csak listázható, állítható helyre, illetve kényszerítve/véglegesen törölhető. Az objektumok megtekintéséhez használja az Azure CLI-parancsot (a how to use Key Vault soft-delete with CLI ) (A Key Vault soft-delete használata a PARANCSSORi felület használatával) vagy a Azure PowerShell paramétert `az keyvault key list-deleted` (Key Vault [soft-delete](./key-vault-recovery.md)használata `-InRemovedState` a [PowerShell-sel)](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)leírtak szerint.  

Ugyanakkor a Key Vault előre meghatározott megőrzési időtartam után ütemezi a törölt kulcstartónak vagy Key Vault-objektumnak megfelelő mögöttes adatok törlését. A tárolónak megfelelő DNS-rekordot a megőrzési időtartam alatt is megőrzi a rendszer.

### <a name="soft-delete-retention-period"></a>A soft-delete megőrzési időszaka

A rendszer 90 napig őrzi meg a nem törölt erőforrásokat egy meghatározott időtartamra. A soft-delete megőrzési időtartam alatt a következők érvényesek:

- Felsorolhatja az előfizetés helyreállított törlési állapotában lévő összes kulcstartót és Kulcstartó-objektumot, valamint a hozzáférés törlésével és helyreállításával kapcsolatos információkat.
  - Csak a speciális engedélyekkel rendelkező felhasználók listozhatja a törölt tárolókat. Javasoljuk, hogy a felhasználók hozzanak létre egy egyéni szerepkört ezekkel a speciális engedélyekkel a törölt tárolók kezelésével.
- Azonos nevű kulcstartó nem lehet ugyanazon a helyen; ennek megfelelően nem lehet létrehozni key vault-objektumot egy adott tárolóban, ha a kulcstartó azonos nevű, törölt állapotban található objektumot tartalmaz.
- Egy kulcstartót vagy key vault-objektumot csak egy kifejezetten kiemelt jogosultságú felhasználó állítható vissza egy helyreállítási parancs a megfelelő proxyerőforrásra való kiadásával.
  - Az egyéni szerepkör felhasználója, aki jogosultsággal rendelkezik kulcstartó létrehozására az erőforráscsoportban, visszaállíthatja a tárolót.
- Egy kulcstartót vagy key vault-objektumot csak egy kifejezetten kiemelt jogosultságú felhasználó törölhet úgy, hogy törlési parancsot ad ki a megfelelő proxyerőforráson.

A szolgáltatás a megőrzési időtartam végén véglegesen törli a helyreállíthatóan törölt kulcstartót vagy Key Vault-objektumot és annak tartalmát, kivéve, ha a kulcstartót vagy a Key Vault-objektumot helyreállítják. Előfordulhat, hogy az erőforrás-törlés nincs átütemezve.

### <a name="billing-implications"></a>Számlázással kapcsolatos következmények

Általánosságban elmondható, hogy ha egy objektum (kulcstartó, kulcs vagy titkos kulcs) törölve van, csak két művelet lehetséges: a "végleges törlés" és a "helyreállítás". A többi művelet sikertelen lesz. Ezért annak ellenére, hogy az objektum létezik, nem hajthat végre műveleteket, ezért nem történik használat, így nincs számla. Vannak azonban a következő kivételek:

- A "véglegesen véglegesen kiürített" és a "helyreállítás" műveletek beleszámnak a normál kulcstartó-műveletekbe, és ki lesznek számlázva.
- Ha az objektum HSM-kulcs, akkor a HSM által védett kulcs havi kulcsverziónkénti díj vonatkozik, ha a kulcsverziót az elmúlt 30 napban használták. Ezt követően, mivel az objektum törölt állapotban van, nem végezhető rajta művelet, így nem számítunk fel díjat.

## <a name="next-steps"></a>Következő lépések

Az alábbi két útmutató a soft-delete elsődleges használati forgatókönyveket tartalmazza.

- [A Key Vault törlése a Portallal](./key-vault-recovery.md?tabs=azure-portal)
- [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](./key-vault-recovery.md) 
- [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](./key-vault-recovery.md)
