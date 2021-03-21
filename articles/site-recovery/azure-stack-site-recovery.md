---
title: Azure Stack virtuális gépek replikálása az Azure-ba Azure Site Recovery használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a vész-helyreállítást az Azure-ba Azure Stack virtuális gépekre a Azure Site Recovery szolgáltatással.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 36e11bfe5354644f9ef6603ffe20cb2e86074323
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016904"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Azure Stack rendszerű virtuális gépek replikálása az Azure-ba

Ez a cikk bemutatja, hogyan állíthatja be a vész-helyreállítási Azure Stack virtuális gépeket az Azure-ba a [Azure site Recovery szolgáltatás](site-recovery-overview.md)használatával.

Site Recovery az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához járul hozzá. A szolgáltatás biztosítja, hogy a virtuális gépek számítási feladatai a vártnál továbbra is rendelkezésre álljanak, és váratlan kimaradások történnek.

- Site Recovery összehangolja és kezeli a virtuális gépek replikálását az Azure Storage-ba.
- Ha áramkimaradás fordul elő az elsődleges helyen, akkor Site Recovery az Azure-ba történő feladatátvételhez.
- A feladatátvétel során az Azure-beli virtuális gépek a tárolt virtuálisgép-adatokból jönnek létre, és a felhasználók továbbra is hozzáférhetnek az Azure-beli virtuális gépeken futó munkaterhelésekhez.
- Ha minden újra működik, az Azure-beli virtuális gépeket visszaállíthatja az elsődleges helyre, és megkezdheti az Azure Storage-ba való replikálást.


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * **1. lépés: az Azure stack virtuális gépek előkészítése a replikáláshoz**. Győződjön meg arról, hogy a virtuális gépek megfelelnek Site Recovery követelményeinek, és előkészíti a Site Recovery mobilitási szolgáltatás telepítését. Ez a szolgáltatás minden replikálni kívánt virtuális gépen telepítve van.
> * **2. lépés: Recovery Services**-tároló beállítása. Állítson be egy tárolót a Site Recoveryhoz, és adja meg, hogy mit szeretne replikálni. Site Recovery összetevőket és műveleteket a tárolóban kell konfigurálni és felügyelni.
> * **3. lépés: a forrás-replikációs környezet beállítása**. Site Recovery konfigurációs kiszolgáló beállítása. A konfigurációs kiszolgáló egyetlen Azure Stack virtuális gép, amely a Site Recovery által igényelt összes összetevőt futtatja. A konfigurációs kiszolgáló beállítása után regisztrálja a tárolóban.
> * **4. lépés: a cél replikációs környezet beállítása**. Válassza ki az Azure-fiókját és a használni kívánt Azure Storage-fiókot és-hálózatot. A replikáció során a rendszer átmásolja a virtuális gépeket az Azure Storage szolgáltatásba. A feladatátvételt követően az Azure-beli virtuális gépek csatlakoznak a megadott hálózathoz.
> * **5. lépés: a replikáció engedélyezése**. Konfigurálja a replikációs beállításokat, és engedélyezze a replikálást a virtuális gépek számára. A mobilitási szolgáltatás telepítése egy virtuális gépre történik, ha a replikáció engedélyezve van. Site Recovery végrehajtja a virtuális gép kezdeti replikálását, majd megkezdi a folyamatban lévő replikációt.
> * **6. lépés: vész-helyreállítási részletezés futtatása**: miután a replikálás már fut, ellenőrizze, hogy a feladatátvétel a várt módon fog-e működni a részletezés futtatásával. A részletezés elindításához futtasson egy feladatátvételi tesztet Site Recovery. A feladatátvételi teszt nem érinti az éles környezetet.

Ezekkel a lépésekkel teljes körű feladatátvételt futtathat az Azure-ban, és amikor szükséges.

## <a name="architecture"></a>Architektúra

![A diagram Recovery Services tárolókat jeleníti meg két bérlőhöz, amelyek a bérlői előfizetésekhez kapcsolódnak a közös Azure Stack infrastruktúrában.](./media/azure-stack-site-recovery/architecture.png)

