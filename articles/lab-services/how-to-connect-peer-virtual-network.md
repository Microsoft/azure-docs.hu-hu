---
title: Kapcsolódás a Azure Lab Services társ hálózatához | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a labor-hálózat egy másik hálózathoz társként. Például összekapcsolhatja a helyszíni szervezeti vagy egyetemi hálózatot a labor virtuális hálózatával az Azure-ban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 99ea72f11c8f389a15171a1fe3b376646494903a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434243"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>A labor hálózatának összekötése egy társ virtuális hálózattal Azure Lab Services

Ez a cikk a labor-hálózat más hálózattal való társításával kapcsolatos információkat tartalmaz.

## <a name="overview"></a>Áttekintés

A virtuális hálózatok közötti kapcsolat lehetővé teszi az Azure-beli virtuális hálózatok zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális gépek közötti forgalmat a rendszer a Microsoft gerinc-infrastruktúrán keresztül irányítja át, hasonlóan a forgalomhoz, mint az azonos virtuális hálózatban lévő virtuális gépek között, a magánhálózati IP-címeken keresztül. További információ: [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).

Előfordulhat, hogy a tesztkörnyezet hálózatát egy társ virtuális hálózattal kell összekötnie bizonyos helyzetekben, többek között az alábbiakat:

- A laborban található virtuális gépek olyan szoftverekkel rendelkeznek, amelyek a licencek megvásárlásához kapcsolódnak a helyszíni licenckiszolgálóra.
- A laborban található virtuális gépeknek hozzá kell férniük az adatkészletekhez (vagy bármely más fájlhoz) az egyetemi hálózati megosztásokon.

Bizonyos helyszíni hálózatok az Azure-Virtual Networkhoz kapcsolódnak [ExpressRoute](../expressroute/expressroute-introduction.md) vagy [Virtual Network átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md)keresztül. Ezeket a szolgáltatásokat Azure Lab Serviceson kívül kell beállítani. Ha szeretne többet megtudni arról, hogyan csatlakoztathat helyszíni hálózatot az Azure-hoz az ExpressRoute használatával, tekintse meg az [ExpressRoute áttekintése](../expressroute/expressroute-introduction.md)című témakört. Virtual Network átjárót használó helyszíni kapcsolat esetén az átjárónak, a megadott virtuális hálózatnak és a labor-fióknak ugyanabban a régióban kell lennie.

> [!NOTE]
> Ha olyan Azure-Virtual Network hoz létre, amely egy labor-fiókkal lesz társítva, fontos tisztában lennie azzal, hogy a virtuális hálózat régiója milyen hatással van a laborok létrehozására.  További információkért tekintse meg a rendszergazdai útmutató szakaszát a [regions\locations](./administrator-guide.md#regionslocations)webhelyen.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a labor-fiók létrehozásakor

Az új [labor-fiók létrehozása](tutorial-setup-lab-account.md)során kiválaszthat egy meglévő virtuális hálózatot, amely a **speciális** lapon, a **társ virtuális hálózat** legördülő listában látható.  A lista csak a labor-fiókkal megegyező régióban lévő virtuális hálózatokat jeleníti meg. A kiválasztott virtuális hálózat csatlakoztatva van a labor-fiókban létrehozott laborokhoz.  A jelen módosítás után létrehozott Labs-beli virtuális gépek hozzáférhetnek a virtuális hálózatban lévő erőforrásokhoz.

![VNet kiválasztása társként](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Címtartomány

Emellett lehetőség van arra is, hogy **címtartományt** biztosítson a Labs virtuális gépei számára.  A **címtartomány** tulajdonság csak akkor érvényes, ha a tesztkörnyezetben engedélyezve van egy **társ virtuális hálózat** . Ha a címtartomány meg van adni, a rendszer a labor fiókban található laborok alatt lévő összes virtuális gépet létrehozza az adott címtartományból. A címtartomány legyen CIDR (például 10.20.0.0/20), és ne legyen átfedésben a meglévő címtartományok.  Címtartomány megadásakor fontos meggondolni a létrehozandó *laborok* számát, és biztosítania kell a címtartomány megadását. A labor-szolgáltatások legfeljebb 512 virtuális gépet feltételeznek laborban.  Egy "/23" nevű IP-címtartomány például csak egy labort hozhat létre.  A (z) "/21" tartománya négy labor létrehozását teszi lehetővé.

Ha nincs megadva a **címtartomány** , a labor Services az Azure által megadott alapértelmezett címtartományt fogja használni, amikor létrehozza a virtuális hálózatot a virtuális hálózattal.  A tartomány gyakran hasonló, mint 10. x. 0.0/16.  Ez az IP-címtartomány átfedését eredményezheti, ezért ügyeljen arra, hogy adjon meg egy címtartományt a tesztkörnyezet beállításai között, vagy ellenőrizze, hogy a virtuális hálózat milyen címtartományt használ.

> [!NOTE]
> A labor létrehozása sikertelen lehet, ha a labor-fiók egy virtuális hálózathoz van hozzárendelve, de az IP-címtartomány túl keskeny. Kifogyhat a címtartomány, ha túl sok labor van a labor-fiókban (az egyes laborok 512-es címeket használnak). 
> 
> Ha a tesztkörnyezet létrehozása sikertelen, forduljon a labor-fiók tulajdonosához/rendszergazdájához, és kérje a címtartomány növelését. A rendszergazda növelheti a címtartományt a [virtuális gépekhez tartozó címtartomány megadása a labor-fiókban](#specify-an-address-range-for-vms-in-the-lab-account) című szakaszban említett lépések alapján. 

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurálás a labor-fiók létrehozása után

Ugyanezt a tulajdonságot engedélyezheti a **labor-fiók** lap **Labs-konfiguráció** lapján, ha nem állított be egy társ hálózatot a labor-fiók létrehozásakor. Az erre a beállításra végzett módosítás csak a módosítás után létrehozott laborokra vonatkozik. Ahogy a képen is látható, a labor fiókban engedélyezheti vagy letilthatja a Labs **társ virtuális hálózatát** .

![VNet-társítás engedélyezése vagy letiltása a tesztkörnyezet létrehozása után](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Ha kijelöl egy virtuális hálózatot a **társ virtuális hálózat** mezőhöz, akkor a **tesztkörnyezet-létrehozó engedélyezése a laborban hely** beállítás le van tiltva. Ennek az az oka, hogy a labor-fiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a társ virtuális hálózatban lévő erőforrásokhoz való kapcsolódáshoz szükséges labor-fióknak.

> [!IMPORTANT]
> A társ virtuális hálózat beállítás csak a módosítás után létrehozott laborokra vonatkozik, nem a meglévő Labs-re.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Címtartomány megadása a virtuális gépek számára a labor-fiókban
A következő eljárás a virtuális gépekhez tartozó címtartomány megadásának lépéseit írja le a laborban. Ha frissíti a korábban megadott tartományt, a módosított címtartomány csak a módosítás után létrehozott virtuális gépekre vonatkozik. 

Íme néhány korlátozás a címtartomány megadásához, amelyet érdemes szem előtt tartani. 

- Az előtagnak kisebbnek vagy egyenlőnek kell lennie, mint 23. 
- Ha egy virtuális hálózat a labor-fiókhoz van társítva, a megadott címtartomány nem lehet átfedésben a társ virtuális hálózatból származó címtartományból.

1. A **labor-fiók** lapon válassza a bal oldali menü **Labs-beállítások** elemét.
2. A **címtartomány** mezőben határozza meg a laborban létrehozandó virtuális gépek címtartomány-tartományát. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Címtartomány konfigurálása](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)