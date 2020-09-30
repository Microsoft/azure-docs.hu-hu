---
title: Azure VMware-megoldás virtuális gépek biztonsági mentése Azure Backup Server
description: Konfigurálja az Azure VMware-megoldási környezetét a virtuális gépek biztonsági mentésére Azure Backup Server használatával.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: a62bccb729cfa6aec89a3ce6de7283f5d9412428
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580313"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Azure VMware-megoldás virtuális gépek biztonsági mentése Azure Backup Server

Ebben a cikkben áttekintjük a VMWare virtuális gépek (VM-EK) Azure VMware-megoldáson futó biztonsági mentésének eljárásait Azure Backup Server használatával. Mielőtt elkezdené, győződjön meg róla, hogy alaposan átugorja [Microsoft Azure Backup-kiszolgáló beállítása az Azure VMware megoldáshoz](set-up-backup-server-for-azure-vmware-solution.md).

Ezután az összes szükséges eljárást bemutatjuk:

> [!div class="checklist"] 
> * Hozzon létre egy biztonságos csatornát, hogy Azure Backup Server képes legyen kommunikálni a VMware-kiszolgálókkal HTTPS-kapcsolaton keresztül. 
> * Adja hozzá a fiók hitelesítő adatait Azure Backup Serverhoz. 
> * Adja hozzá a vCenter a Azure Backup Serverhoz. 
> * Állítson be egy védelmi csoportot, amely tartalmazza azokat a VMware virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni, adja meg a biztonsági mentési beállításokat, és ütemezze a biztonsági mentést.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Biztonságos kapcsolatok létrehozása a vCenter-kiszolgálóval

Alapértelmezés szerint a Azure Backup Server HTTPS-kapcsolaton keresztül kommunikál a VMware-kiszolgálókkal. A HTTPS-kapcsolat beállításához töltse le a VMware hitelesítésszolgáltató (CA) tanúsítványát, és importálja azt a Azure Backup Server.

### <a name="set-up-the-certificate"></a>A tanúsítvány beállítása

1. A böngészőben a Azure Backup Server gépen adja meg a vSphere webes ügyfél URL-címét.

   > [!NOTE] 
   > Ha a VMware **első lépések** lap nem jelenik meg, ellenőrizze a kapcsolatok és a böngésző proxybeállításait, és próbálkozzon újra.

1. A VMware **első lépések** lapon válassza a **megbízható legfelső szintű hitelesítésszolgáltatói tanúsítványok letöltése**lehetőséget.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere webes ügyfél":::

1. Mentse a **download.zip** fájlt a Azure Backup Server gépre, majd bontsa ki a tartalmát a **tanúsítványok** mappába, amely a következőket tartalmazza:

   - A főtanúsítvány fájlja egy olyan bővítménysel kezdődik, amely egy számú, például. 0 és. 1 sorszámú.
   - CRL-fájl egy olyan bővítménnyel, amely egy. R0 vagy. R1 kiterjesztéssel kezdődik.

1. A **tanúsítványok** mappában kattintson a jobb gombbal a főtanúsítvány fájlra, majd válassza az **Átnevezés** lehetőséget, hogy a bővítményt **. CRT**-re módosítsa.

   A fájl ikon egy főtanúsítványt jelképező egyikre változik.

1. Kattintson a jobb gombbal a főtanúsítványra, majd válassza a **tanúsítvány telepítése**lehetőséget.