**Hely** | **Összetevő** |**Részletek**
--- | --- | ---
**Konfigurációs kiszolgáló** | Egyetlen Azure Stack virtuális gépen fut. | Az egyes előfizetésekben beállíthatja a konfigurációs kiszolgáló virtuális gépet. Ez a virtuális gép a következő Site Recovery összetevőket futtatja:<br/><br/> -Konfigurációs kiszolgáló: koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt. -Process Server: replikációs átjáróként működik. Replikációs adatfogadást fogad, optimalizálja a gyorsítótárazást, a tömörítést és a titkosítást; és elküldi az Azure Storage-nak.<br/><br/> Ha a replikálni kívánt virtuális gépek túllépik az alább megadott korlátokat, beállíthat egy külön önálló folyamat-kiszolgálót. [További információ](vmware-azure-set-up-process-server-scale.md).
**Mobilitási szolgáltatás** | Minden olyan virtuális gépen telepítve van, amelyet replikálni szeretne. | A cikk lépéseiben előkészítünk egy fiókot, hogy a mobilitási szolgáltatás automatikusan telepítve legyen egy virtuális gépen, ha a replikáció engedélyezve van. Ha nem szeretné automatikusan telepíteni a szolgáltatást, számos más módszer is használható. [További információ](vmware-azure-install-mobility-service.md).
**Azure** | Az Azure-ban szüksége van egy Recovery Services-tárolóra, egy Storage-fiókra és egy virtuális hálózatra. |  A replikált fájlokat a rendszer a Storage-fiókban tárolja. Feladatátvétel esetén az Azure-beli virtuális gépek hozzáadódnak az Azure-hálózathoz.


A replikáció a következőképpen működik:

