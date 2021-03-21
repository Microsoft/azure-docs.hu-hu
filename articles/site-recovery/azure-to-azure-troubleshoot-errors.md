---
title: Azure-beli virtuális gépek replikálásának hibája Azure Site Recovery
description: Hibák elhárítása az Azure-beli virtuális gépek vész-helyreállításra való replikálásakor.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900416"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure-ból Azure-ba történő virtuálisgép-replikációval kapcsolatos hibák elhárítása

Ez a cikk az Azure Virtual Machines (VM) egyik régióból a másikba történő replikálásának és helyreállításának során Azure Site Recovery gyakori hibáinak elhárítását ismerteti. További információ a támogatott konfigurációkról: Azure-beli [virtuális gépek replikálásának támogatási mátrixa](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás-kvótával kapcsolatos problémák (hibakód: 150097)

Győződjön meg arról, hogy az előfizetése engedélyezve van az Azure-beli virtuális gépek létrehozásához a vész-helyreállítási (DR) régióként használni kívánt régióban. Az előfizetéshez elegendő kvóta szükséges ahhoz, hogy a virtuális gépeket a szükséges méretek alapján hozza létre. Alapértelmezés szerint a Site Recovery kiválasztja a virtuálisgép-mérethez hasonló cél virtuális gép méretét. Ha az egyező méret nem érhető el, Site Recovery automatikusan kiválasztja a legközelebbi elérhető méretet.

Ha nincs olyan méret, amely támogatja a forrás virtuális gép konfigurációját, a következő üzenet jelenik meg:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Lehetséges okok

- Az előfizetés-azonosító nincs engedélyezve a célként megadott régióban található virtuális gépek létrehozásához.
- Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával a virtuálisgép-méretek létrehozásához a célként megadott régióban.
- Nem található megfelelő célként megadott virtuálisgép-méret a forrás virtuális gép hálózati adapterének (NIC) számával (2), az előfizetés-AZONOSÍTÓhoz a célként megadott régióban.

### <a name="fix-the-problem"></a>A probléma javítása

Lépjen kapcsolatba az [Azure számlázási támogatási szolgálatával](../azure-portal/supportability/resource-manager-core-quotas-request.md) , és engedélyezze, hogy előfizetése virtuális gépeket hozzon létre a célhelyen a szükséges méretekben. Ezután próbálja megismételni a sikertelen műveletet.

Ha a célhely kapacitása korlátozást tartalmaz, tiltsa le a replikációt az adott helyre. Ezután engedélyezze a replikálást egy másik helyre, ahol az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha nem minden legújabb megbízható főtanúsítvány van jelen a virtuális gépen, akkor a Site Recovery replikációjának engedélyezéséhez szükséges feladat sikertelen lehet. A virtuális gépről érkező Site Recovery szolgáltatás-hívások hitelesítése és engedélyezése a tanúsítványok nélkül meghiúsul.

Ha a replikációs feladatok engedélyezése nem sikerül, a következő üzenet jelenik meg:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Lehetséges ok

Az engedélyezéshez és a hitelesítéshez szükséges megbízható főtanúsítványok nem jelennek meg a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

#### <a name="windows"></a>Windows

A Windows operációs rendszert futtató virtuális gépek esetében telepítse a legújabb Windows-frissítéseket, hogy az összes megbízható főtanúsítvány megtalálható legyen a virtuális gépen. A szervezeten belüli, a Windows Update Management vagy a Certificate Update felügyeleti folyamattal megszerezheti a legfelső szintű tanúsítványokat és a frissített visszavont tanúsítványok listáját a virtuális gépeken.

- Ha leválasztott környezetet használ, a tanúsítványok lekéréséhez kövesse a szervezet szabványos Windows-frissítési folyamatát.
- Ha a szükséges tanúsítványok nem találhatók a virtuális gépen, akkor a Site Recovery szolgáltatás felé irányuló hívások biztonsági okokból sikertelenek lesznek.

A probléma megoldásának ellenőrzéséhez nyissa meg a `login.microsoftonline.com` virtuális gép egy böngészőjét.

További információ: [megbízható gyökerek és nem engedélyezett tanúsítványok konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Kövesse a Linux operációs rendszer verziójának forgalmazója által biztosított útmutatást a legújabb megbízható főtanúsítványok és a tanúsítvány-visszavonási lista legújabb, a virtuális gépen való lekéréséhez.

Mivel a SUSE Linux szimbolikus hivatkozásokat vagy symlinkeket használ a tanúsítványok listájának karbantartásához, kövesse az alábbi lépéseket:

1. Jelentkezzen be **root** felhasználóként. A kivonatoló szimbólum ( `#` ) az alapértelmezett parancssor.

1. A könyvtár módosításához futtassa a következő parancsot:

   `cd /etc/ssl/certs`

1. Győződjön meg arról, hogy a Symantec legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa megtalálható-e:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Ha nem található a Symantec legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa, futtassa a következő parancsot a fájl letöltéséhez. Keressen hibákat, és kövesse az ajánlott műveleteket a hálózati hibákhoz.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Győződjön meg arról, hogy a Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa megtalálható-e:

   `ls Baltimore_CyberTrust_Root.pem`

   - Ha a Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa nem található, futtassa ezt a parancsot a tanúsítvány letöltéséhez:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Győződjön meg arról, hogy az DigiCert_Global_Root_CA-tanúsítvány megtalálható:

   `ls DigiCert_Global_Root_CA.pem`

    - Ha a DigiCert_Global_Root_CA nem található, futtassa a következő parancsokat a tanúsítvány letöltéséhez:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Ha frissíteni szeretné a tanúsítvány tulajdonosának kivonatait az újonnan letöltött tanúsítványokhoz, futtassa az Újratördelési parancsfájlt:

   `c_rehash`

1. Futtassa az alábbi parancsokat, hogy meggyőződjön arról, hogy a tulajdonos kivonatai a tanúsítványokhoz lettek-e létrehozva:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Hozzon létre egy másolatot a fájlról _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. PEM_ fájlnév _b204d74a. 0_ fájllal:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Hozzon létre egy másolatot a fájlról _Baltimore_CyberTrust_Root. PEM_ fájlnév _653b494a. 0_ fájllal:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Hozzon létre egy másolatot a fájlról _DigiCert_Global_Root_CA. PEM_ fájlnév _3513523f. 0_ fájllal:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Győződjön meg arról, hogy a fájlok jelen vannak:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő URL-címek vagy IP-címtartományok (hibakód: 151037 vagy 151072)

Site Recovery replikálás működéséhez az adott URL-címekre irányuló kimenő kapcsolat szükséges a virtuális gépről. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatot, akkor előfordulhat, hogy ezek egyike a probléma. Noha az URL-címeken keresztül továbbra is támogatja a kimenő hozzáférést, az IP-címtartományok engedélyezési listájának használata már nem támogatott.

#### <a name="possible-causes"></a>Lehetséges okok

- A tartománynévrendszer (DNS) feloldási hibája miatt nem lehet kapcsolódni Site Recovery végpontokhoz.
- Ez a probléma gyakoribb az ismételt védelem során, amikor a virtuális gép feladatátvétele megtörtént, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióból.

#### <a name="fix-the-problem"></a>A probléma javítása

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból.

Annak ellenőrzését, hogy a virtuális gép használ-e egyéni DNS-beállítást:

1. Nyissa meg a **virtuális gépeket** , és válassza ki a virtuális gépet.
1. Navigáljon a virtuális gépek **beállításaihoz** , és válassza a **hálózatkezelés** lehetőséget.
1. A **virtuális hálózat/alhálózat** területen válassza a virtuális hálózat erőforrás-lapjának megnyitására szolgáló hivatkozást.
1. Lépjen a **Beállítások** és a **DNS-kiszolgálók** elemre.

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a DNS-kiszolgáló nem érhető el, tegye elérhetővé a DNS-kiszolgáló meghibásodása vagy a hely létrehozása a DR hálózat és a DNS között.

> [!NOTE]
> Ha privát végpontokat használ, győződjön meg arról, hogy a virtuális gépek fel tudják oldani a magánhálózati DNS-rekordokat.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com – hiba.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: Site Recovery konfiguráció nem sikerült (151196)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet csatlakozni a hitelesítéshez és az Identity IP4-végpontok Microsoft 365ához.

#### <a name="fix-the-problem"></a>A probléma javítása

Azure Site Recovery szükséges hozzáférés Microsoft 365 IP-tartományokhoz a hitelesítéshez.
Ha az Azure hálózati biztonsági csoport (NSG) szabályai/tűzfal proxyja segítségével vezérli a kimenő hálózati kapcsolatot a virtuális gépen, ügyeljen arra, hogy [Azure Active Directory (HRE)](../virtual-network/network-security-groups-overview.md#service-tags) NSG-szabályt használjon a HRE való hozzáférés engedélyezéséhez. Az IP-cím alapú NSG-szabályok már nem támogatottak.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: Site Recovery konfiguráció nem sikerült (151197)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet kapcsolódni Azure Site Recovery szolgáltatási végpontokhoz.

#### <a name="fix-the-problem"></a>A probléma javítása

Ha az Azure hálózati biztonsági csoport (NSG) szabályait/tűzfal proxyját használja a kimenő hálózati kapcsolat vezérléséhez a virtuális gépen, ügyeljen arra, hogy a szolgáltatás címkéit használja. A továbbiakban nem támogatjuk az IP-címek engedélyezési listáját a NSG-on keresztül Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>4. probléma: a replikáció meghiúsul, ha a hálózati forgalom helyszíni proxykiszolgálót használ (151072)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és a mobilitási szolgáltatás ügynöke nem ismeri fel automatikusan a proxybeállításokat az Internet Explorerben (IE).

#### <a name="fix-the-problem"></a>A probléma javítása

1. A mobilitási szolgáltatás ügynöke észleli a proxybeállításokat az IE-ből a Windows és `/etc/environment` Linux rendszeren.
1. Ha a proxyt csak a mobilitási szolgáltatáshoz szeretné beállítani, akkor a proxy részleteit a _ProxyInfo. conf fájlban_ adhatja meg a következő helyen:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. A _ProxyInfo. conf_ _fájlnak_ a következő ini-formátumúnak kell lennie.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> A mobilitási szolgáltatás ügynöke csak a nem **hitelesített proxykat** támogatja.

### <a name="more-information"></a>További információ

A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-címtartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)megadásához kövesse az [Azure-beli hálózatkezelés az Azure-ban való replikálásával foglalkozó témakör](azure-to-azure-about-networking.md)útmutatását.

## <a name="disk-not-found-in-vm-error-code-150039"></a>A lemez nem található a virtuális gépen (hibakód: 150039)

A virtuális géphez csatolt új lemezt inicializálni kell. Ha a lemez nem található, a következő üzenet jelenik meg:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Lehetséges okok

- Új adatlemez lett csatolva a virtuális géphez, de nem lett inicializálva.
- A virtuális gépen belüli adatlemez nem jelenti megfelelően a logikai egység számát (LUN), amelyen a lemez a virtuális géphez lett csatolva.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy az adatlemezek inicializálva vannak, majd próbálja megismételni a műveletet.

- **Windows**: [új lemez csatolása és inicializálása](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [új adatlemez inicializálása Linuxon](../virtual-machines/linux/add-disk.md).

Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Több lemez érhető el védelemhez (hibakód: 153039)

### <a name="possible-causes"></a>Lehetséges okok

- A védelem után egy vagy több lemezt nemrégiben adtak hozzá a virtuális géphez.
- Egy vagy több lemez inicializálása a virtuális gép védelme után történt.

### <a name="fix-the-problem"></a>A probléma javítása

A virtuális gép replikálási állapotának újbóli megadásához választhatja a lemezek védelme vagy a figyelmeztetés mellőzése lehetőséget.

#### <a name="to-protect-the-disks"></a>A lemezek védelme

1. Nyissa meg a **replikált elemek**  >  _virtuális gépek neve_  >  **lemezeket**.
1. Válassza ki a nem védett lemezt, majd válassza a **replikáció engedélyezése** lehetőséget:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Engedélyezze a replikálást a virtuális gépek lemezein.":::

#### <a name="to-dismiss-the-warning"></a>A figyelmeztetés mellőzése

1. Nyissa meg a **replikált elemek**  >  _virtuális gép nevét_.
1. Válassza ki a figyelmeztetést az **Áttekintés** szakaszban, majd kattintson az **OK gombra**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Új lemezre vonatkozó figyelmeztetés mellőzése.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>A tárból eltávolított virtuális gép információkkal fejeződött be (hibakód: 150225)

Ha a Site Recovery megvédi a virtuális gépet, a a forrás virtuális gépre mutató hivatkozásokat hoz létre. Ha eltávolítja a védelmet vagy letiltja a replikálást, Site Recovery eltávolítja ezeket a hivatkozásokat a karbantartási feladatok részeként. Ha a virtuális gép rendelkezik erőforrás-zárolással, a karbantartási feladatot a rendszer az adatokkal együtt végrehajtja. Az információ szerint a virtuális gép el lett távolítva a Recovery Services-tárolóból, de az elavult hivatkozások némelyikét nem lehetett törölni a forrásszámítógépen.

Figyelmen kívül hagyhatja ezt a figyelmeztetést, ha soha nem szeretné többé a virtuális gépet védelemmel ellátni. Ha azonban később el kell végeznie ezt a virtuális gépet, kövesse a jelen szakaszban ismertetett lépéseket a hivatkozások törléséhez.

> [!WARNING]
> Ha nem végzi el a karbantartást:
>
> - Ha a Recovery Services-tároló segítségével engedélyezi a replikálást, a virtuális gép nem jelenik meg.
> - Ha a **virtuális gép** beállításait a vész-helyreállítás használatával próbálja meg védelemmel ellátni  >    >  , a művelet sikertelen lesz, és az üzenet- **replikálás nem engedélyezhető** a virtuális gépen lévő elavult erőforrás-hivatkozások miatt.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Szüntesse meg a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. Az alábbi ábrán például törölni kell a nevű virtuális gép erőforrás-zárolását `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a virtuális gép zárolását.":::

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, _Cleanup-stale-asr-config-Azure-VM.ps1_. Adja meg az **előfizetés azonosítóját**, a virtuálisgép- **erőforráscsoportot** és a **virtuális gép nevét** paraméterként.
1. Ha az Azure-beli hitelesítő adatok megadását kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Az elavult erőforrásokkal rendelkező virtuális gépen nincs engedélyezve a replikáció (hibakód: 150226)

### <a name="possible-causes"></a>Lehetséges okok

A virtuális gépen elavult konfiguráció található a korábbi Site Recovery-védelemből.

Ha az Azure-beli virtuális gép replikálását a Site Recovery használatával engedélyezte, elavult konfigurációt lehet használni az Azure-beli virtuális gépen, majd:

- Letiltotta a replikálást, de a forrás virtuális gépnek erőforrás-zárolása volt.
- A Site Recovery-tárolót a virtuális gép replikációjának explicit letiltása nélkül törölte.
- Törölte a Site Recovery tárolót tartalmazó erőforráscsoportot anélkül, hogy explicit módon le kellene tiltani a replikálást a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Szüntesse meg a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. Az alábbi ábrán például törölni kell a nevű virtuális gép erőforrás-zárolását `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a virtuális gép zárolását.":::

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, _Cleanup-stale-asr-config-Azure-VM.ps1_. Adja meg az **előfizetés azonosítóját**, a virtuálisgép- **erőforráscsoportot** és a **virtuális gép nevét** paraméterként.
1. Ha az Azure-beli hitelesítő adatok megadását kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Nem lehet kijelölni a virtuális gépet vagy az erőforráscsoportot a replikációs feladatok engedélyezésekor

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>1. probléma: az erőforráscsoport és a forrás virtuális gép különböző helyen található

Site Recovery jelenleg a forrás-régió erőforráscsoport és a virtuális gépek azonos helyen kell lenniük. Ha nem, akkor nem fogja tudni megkeresni a virtuális gépet vagy az erőforráscsoportot a védelem alkalmazására irányuló kísérlet során.

Megkerülő megoldásként engedélyezheti a virtuális gép replikálását a Recovery Services-tároló helyett. Lépjen a **forrás virtuális gép**  >  **tulajdonságai** vész  >  -**helyreállítás** menüpontra, és engedélyezze a replikálást.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>2. probléma: az erőforráscsoport nem része a kijelölt előfizetésnek

Előfordulhat, hogy nem találja az erőforráscsoportot a védelem idején, ha az erőforráscsoport nem része a kijelölt előfizetésnek. Győződjön meg arról, hogy az erőforráscsoport a használt előfizetéshez tartozik.

### <a name="issue-3-stale-configuration"></a>3. probléma: elavult konfiguráció

Előfordulhat, hogy nem látja azt a virtuális gépet, amelyet engedélyezni szeretne a replikáláshoz, ha az Azure-beli virtuális gépen elavult Site Recovery konfiguráció található. Ez az állapot akkor fordulhat elő, ha a Site Recovery használatával engedélyezte az Azure-beli virtuális gép replikálását, majd:

- A Site Recovery-tárolót a virtuális gép replikációjának explicit letiltása nélkül törölte.
- Törölte a Site Recovery tárolót tartalmazó erőforráscsoportot anélkül, hogy explicit módon le kellene tiltani a replikálást a virtuális gépen.
- Letiltotta a replikálást, de a forrás virtuális gépnek erőforrás-zárolása volt.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> Győződjön meg arról, hogy az `AzureRM.Resources` ebben a szakaszban említett szkript használata előtt frissíti a modult. Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Távolítsa el a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását, ha van ilyen. Az alábbi ábrán például törölni kell a nevű virtuális gép erőforrás-zárolását `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a virtuális gép zárolását.":::

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, _Cleanup-stale-asr-config-Azure-VM.ps1_. Adja meg az **előfizetés azonosítóját**, a virtuálisgép- **erőforráscsoportot** és a **virtuális gép nevét** paraméterként.
1. Ha az Azure-beli hitelesítő adatok megadását kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="unable-to-select-a-vm-for-protection"></a>Nem lehet kijelölni a virtuális gépet a védelemhez

### <a name="possible-cause"></a>Lehetséges ok

A virtuális gépen egy sikertelen vagy nem válaszoló állapotú bővítmény van telepítve

### <a name="fix-the-problem"></a>A probléma javítása

Nyissa meg a **virtuális gépek**  >  **beállításait**  >  tartalmazó **bővítményeket** , és győződjön meg arról, hogy a bővítmények hibás állapotban vannak. Távolítsa el a sikertelen bővítményeket, majd próbálkozzon újra a virtuális gép védelemmel.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>A virtuális gép kiépítési állapota érvénytelen (hibakód: 150019)

A virtuális gép replikálásának engedélyezéséhez a létesítési állapotnak **sikeresnek** kell lennie. Az alábbi lépéseket követve ellenőrizheti a kiépítési állapotot:

1. A Azure Portal válassza ki a **erőforrás-kezelő** az **összes szolgáltatásból**.
1. Bontsa ki az **előfizetések** listát, és válassza ki az előfizetését.
1. Bontsa ki a **ResourceGroups** listát, és válassza ki a virtuális gép erőforrás-csoportját.
1. Bontsa ki az **erőforrások** listát, és válassza ki a virtuális gépet.
1. A jobb oldalon található példány nézetben tekintse meg a **provisioningState** mezőt.

### <a name="fix-the-problem"></a>A probléma javítása

- Ha a **ProvisioningState** **sikertelen**, a hibakereséshez forduljon az ügyfélszolgálathoz.
- Ha a **ProvisioningState** **frissítése** folyamatban van, előfordulhat, hogy egy másik bővítményt is üzembe kell helyezni. Győződjön meg arról, hogy vannak-e folyamatban lévő műveletek a virtuális gépen, várjon, amíg befejeződik, majd próbálja megismételni a sikertelen Site Recovery feladatot a replikáció engedélyezéséhez.

## <a name="unable-to-select-target-vm"></a>A cél virtuális gép nem választható ki

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>1. probléma: a virtuális gép olyan hálózathoz csatlakozik, amely már le van képezve egy célként megadott hálózatra.

A vész-helyreállítási konfiguráció során, ha a forrás virtuális gép egy virtuális hálózat része, és egy másik virtuális hálózat már le van képezve egy, a célként megadott erőforráscsoport egyik hálózatával, a hálózati kijelölés legördülő lista nem érhető el (a halványan jelenik meg).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="A hálózati kiválasztási lista nem érhető el.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>2. probléma: előzőleg védett a virtuális gép, majd letiltotta a replikálást.

A virtuális gép replikálásának letiltása nem törli a hálózati leképezést. A leképezést törölni kell a Recovery Services tárolóból, ahol a virtuális gép védett volt. Válassza ki a **Recovery Services** -tárolót, és lépjen az Azure-beli   >    >  **virtuális gépek**  >  **hálózati leképezése** site Recovery-infrastruktúra kezelése elemre.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Hálózati leképezés törlése.":::

A vész-helyreállítási telepítés során konfigurált célként megadott hálózat a kezdeti beállítás után és a virtuális gép védelme után módosítható. A **hálózati leképezés módosításához** válassza ki a hálózat nevét:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Módosítsa a hálózat leképezését.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ vagy VSS (hibakód: 151025)

Ha a COM+ vagy Kötet árnyékmásolata szolgáltatás (VSS) hiba történik, a következő üzenet jelenik meg:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Lehetséges okok

- A COM+ rendszeralkalmazás-szolgáltatás le van tiltva.
- A Kötet árnyékmásolata szolgáltatás le van tiltva.

### <a name="fix-the-problem"></a>A probléma javítása

Állítsa be a COM+ rendszeralkalmazást, és Kötet árnyékmásolata szolgáltatás automatikus vagy manuális indítási módba.

1. Nyissa meg a szolgáltatások konzolt a Windows rendszerben.
1. Győződjön meg arról, hogy a COM+ rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatás nincs **Letiltva** az **indítási típusként**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Győződjön meg arról, hogy a COM Plus rendszeralkalmazás indítási típusa és Kötet árnyékmásolata szolgáltatás.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>A felügyelt lemez mérete nem támogatott (hibakód: 150172)

Ha ez a hiba történik, a következő üzenet jelenik meg:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Lehetséges ok

A lemez kisebb, mint a támogatott 1024 MB-os méret.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy a lemez mérete a támogatott tartományon belül van, majd próbálja megismételni a műveletet.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>A védelem nem engedélyezett, ha a GRUB az eszköz nevét használja (hibakód: 151126)

### <a name="possible-causes"></a>Lehetséges okok

A Linux Grand Unified bootloader (GRUB) konfigurációs fájljai (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_ vagy _/etc/default/grub_) megadhatják a tényleges eszköznév helyett a `root` és paraméterekhez tartozó, univerzálisan egyedi azonosító (UUID) értékeket `resume` . A Site Recovery UUID-ket igényel, mert az eszközök nevei módosíthatók. Újraindításkor előfordulhat, hogy a virtuális gép nem ugyanazzal a névvel van ellátva a feladatátvétel során, ami problémákat okozhat.

Az alábbi példák olyan GRUB-fájlokból származó sorok, amelyekben az eszközök nevei a szükséges UUID-EK helyett megjelennek:

- Fájl _/boot/GRUB2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Fájl: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>A probléma javítása

Cserélje le az egyes eszközök nevét a megfelelő UUID-ra:

1. A parancs végrehajtásával keresse meg az eszköz UUID-azonosítóját `blkid <device name>` . Például:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Cserélje le az eszköz nevét az UUID-ra, a formátumok és a elemre `root=UUID=<UUID>` `resume=UUID=<UUID>` . Ha például a csere után a _/boot/grub/menu.lst_ sor a következő sorba fog hasonlítani:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Próbálja megismételni a védelmet.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>A védelem nem sikerült, mert a GRUB-eszköz nem létezik (hibakód: 151124)

### <a name="possible-cause"></a>Lehetséges ok

A GRUB konfigurációs fájljai (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_ vagy _/etc/default/grub_) tartalmazhatják a paramétereket vagy a-t `rd.lvm.lv` `rd_LVM_LV` . Ezek a paraméterek azonosítják a rendszerindításkor felderített logikai kötet-kezelő (LVM) eszközöket. Ha ezek az LVM-eszközök nem léteznek, a védett rendszer nem fog elindulni, és a rendszerindítási folyamat elakad. Ugyanez a probléma a feladatátvételi virtuális géppel is látható lesz. Íme néhány példa:

- Fájl: _/boot/GRUB2/grub.cfg_ a RHEL7-on:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Fájl: _/etc/default/grub_ a RHEL7-on:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Fájl: _/boot/grub/menu.lst_ a 64 bites rhel6-on:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

Az egyes példákban a GRUB-nak két LVM-eszközt kell észlelni a névvel `root` és a `swap` kötet csoportból `rootvg` .

### <a name="fix-the-problem"></a>A probléma javítása

Ha az LVM-eszköz nem létezik, hozza létre, vagy távolítsa el a megfelelő paramétereket a GRUB konfigurációs fájljaiból. Ezután próbálkozzon újra a védelem engedélyezéséhez.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>A mobilitási szolgáltatás frissítése figyelmeztetésekkel fejeződött be (hibakód: 151083)

A Site Recovery mobilitási szolgáltatásnak számos összetevője van, amelyek közül az egyik neve szűrő-illesztőprogram. A szűrő-illesztőprogram csak a rendszer újraindításakor töltődik be a rendszermemóriába. Ha egy mobilitási szolgáltatás frissítése magában foglalja a szűrő-illesztőprogram változásait, a rendszer frissíti a gépet, de még mindig megjelenik egy figyelmeztetés arról, hogy egyes javítások újraindítást igényelnek. A figyelmeztetés akkor jelenik meg, ha a szűrő-illesztőprogram javításai csak akkor lépnek életbe, ha az új szűrő illesztőprogramja be van töltve, ami csak újraindítás közben történik.

> [!NOTE]
> Ez csak egy figyelmeztetés. A meglévő replikálás az új ügynök frissítése után is továbbra is működni fog. Ha az új szűrő illesztőprogramjának előnyeit szeretné kipróbálni, a régi szűrő-illesztőprogram továbbra is működni fog, ha nem indítja újra.
>
> A szűrő illesztőprogramján kívül a mobilitási szolgáltatás frissítésének egyéb továbbfejlesztései és javításai is az újraindítás nélkül lépnek életbe.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>A védelem nincs engedélyezve, ha a replika felügyelt lemeze létezik

Ez a hiba akkor fordul elő, ha a replika felügyelt lemeze már létezik, a várt címkék nélkül a cél erőforráscsoporthoz.

### <a name="possible-cause"></a>Lehetséges ok

Ez a probléma akkor fordulhat elő, ha a virtuális gépet korábban védelemmel látta el, és a replikálás letiltását követően a replika lemezét nem távolította el a rendszer.

### <a name="fix-the-problem"></a>A probléma javítása

Törölje a hibaüzenetben azonosított replika lemezt, majd próbálja megismételni a sikertelen védelmi feladatot.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>A védelem engedélyezése nem sikerült, mert a telepítő nem találja a legfelső szintű lemezt (hibakód: 151137)

Ez a hiba olyan linuxos gépek esetén fordul elő, amelyekben az operációsrendszer-lemez Azure Disk Encryption (ADE) használatával titkosítva van. Ez a probléma csak az ügynök 9,35-es verziójában érvényes.

### <a name="possible-causes"></a>Lehetséges okok

A telepítő nem találja a legfelső szintű fájlrendszert futtató főlemezt.

### <a name="fix-the-problem"></a>A probléma javítása

A probléma megoldásához kövesse az alábbi lépéseket:

1. Keresse meg az ügynök BITS szolgáltatást a RHEL és a CentOS gépek _/var/lib/waagent_ a következő paranccsal: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Várt kimenet:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Hozzon létre egy új könyvtárat, és módosítsa a könyvtárat erre az új könyvtárba.
3. Bontsa ki az első lépésben található ügynök fájlját az alábbi parancs használatával:

    `tar -xf <Tar Ball File>`

4. Nyissa meg a (z) _prereq_check_installer.js_ fájlt, és törölje a következő sorokat. Ezt követően mentse a fájlt.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Hívja meg a telepítőt a parancs használatával: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Ha a telepítő sikeres, próbálkozzon újra a replikációs feladat engedélyezése lehetőséggel.

## <a name="next-steps"></a>Következő lépések

[Azure-beli virtuális gépek replikálása másik Azure-régióba](azure-to-azure-how-to-enable-replication.md)