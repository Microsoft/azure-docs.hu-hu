---
title: A dinamikus DNS használata a gazdagépek regisztrálásához az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a dinamikus DNS-t a gazdagépek saját DNS-kiszolgálókon való regisztrálásához.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220422"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dinamikus DNS használata a gazdagépek regisztrálásához a saját DNS-kiszolgálón

Az Azure a virtuális gépek (VM) és a szerepkör-példányok névfeloldását [teszi lehetővé](virtual-networks-name-resolution-for-vms-and-role-instances.md) . Ha a névfeloldásnak meg kell haladnia az Azure alapértelmezett DNS-je által biztosított képességekkel, megadhatja saját DNS-kiszolgálóit. A saját DNS-kiszolgálóinak használatával a saját igényeinek megfelelően testre szabhatja a DNS-megoldást. Előfordulhat például, hogy a Active Directory tartományvezérlőn keresztül kell hozzáférnie a helyszíni erőforrásokhoz.

Ha az egyéni DNS-kiszolgálók Azure-beli virtuális gépekként vannak tárolva, a gazdagépek feloldásához továbbíthatja az állomásnév-lekérdezéseket ugyanarra a virtuális hálózatra az Azure-ban. Ha nem szeretné használni ezt a beállítást, a DNS-kiszolgálón regisztrálhatja a virtuális gép állomásnevét a dinamikus DNS (DDNS) használatával. Az Azure nem rendelkezik a hitelesítő adatokkal a DNS-kiszolgálókon lévő rekordok közvetlen létrehozásához, ezért az alternatív megoldások gyakran szükségesek. Néhány gyakori forgatókönyv, a következő alternatívákkal:

## <a name="windows-clients"></a>Windows-ügyfelek
A tartományhoz nem csatlakoztatott Windows-ügyfelek megkísérlik a nem biztonságos DDNS-frissítéseket a rendszerindításkor vagy az IP-címük változásakor. A DNS-név az állomásnév és az elsődleges DNS-utótag. Az Azure üresen hagyja az elsődleges DNS-utótagot, de beállíthatja a virtuális gép utótagját a [felhasználói felületen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) vagy a [powershellen](/powershell/module/dnsclient/set-dnsclient)keresztül.

A tartományhoz csatlakoztatott Windows-ügyfelek a biztonságos DDNS használatával regisztrálják az IP-címüket a tartományvezérlőhöz. A tartományhoz való csatlakozás folyamata beállítja az elsődleges DNS-utótagot az ügyfélen, és létrehozza és karbantartja a megbízhatósági kapcsolatot.

## <a name="linux-clients"></a>Linux-ügyfelek
A Linux-ügyfelek általában nem regisztrálják magukat a DNS-kiszolgálóval indításkor, feltételezik, hogy a DHCP-kiszolgáló végzi. Az Azure DHCP-kiszolgálói nem rendelkeznek a rekordok a DNS-kiszolgálón való regisztrálásához szükséges hitelesítő adatokkal. A DDNS-frissítések elküldéséhez használhat egy nevű eszközt is `nsupdate` , amely a kötési csomagban található. Mivel a DDNS protokoll szabványosítva van, akkor is használhatja, ha `nsupdate` nem használ kötést a DNS-kiszolgálón.

A DHCP-ügyfél által biztosított hookokat a DNS-kiszolgáló hostname bejegyzésének létrehozására és karbantartására használhatja. A DHCP-ciklus során az ügyfél végrehajtja a parancsfájlokat a */etc/DHCP/dhclient-Exit-Hooks.d/*-ben. A hookok segítségével regisztrálja az új IP-címet a használatával `nsupdate` . Például:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

A `nsupdate` parancs használatával biztonságos DDNS-frissítéseket is végrehajthat. Ha például egy kötési DNS-kiszolgálót használ, egy nyilvános titkos kulcspár jön létre ( `http://linux.yyz.us/nsupdate/` ). A DNS-kiszolgáló konfigurálva van ( `http://linux.yyz.us/dns/ddns-server.html` ) a kulcs nyilvános részével, hogy ellenőrizni tudja a kérelem aláírását. Ahhoz, hogy a kulcspár elérhető legyen a számára `nsupdate` , használja a `-k` lehetőséget a DDNS frissítési kérelem aláírásához.

Ha Windows DNS-kiszolgálót használ, Kerberos-hitelesítést használhat a paraméterrel a alkalmazásban `-g` `nsupdate` , de a Windows-verziójában nem érhető el `nsupdate` . A Kerberos használatához a használatával `kinit` töltse be a hitelesítő adatokat. Például betöltheti a hitelesítő adatokat egy [keytab kiterjesztésű-fájlból](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), majd `nsupdate -g` felveszi a hitelesítő adatokat a gyorsítótárból.

Ha szükséges, DNS-keresési utótagot adhat hozzá a virtuális gépekhez. A DNS-utótag a */etc/resolv.conf* fájlban van megadva. A legtöbb Linux-disztribúció automatikusan kezeli a fájl tartalmát, így általában nem szerkesztheti. A DHCP-ügyfél parancsának használatával azonban felülbírálhatja az utótagot `supersede` . Az utótag felülbírálásához adja hozzá a következő sort a */etc/DHCP/dhclient.conf* -fájlhoz:

```
supersede domain-name <required-dns-suffix>;
```