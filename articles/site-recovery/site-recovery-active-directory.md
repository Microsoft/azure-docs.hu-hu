---
title: Active Directory/DNS-vész-helyreállítás beállítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan valósítható meg a vész-helyreállítási megoldás a Active Directory és a DNS-hez a Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132326"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Vész-helyreállítás beállítása a Active Directory és a DNS-hez

Az olyan vállalati alkalmazások, mint a SharePoint, a Dynamics AX és az SAP, a Active Directory és a DNS-infrastruktúra megfelelő működéséhez függenek. Az alkalmazások vész-helyreállításának beállításakor gyakran szükség van a Active Directory és a tartománynévrendszer (DNS) helyreállítására a többi alkalmazás-összetevő helyreállítása előtt, így biztosítva a megfelelő alkalmazások működőképességét.

A [site Recovery](site-recovery-overview.md) használatával vész-helyreállítási tervet hozhat létre a Active Directoryhoz. Megszakítás esetén feladatátvételt indíthat. Az Active Directory perceken belül működőképes lehet. Ha az elsődleges helyen több alkalmazáshoz is telepített Active Directory, például a SharePoint és az SAP esetében, akkor előfordulhat, hogy a teljes webhely feladatátvételét szeretné elvégezni. Active Directory a Site Recovery használatával végezheti el a feladatátvételt. Ezután hajtsa végre a többi alkalmazás feladatátvételét az alkalmazásspecifikus helyreállítási tervekkel.

Ez a cikk azt ismerteti, hogyan hozható létre vész-helyreállítási megoldás a Active Directory számára. Előfeltételeket és feladatátvételi utasításokat is tartalmaz. Mielőtt elkezdené, ismerkedjen meg Active Directory és Site Recoveryával.

## <a name="prerequisites"></a>Előfeltételek

- Ha az Azure-ba replikálódik, [készítse elő az Azure-erőforrásokat](tutorial-prepare-azure.md), például egy előfizetést, egy Azure-Virtual Network, egy Storage-fiókot és egy Recovery Services-tárolót.
- Tekintse át az összes összetevő [támogatási követelményeit](./vmware-physical-azure-support-matrix.md) .

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálása

