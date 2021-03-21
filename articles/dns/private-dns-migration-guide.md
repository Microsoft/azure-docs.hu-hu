---
title: Örökölt Azure DNS Private Zones áttelepítése új erőforrás-modellre
titleSuffix: Azure DNS
description: Ez az útmutató lépésről lépésre bemutatja, hogyan telepítheti át a régi magánhálózati DNS-zónákat a legújabb erőforrás-modellre
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 72d046cde70d1224eb1fd47f527c9e49c6b002f6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500461"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Örökölt Azure DNS privát zónák áttelepítése új erőforrás-modellbe

A nyilvános előzetes verzióban a privát DNS-zónák "dnszones" erőforrással lettek létrehozva "zoneType" tulajdonsággal a "Private" értékre állítva. Az ilyen zónák 2019 december 31-ig nem támogatottak, és a GA Resource modelre kell migrálni, amely "privateDnsZones" típusú erőforrástípust használ a "dnszones" helyett. Az áttelepítési folyamat egyszerű, és egy PowerShell-szkripttel automatizáljuk ezt a folyamatot. Ez az útmutató lépésről lépésre bemutatja, hogyan telepítheti át a Azure DNS saját zónáit az új erőforrás-modellbe.

Az áttelepítést igénylő dnszones-erőforrások megállapítása; hajtsa végre az alábbi parancsot az Azure CLI-ben.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy telepítette a Azure PowerShell legújabb verzióját. További információ a Azure PowerShell (az) és a telepítési látogatásról https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Győződjön meg arról, hogy az az. PrivateDns modul van telepítve a Azure PowerShell. A modul telepítéséhez nyisson meg egy rendszergazda jogú PowerShell-ablakot (felügyeleti mód), és írja be a következő parancsot.

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Az áttelepítési folyamat teljesen automatizált, és nem várt állásidőt okoz. Ha azonban a Azure DNS privát zónákat (előzetes verzió) kritikus éles környezetben használja, akkor a tervezett karbantartási időszak során a következő áttelepítési folyamatot kell végrehajtania. Győződjön meg arról, hogy nem módosítja a magánhálózati DNS-zónák konfigurációját vagy rekordjait az áttelepítési parancsfájl futtatása közben.

## <a name="installing-the-script"></a>A parancsfájl telepítése

Nyisson meg egy rendszergazda jogú PowerShell-ablakot (felügyeleti mód), és futtassa a következő parancsot

```powershell
install-script PrivateDnsMigrationScript
```

Adja meg a "A" értéket, ha a rendszer kéri a parancsfájl telepítését

![A parancsfájl telepítése](./media/private-dns-migration-guide/install-migration-script.png)

Manuálisan is beszerezheti a PowerShell-szkript legújabb verzióját a következő helyen: https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Az áttelepítési parancsfájl nem futtatható az Azure Cloud shellben, és az internethez csatlakozó virtuális gépen vagy helyi számítógépen kell végrehajtani.

## <a name="running-the-script"></a>A parancsfájl futtatása

Futtassa a következő parancsot a szkript futtatásához

```powershell
PrivateDnsMigrationScript.ps1
```