1. A **tanúsítvány importálása varázslóban**válassza a **helyi gép** lehetőséget a tanúsítvány célhelye, majd kattintson a **Tovább gombra**.

   ![Varázsló kezdőlapja](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Ha a rendszer kéri, erősítse meg, hogy engedélyezni szeretné a számítógép módosításait.

1. Válassza a **minden tanúsítvány tárolása a következő tárolóban**lehetőséget, majd válassza a **Tallózás** lehetőséget a tanúsítványtároló kiválasztásához.

   ![Tanúsítvány tárolása](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Válassza ki a **megbízható legfelső szintű hitelesítésszolgáltatók** mappát, majd kattintson az **OK gombra**.

1. Tekintse át a beállításokat, és válassza a **Befejezés** lehetőséget a tanúsítvány importálásának megkezdéséhez.

   ![A tanúsítvány ellenőrzése a megfelelő mappában van](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. A tanúsítvány importálásának megerősítése után jelentkezzen be a vCenter-kiszolgálóra, és erősítse meg, hogy a kapcsolódás biztonságos.

### <a name="enable-tls-12-on-azure-backup-server"></a>A TLS 1,2 engedélyezése Azure Backup Server

A VMware 6,7-es verziójában a TLS engedélyezve volt a kommunikációs protokollként. 

1. Másolja a következő beállításjegyzék-beállításokat, és illessze be őket a Jegyzettömbbe. Ezután mentse a fájlt TLS-ként. REG nélkül. txt kiterjesztéssel.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Kattintson a jobb gombbal a TLS elemre. REG-fájlt, majd válassza az **Egyesítés** vagy a **Megnyitás** lehetőséget a beállítások beállításjegyzékhez való hozzáadásához.

## <a name="add-the-provisioning-ip-address"></a>A kiépítési IP-cím hozzáadása 

Az előzetes verzióban az Azure VMware megoldás nem oldja meg az ESX-gazdagépet a virtuális hálózaton üzembe helyezett virtuális gépről. További lépéseket kell végrehajtania a gazda fájl bejegyzésének a Azure Backup Server virtuális gépen való hozzáadásához.

### <a name="identify-the-ip-address-for-esxi-hosts"></a>Az ESXi-gazdagépek IP-címének azonosítása

1. Nyissa meg a böngészőt, és jelentkezzen be a vCenter URL-címekre. 

   > [!TIP]
   > Az URL-címeket a [Kapcsolódás a saját felhő helyi vCenter](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud)találja.

1. Válassza ki azt a fürtöt, amely számára engedélyezni szeretné a biztonsági mentést a vSphere-ügyfélen.

   :::image type="content" source="media/azure-vmware-solution-backup/vsphere-client-select-host.png" alt-text="vSphere webes ügyfél":::

1. Válassza **Configure**a  >  **hálózatkezelés**  >  **VMKernel-adapterek**konfigurálása lehetőséget. Az eszközök listájában keresse meg azt a hálózati adaptert, amelyen engedélyezve van az **üzembe** helyezési szerepkör. Jegyezze fel az **IP-címet** és az ESXi-gazdagépet.

   :::image type="content" source="media/azure-vmware-solution-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="vSphere webes ügyfél":::

1. Ismételje meg az előző lépést minden olyan fürthöz tartozó ESXi-állomásnál, amely számára engedélyezni szeretné a biztonsági mentést.

### <a name="update-the-host-file-on-azure-backup-server"></a>A gazdagép fájljának frissítése Azure Backup Server

1. Nyissa meg a Jegyzettömböt rendszergazdaként.

1. Válassza **File**ki  >  a**megnyitott**fájlt, és keressen rá a c:\Windows\System32\Drivers\etc\hosts. kifejezésre.

1. Adja hozzá a bejegyzést az egyes ESXi-gazdagépekhez az előző szakaszban azonosított IP-címmel együtt.

1. Mentse a módosításokat, és zárjuk be a jegyzettömböt.

## <a name="add-the-account-on-azure-backup-server"></a>Fiók hozzáadása Azure Backup Server

1. Nyissa meg a Azure Backup Servert, és a Azure Backup Server konzolon válassza a **felügyeleti**  >  **üzemi kiszolgálók**  >  **VMware kezelése**lehetőséget.

   ![Azure Backup Server konzol](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. A **hitelesítő adatok kezelése** párbeszédpanelen válassza a **Hozzáadás**lehetőséget.

   ![A hitelesítő adatok kezelése párbeszédpanelen válassza a Hozzáadás lehetőséget.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. A **hitelesítő adatok hozzáadása** párbeszédpanelen adja meg az új hitelesítő adat nevét és leírását. Adja meg a VMware-kiszolgálón megadott felhasználónevet és jelszót.

   > [!NOTE] 
   > Ha a VMware-kiszolgáló és a Azure Backup Server nem ugyanabban a tartományban van, adja meg a tartományt a **Felhasználónév** mezőben.

   ![Azure Backup Server hitelesítő adat hozzáadása párbeszédpanel](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Az új hitelesítő adat hozzáadásához válassza a **Hozzáadás** lehetőséget.

   ![Képernyőfelvétel: a hitelesítő adatok kezelése párbeszédpanel Azure Backup Server új hitelesítő adatokkal jelenik meg.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>A vCenter-kiszolgáló hozzáadása a Azure Backup Server

1. A Azure Backup Server-konzolon válassza a **felügyelet**  >  **üzemi kiszolgálók**  >  **Hozzáadás**elemet.

   ![Az üzemi kiszolgáló hozzáadása varázsló megnyitása](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Válassza ki a **VMware-kiszolgálók**elemet, majd kattintson a **Tovább gombra**.

   ![Üzemi kiszolgáló hozzáadása varázsló](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. A vCenter IP-címének megadásához.

   ![VMware-kiszolgáló meghatározása](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Az **SSL-port** mezőbe írja be a vCenter való kommunikációhoz használt portot.

   > [!TIP] 
   > Az alapértelmezett port a 443-as port, de ha a vCenter egy másik portot figyel, akkor megváltoztathatja azt.

1. A **hitelesítő adatok megadása** mezőben válassza ki az előző szakaszban létrehozott hitelesítő adatokat.

1. A **Hozzáadás** gombra kattintva vegye fel a vCenter a kiszolgálók listára, és válassza a **tovább**lehetőséget.

   ![VMware-kiszolgáló és hitelesítő adat hozzáadása](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Az **Összefoglalás** lapon válassza a **Hozzáadás** lehetőséget a vCenter Azure Backup Serverhoz való hozzáadásához.

   Az új kiszolgáló azonnal hozzá lesz adva. a vCenter nem igényel ügynökre.

   ![VMware-kiszolgáló hozzáadása a Azure Backup Serverhoz](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. A **Befejezés** lapon tekintse át a beállításokat, majd kattintson a **Bezárás**gombra.

   ![Befejezés lap](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Az **üzemi kiszolgáló** területen látható vCenter-kiszolgálót a **VMware-kiszolgálóként** **és az** **ügynök állapotaként** kell megtekinteni. Ha az **ügynök állapota** **ismeretlenként**jelenik meg, válassza a **frissítés**lehetőséget.

## <a name="configure-a-protection-group"></a>Védelmi csoport konfigurálása

A védelmi csoportok több virtuális gépet gyűjtenek, és ugyanazokat az adatmegőrzési és biztonsági mentési beállításokat alkalmazzák a csoport összes virtuális gépére.

1. A Azure Backup Server-konzolon válassza a **védelem**  >  **új**lehetőséget.

   ![Az új védelmi csoport létrehozása varázsló megnyitása](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Az **új védelmi csoport létrehozása** varázsló kezdőlapján kattintson a **Tovább gombra**.

   ![Új védelmi csoport létrehozása varázsló párbeszédpanel](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók**lehetőséget, majd kattintson a **tovább**gombra. Megjelenik a **csoporttagok kiválasztása** lap.

1. A **csoporttagok kiválasztása** lapon válassza ki azokat a virtuális gépeket (vagy virtuálisgép-mappákat), amelyekről biztonsági másolatot szeretne készíteni, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Ha kijelöl egy mappát vagy virtuális gépet, akkor a mappában lévő mappák is ki vannak választva a biztonsági mentéshez. Törölheti azokat a mappákat vagy virtuális gépeket, amelyekről nem kíván biztonsági másolatot készíteni. Ha már folyamatban van egy virtuális gép vagy mappa biztonsági mentése, nem választható ki, amely biztosítja, hogy a rendszer duplikált helyreállítási pontokat hozzon létre a virtuális gépekhez.

   ![Csoporttagok kiválasztása](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Az **adatvédelmi módszer kiválasztása** lapon adja meg a védelmi csoport és a védelmi beállítások nevét. 

1. Állítsa be a rövid távú védelmet a **lemezre**, engedélyezze az online védelmet, majd kattintson a **tovább**gombra.

   ![Az adatvédelmi módszer kiválasztása](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Itt adhatja meg, hogy mennyi ideig szeretné megőrizni a lemezre történő biztonsági mentést.

   - **Megőrzési**időtartam: azon napok száma, amelyeken a lemezes helyreállítási pontok megmaradnak.
   - **Expressz teljes biztonsági mentés**: a lemezes helyreállítási pontok gyakorisága. A rövid távú biztonsági mentések időpontjának vagy dátumának módosításához kattintson a **módosítás**gombra.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="vSphere webes ügyfél":::

1. A **Disk Storage foglalásának áttekintése** lapon tekintse át a virtuális gépek biztonsági másolatainak rendelkezésre álló lemezterületet.

   - Az ajánlott lemez-foglalások a megadott megőrzési időtartamon, a munkaterhelés típusától és a védett adatok méretén alapulnak. Végezze el a szükséges módosításokat, majd kattintson a **tovább**gombra.
   - **Adatméret:** A védelmi csoportban lévő adatméret.
   - **Lemezterület:** A védelmi csoport számára javasolt lemezterület. Ha módosítani kívánja ezt a beállítást, akkor az egyes adatforrások által becsült mennyiségnél valamivel nagyobb teljes terület foglalása.
   - **Storage-készlet részletei:** Megjeleníti a tárolási készlet állapotát, amely tartalmazza a teljes és a fennmaradó lemez méretét.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="vSphere webes ügyfél":::

   > [!NOTE]
   > Bizonyos helyzetekben a jelentett adatméret nagyobb, mint a virtuális gép tényleges mérete. Tisztában vagyunk a probléma megoldásával és jelenleg vizsgálja.

1. A **replika-létrehozási módszer kiválasztása** lapon adja meg, hogyan szeretné elkészíteni a kezdeti biztonsági mentést, és válassza a **tovább**lehetőséget.

   - Az alapértelmezett érték **automatikusan a hálózaton keresztül** történik, és **most**. Ha az alapértelmezett értéket használja, akkor az off-Peak időt kell megadnia. Ha **később**választja, adjon meg egy napot és egy időpontot.
   - Nagy mennyiségű vagy kevésbé optimális hálózati feltételnél érdemes lehet a cserélhető adathordozó használatával offline módon replikálni az adatfájlokat.

   ![Replika-létrehozási módszer kiválasztása](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. **Konzisztencia-ellenőrzési beállítások**esetén válassza ki, hogyan és mikor szeretné automatizálni a konzisztencia-ellenőrzéseket, és válassza a **tovább**lehetőséget.

   - A konzisztencia-ellenőrzéseket futtathatja, ha a replika adatai inkonzisztensek, vagy egy meghatározott ütemterv alapján.
   - Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, akkor a jobb gombbal a védelmi csoportra kattintva **végezzen konzisztencia**-ellenőrzést.

1. Az **online védelem adatvédelmének megadása** lapon válassza ki azokat a virtuális gépeket vagy virtuálisgép-mappákat, amelyekről biztonsági másolatot szeretne készíteni, majd kattintson a **tovább**gombra. 

   > [!TIP]
   > A tagokat egyenként is kiválaszthatja, vagy az **összes kijelölése** lehetőséget választva kiválaszthatja az összes tagot.

   ![Online védelmi adatértékek meghatározása](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Az **online biztonsági mentés** időpontjának megadása lapon jelezze, hogy milyen gyakran szeretne biztonsági másolatot készíteni a helyi tárolóból az Azure-ba, majd válassza a **tovább**lehetőséget. 

   - A Felhőbeli helyreállítási pontok, amelyeket a rendszer az ütemterv alapján hoz létre. 
   - A helyreállítási pont létrehozása után a rendszer átviszi a Recovery Services-tárolóba az Azure-ban.

   ![Online biztonsági mentési ütemterv megadása](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Az **online adatmegőrzési szabály megadása** lapon adja meg, hogy mennyi ideig szeretné megőrizni a napi, heti, havi vagy éves biztonsági mentésből az Azure-ba létrehozott helyreállítási pontokat, majd válassza a **tovább**lehetőséget.

   - Nincs időkorlát arra vonatkozóan, hogy mennyi ideig tarthat az Azure-beli adatmegőrzés.
   - Az egyetlen korlát, hogy a védett példányok esetében nem lehet több, mint 9 999 helyreállítási pont. Ebben a példában a védett példány a VMware-kiszolgáló.

   ![Online adatmegőrzési szabály meghatározása](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd válassza a **csoport létrehozása**lehetőséget.

   ![Védelmi csoport tagja és a beállítás összegzése](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Figyelés a Azure Backup Server-konzollal

Miután konfigurálta a védelmi csoportot az Azure VMware-megoldás virtuális gépei biztonsági mentéséhez, a Azure Backup Server-konzol használatával figyelheti a biztonsági mentési feladatok és riasztások állapotát. Itt láthatja a figyelést.

- A **figyelés** ablaktábla **riasztások** lapján nyomon követheti a védelmi csoportok hibáit, figyelmeztetéseit és általános információit egy adott védett számítógép vagy az üzenet súlyossága alapján. Megtekintheti az aktív és az inaktív riasztásokat, és beállíthatja az e-mailes értesítéseket.
- A **figyelés** ablaktábla **feladatok** lapján megtekintheti a Azure Backup Server által kezdeményezett feladatokat egy adott védett adatforrás vagy védelmi csoport számára. Nyomon követheti a feladat előrehaladását, vagy megtekintheti a feladatok által felhasznált erőforrásokat.
- A **védelem** munkaterületen megtekintheti a kötetek és megosztások állapotát a védelmi csoportban. A konfigurációs beállításokat, például a helyreállítási beállításokat, a lemez kiosztását és a biztonsági mentési ütemtervet is megtekintheti.
- A **felügyelet** munkaterületen megtekintheti a **lemezek, online**és **ügynökök** lapokat a tárolóban lévő lemezek állapotának ellenőrzéséhez, az Azure-ba való regisztráláshoz és a DPM-ügynök állapotának üzembe helyezéséhez.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="vSphere webes ügyfél":::

## <a name="restore-vmware-virtual-machines"></a>VMware virtuális gépek visszaállítása

A Azure Backup Server felügyeleti konzol két módon lehet helyreállítani az adatgyűjtést. Kereshet vagy böngészhet. Az adatok helyreállításakor előfordulhat, hogy nem szeretné visszaállítani az adatokat vagy egy virtuális gépet ugyanarra a helyre. Emiatt a Azure Backup Server három helyreállítási lehetőséget támogat a VMware virtuális gépek biztonsági másolatainak:

- **Eredeti hely helyreállítása (OLR)**: a védett virtuális gép eredeti helyükre történő visszaállításához használja a OLR-t. A virtuális gépeket csak akkor állíthatja vissza az eredeti helyükre, ha a biztonsági mentés óta nincs lemez hozzáadva vagy törölve. Ha lemezek lettek hozzáadva vagy törölve, akkor másik helyre történő helyreállítást kell használnia.
- **Másik helyre történő helyreállítás (ALR)**: Ha az eredeti virtuális gép hiányzik, vagy nem szeretné megzavarni az eredeti virtuális gépet, állítsa helyre a virtuális gépet egy másik helyre. Egy virtuális gép másik helyre történő helyreállításához meg kell adnia egy ESXi-gazdagép, erőforráskészlet, mappa, valamint a tároló adattárát és elérési útját. A visszaállított virtuális gép az eredeti virtuális gépről való megkülönböztetéséhez Azure Backup Server a "helyreállított" elemet a virtuális gép nevéhez.
- **Különálló fájl helye-helyreállítás (ILR)**: Ha a védett virtuális gép egy Windows Server rendszerű virtuális gép, a virtuális gépen található egyes fájlok vagy mappák a Azure Backup Server ILR funkciójával állíthatók helyre. Az egyes fájlok helyreállításához tekintse meg a jelen cikk későbbi részében ismertetett eljárást. Egy különálló fájl virtuális gépről való visszaállítása csak a Windows rendszerű virtuális gépek és a lemezes helyreállítási pontok esetében érhető el.

### <a name="restore-a-recovery-point"></a>Helyreállítási pont visszaállítása

1. A Azure Backup Server felügyeleti konzol válassza ki a **helyreállítás** nézetet. 

1. A **Tallózás** panelen tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a **helyreállítási pontok** ablaktáblán megjelennek a rendelkezésre álló helyreállítási pontok.

   ![Rendelkezésre álló helyreállítási pontok](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. A **helyreállítási pontok** ablaktáblán a naptár és legördülő menük segítségével Válasszon ki egy dátumot a helyreállítási pont létrehozásakor. A félkövérrel szedett naptári dátumok rendelkeznek elérhető helyreállítási pontokkal. Másik lehetőségként kattintson a jobb gombbal a virtuális gépre, és válassza az **összes helyreállítási pont megjelenítése** lehetőséget, majd a listából válassza ki a helyreállítási pontot.

   > [!NOTE] 
   > A rövid távú védelem érdekében válasszon egy lemezes helyreállítási pontot a gyorsabb helyreállítás érdekében. A rövid távú helyreállítási pontok lejárta után csak az **online** helyreállítási pontok jelennek meg a helyreállításhoz.

1. Az online helyreállítási pontról történő helyreállítás előtt gondoskodjon arról, hogy az előkészítési hely elegendő szabad területet tartalmazzon a helyreállítani kívánt virtuális gép teljes tömörítetlen méretének tárolásához. Az előkészítési hely megtekinthető vagy módosítható az **előfizetési beállítások konfigurálása varázsló**futtatásával.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="vSphere webes ügyfél":::

1. Válassza **a helyreállítás lehetőséget** a **helyreállítási varázsló**megnyitásához.

   ![Helyreállítási varázsló, a helyreállítás kijelölésének áttekintése lap](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. A **tovább** gombra kattintva nyissa **meg a helyreállítási beállítások megadása** képernyőt. Kattintson a **tovább** gombra, ha a **helyreállítási típus kiválasztása** képernyőre szeretne lépni. 

   > [!NOTE]
   > A VMware-alapú munkaterhelések nem támogatják a hálózati sávszélesség szabályozásának engedélyezését.

1. A **helyreállítási típus kiválasztása** lapon válassza ki, hogy a helyreállítást az eredeti példányra vagy egy új helyre kívánja-e visszaállítani, majd válassza a **tovább**lehetőséget.

   - Ha a **helyreállítás az eredeti példányra**lehetőséget választja, nem kell további döntéseket hoznia a varázslóban. A rendszer az eredeti példányra vonatkozó adatgyűjtést használja.
   - Ha **bármelyik gazdagépen a helyreállítás virtuális géphez**lehetőséget választja, akkor a **célhely megadása** képernyőn adja meg az **ESXi-gazdagép**, az **erőforráskészlet**, a **mappa**és az **elérési út**adatait.

   ![Helyreállítási típus kiválasztása lap](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd **kattintson a helyreállítás** elemre a helyreállítási folyamat elindításához. 

   A **helyreállítás állapota** képernyőn a helyreállítási művelet előrehaladása látható.

### <a name="restore-an-individual-file-from-a-vm"></a>Egyedi fájl visszaállítása virtuális gépről

A védett virtuális gépek helyreállítási pontjairól is visszaállíthatja az egyes fájlokat. Ez a funkció csak Windows Server rendszerű virtuális gépek esetén érhető el. Az egyes fájlok visszaállítása hasonló a teljes virtuális gép visszaállításához, kivéve, ha a VMDK navigál, és a helyreállítási folyamat elindítása előtt megkeresi a kívánt fájlokat. 

> [!NOTE]
> Egy különálló fájl virtuális gépről való visszaállítása csak a Windows rendszerű virtuális gépek és a lemezes helyreállítási pontok esetében érhető el.

1. A Azure Backup Server felügyeleti konzol válassza ki a **helyreállítás** nézetet.

1. A **Tallózás** panelen tallózással vagy szűréssel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a **helyreállítási pontok** ablaktáblán megjelennek a rendelkezésre álló helyreállítási pontok.

   ![Elérhető helyreállítási pontok](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. A **helyreállítási pontok** ablaktáblán a naptár használatával válassza ki a kívánt helyreállítási pontokat tartalmazó dátumot. A biztonsági mentési házirend konfigurálásának módjától függően a dátumok több helyreállítási ponttal is rendelkezhetnek. 

1. Miután kiválasztotta a helyreállítási pont készítésének napját, győződjön meg arról, hogy a megfelelő **helyreállítási időt**választja. 

   > [!NOTE]
   > Ha a kiválasztott dátum több helyreállítási ponttal rendelkezik, válassza ki a helyreállítási pontot a **helyreállítási idő** legördülő menüben. 

   Miután kiválasztotta a helyreállítási pontot, megjelenik a helyreállítható elemek listája az **elérési út** ablaktáblán.

1. A visszaállítani kívánt fájlok megkereséséhez az **elérési út** ablaktáblán kattintson duplán a helyreállítható **elem** oszlopban található elemre a megnyitásához. Ezután válassza ki a helyreállítani kívánt fájlt vagy mappákat. Több elem kiválasztásához válassza a **CTRL** billentyűt az egyes elemek kijelölésekor. Az **elérési út** ablaktáblán megkeresheti a **helyreállítható elem** oszlopban megjelenő fájlok vagy mappák listáját.
    
   > [!NOTE]
   > Az **alábbi keresési lista** nem keres almappákban. Az almappákban való kereséshez kattintson duplán a mappára. A **fel** gomb használatával lépjen át egy alárendelt mappából a szülőmappa mappájába. Több elemet is kijelölhet (fájlok és mappák), de ugyanabban a szülő mappában kell lenniük. Ugyanabban a helyreállítási feladatokban nem állíthatók helyre több mappából származó elemek.

   ![Visszaállítási kijelölés áttekintése](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Ha kiválasztotta a helyreállításhoz szükséges elemeket, a felügyeleti konzol eszköz menüszalagján **kattintson a helyreállítás** elemre a **helyreállítási varázsló**megnyitásához. A **helyreállítási varázslóban**a helyreállítási beállítások **áttekintése** képernyő megjeleníti a helyreállítandó kijelölt elemeket.

1. A **helyreállítási beállítások megadása** képernyőn hajtsa végre az alábbi műveletek egyikét:

   - Válassza a **módosítás** lehetőséget a hálózati sávszélesség szabályozásának engedélyezéséhez. A **szabályozás** párbeszédpanelen jelölje be a **hálózati sávszélesség-használat szabályozásának engedélyezése** jelölőnégyzetet a beállítás bekapcsolásához. Ha engedélyezve van, konfigurálja a **beállításokat** és a **munkatervet**.
   - Kattintson a **tovább** gombra a hálózati sávszélesség-szabályozás letiltásához.

1. A **helyreállítási típus kiválasztása** képernyőn válassza a **tovább**lehetőséget. A fájlokat és mappákat csak hálózati mappába állíthatja helyre.

1. A **célhely megadása** képernyőn válassza a **Tallózás** lehetőséget a fájlok vagy mappák hálózati helyének megkereséséhez. Azure Backup Server létrehoz egy mappát, ahol az összes helyreállított elem másolása történik. A Mappanév MABS_day – hónap év előtaggal rendelkezik. Ha kijelöl egy helyet a helyreállított fájlokhoz vagy mappához, a rendszer a hely részleteit, például a **célhelyet**, a **célhely elérési útját**és a **rendelkezésre álló területet**adja meg.

   ![A fájlok visszaállítási helyének meghatározása](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. A **helyreállítási beállítások megadása** képernyőn válassza ki, hogy melyik biztonsági beállítást kívánja alkalmazni. Dönthet úgy, hogy módosítja a hálózati sávszélesség-használat szabályozását, de alapértelmezés szerint le van tiltva a szabályozás. Továbbá a **Tárolóhálózati helyreállítás** és az **értesítés** nincs engedélyezve.

1. Az **Összefoglalás** képernyőn tekintse át a beállításokat, majd **kattintson a helyreállítás** elemre a helyreállítási folyamat elindításához. A **helyreállítás állapota** képernyőn a helyreállítási művelet előrehaladása látható.

## <a name="next-steps"></a>További lépések

A biztonsági másolatok beállítása során felmerülő problémák elhárításához tekintse át a Azure Backup Server hibaelhárítási útmutatóját.

> [!div class="nextstepaction"]
> [A Azure Backup Server hibaelhárítási útmutatója](../backup/backup-azure-mabs-troubleshoot.md)