- Be kell állítania Site Recovery replikációt legalább egy olyan virtuális gépen (VM), amely tartományvezérlőt vagy DNS-t üzemeltet.
- Ha több tartományvezérlővel rendelkezik a környezetben, egy további tartományvezérlőt is be kell állítania a célhelyen. A További tartományvezérlő lehet az Azure-ban vagy egy másodlagos helyszíni adatközpontban is.
- Ha csak néhány alkalmazással és egy tartományvezérlővel rendelkezik, akkor előfordulhat, hogy a teljes helyet együtt szeretné felvenni a feladatátvételre. Ebben az esetben javasoljuk, hogy az Site Recovery használatával replikálja a tartományvezérlőt a célhelyre az Azure-ban vagy egy másodlagos helyszíni adatközpontban. A [feladatátvételi teszthez](#test-failover-considerations)használhatja ugyanazt a replikált tartományvezérlőt vagy DNS virtuális gépet.
- Ha sok alkalmazást és több tartományvezérlőt használ a környezetben, vagy ha egyszerre több alkalmazás feladatátvételét tervezi, a tartományvezérlő virtuális gép Site Recovery-vel való replikálásán kívül azt javasoljuk, hogy állítson be egy további tartományvezérlőt a célhelyre (az Azure-ban vagy egy másodlagos helyszíni adatközpontban). A [feladatátvételi teszthez](#test-failover-considerations)egy site Recovery által replikált tartományvezérlőt használhat. A feladatátvételhez a további tartományvezérlőt használhatja a célhelyen.

## <a name="enable-protection-with-site-recovery"></a>Védelem engedélyezése Site Recovery

A Site Recovery használatával biztosíthatja a tartományvezérlőt vagy a DNS-t futtató virtuális gép védettségét.

### <a name="protect-the-vm"></a>A virtuális gép megóvása

A [feladatátvételi teszthez](#test-failover-considerations)a site Recovery használatával replikált tartományvezérlőt használja a rendszer. Győződjön meg arról, hogy az megfelel a következő követelményeknek:

1. A tartományvezérlő a globáliskatalógus-kiszolgáló.
1. A tartományvezérlőnek a feladatátvételi teszt során szükséges szerepkörök tulajdonosainak kell lennie a rugalmas egyedüli főkiszolgálói (FSMO) szerepkörben. Ellenkező esetben ezeket a szerepköröket a feladatátvétel után le kell [ragadni](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) .

### <a name="configure-vm-network-settings"></a>Virtuálisgép-hálózati beállítások konfigurálása

A tartományvezérlőt vagy a DNS-t futtató virtuális gép esetében a Site Recovery konfigurálja a hálózati beállításokat a replikált virtuális gép **számítási és hálózati** beállításai alatt. Ez biztosítja, hogy a virtuális gép a megfelelő hálózathoz legyen csatlakoztatva a feladatátvételt követően.

## <a name="protect-active-directory"></a>Védelem Active Directory

### <a name="site-to-site-protection"></a>Helyek közötti védelem

Hozzon létre egy tartományvezérlőt a másodlagos helyen. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg az elsődleges helyen használt tartomány nevét. A **Active Directory helyek és szolgáltatások** beépülő modullal konfigurálhatja azokat a Helykapcsolati objektum beállításait, amelyekhez a helyek hozzá lettek adva. A helykapcsolatok beállításainak konfigurálásával szabályozhatja, hogy mikor történjen a replikálás két vagy több hely között, és milyen gyakran fordul elő. További információ: a [helyek közötti replikáció ütemezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Helyek közötti védelem

Először hozzon létre egy tartományvezérlőt egy Azure-beli virtuális hálózaton. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg ugyanazt a tartománynevet, amelyet az elsődleges helyen használ.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgálóját, hogy a DNS-kiszolgálót használja az Azure-ban.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure-hálózat":::

### <a name="azure-to-azure-protection"></a>Azure – Azure védelem

Először hozzon létre egy tartományvezérlőt egy Azure-beli virtuális hálózaton. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg ugyanazt a tartománynevet, amelyet az elsődleges helyen használ.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgálóját, hogy a DNS-kiszolgálót használja az Azure-ban.

## <a name="test-failover-considerations"></a>Feladatátvételi teszttel kapcsolatos megfontolások

Az éles munkaterhelésekre gyakorolt hatás elkerülése érdekében a feladatátvételi teszt olyan hálózatban történik, amely el van különítve az üzemi hálózatról.

A legtöbb alkalmazáshoz tartományvezérlő vagy DNS-kiszolgáló jelenléte szükséges. Ezért az alkalmazás feladatátvétele előtt létre kell hoznia egy tartományvezérlőt az elkülönített hálózatban, amelyet a feladatátvételi teszthez kell használni. Ennek legegyszerűbb módja, ha a Site Recovery használatával replikálja a tartományvezérlőt vagy a DNS-t futtató virtuális gépeket. Ezután futtasson egy feladatátvételi tesztet a tartományvezérlő virtuális gépről, mielőtt futtatja az alkalmazás helyreállítási tervének feladatátvételi tesztjét.

1. A Site Recovery használatával [replikálhatja](vmware-azure-tutorial.md) a tartományvezérlőt vagy a DNS-t futtató virtuális gépet.
1. Hozzon létre egy elkülönített hálózatot. Az Azure-ban létrehozott bármely virtuális hálózat alapértelmezés szerint el van különítve más hálózatokból. Azt javasoljuk, hogy használja ugyanazt az IP-címtartományt ehhez a hálózathoz, amelyet az üzemi hálózatában használ. Ne engedélyezze a helyek közötti kapcsolatot ezen a hálózaton.
1. Adjon meg egy DNS IP-címet az elkülönített hálózatban. Használja azt az IP-címet, amelyre a DNS-virtuális gép beolvasása várható. Ha az Azure-ba végzi a replikálást, adja meg a feladatátvételhez használt virtuális gép IP-címét. Az IP-cím megadásához a replikált virtuális gép **számítási és hálózati** beállításainál válassza ki a **cél IP-** beállításokat.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure test Network":::

   > [!TIP]
   > Site Recovery a virtuális gép **számítási és hálózati** beállításaiban megadott IP-cím használatával megkísérli létrehozni a teszt virtuális gépeket egy azonos nevű alhálózatban. Ha az azonos nevű alhálózat nem érhető el a feladatátvételi teszthez megadott Azure-beli virtuális hálózaton, a teszt virtuális gép a betűrendbe szedett első alhálózatban jön létre.
   >
   > Ha a cél IP-cím a kiválasztott alhálózat része, Site Recovery megpróbálja létrehozni a feladatátvételi teszt virtuális gépet a cél IP-cím használatával. Ha a cél IP-cím nem része a kiválasztott alhálózatnak, a rendszer a kijelölt alhálózat következő elérhető IP-címének használatával hozza létre a feladatátvételi teszt virtuális gépet.

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvétel tesztelése másodlagos helyre

1. Ha egy másik helyszíni helyre végzi a replikálást, és DHCP-t használ, [állítsa be a DNS-t és a DHCP-t a feladatátvételi teszthez](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Végezzen feladatátvételi tesztet az elkülönített hálózaton futó tartományvezérlő virtuális gépről. A feladatátvételi teszt végrehajtásához használja a tartományvezérlő virtuális gép legújabb elérhető, _egységes_ helyreállítási pontját.
1. Futtasson feladatátvételi tesztet az alkalmazást futtató virtuális gépeket tartalmazó helyreállítási tervhez.
1. A tesztelés befejezésekor törölje a _feladatátvételi tesztet_ a tartományvezérlő virtuális gépén. Ez a lépés törli a feladatátvételi teszthez létrehozott tartományvezérlőt.

### <a name="remove-references-to-other-domain-controllers"></a>Más tartományvezérlőkre mutató hivatkozások eltávolítása

Ha feladatátvételi tesztet kezdeményez, ne adja meg az összes tartományvezérlőt a tesztelési hálózaton. Ha el szeretné távolítani az éles környezetben létező többi tartományvezérlőre mutató hivatkozásokat, előfordulhat, hogy le kell kérnie az [FSMO Active Directory szerepköröket](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) , és el kell végeznie a [metaadatok törlését](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) a hiányzó tartományvezérlőkön.

### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációs védelem által okozott problémák

> [!IMPORTANT]
> Az ebben a szakaszban leírt konfigurációk némelyike nem szabványos vagy Alapértelmezett tartományvezérlői konfiguráció. Ha nem szeretné, hogy ezek a módosítások egy éles tartományvezérlőn legyenek, létrehozhat egy Site Recovery dedikált tartományvezérlőt, amelyet a feladatátvételi teszthez kíván használni. Hajtsa végre ezeket a módosításokat csak a tartományvezérlőn.

A Windows Server 2012-től kezdve a [további óvintézkedések Active Directory Domain Servicesba (AD DS) épülnek](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ezek a védelmi intézkedések segítenek a virtualizált tartományvezérlők védelmében a frissítési sorszám (USN) visszaállításakor, ha az alapul szolgáló hypervisor platform támogatja a virtuális gépek **GenerationID**. Az Azure támogatja a **virtuális gépek GenerationID**. Emiatt a Windows Server 2012-es vagy újabb verzióit futtató tartományvezérlők az Azure Virtual Machines szolgáltatásban rendelkeznek ezekkel a további garanciákkal.

A **virtuális gép GenerationID** alaphelyzetbe állításakor a AD DS adatbázis **InvocationID** értékét is alaphelyzetbe állítja. Emellett a rendszer elveti a relatív azonosító (RID) készletet, és a `SYSVOL` mappa nem mérvadóként van megjelölve. További információ: [Bevezetés a Active Directory Domain Services virtualizációba](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és a [elosztott fájlrendszer replikáció biztonságos virtualizálása (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Az Azure-ba való feladatátvétel a **VM-GenerationID** alaphelyzetbe állítását okozhatja. A **VM-GenerationID** alaphelyzetbe állítása további óvintézkedéseket indít, amikor a tartományvezérlő virtuális gépe elindul az Azure-ban. Ez jelentős késleltetést eredményezhet a tartományvezérlő virtuális gépre való bejelentkezéshez.

Mivel ez a tartományvezérlő csak feladatátvételi tesztben használatos, a virtualizációs védelem nem szükséges. Annak biztosítása érdekében, hogy a tartományvezérlő virtuális gép **GenerationID** értéke ne változzon, a helyszíni tartományvezérlőn a következők értékét módosíthatja `DWORD` : **4**

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációs védelem tünetei

Ha a virtualizálási garanciákat egy feladatátvételi teszt után indítja el, a következő tünetek közül választhat:

- A **GenerationID** értéke megváltozik:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Létrehozás-azonosító módosítása":::

- A **InvocationID** értéke megváltozik:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Meghívási azonosító változása":::

- `SYSVOL` a mappa és a `NETLOGON` megosztások nem érhetők el.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="SYSVOL mappa megosztása":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="NtFrs SYSVOL mappa":::

- A rendszer törli a DFSR-adatbázisokat.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="DFSR-adatbázisok törölve":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Tartományvezérlői hibák elhárítása a feladatátvételi teszt során

> [!IMPORTANT]
> Az ebben a szakaszban leírt konfigurációk némelyike nem szabványos vagy Alapértelmezett tartományvezérlői konfiguráció. Ha nem szeretné, hogy ezek a módosítások egy éles tartományvezérlőn legyenek, létrehozhat egy Site Recovery-feladatátvételi teszthez dedikált tartományvezérlőt. A módosításokat csak erre a dedikált tartományvezérlőre végezze.

1. A parancssorban futtassa a következő parancsot annak vizsgálatához, hogy a `SYSVOL` mappa és `NETLOGON` a mappa meg van-e osztva:

    `NET SHARE`

1. A parancssorban futtassa a következő parancsot, és győződjön meg arról, hogy a tartományvezérlő megfelelően működik:

    `dcdiag /v > dcdiag.txt`

1. A kimeneti naplóban keresse meg a következő szöveget. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Ha az előző feltételek teljesülnek, valószínű, hogy a tartományvezérlő megfelelően működik. Ha nem, hajtsa végre a következő lépéseket:

1. Végezze el a tartományvezérlő mérvadó visszaállítását. Tartsa szem előtt a következő információkat:

    - Bár a fájlreplikációs [szolgáltatás (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)használatával nem javasoljuk a replikálást, ha FRS-replikációt használ, kövesse a mérvadó visszaállítás lépéseit. A folyamatot a [BurFlags beállításkulcs használatával írja le a fájlreplikációs szolgáltatás újrainicializálásához](https://support.microsoft.com/kb/290762).

      A BurFlags kapcsolatos további információkért tekintse [meg a D2-és D4-es blogbejegyzéset: mi ez?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Ha DFSR-replikációt használ, hajtsa végre a mérvadó visszaállítás lépéseit. A folyamat a [mérvadó és nem mérvadó szinkronizálás kényszerítése a DFSR replikált SYSVOL-mappához (például "D4/D2" a fájlreplikációs szolgáltatáshoz)](https://support.microsoft.com/kb/2218556).

      Használhatja a PowerShell-függvényeket is. További információ: [DFSR-SYSVOL mérvadó/nem mérvadó visszaállítás PowerShell-függvények](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. A kezdeti szinkronizálási követelmény megkerüléséhez állítsa be a következő beállításkulcsot **0** értékre a helyszíni tartományvezérlőn. Ha a `DWORD` nem létezik, akkor a **Paraméterek** csomópontban hozhatja létre.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   További információ [: a 4013-es DNS-eseményazonosító hibáinak megoldása: a DNS-kiszolgáló nem tudta betölteni az ad integrált DNS-zónákat](https://support.microsoft.com/kb/2001093).

1. Tiltsa le azt a követelményt, hogy a globáliskatalógus-kiszolgáló elérhető legyen a felhasználói bejelentkezés érvényesítéséhez. Ehhez a helyszíni tartományvezérlőn állítsa be a következő beállításkulcsot **1** értékre. Ha a `DWORD` nem létezik, akkor az **LSA** csomópontban hozhatja létre.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   További információt [a globális katalógus működéséről](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))szóló témakörben talál.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS-és tartományvezérlők különböző gépeken

Ha a tartományvezérlőt és a DNs-t ugyanazon a virtuális gépen futtatja, akkor kihagyhatja ezt az eljárást.

Ha a DNS nem ugyanazon a virtuális gépen található, mint a tartományvezérlő, létre kell hoznia egy DNS virtuális gépet a feladatátvételi teszthez. Használhatja a friss DNS-kiszolgálót, és létrehozhatja az összes szükséges zónát. Ha például a Active Directory tartománya `contoso.com` , létrehozhat egy nevű DNS-zónát `contoso.com` . A Active Directorynak megfelelő bejegyzéseket a következőképpen kell frissíteni a DNS-ben:

1. Győződjön meg arról, hogy ezek a beállítások érvényben vannak, mielőtt a helyreállítási tervben szereplő bármely más virtuális gép elindul:

   - A zónát az erdő gyökerének neve után kell elnevezni.
   - A zónának fájlból kell állnia.
   - A zónának engedélyezve kell lennie a biztonságos és nem biztonságos frissítésekhez.
   - A tartományvezérlőt üzemeltető virtuális gép feloldójának a DNS virtuális gép IP-címére kell mutatnia.

1. Futtassa a következő parancsot a tartományvezérlőt üzemeltető virtuális gépen:

   `nltest /dsregdns`

1. Futtassa a következő parancsokat egy zóna DNS-kiszolgálón való hozzáadásához, engedélyezze a nem biztonságos frissítéseket, és adjon hozzá egy bejegyzést a zónához a DNS-ben:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Következő lépések

[További](site-recovery-workload.md) információ a vállalati munkaterhelések Azure site Recoverysal való védelméről.
