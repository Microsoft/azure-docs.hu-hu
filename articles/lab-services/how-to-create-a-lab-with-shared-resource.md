---
title: Tesztkörnyezet létrehozása megosztott erőforrással | Azure Lab Services
description: Megtudhatja, hogyan hozhat létre olyan labort, amely a tanulók között megosztott erőforrást igényel.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647970"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Tesztkörnyezet létrehozása megosztott erőforrással Azure Lab Services

A tantermi laborok létrehozásakor esetenként előfordulhat, hogy egyes erőforrásoknak meg kell osztaniuk a laborban tanulók között.  Például van egy licenckiszolgáló vagy egy SQL Server egy adatbázis-osztályhoz.  Ebből a cikkből megtudhatja, hogy milyen lépésekkel engedélyezheti a megosztott erőforrást egy laborban.  Arról is beszélünk, hogyan lehet korlátozni az adott megosztott erőforráshoz való hozzáférést.

## <a name="architecture"></a>Architektúra

Ahogy az alábbi ábrán is látható, labor-fiókkal fogunk rendelkezni.  A labor fiók a vnet-társítási beállításokat fogja tartalmazni, így a tesztkörnyezet virtuális hálózata csatlakozik a megosztott erőforrás hálózatához.  Az alábbi ábrán két virtuális hálózat nem átfedésben lévő IP-tartománnyal rendelkezik.  Ezek az IP-címtartományok csupán tartományok.  Azt is vegye figyelembe, hogy a megosztott erőforrás virtuális hálózata a labor-fiókkal megegyező előfizetésben található.

![Laboratóriumi szolgáltatások megosztott erőforrás-architektúrával](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Megosztott erőforrás beállítása

A tesztkörnyezet létrehozása előtt létre kell hozni a megosztott erőforrás virtuális hálózatát.  A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [virtuális hálózat](../virtual-network/quick-create-portal.md)létrehozása.  Fontos a virtuális hálózatok tartományának megtervezése, hogy ne legyenek átfedésben a labor gépek IP-címével.  A hálózat megtervezésével kapcsolatos további információkért lásd a [virtuális hálózatok](../virtual-network/virtual-network-vnet-plan-design-arm.md) tervezése című cikket. A példánkban a megosztott erőforrás a 10.2.0.0/16 tartománnyal rendelkező virtuális hálózaton található.  Ha még nem tette meg, [hozzon létre egy alhálózatot](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) a megosztott erőforrás tárolásához.  A példában a 10.2.0.0/24 tartományt használjuk, de a tartomány a hálózat igényeitől függően eltérő lehet.

A megosztott erőforrás lehet egy virtuális gépen vagy egy Azure-ban biztosított szolgáltatásban futó szoftver. A megosztott erőforrásnak magánhálózati IP-címen keresztül kell elérhetőnek lennie.  Ha a megosztott erőforrást csak privát IP-címen keresztül teszi elérhetővé, korlátozza a hozzáférést a megosztott erőforráshoz.

A diagram egy hálózati biztonsági csoportot (NSG) is megjelenít, amely a tanuló virtuális gépről érkező forgalom korlátozására használható.  Írhat például egy olyan biztonsági szabályt, amely a tanuló virtuális gép IP-címeiről érkező forgalmat állítja be, csak egy megosztott erőforráshoz fér hozzá, és semmi más nem.  A biztonsági szabályok beállításával kapcsolatos további információkért lásd: a [hálózati biztonsági csoport kezelése](../virtual-network/manage-network-security-group.md#work-with-security-rules). Ha a megosztott erőforrásokhoz való hozzáférést egy adott laborhoz szeretné korlátozni, szerezze be a labor IP-címét a Lab- [fiók labor beállításaiból](manage-labs.md#view-labs-in-a-lab-account) , és állítson be egy bejövő szabályt, amely csak az adott IP-címről engedélyezi a hozzáférést.  Ne felejtse el engedélyezni a 49152 és 65535 közötti portokat az adott IP-címhez.  Opcionálisan megkeresheti a tanuló virtuális gépek magánhálózati IP-címét a [virtuálisgép-készlet lap](how-to-set-virtual-machine-passwords.md)használatával.

Ha a megosztott erőforrás egy szükséges szoftvert futtató Azure-beli virtuális gép, előfordulhat, hogy módosítania kell a virtuális gép alapértelmezett tűzfalszabály-szabályait.

### <a name="tips-for-shared-resources---license-server"></a>Tippek megosztott erőforrásokhoz – licenckiszolgáló
Az egyik leggyakoribb megosztott erőforrás a licenckiszolgáló, íme néhány tipp arról, hogyan lehet sikeres a beállítással.
#### <a name="server-region"></a>Kiszolgáló régiója
A licenckiszolgálót ahhoz a virtuális hálózathoz kell csatlakoztatni, amely a laborhoz van csatlakoztatva, ezért a licenckiszolgálóra a labor-fiókkal megegyező régióban kell lennie.

#### <a name="static-private-ip-and-mac-address"></a>Statikus magánhálózati IP-cím és MAC-cím
Alapértelmezés szerint a virtuális gépek dinamikus magánhálózati IP-címmel rendelkeznek, [mielőtt bármilyen szoftvert beállít a magánhálózati IP-cím statikusra állítása előtt](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Ez a beállítás a magánhálózati IP-címet és a MAC-címet statikusra állítja.  

#### <a name="control-access"></a>Vezérlési hozzáférés
A licenckiszolgáló hozzáférésének szabályozása kulcs.  Ha a virtuális gép beállítása a telepítéshez továbbra is szükséges lesz a karbantartáshoz, a hibaelhárításhoz és a frissítéshez.  Íme néhány különböző módszer.
- [Igény szerinti (JIT) hozzáférés beállítása Azure Security Centeron belül.](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [Hálózati biztonsági csoport beállítása a hozzáférés korlátozására.](../virtual-network/network-security-groups-overview.md)
- [Állítsa be a Bastion-t a licenckiszolgáló biztonságos elérésének engedélyezéséhez.](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>Labor-fiók

Megosztott erőforrás használatához be kell állítani a labor-fiókot egy [egyenrangú virtuális hálózat](how-to-connect-peer-virtual-network.md)használatára.  Ebben az esetben a megosztott erőforrást birtokló virtuális hálózatra lesz szükség.

>[!WARNING]
>Az osztályhoz tartozó labort úgy kell **létrehozni,** hogy a labor-fiók a megosztott erőforrást használó virtuális hálózatra van kiosztva.  
Sablon számítógép

Miután a labor-fiókja a virtuális hálózathoz csatlakozik, a sablon számítógépének most már hozzáféréssel kell rendelkeznie a megosztott erőforráshoz.  Előfordulhat, hogy frissítenie kell a tűzfalszabályok beállításait attól függően, hogy milyen megosztott erőforrás érhető el.