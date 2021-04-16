---
title: Rövid útmutató – Azure privát DNS-zóna létrehozása a Azure Portal
description: Ebben a rövid útmutatóban létrehoz és tesztel egy privát DNS-zónát és -rekordot a Azure DNS. Ez egy részletes útmutató az első saját DNS-zóna és -rekord létrehozásához és kezeléséhez a Azure Portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: 2115062db8615ca782cb987903e5ebfc83400cd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535451"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Rövid útmutató: Azure privát DNS-zóna létrehozása a Azure Portal

Ez a rövid útmutató végigvezeti az első saját DNS-zóna és -rekord létrehozásához szükséges lépéseken a Azure Portal.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Ha privát DNS-zónát szeretne közzétenni a virtuális hálózaton, meg kell adnia azon virtuális hálózatok listáját, amelyek feloldhatnak rekordokat a zónában.  Ezeket összekapcsolt *virtuális hálózatoknak* nevezzük. Ha az automatikus regisztráció engedélyezve van, a Azure DNS frissíti a zónarekordokat is, amikor egy virtuális gép létrejön, módosítja annak IP-címét vagy törlődik.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

Ha szeretné, ezt a rövid [](private-dns-getstarted-powershell.md) útmutatót a Azure PowerShell vagy az Azure CLI használatával [is befejezheti.](private-dns-getstarted-cli.md)

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

A következő példában létrehozunk egy private.contoso.com nevű DNS-zónát  egy **MyAzureResourceGroup nevű erőforráscsoportban.**

A DNS-zóna egy tartomány DNS-bejegyzését tartalmazza. Ahhoz, hogy a tartományt a Azure DNS, hozzon létre egy DNS-zónát a tartománynévhez.

![saját DNS zónák keresése](media/private-dns-portal/search-private-dns.png)

1. A portál keresősávjában írja be a **privát DNS-zónák** szöveget a keresőmezőbe, majd nyomja le az **Enter billentyűt.**
1. Válassza **saját DNS zónát.**
2. Válassza **a Privát DNS-zóna létrehozása lehetőséget.**

1. A **Create saját DNS zone** (Új zóna létrehozása) lapon írja be vagy válassza ki a következő értékeket:

   - **Erőforráscsoport:** Válassza az **Új létrehozása** lehetőséget, írja be a *MyAzureResourceGroup* gombra, majd kattintson az **OK gombra.** Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül.
   -  **Név:** *Private.contoso.com* a következőt: .
1. Az **Erőforráscsoport helye mezőben** válassza az **USA nyugati középső régiója lehetőséget.**

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

A zóna létrehozása eltarthat néhány percig.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie az alábbi paramétereket a lépésekben az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Meglévő erőforráscsoport kiválasztása) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | USA nyugati középső régiója      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

Ha a privát DNS-zónát egy virtuális hálózathoz szeretné kapcsolni, hozzon létre egy virtuális hálózati kapcsolatot.

![Virtuális hálózati kapcsolat hozzáadása](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Nyissa meg **a MyAzureResourceGroup** erőforráscsoportot, és válassza ki **private.contoso.com** saját zónát.
2. A bal oldali panelen válassza a **Virtuális hálózati kapcsolatok lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget.
4. A Hivatkozás neve mezőbe írja be a **myLink** **nevet.**
5. A **Virtuális hálózat mezőben** válassza a **myAzureVNet lehetőséget.**
6. Jelölje be **az Automatikus regisztráció engedélyezése** jelölőnégyzetet.
7. Válassza az **OK** lehetőséget.

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

1. A portál bal felső részén válassza az Erőforrás létrehozása, majd **a** **Windows Server 2016 Datacenter lehetőséget.**
1. Válassza **a MyAzureResourceGroup** erőforráscsoportot.
1. A virtuális gép neveként írja be a **myVM01** – nevet.
1. A **Régió mezőben válassza** az USA nyugati középső **régióját.**
1. Adja meg a rendszergazda felhasználónevét.
2. Adjon meg egy jelszót, és erősítse meg a jelszót.
5. A **Nyilvános bejövő portok beállításhoz** válassza a **Kijelölt** portok engedélyezése lehetőséget, majd a Bejövő portok kiválasztása beállításhoz válassza az **RDP (3389)** **lehetőséget.**
10. Fogadja el a lap többi alapértelmezett beállítását, majd kattintson a **Tovább: Lemezek**>.
11. Fogadja el az alapértelmezett értékeket a **Lemezek lapon,** majd kattintson a **Tovább: Hálózatkezelés >.**
1. Győződjön meg arról, hogy a **myAzureVNet van** kiválasztva a virtuális hálózathoz.
1. Fogadja el a lap többi alapértelmezett beállítását, majd kattintson a **Tovább: Felügyelet >.**
2. **Rendszerindítási diagnosztika esetén** válassza a **Ki** lehetőséget, fogadja el a többi alapértelmezett beállítást, majd válassza a Felülvizsgálat + **létrehozás lehetőséget.**
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás gombra.**

Ismételje meg ezeket a lépéseket, és hozzon létre egy másik virtuális gépet **myVM02 névvel.**

A két virtuális gép befejezése eltarthat néhány percig.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

 Az alábbi példa egy adatbázis  relatív nevű rekordot hoz létre a(z) DNS private.contoso.com zónában a **MyAzureResourceGroup erőforráscsoportban.**  A rekordhalmaz teljes neve **db.private.contoso.com.** A rekord típusa "A", és a **myVM01 IP-címe .**

1. Nyissa meg **a MyAzureResourceGroup** erőforráscsoportot, és válassza ki **a private.contoso.com** zónát.
2. Válassza a **+ Rekordhalmaz** lehetőséget.
3. A **Név mezőbe írja** be a következőt: **db.**
4. Az **IP-cím mezőbe** írja be a **myVM01 géphez látható IP-címet.** Ezt automatikusan regisztrálni kell a virtuális gép miközben elindult.
5. Válassza az **OK** lehetőséget.

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most már tesztelheti a saját zónája **névfeloldási private.contoso.com** számára.

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
