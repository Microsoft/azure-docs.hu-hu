---
title: Az Azure-hoz való kapcsolódás hibáinak elhárítása az Azure-ban Azure Site Recovery
description: Az Azure-beli virtuális gép vész-helyreállítási kapcsolati problémáinak elhárítása
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652262"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure – Azure virtuálisgép-hálózat csatlakozási problémáinak elhárítása

Ez a cikk a hálózati kapcsolattal kapcsolatos gyakori problémákat ismerteti, amikor Azure-beli virtuális gépeket (VM) replikál és helyreállít az egyik régióból a másikba. A hálózati követelményekkel kapcsolatos további információkért tekintse [meg az Azure-beli virtuális gépek replikálásának kapcsolódási követelményeit](azure-to-azure-about-networking.md)ismertető témakört.

Ahhoz, hogy Site Recovery replikáció működjön, az adott URL-címekhez vagy IP-tartományokhoz kimenő kapcsolat szükséges a virtuális gépről. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatot, akkor előfordulhat, hogy ezek egyike a probléma.

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Tárolás                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótár-tárolási fiókot, használhat egy engedélyezési listát az adott Storage-fiók URL-címeihez. Például a `cache1.blob.core.windows.net` és `cache2.blob.core.windows.net` a helyett `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez. |
| Replikáció               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen. A megfelelő _site Recovery IP-címet_ használhatja, ha a tűzfal proxyja támogatja az IP-címeket. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő _site Recovery figyelési IP-címet_ . |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat Site Recovery URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>1. probléma: nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (151195)

#### <a name="possible-cause"></a>Lehetséges ok

A tartománynévrendszer (DNS) feloldási hibája miatt nem lehet kapcsolódni Site Recovery végpontokhoz. Ez a probléma gyakoribb a virtuális gép feladatátvétele során, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióból.

#### <a name="resolution"></a>Feloldás

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból.

Annak ellenőrzését, hogy a virtuális gép használ-e egyéni DNS-beállítást:

1. Nyissa meg a **virtuális gépeket** , és válassza ki a virtuális gépet.
1. Navigáljon a virtuális gépek **beállításaihoz** , és válassza a **hálózatkezelés** lehetőséget.
1. A **virtuális hálózat/alhálózat** területen válassza a virtuális hálózat erőforrás-lapjának megnyitására szolgáló hivatkozást.
1. Lépjen a **Beállítások** és a **DNS-kiszolgálók** elemre.

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a DNS-kiszolgáló nem érhető el, tegye elérhetővé a DNS-kiszolgáló meghibásodása vagy a hely létrehozása a DR hálózat és a DNS között.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com – hiba":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: Site Recovery konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek **szabványos** belső terheléselosztó mögött vannak, alapértelmezés szerint nem férhet hozzá a Microsoft 365 IP-címekhez, például a következőhöz: `login.microsoftonline.com` . Módosítsa az **alapszintű** belső terheléselosztó típusára, vagy hozzon létre kimenő hozzáférést a [standard Load Balancer terheléselosztás és kimenő szabályok konfigurálása az Azure CLI használatával](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)című cikkben említettek szerint.

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet csatlakozni a hitelesítéshez és az Identity IP4-végpontok Microsoft 365ához.

#### <a name="resolution"></a>Feloldás

- A Azure Site Recovery hozzáférést igényel a Microsoft 365 IP-tartományokhoz a hitelesítéshez.
- Ha az Azure hálózati biztonsági csoport (NSG) szabályai/tűzfal proxyja segítségével vezérli a kimenő hálózati kapcsolatot a virtuális gépen, győződjön meg arról, hogy engedélyezi a kommunikációt a Microsoft 365 IP-címtartományok számára. Hozzon létre egy [Azure Active Directory (Azure ad) Service tag](../virtual-network/network-security-groups-overview.md#service-tags) -alapú NSG-szabályt, amely lehetővé teszi az Azure ad-nek megfelelő összes IP-cím elérését.
- Ha a jövőben új címeket adnak hozzá az Azure AD-hoz, létre kell hoznia új NSG-szabályokat.

### <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja a virtuális gépek NSG-szabályait a replikáláshoz.

- Ha NSG szabályokat használ a kimenő kapcsolatok vezérlésére, a **https-kimenő szabályok engedélyezése** a 443-as portra az összes szükséges IP-címtartományok esetében.
- A példa azt feltételezi, hogy a virtuális gép forrásának helye az **USA keleti** régiója, a célhely pedig az **USA középső** régiója.

#### <a name="nsg-rules---east-us"></a>NSG-szabályok – USA keleti régiója

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a NSG, ahogy az alábbi képernyőfelvételen is látható. Ez a példa a **célként megadott szolgáltatási címkét** használja: _Storage. EastUS_ és a **célport tartománya**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="A képernyőfelvétel a kimenő biztonsági szabály hozzáadása panelt jeleníti meg a Storage dot East U S-hoz tartozó biztonsági szabályokhoz.":::

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a NSG, ahogy az alábbi képernyőfelvételen is látható. Ez a példa a **célként megadott szolgáltatási címkét** használja: a _AzureActiveDirectory_ és a **célport tartományait**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="A képernyőképen látható a kimenő biztonsági szabály hozzáadása panel a Azure Active Directory biztonsági szabályához.":::

1. A fenti biztonsági szabályokhoz hasonlóan hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "EventHub. CentralUS" számára a NSG, amely megfelel a célhelynek. Ez lehetővé teszi Site Recovery figyeléshez való hozzáférést.
1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureSiteRecovery" számára a NSG. Ez bármely régióban engedélyezi Site Recovery szolgáltatás elérését.

#### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ebben a példában ezek a NSG szabályok szükségesek ahhoz, hogy a replikáció engedélyezhető legyen a célként megadott régióban a feladatátvételt követően:

1. Hozzon létre egy HTTPS kimenő biztonsági szabályt a _Storage. CentralUS_:

   - **Célhely szolgáltatás címkéje**: _Storage. CentralUS_
   - **Célport tartományai**: _443_

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a _AzureActiveDirectory_.

   - **Rendeltetési szolgáltatás címkéje**: _AzureActiveDirectory_
   - **Célport tartományai**: _443_

1. A fenti biztonsági szabályokhoz hasonlóan hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "EventHub. EastUS" számára a NSG, amely megfelel a forrás helyének. Ez lehetővé teszi Site Recovery figyeléshez való hozzáférést.
1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureSiteRecovery" számára a NSG. Ez bármely régióban engedélyezi Site Recovery szolgáltatás elérését.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: Site Recovery konfiguráció nem sikerült (151197)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet kapcsolódni Azure Site Recovery szolgáltatási végpontokhoz.

#### <a name="resolution"></a>Feloldás

Ha Azure hálózati biztonsági csoport (NSG) szabályt vagy tűzfal-proxyt használ a kimenő hálózati kapcsolat vezérlésére a gépen, több szolgáltatás-címkét is engedélyezni kell. [További információk](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4. probléma: az Azure – Azure replikáció sikertelen volt, ha a hálózati forgalom a helyszíni proxykiszolgálón keresztül halad (151072)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és a Azure Site Recovery mobilitási szolgáltatás ügynöke nem ismeri fel automatikusan a proxybeállításokat az Internet Explorerben (IE).

#### <a name="resolution"></a>Feloldás

1. A mobilitási szolgáltatás ügynöke észleli a proxybeállításokat az IE-ből a Windows és `/etc/environment` Linux rendszeren.
1. Ha inkább a proxyt szeretné beállítani Azure Site Recovery mobilitási szolgáltatáshoz, a proxy részleteit a _ProxyInfo. conf fájlban_ találja a következő helyen:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. A _ProxyInfo. conf_ _fájlnak_ a következő ini formátumúnak kell lennie:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery mobilitási szolgáltatás ügynöke csak a nem **hitelesített proxykat** támogatja.

### <a name="fix-the-problem"></a>A probléma javítása

[A szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)engedélyezéséhez kövesse a [hálózatkezelési útmutató dokumentum](./azure-to-azure-about-networking.md)lépéseit.

## <a name="next-steps"></a>Következő lépések

[Azure-beli virtuális gépek replikálása másik Azure-régióba](azure-to-azure-how-to-enable-replication.md)