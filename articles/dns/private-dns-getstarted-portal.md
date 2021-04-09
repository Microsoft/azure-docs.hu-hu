---
title: Rövid útmutató – Azure Private DNS-zóna létrehozása a Azure Portal használatával
description: Ebben a rövid útmutatóban egy privát DNS-zónát és-rekordot hoz létre és tesztel a Azure DNSban. Ez egy lépésenkénti útmutató, amellyel létrehozhatja és kezelheti az első saját DNS-zónáját és-rekordját a Azure Portal használatával.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310053"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Rövid útmutató: Azure Private DNS-zóna létrehozása a Azure Portal használatával

Ez a rövid útmutató végigvezeti az első saját DNS-zóna és-rekord létrehozásának lépésein a Azure Portal használatával.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Ha privát DNS-zónát szeretne közzétenni a virtuális hálózaton, meg kell adnia azon virtuális hálózatok listáját, amelyek feloldhatnak rekordokat a zónában.  Ezeket *csatolt* virtuális hálózatoknak nevezzük. Ha engedélyezve van az automatikus regisztráció, a Azure DNS a zóna rekordjait is frissíti, amikor létrejön egy virtuális gép, megváltoztatja az IP-címét, vagy törli azt.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Ha szeretné, a rövid útmutató [Azure PowerShell](private-dns-getstarted-powershell.md) vagy az [Azure CLI](private-dns-getstarted-cli.md)használatával is elvégezhető.

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

A következő példa egy **Private.contoso.com** nevű DNS-zónát hoz létre egy **MyAzureResourceGroup** nevű erőforráscsoport-csoportban.

Egy DNS-zóna tartalmazza a tartomány DNS-bejegyzéseit. A tartomány Azure DNSban való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez.

![saját DNS zónák keresése](media/private-dns-portal/search-private-dns.png)

1. A portálon a keresés szövegmezőbe írja be a **saját DNS-zónák** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza ki **saját DNS zónát**.
2. Válassza a **saját DNS-zóna létrehozása** lehetőséget.

1. A **saját DNS zóna létrehozása** lapon írja be vagy válassza ki a következő értékeket:

   - **Erőforráscsoport**: válassza az **új létrehozása** elemet, írja be a *MyAzureResourceGroup*, majd kattintson **az OK gombra**. Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül.
   -  **Név**: írja be a *Private.contoso.com* a példához.
1. Az **erőforráscsoport helye** területen válassza az **USA nyugati középső** régiója lehetőséget.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

A zóna létrehozása eltarthat néhány percig.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (meglévő erőforráscsoport kiválasztása) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | USA nyugati középső régiója      |
| **\<IPv4-address-space>**   | 10.2.0.0 \ 16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0 \ 24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

Ha a privát DNS-zónát egy virtuális hálózathoz szeretné kapcsolni, hozzon létre egy virtuális hálózati kapcsolatot.

![Virtuális hálózati kapcsolat hozzáadása](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Nyissa meg a **MyAzureResourceGroup** erőforráscsoportot, és válassza ki a **Private.contoso.com** saját zónáját.
2. A bal oldali ablaktáblán válassza a **virtuális hálózati kapcsolatok** elemet.
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja  be a myLink **nevet a hivatkozás neveként**.
5. A **Virtual Network** esetében válassza a **myAzureVNet** lehetőséget.
6. Jelölje be az **automatikus regisztráció engedélyezése** jelölőnégyzetet.
7. Válassza az **OK** lehetőséget.

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

1. A portálon kattintson a bal felső sarokban található **erőforrás létrehozása** elemre, majd válassza a **Windows Server 2016 Datacenter** lehetőséget.
1. Válassza ki az erőforráscsoport **MyAzureResourceGroup** .
1. Írja be a **myVM01** -nevet a virtuális gép nevére.
1. Válassza ki az **USA nyugati középső** **régióját**.
1. Adja meg a rendszergazda Felhasználónév nevét.
2. Adja meg a jelszót, és erősítse meg a jelszót.
5. A **nyilvános bejövő portok** esetében válassza a **kiválasztott portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a **bejövő portok kiválasztásához**.
10. Fogadja el az oldal többi alapértelmezett beállítását, majd kattintson a **Tovább gombra: lemezek >**.
11. Fogadja el az alapértelmezett értékeket a **lemezek** lapon, majd kattintson a **Tovább gombra: hálózatkezelés >**.
1. Győződjön meg arról, hogy a virtuális hálózat **myAzureVNet** van kiválasztva.
1. Fogadja el az oldal többi alapértelmezett beállítását, majd kattintson a **Tovább gombra: felügyeleti >**.
2. A **rendszerindítási diagnosztika** esetében válassza a **ki** lehetőséget, fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** elemet.
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

Ismételje meg ezeket a lépéseket, és hozzon létre egy **myVM02** nevű virtuális gépet.

A virtuális gépek betöltéséhez néhány percet is igénybe vehet.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

 A következő példa létrehoz egy rekordot a relatív name **db** -vel a DNS-zóna **Private.contoso.com**, az erőforráscsoport **MyAzureResourceGroup**. A **db.Private.contoso.com** teljesen minősített neve. A rekord típusa "A", a **myVM01** IP-címével.

1. Nyissa meg a **MyAzureResourceGroup** erőforráscsoportot, és válassza ki a **Private.contoso.com** saját zónáját.
2. Válassza a **+ Rekordhalmaz** lehetőséget.
3. A **név** mezőbe írja be a következőt: **adatbázis**.
4. Az **IP-cím** mezőbe írja be a **myVM01** megjelenő IP-címet. Ezt automatikusan regisztrálni kell a virtuális gép indításakor.
5. Válassza az **OK** lehetőséget.

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a **Private.contoso.com** saját zónájának névfeloldását.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa az alábbi parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Ismételje meg ezt a myVM02 gép esetében is.

### <a name="ping-the-vms-by-name"></a>Virtuális gépek pingelése név alapján

1. A myVM02 gép Windows PowerShell parancssorából pingelje meg a myVM01 gépet az automatikusan regisztrált gazdagépnév használatával:
   ```
   ping myVM01.private.contoso.com
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Most pingelje meg a korábban létrehozott **db** nevet:
   ```
   ping db.private.contoso.com
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a **MyAzureResourceGroup** erőforráscsoportot az ebben a rövid útmutatóban létrehozott erőforrások törléséhez.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure DNS privát zónák forgatókönyvei](private-dns-scenarios.md)