1. A tárolóban adja meg a replikálás forrását és célját, állítsa be a konfigurációs kiszolgálót, hozzon létre egy replikációs házirendet, és engedélyezze a replikálást.
2. A mobilitási szolgáltatás telepítve van a gépen (ha leküldéses telepítést használt), és a gépek a replikációs házirendnek megfelelően megkezdik a replikálást.
3. A kiszolgáló adatainak kezdeti másolatát az Azure Storage-ba replikálja a rendszer.
4. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. A konfigurációs kiszolgáló a replikálást az Azure-ban (HTTPS 443 kimenő port) hangolja össze.
6. A Process Server adatokat fogad a forrásoldali gépekről, optimalizálja és titkosítja, majd elküldi az Azure Storage-nak (443-es port).
7. A replikált gépek kommunikálnak a konfigurációs kiszolgálóval (HTTPS 443 bejövő port, replikálási felügyelet céljából. A gépek replikációs adatküldést küldenek a folyamat kiszolgálójára (a HTTPS 9443 bejövő portja módosítható).
8. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute nyilvános társviszony-létesítési szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

## <a name="prerequisites"></a>Előfeltételek

Ezt a forgatókönyvet kell beállítania.

**Követelmény** | **Részletek**
--- | ---
**Azure-előfizetési fiók** | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
**Azure-fiók engedélyei** | Az Ön által használt Azure-fióknak a következő engedélyekkel kell rendelkeznie:<br/><br/> -Recovery Service-tároló létrehozása<br/><br/> -Hozzon létre egy virtuális gépet a forgatókönyvhöz használt erőforráscsoport és virtuális hálózat között.<br/><br/> – Írás a megadott Storage-fiókba<br/><br/> Vegye figyelembe:<br/><br/> -Fiók létrehozásakor Ön az előfizetés rendszergazdája, és az összes művelet elvégezhető.<br/><br/> – Ha meglévő előfizetést használ, és Ön nem a rendszergazda, akkor a rendszergazdával kell dolgoznia a tulajdonosi vagy közreműködői engedélyek hozzárendeléséhez.<br/><br/> – Ha részletesebb engedélyekre van szüksége, tekintse át [ezt a cikket](site-recovery-role-based-linked-access-control.md).
**Azure Stack virtuális gép** | Szüksége van egy Azure Stack virtuális gépre a bérlői előfizetésben, amely Site Recovery konfigurációs kiszolgálóként lesz telepítve.


### <a name="prerequisites-for-the-configuration-server"></a>A konfigurációs kiszolgáló előfeltételei

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>1. lépés: Azure Stack virtuális gépek előkészítése

### <a name="verify-the-operating-system"></a>Az operációs rendszer ellenőrzése

Győződjön meg arról, hogy a virtuális gépek a táblázatban összegzett operációs rendszerek egyikét futtatják.


**Operációs rendszer** | **Részletek**
--- | ---
**64 bites Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (SP1)
**CentOS** | 5,2 – 5,11, 6,1, 6,9, 7,0 és 7,3
**Ubuntu** | 14,04 LTS-kiszolgáló, 16,04 LTS-kiszolgáló. [Támogatott kernelek](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions) áttekintése

### <a name="prepare-for-mobility-service-installation"></a>Felkészülés a mobilitási szolgáltatás telepítésére

Minden replikálni kívánt virtuális gépnek telepítve kell lennie a mobilitási szolgáltatásnak. Ahhoz, hogy a Process Server automatikusan telepítse a szolgáltatást a virtuális gépen, amikor a replikáció engedélyezve van, ellenőrizze a virtuális gép beállításait.

#### <a name="windows-machines"></a>Windows rendszerű gépek

- Hálózati kapcsolatra van szüksége a virtuális gép között, amelyen engedélyezni szeretné a replikálást, és a Process Servert futtató számítógép (alapértelmezés szerint ez a konfigurációs kiszolgáló virtuális gép).
- Olyan fiókra van szüksége, amely rendszergazdai jogosultságokkal rendelkezik (tartományi vagy helyi) azon a gépen, amelyhez engedélyezi a replikációt.
    - Ezt a fiókot a Site Recovery beállításakor kell megadnia. Ezt követően a folyamat-kiszolgáló ezt a fiókot használja a mobilitási szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot csak Site Recovery fogja használni a leküldéses telepítéshez és a mobilitási szolgáltatás frissítéséhez.
    - Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a virtuális gépen:
        - A beállításjegyzékben hozzon létre DWORD Value **LocalAccountTokenFilterPolicy** HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System alatt.
        - Az értéket állítsa be 1-re.
        - Ehhez írja be a következőt a parancssorba: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- A replikálni kívánt virtuális gépen a Windows tűzfalon engedélyezze a fájl-és nyomtatómegosztás, valamint a WMI használatát.
    - Ehhez futtassa a **WF. msc fájlt** a Windows tűzfal konzoljának megnyitásához. Kattintson a jobb gombbal a **Bejövő szabályok**  >  **új szabály** elemre. Válassza az **előre definiált** lehetőséget, majd válassza a **fájl-és nyomtatómegosztás** elemet a listából. Fejezze be a varázslót, és jelölje be a kapcsolódás > **befejezésének** engedélyezése jelölőnégyzetet.
    - Tartományi számítógépek esetén ezt a csoportházirend-objektum használatával végezheti el.


#### <a name="linux-machines"></a>Linux rendszerű gépek

- Győződjön meg arról, hogy van hálózati kapcsolat a Linux-számítógép és a folyamat-kiszolgáló között.
- Azon a gépen, amelyre engedélyezi a replikációt, szüksége van egy fiókra, amely a forrás Linux-kiszolgáló legfelső szintű felhasználója:
    - Ezt a fiókot a Site Recovery beállításakor kell megadnia. Ezt követően a folyamat-kiszolgáló ezt a fiókot használja a mobilitási szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot csak Site Recovery fogja használni a leküldéses telepítéshez és a mobilitási szolgáltatás frissítéséhez.
- Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
- Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
- Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
- Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
    1. Ehhez jelentkezzen be root-ként.
    2. Keresse meg a **PasswordAuthentication** kezdetű sort a/etc/ssh/sshd_config fájlban. Állítsa vissza a sort, és módosítsa az értéket **yes** értékre.
    3. Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

        ![Linuxos mobilitási szolgáltatás](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Indítsa újra az sshd szolgáltatást.


### <a name="note-the-vm-private-ip-address"></a>Jegyezze fel a virtuális gép magánhálózati IP-címét

Keresse meg az IP-címet az összes replikálni kívánt gépen:

1. A Azure Stack-portálon kattintson a virtuális gépre.
2. Az **erőforrás** menüben kattintson a **hálózati adapterek** elemre.
3. Jegyezze fel a magánhálózati IP-címet.

    ![Magánhálózati IP-cím](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2. lépés: tár létrehozása és a replikációs cél kiválasztása

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **felügyeleti eszközök**  >  **biztonsági mentése és site Recovery** lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet.
3. Az **erőforráscsoport** területen hozzon létre vagy válasszon ki egy erőforráscsoportot. **ContosoRG** használunk.
4. A **hely** mezőben adja meg az Azure-régiót. a **Nyugat-Európa** beállítást használjuk.
5. Ha gyorsan el szeretné érni a tárolót az irányítópultról, válassza a **rögzítés az irányítópulton**  >  **Létrehozás** lehetőséget.

   ![Új tároló létrehozása](./media/azure-stack-site-recovery/new-vault-settings.png)

   Az új tároló megjelenik az **irányítópult**  >  **minden erőforrás** és a fő **Recovery Services** -tárolók lapon.

### <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. **Recovery Services** -tárolóban > adja meg a tár nevét. **ContosoVMVault** használunk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **védelmi cél**  >  ,**ahol a gépek találhatók**, válassza **a** helyszíni lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A-ben **a gépek virtualizáltak**, válassza a **nem virtualizált/egyéb** lehetőséget. Ez után válassza az **OK** gombot.

    ![Védelmi cél](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3. lépés: a forrás-környezet beállítása

Állítsa be a konfigurációs kiszolgáló számítógépét, regisztrálja a tárolóban, és fedezze fel a replikálni kívánt gépeket.

1. Kattintson az **infrastruktúra előkészítése**  >  **forrás** elemre.
2. A **Forrás előkészítése** ablakban kattintson a **+Konfigurációs kiszolgáló** gombra.

    ![Képernyőfelvétel a + konfigurációs kiszolgáló párbeszédpanelről "kattintson a fenti parancssáv + konfigurációs kiszolgáló elemére egy..." gombra.](./media/azure-stack-site-recovery/plus-config-srv.png)

3. A **kiszolgáló hozzáadása** területen győződjön meg arról, hogy a **konfigurációs kiszolgáló** megjelenik a **kiszolgáló típusa mezőben**.
5. Töltse le a Site Recovery egyesített telepítési telepítőfájlt.
6. Töltse le a tároló regisztrációs kulcsát. Az egyesített telepítő futtatásakor szüksége lesz a regisztrációs kulcsra. A kulcs a generálásától számított öt napig érvényes.

    ![Képernyőkép: a kiszolgáló hozzáadása párbeszédpanel, amely a kiszolgáló típusa beállítással a konfigurációs kiszolgálóra van beállítva, és a tároló regisztrációs kulcsának letöltése gomb kiemelve.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery egyesített telepítő futtatása

A konfigurációs kiszolgáló telepítéséhez és regisztrálásához végezzen RDP-kapcsolattal a konfigurációs kiszolgálóhoz használni kívánt virtuális gépet, és futtassa az egyesített telepítőt.

Mielőtt elkezdené, győződjön meg róla, hogy az óra [szinkronizálva van egy időkiszolgálóval](/windows-server/networking/windows-time-service/windows-time-service-top) a virtuális gépen a Kezdés előtt. A telepítés sikertelen lesz, ha az idő több mint öt percet vesz igénybe a helyi idő alatt.

Most telepítse a konfigurációs kiszolgálót:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló a parancssorból is telepíthető. [További információ](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **konfigurációs kiszolgálók**  >  **_kiszolgáló neve_*_ > _* frissítési kiszolgáló** elemet.

## <a name="step-4-set-up-the-target-environment"></a>4. lépés: a célként megadott környezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Az **infrastruktúra előkészítése**  >  **cél** területen válassza ki a használni kívánt Azure-előfizetést.
2. A cél telepítési modell meghatározása.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ha nem találja őket, létre kell hoznia legalább egy Storage-fiókot és egy virtuális hálózatot a varázsló befejezéséhez.


## <a name="step-5-enable-replication"></a>5. lépés: A replikáció engedélyezése

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Kattintson az **infrastruktúra előkészítése**  >  **replikációs beállítások** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO).
    - A replikált adathelyreállítási pontok a beállított időponttal összhangban jönnek létre.
    - Ez a beállítás nincs hatással a replikációra, ami folyamatosan működik. Csak akkor ad ki riasztást, ha a küszöbértéket a rendszer nem létrehozta helyreállítási pont nélkül éri el.
4. A **helyreállítási pont megőrzése** területen határozza meg, hogy az egyes helyreállítási pontok meddig legyenek tárolva. A replikált virtuális gépek a megadott időtartomány bármely pontjára visszaállíthatók.
5. Az alkalmazással **konzisztens Pillanatképek gyakorisága** mezőben határozza meg, hogy milyen gyakran hozza létre az alkalmazások konzisztens pillanatképeit.

    - Az alkalmazás-konzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időponthoz tartozó pillanatképe.
    - Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gépen lévő alkalmazások konzisztens állapotban legyenek a pillanatkép készítésekor.
6. A szabályzat létrehozásához válassza az **OK** gombot.


### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

Most kihagyhatja ezt a lépést. A **központi telepítés tervezése** legördülő listában kattintson az **Igen, elvégeztem**.



### <a name="enable-replication"></a>A replikáció engedélyezése

Győződjön meg arról, hogy végrehajtotta az 1. [lépés: a számítógép előkészítése című témakörben](#step-1-prepare-azure-stack-vms)foglalt összes feladatot. Ezután engedélyezze a replikálást a következőképpen:

1. Válassza az **alkalmazás**  >  **forrásának** replikálása lehetőséget.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa** területen válassza a **fizikai gépek** lehetőséget.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.
5. A **cél** mezőben válassza ki azt az előfizetést és erőforráscsoportot, amelyben a virtuális gépeket a feladatátvételt követően létre szeretné hozni. Válassza ki a feladatátvételi virtuális gépekhez használni kívánt telepítési modellt.
6. Válassza ki azt az Azure Storage-fiókot, amelyben tárolni kívánja a replikált fájlokat.
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Válassza a **beállítás most a kijelölt gépekhez** lehetőséget, hogy a hálózati beállítást a védelemre kiválasztott összes gépre alkalmazza. Válassza a **Konfigurálás később** lehetőséget, ha az Azure-hálózatot külön szeretné kiválasztani az egyes gépekhez.
9. A **fizikai gépek** területen kattintson a **+ fizikai gép** elemre. Adja meg a replikálni kívánt gépek nevét, IP-címét és operációsrendszer-típusát.

    - Használja a gép belső IP-címét.
    - Ha a nyilvános IP-címet adta meg, előfordulhat, hogy a replikáció nem a várt módon működik.

10. A **Tulajdonságok**  >  **konfigurálása tulajdonságok** területen válassza ki azt a fiókot, amellyel a Process Server automatikusan telepíti a mobilitási szolgáltatást a gépen.
11. A replikációs **Beállítások**  >  **replikációs beállítások konfigurálása** területen győződjön meg arról, hogy a megfelelő replikációs házirend van kiválasztva.
12. Kattintson a **replikáció engedélyezése** lehetőségre.
13. A **védelem engedélyezése** feladat előrehaladásának nyomon követése a **Beállítások**  >  **feladatok**  >  **site Recovery feladatok** részen. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

> [!NOTE]
> Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
>
> 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
>
> A hozzáadott virtuális gépek figyeléséhez tekintse meg a virtuális gépek legutóbbi felderített idejét a **konfigurációs kiszolgálók**  >  **utolsó kapcsolatfelvétele** című résznél. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6. lépés: vész-helyreállítási részletezés futtatása

Futtasson egy feladatátvételi tesztet az Azure-ba, és ellenőrizze, hogy minden a várt módon működik-e. Ez a feladatátvétel nem befolyásolja az éles környezetet.

### <a name="verify-machine-properties"></a>Számítógép tulajdonságainak ellenőrzése

A feladatátvételi teszt futtatása előtt ellenőrizze a gép tulajdonságait, és győződjön meg arról, hogy azok megfelelnek az [Azure követelményeinek](vmware-physical-azure-support-matrix.md#azure-vm-requirements). A tulajdonságokat az alábbiak szerint tekintheti meg és módosíthatja:

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat** területen módosítsa a beállításokat igény szerint.

    - Módosíthatja az Azure-beli virtuális gép nevét, az erőforráscsoportot, a célként megadott méretet, a [rendelkezésre állási](../virtual-machines/windows/tutorial-availability-sets.md)csoportot és a felügyelt lemez beállításait.
    - Megtekintheti és módosíthatja a hálózati beállításokat is. Ezek közé tartozik a hálózat/alhálózat, amelyhez az Azure-beli virtuális gép csatlakoztatva van a feladatátvétel után, valamint a virtuális géphez hozzárendelt IP-cím.
1. A **lemezek** területen tekintse meg a virtuális gép operációs rendszerével és adatlemezével kapcsolatos információkat.


### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel a megadott helyreállítási pont használatával dolgozza fel az adatfeldolgozást:
    - **Legutóbb feldolgozott**: a gép feladatátvételt hajt végre a site Recovery által feldolgozott legutóbbi helyreállítási pontra. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legújabb alkalmazás – konzisztens**: a számítógép feladatátvételt hajt végre a legújabb, alkalmazás-konzisztens helyreállítási ponttal.
    - **Egyéni**: válassza ki a feladatátvételhez használt helyreállítási pontot.

3. Az Azure-beli virtuális gépek a feldolgozott adatkezelési szolgáltatással hozhatók létre.
4. A feladatátvételi teszt képes automatikusan törölni a részletezés során létrehozott Azure-beli virtuális gépeket.

Futtasson egy feladatátvételi tesztet a virtuális gépen a következőképpen:

1. A **Beállítások**  >  **replikált elemek** területen kattintson a virtuális gép > **+ feladatátvételi teszt** elemre.
2. Ebben az útmutatóban a **legújabb feldolgozott** helyreállítási pontot fogjuk használni.
3. A **feladatátvételi teszt** területen válassza ki a cél Azure-hálózatot.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra.
5. A folyamat nyomon követéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait. Vagy kattintson a **feladatátvételi teszt** feladat a tár *neve*  >  **Beállítások**  >  **feladatok**  > **site Recovery feladatok** lehetőségre.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép a megfelelő méretű, a megfelelő hálózathoz csatlakozik, és fut.
7. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban. [További információ](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **jegyzetek** területen mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket.

## <a name="fail-over-and-fail-back"></a>Feladatátadás és feladatátvétel

Miután beállította a replikálást, és lefuttatott egy részletezést, hogy minden megfelelően működik, a gépeket szükség szerint feladatátvételt hajthat végre az Azure-ban.

A feladatátvétel futtatása előtt, ha a feladatátvétel után csatlakozni szeretne a géphez az Azure-ban, a Kezdés előtt [elő kell készítenie a csatlakozást](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) .

Ezután futtassa a feladatátvételt az alábbiak szerint:


1. A **Beállítások**  >  **replikált elemek** területen kattintson a gép > **feladatátvétel** elemre.
2. Válassza ki a használni kívánt helyreállítási pontot.
3. A **feladatátvételi teszt** területen válassza ki a cél Azure-hálózatot.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. Ezzel a beállítással Site Recovery a feladatátvétel elindítása előtt megkísérli leállítani a forrás gépet. A feladatátvétel azonban akkor is folytatódik, ha a Leállítás sikertelen.
5. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A feladatátvételi folyamat a **feladatok** lapon követhető.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ha a feladatátvételt követően előkészítette a csatlakozást, győződjön meg arról, hogy a virtuális gép a megfelelő méretű, a megfelelő hálózathoz csatlakozik, és fut.
7. A virtuális gép ellenőrzése után kattintson a **véglegesítés** gombra a feladatátvétel befejezéséhez. Ezzel törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.


### <a name="fail-back-to-azure-stack"></a>Feladat-visszavétel Azure Stack

Ha az elsődleges hely újra működik, az Azure-ból Azure Stackra is visszatérhet. Ehhez kövesse az [itt](/azure-stack/operator/site-recovery-failback?view=azs-2005)ismertetett lépéseket.

## <a name="conclusion"></a>Összegzés

Ebben a cikkben Azure Stack virtuális gépeket replikált az Azure-ba. A replikáció a helyén egy vész-helyreállítási részletezést futtatott, amely gondoskodik arról, hogy az Azure-beli feladatátvétel az elvárásoknak megfelelően működik. A cikk a teljes feladatátvétel Azure-ba való futtatásának lépéseit is tartalmazza, és visszakerül a Azure Stackra.

## <a name="next-steps"></a>Következő lépések

A feladat-visszavételt követően újra védetté teheti a virtuális gépet, és megkezdheti az Azure-ba való replikálást. ehhez ismételje meg a jelen cikkben ismertetett lépéseket.