![A parancsfájl futtatása](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Adja meg az előfizetés AZONOSÍTÓját, és jelentkezzen be az Azure-ba

A rendszer kérni fogja, hogy adja meg az áttelepíteni kívánt magánhálózati DNS-zónákat tartalmazó előfizetés-azonosítót. A rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába. Fejezze be a bejelentkezést, hogy a parancsfájl hozzáférhessen a saját DNS-zóna erőforrásaihoz az előfizetésben.

![Bejelentkezés az Azure-ba](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Válassza ki az áttelepíteni kívánt DNS-zónákat

Az előfizetésben található összes magánhálózati DNS-zóna listáját lekérő parancsfájl, amely azt kéri, hogy erősítse meg, hogy mely fájlokat szeretné áttelepíteni. Adja meg az "A" értéket az összes magánhálózati DNS-zóna áttelepíteni. Miután elvégezte ezt a lépést, a parancsfájl új erőforrás-modellel hozza létre az új magánhálózati DNS-zónákat, majd az új DSN-zónába másolja az adatfájlokat. Ez a lépés nem változtatja meg a meglévő magánhálózati DNS-zónákat.

![DNS-zónák kijelölése](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS-feloldás átváltása az új DNS-zónákra

A zónák és rekordok új erőforrás-modellbe való másolása után a parancsfájl felszólítja a DNS-feloldás új DNS-zónákra való átváltására. Ez a lépés eltávolítja az örökölt privát DNS-zónák és a virtuális hálózatok közötti társítást. Ha az örökölt zóna le van csatolva a virtuális hálózatokról, a fenti lépésben létrehozott új DNS-zónák automatikusan átveszik az adott virtuális hálózatok DNS-feloldását.

Válassza az "A" lehetőséget az összes virtuális hálózat DNS-feloldásának váltásához.

![Névfeloldás váltása](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>A DNS-feloldás ellenőrzése

A folytatás előtt ellenőrizze, hogy a DNS-zóna DNS-feloldása a várt módon működik-e. Bejelentkezhet az Azure-beli virtuális gépekre, és nslookup-lekérdezést is kiadhat az áttelepített zónákon annak ellenőrzéséhez, hogy a DNS-feloldás működik-e.

![Névfeloldás ellenőrzése](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Ha úgy találja, hogy a DNS-lekérdezések nem oldhatók fel, várjon néhány percet, és próbálkozzon újra a lekérdezésekkel. Ha a DNS-lekérdezések a várt módon működnek, akkor írja be az "Y" értéket, ha a parancsfájl arra kéri, hogy távolítsa el a virtuális hálózatot a magánhálózati DNS-zónából.

![Névfeloldás megerősítése](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Ha az áttelepített zónákra vonatkozó DNS-feloldás miatt nem a várt módon működik, írja be az "N" értéket a fenti lépésben, a szkript pedig visszaváltja a DNS-feloldást az örökölt zónákra. Hozzon létre egy támogatási jegyet, és segítünk a DNS-zónák áttelepítésében.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ez a lépés törli a régi DNS-zónákat, és csak azt követően kell végrehajtani, hogy ellenőrizte, hogy a DNS-feloldás a várt módon működik-e. A rendszer felszólítja az egyes privát DNS-zónák törlésére. Ha ellenőrzi, hogy az adott zóna DNS-feloldása megfelelően működik-e, írja be az "Y" értéket minden kérdésnél.

![A fölöslegessé vált elemek eltávolítása](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Az automatizálás frissítése

Ha automatizálást használ, beleértve a sablonokat, a PowerShell-parancsfájlokat vagy az SDK használatával fejlesztett egyéni kódokat, frissítenie kell az automationt, hogy az új erőforrás-modellt használja a magánhálózati DNS-zónákhoz. Alább láthatók az új privát DNS CLI/PS/SDK dokumentációra mutató hivatkozások.
* [Azure DNS privát zónák REST API](/rest/api/dns/privatedns/privatezones)
* [Privát zónák Azure DNS parancssori felület](/cli/azure/ext/privatedns/network/private-dns)
* [Privát zónák Azure DNS PowerShell](/powershell/module/az.privatedns/)
* [Azure DNS Private Zones SDK](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>További segítségre van szüksége

Hozzon létre egy támogatási jegyet, ha további segítségre van szüksége az áttelepítési folyamathoz, vagy valamilyen okból kifolyólag a fenti felsorolt lépések nem működnek. Adja meg a PowerShell-parancsfájl által a támogatási jegyhez létrehozott átirat-fájlt.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNS a [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok, beleértve a bizonyos típusú műveletek várható viselkedését, [saját DNS a gyakori](./dns-faq-private.md)kérdések című témakört.

* A DNS-zónák és-rekordok megismerése a [DNS-zónák és-rekordok áttekintésével](dns-zones-records.md).

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.