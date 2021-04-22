---
title: Virtuális gépek Azure VMware Solution biztonsági Azure Backup Server
description: Konfigurálja a Azure VMware Solution környezetet a virtuális gépek biztonsági Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871776"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Virtuális gépek Azure VMware Solution biztonsági Azure Backup Server

Ebben a cikkben a virtuális gépeken futó VMware virtuális gépek biztonsági Azure VMware Solution biztonsági Azure Backup Server. Először alaposan végig kell mennie a Set up Microsoft Azure Backup Server for Azure VMware Solution ( A Microsoft Azure Backup [Server beállítása) Azure VMware Solution.](set-up-backup-server-for-azure-vmware-solution.md)

Ezután végigveszünk minden szükséges eljárást a következő műveletekhez:

> [!div class="checklist"] 
> * Állítson be egy biztonságos csatornát, hogy a Azure Backup Server HTTPS-kapcsolaton keresztül kommunikáljanak a VMware-kiszolgálókkal. 
> * Adja hozzá a fiók hitelesítő adatait a Azure Backup Server. 
> * Adja hozzá a vCentert a Azure Backup Server. 
> * Állítson be egy védelmi csoportot, amely tartalmazza azokat a VMware virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni, adja meg a biztonsági mentési beállításokat, és ütemezi a biztonsági mentést.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Biztonságos kapcsolat létrehozása a vCenter-kiszolgálóval

Alapértelmezés szerint a Azure Backup Server HTTPS-protokollon keresztül kommunikál a VMware-kiszolgálókkal. A HTTPS-kapcsolat beállításhoz töltse le a VMware hitelesítésszolgáltató (CA) tanúsítványát, és importálja a Azure Backup Server.

### <a name="set-up-the-certificate"></a>A tanúsítvány beállítása

1. A böngészőben a Azure Backup Server adja meg a vSphere webes ügyfél URL-címét.

   > [!NOTE] 
   > Ha a VMware **Első lépések** lap nem jelenik meg, ellenőrizze a kapcsolat és a böngésző proxybeállítását, és próbálkozzon újra.

1. A VMware **első lépések lapján** válassza a Megbízható legfelső szintű hitelesítésszolgáltatói tanúsítványok letöltése **lehetőséget.**

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere webes ügyfél":::

1. Mentse **adownload.zip** fájlt a Azure Backup Server gépre, majd bontsa ki annak tartalmát a **certs** mappába, amely a következőt tartalmazza:

   - Főtanúsítvány-fájl kiterjesztéssel, amely számokkal (például .0 és .1) kezdődik.
   - CRL-fájl kiterjesztéssel, amely egy olyan sorozatmal kezdődik, mint az .r0 vagy az .r1.

1. A **certs mappában** kattintson a jobb gombbal a főtanúsítvány-fájlra, és válassza az **Átnevezés** lehetőséget a **kiterjesztés .crt kiterjesztésre való váltáshoz.**

   A fájl ikonja egy főtanúsítványnak megfelelőre változik.

1. Kattintson a jobb gombbal a főtanúsítványra, és válassza a **Tanúsítvány telepítése lehetőséget.**

1. A **Tanúsítványimport varázslóban** válassza a **Helyi gép** lehetőséget a tanúsítvány célhelyének, majd kattintson a Tovább **gombra.**

   ![Varázsló kezdőlapja](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Ha a rendszer kéri, erősítse meg, hogy engedélyezni szeretné a számítógép módosításait.

1. Válassza **a Minden tanúsítvány tárolása a következő tárolóban** lehetőséget, majd válassza a **Tallózás** lehetőséget a tanúsítványtároló kiválasztásához.

   ![Tanúsítványtároló](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. **Célmappának megbízható legfelső szintű hitelesítésszolgáltatók** válassza az "Ok" lehetőséget, majd kattintson az **OK gombra.**

1. Tekintse át a beállításokat, és **válassza a Befejezés** lehetőséget a tanúsítvány importálásának elkezdéséhez.

   ![Ellenőrizze, hogy a tanúsítvány a megfelelő mappában van-e](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. A tanúsítvány importálásának megerősítése után jelentkezzen be a vCenter-kiszolgálóra, és győződjön meg arról, hogy a kapcsolat biztonságos.

### <a name="enable-tls-12-on-azure-backup-server"></a>A TLS 1.2 engedélyezése a Azure Backup Server

A VMware 6.7-es vagy újabb rendszerében a TLS volt engedélyezve kommunikációs protokollként. 

1. Másolja ki az alábbi beállításjegyzék-beállításokat, és illessze be őket a Jegyzettömbbe. Ezután mentse a fájlt TLS-ként. REG a .txt kiterjesztés nélkül.

   ```
   
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

1. Kattintson a jobb gombbal a TLS-re. REG-fájl, majd válassza az **Egyesítés** **vagy** Megnyitás lehetőséget a beállítások beállításjegyzékhez való hozzáadásához.


## <a name="add-the-account-on-azure-backup-server"></a>Adja hozzá a fiókot a Azure Backup Server

1. Nyissa Azure Backup Server, majd a Azure Backup Server **konzolján** válassza a Felügyeleti éles kiszolgálók  >    >  **VMware kezelése lehetőséget.**

   ![Azure Backup Server konzol](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. A Hitelesítő **adatok kezelése párbeszédpanelen** válassza a Hozzáadás **lehetőséget.**

   ![A Hitelesítő adatok kezelése párbeszédpanelen válassza a Hozzáadás lehetőséget.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. A Hitelesítő **adatok hozzáadása** párbeszédpanelen adja meg az új hitelesítő adat nevét és leírását. Adja meg a VMware-kiszolgálón megadott felhasználónevet és jelszót.

   > [!NOTE] 
   > Ha a VMware-Azure Backup Server nem ugyanabban a tartományban vannak, adja meg a tartományt a **Felhasználónév mezőben.**

   ![Azure Backup Server Hitelesítő adatok hozzáadása párbeszédpanel](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Az **új hitelesítő** adat hozzáadásához válassza a Hozzáadás lehetőséget.

   ![Képernyőkép a Azure Backup Server kezelése párbeszédpanelről az új hitelesítő adatok megjelenítésével.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Adja hozzá a vCenter-kiszolgálót a Azure Backup Server

1. A Azure Backup Server válassza a **Felügyeleti**  >  **éles kiszolgálók Hozzáadás**  >  **lehetőséget.**

   ![Nyissa meg az Éles kiszolgáló hozzáadása varázslót](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Válassza a **VMware Servers (VMware-kiszolgálók)** lehetőséget, majd kattintson **a Next (Tovább) gombra.**

   ![Éles kiszolgáló hozzáadása varázsló](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Adja meg a vCenter IP-címét.

   ![VMware-kiszolgáló megadása](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. Az **SSL-port mezőbe** írja be a vCenter-sel való kommunikációhoz használt portot.

   > [!TIP] 
   > Az alapértelmezett port a 443-as port, de módosíthatja, ha a vCenter egy másik portot figyel.

1. A Hitelesítő **adatok megadása mezőben** válassza ki az előző szakaszban létrehozott hitelesítő adatokat.

1. Válassza **a Hozzáadás** lehetőséget a vCenter a kiszolgálók listájához való hozzáadásához, majd kattintson a Tovább **gombra.**

   ![VMware-kiszolgáló és hitelesítő adatok hozzáadása](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Az Összefoglalás **lapon** válassza a **Hozzáadás lehetőséget, hogy** hozzáadja a vCentert Azure Backup Server.

   Az új kiszolgáló hozzáadása azonnal megkezdődik. A vCenternek nincs szüksége ügynökre.

   ![VMware-kiszolgáló hozzáadása Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. A Befejezés **lapon tekintse** át a beállításokat, majd válassza a Bezárás **lehetőséget.**

   ![Oldal befejezése](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Megjelenik a vCenter-kiszolgáló az Éles kiszolgáló **listában a következővel:**
   - Írja be a **következőt: VMware Server** 
   - Az ügynök állapota **OK** 
   
      Ha az Ügynök állapota **Ismeretlen,** **válassza** a **Frissítés lehetőséget.**

## <a name="configure-a-protection-group"></a>Védelmi csoport konfigurálása

A védelmi csoportok több virtuális gépeket gyűjtenek, és ugyanazon adatmegőrzési és biztonsági mentési beállításokat alkalmazzák a csoport összes virtuális gépe esetében.

1. A Azure Backup Server válassza a **Védelem Új**  >  **lehetőséget.**

   ![Nyissa meg az Új védelmi csoport létrehozása varázslót](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Az Új **védelmi csoport létrehozása varázsló kezdőlapján** válassza a Tovább **lehetőséget.**

   ![Új védelmi csoport létrehozása varázsló párbeszédpanel](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. A Védelmi **csoport típusának kiválasztása lapon** válassza a **Kiszolgálók,** majd a Tovább **lehetőséget.** Megjelenik **a Csoporttagok kiválasztása** lap.

1. A **Csoporttagok kiválasztása lapon** válassza ki azokat a virtuális gépeket (vagy virtuálisgép-mappákat), amelyekről biztonsági adatbázist szeretne, majd válassza a Tovább **lehetőséget.**

   > [!NOTE]
   > Amikor kiválaszt egy mappát vagy virtuális gépeket, az ebben a mappában lévő mappák is ki vannak választva a biztonsági mentéshez. Törölje a jelölést a mappák vagy virtuális gépek jelölőnégyzetéből, amelyekről nem kíván biztonsági mentéseket. Ha egy virtuális gépről vagy mappáról már biztonsági másolatot készítettek, nem választhatja ki, ami biztosítja, hogy ne jön létre duplikált helyreállítási pont egy virtuális géphez.

   ![Csoporttagok kiválasztása](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Az **Adatvédelmi módszer kiválasztása lapon** adja meg a védelmi csoport és a védelmi beállítások nevét. 

1. A rövid távú védelmet állítsa Disk **(Lemez)** beállításra, engedélyezze az online védelmet, majd válassza a **Next (Tovább) lehetőséget.**

   ![Az adatvédelmi módszer kiválasztása](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Adja meg, hogy mennyi ideig szeretné lemezen tartani az adatok biztonsági adatait.

   - **Megőrzési** időtartam: A lemez-helyreállítási pontok megőrzésének napjainak száma.
   - **Expressz teljes biztonsági mentés:** A lemez-helyreállítási pontok készítésének a száma. A rövid távú biztonsági mentések időpontjai és dátumai módosításához válassza a **Módosítás lehetőséget.**

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="A lemezalapú védelem rövid távú céljainak meghatározása":::

1. A Foglalás **Disk Storage lapon** tekintse át a virtuális gép biztonsági mentéséhez rendelkezésre álló lemezterületet.

   - A javasolt lemezfoglalások a megadott megőrzési időtartamon, a számítási feladat típusán és a védett adatok méretén alapulnak. Tegye kötelezően a módosításokat, majd válassza a **Tovább lehetőséget.**
   - **Adatméret:** A védelmi csoportban az adatok mérete.
   - **Lemezterület:** A védelmi csoport számára javasolt lemezterület. Ha módosítani szeretné ezt a beállítást, válassza a kisebb helyet, mint az egyes adatforrások becsült mennyisége.
   - **Tárolókészlet részletei:** Megjeleníti a tárolókészlet állapotát, beleértve a teljes és a fennmaradó lemezméretet.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="A tárolókészletben megadott lemezterület áttekintése":::

   > [!NOTE]
   > Bizonyos esetekben a jelentett adatméret nagyobb, mint a virtuális gép tényleges mérete. Tisztában vagyunk a problémával, és jelenleg is vizsgáljuk.

1. A **Replika-létrehozási módszer kiválasztása lapon** adja meg, hogyan szeretné készíteni a kezdeti biztonsági mentést, majd válassza a Tovább **lehetőséget.**

   - Az alapértelmezett érték **az Automatikus a hálózaton keresztül és** a **Most.** Ha az alapértelmezett értéket használja, adjon meg egy csúcsidőn kívüli időt. Ha a Későbbi lehetőséget **választja,** adjon meg egy napot és egy időt.
   - Nagy mennyiségű adat vagy az optimálisnál alacsonyabb hálózati feltételek esetén érdemes lehet az adatokat kapcsolat nélküli üzemmódban replikálni cserélhető adathordozó használatával.

   ![Replika-létrehozási módszer kiválasztása](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. A **Konzisztencia-ellenőrzés beállításaihoz** válassza ki, hogyan és mikor automatizálja a konzisztencia-ellenőrzéseket, majd válassza a **Tovább lehetőséget.**

   - Konzisztencia-ellenőrzéseket futtathat, ha a replikaadatok inkonzisztenssé válnak, vagy egy meghatározott ütemezés szerint.
   - Ha nem szeretne automatikus konzisztencia-ellenőrzéseket konfigurálni, manuális ellenőrzést is futtathat, ha a jobb gombbal a Konzisztencia-ellenőrzés végrehajtása védelmi csoportra **kattint.**

1. Az **Online védelmi adatok megadása lapon** válassza ki azokat a virtuális gépeket vagy virtuálisgép-mappákat, amelyekről biztonsági szeretne biztonsági adatokat, majd válassza a Tovább **lehetőséget.** 

   > [!TIP]
   > A tagokat egyenként is kiválaszthatja, vagy az **Összes kijelölése lehetőséget választva** kijelölheti az összes tagot.

   ![Online védelmi adatok megadása](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Az **Online biztonsági mentés ütemezésének megadása** lapon adja meg, hogy milyen gyakran szeretne biztonsági másolatot készíteni az adatokról a helyi tárolóból az Azure-ba. 

   - Felhőbeli helyreállítási pontok az adatok ütemezés szerinti generálására. 
   - A helyreállítási pont létrehozása után a rendszer továbbítja azt az Azure-beli helyreállítási tárba.

   ![Online biztonsági mentés ütemezésének megadása](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Az **Online adatmegőrzési szabályzat megadása lapon** adja meg, hogy mennyi ideig szeretné megtartani az Azure-ba készített biztonsági mentések során létrehozott helyreállítási pontokat.

   - Nincs időkorlát arra, hogy mennyi ideig tarthatja meg az adatokat az Azure-ban.
   - Az egyetlen korlát az, hogy védett példányonként legfeljebb 9999 helyreállítási pont lehet. Ebben a példában a védett példány a VMware-kiszolgáló.

   ![Online adatmegőrzési szabályzat megadása](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Az Összefoglalás **lapon** tekintse át a beállításokat, majd válassza a **Csoport létrehozása lehetőséget.**

   ![Védelmi csoport tagja és beállítás összegzése](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Figyelése az Azure Backup Server konzollal

Miután úgy konfigurálta a védelmi csoportot, hogy biztonsági mentést Azure VMware Solution virtuális gépekről, a biztonsági mentési feladat és a riasztás állapotát az Azure Backup Server figyelheti. A figyelt állapotok ak.

- A **Figyelés** feladatterületen:
   - A **Riasztások alatt** figyelheti a hibákat, a figyelmeztetéseket és az általános információkat.  Megtekintheti az aktív és inaktív riasztásokat, és beállíthatja az e-mailes értesítéseket.
   - A **Feladatok** alatt megtekintheti az egy adott Azure Backup Server vagy védelmi csoport által elindított feladatokat. Nyomon követheti a feladatok előrehaladását, vagy ellenőrizheti a feladatok által felhasznált erőforrásokat.
- A **Védelem feladatterületen** ellenőrizheti a védelmi csoportban található kötetek és megosztások állapotát. Emellett olyan konfigurációs beállításokat is ellenőrizhet, mint a helyreállítási beállítások, a lemezfoglalás és a biztonsági mentés ütemezése.
- A Felügyelet **feladatterületen** a **Lemezek, Online** és  Ügynökök lapokon ellenőrizheti a tárolókészletben lévő lemezek állapotát, az Azure-ba való regisztrációt és a telepített DPM-ügynök állapotát.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="A biztonsági mentési feladatok állapotának figyelése a Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>VMware virtuális gépek visszaállítása

A Azure Backup Server felügyeleti konzol helyreállítható adatokat kétféleképpen találhatja meg. Kereshet és böngészhet is. Az adatok helyreállításakor előfordulhat, hogy nem szeretne adatokat vagy virtuális gépeket visszaállítani ugyanazon a helyen. Ezért a Azure Backup Server három helyreállítási lehetőséget támogat a VMware virtuális gépek biztonsági mentéséhez:

- **Eredeti hely helyreállítása (OLR):** Az OLR használatával visszaállíthatja a védett virtuális gépeket az eredeti helyükre. A virtuális gép csak akkor állítható vissza az eredeti helyére, ha a biztonsági mentés óta nem lett lemez hozzáadva vagy törölve. Lemezek hozzáadásakor vagy törlésekor másik helyre való helyreállítást kell használnia.
- **Alternatív hely helyreállítása (ALR):** Akkor használja, ha az eredeti virtuális gép hiányzik, vagy nem szeretné megzavarni az eredeti virtuális gépet. Adja meg az ESXi-gazdagép, az erőforráskészlet, a mappa, valamint a táradattár és az elérési út helyét. A visszaállított virtuális gép és az eredeti virtuális gép megkülönböztetése Azure Backup Server a *"-Recovered" (Helyreállítva)* adatokat a virtuális gép nevéhez.
- **Egyéni fájlhely-helyreállítás (ILR):** Ha a védett virtuális gép Egy Windows Server rendszerű virtuális gép, akkor a virtuális gépen belüli egyes fájlok vagy mappák helyreállíthatóak a virtuális gép ILR Azure Backup Server. Az egyes fájlok helyreállításához tekintse meg a cikk későbbi, eljárását. Az egyes fájlok virtuális gépről történő visszaállítása csak Windows rendszerű virtuális gépeken és lemez-helyreállítási pontokon érhető el.

### <a name="restore-a-recovery-point"></a>Helyreállítási pont visszaállítása

1. A Azure Backup Server felügyeleti konzol válassza a **Helyreállítás nézetet.** 

1. A Tallózás **panelen** tallózással vagy szűrővel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a **Helyreállítási pontok a panelhez panelen jelennek meg az elérhető helyreállítási pontok.

   ![Rendelkezésre álló helyreállítási pontok](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. A Helyreállítási **pontok a következő panelen** válassza ki a helyreállítási pont dátumát. A félkövérrel szedett naptári dátumok rendelkezésre álló helyreállítási pontokkal is elérhetők. Azt is választhatja, hogy a jobb gombbal a virtuális gépre kattint, és az **Összes** helyreállítási pont megjelenítése lehetőséget választja, majd kiválasztja a helyreállítási pontot a listából.

   > [!NOTE] 
   > A rövid távú védelem érdekében válasszon lemezalapú helyreállítási pontot a gyorsabb helyreállítás érdekében. A rövid távú helyreállítási pontok lejárata után csak az **online** helyreállítási pontok állíthatók helyre.

1. Online helyreállítási pontról való helyreállítás előtt győződjön meg arról, hogy az előkészítési hely elegendő szabad helyet tartalmaz a helyreállítani kívánt virtuális gép teljes tömörítetlen méretének a számára. Az előkészítési helyet az Előfizetési beállítások konfigurálása varázsló futtatásával lehet **megtekinteni vagy módosítani.**

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server helyreállítási mappa beállításai":::

1. Válassza **a Helyreállítás** lehetőséget a Helyreállítási varázsló **megnyitásához.**

   ![Helyreállítási varázsló, Helyreállítási kiválasztás áttekintése lap](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Kattintson **a Tovább** gombra a Helyreállítási beállítások megadása **képernyőre való ugráshoz.** Kattintson **ismét a Tovább** gombra a Helyreállítási típus kiválasztása **képernyőre való ugráshoz.** 

   > [!NOTE]
   > A VMware számítási feladatok nem támogatják a hálózati sávszélesség szabályozásának engedélyezését.

1. A Helyreállítási **típus kiválasztása lapon** vagy az eredeti példányra, vagy egy új helyre kell helyreállítani.

   - Ha a Helyreállítás az **eredeti példányra** lehetőséget választja, a varázslóban nem kell további lehetőségeket választania. A rendszer az eredeti példány adatait használja.
   - Ha a **Helyreállítás** virtuális gépként lehetőséget választja  bármely gazdagépen, akkor a Cél megadása képernyőn adja meg az ESXi-gazdagép, az Erőforráskészlet, a **Mappa** és az Elérési út  **adatait.**

   ![Helyreállítási típus kiválasztása lap](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Az Összefoglalás **lapon** tekintse át a beállításokat, és válassza a **Helyreállítás lehetőséget** a helyreállítási folyamat elkezdéséhez. 

   A **Helyreállítás állapota** képernyő a helyreállítási művelet előrehaladását jeleníti meg.

### <a name="restore-an-individual-file-from-a-vm"></a>Egyéni fájl visszaállítása virtuális gépről

A védett virtuális gépek helyreállítási pontjáról egyes fájlokat is visszaállíthat. Ez a funkció csak Windows Server rendszerű virtuális gépeken érhető el. Az egyes fájlok visszaállítása hasonlít a teljes virtuális gép visszaállításához, azzal a kivételel, hogy tallózással megkeresi a VMDK-t, és megkeresi a kívánt fájlokat a helyreállítási folyamat elkezdése előtt. 

> [!NOTE]
> Az egyes fájlok virtuális gépről történő visszaállítása csak Windows rendszerű virtuális gépeken és lemez-helyreállítási pontokon érhető el.

1. A Azure Backup Server felügyeleti konzol válassza a **Helyreállítás nézetet.**

1. A Tallózás **panelen** tallózással vagy szűrővel keresse meg a helyreállítani kívánt virtuális gépet. Miután kiválasztott egy virtuális gépet vagy mappát, a **Helyreállítási pontok a panelhez panelen jelennek meg az elérhető helyreállítási pontok.

   ![Elérhető helyreállítási pontok](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. A Helyreállítási **pontok a panelen** a naptár használatával válassza ki a kívánt helyreállítási pontokat tartalmazó dátumot. A biztonsági mentési házirend konfigurálásától függően a dátumok több helyreállítási ponttal is lehetnek. 

1. Miután kiválasztotta a helyreállítási pont napját, győződjön meg arról, hogy a megfelelő helyreállítási **időt választotta.** 

   > [!NOTE]
   > Ha a kiválasztott dátumhoz több helyreállítási pont is van, válassza ki a helyreállítási pontot a Helyreállítási idő legördülő menüben kiválasztva.  

   A helyreállítási pont kiválasztása után a helyreállítható elemek listája megjelenik az Elérési út **panelen.**

1. A helyreállítani kívánt fájlok megkereséhez  az Elérési út ablaktáblán kattintson duplán az elemre a **Helyreállítható** elem oszlopban annak megnyitásához. Ezután válassza ki a helyreállítani kívánt fájlt vagy mappákat. Több elem kijelöléséhez nyomja le a **Ctrl** billentyűt, miközben kijelöli az egyes elemeket. Az Elérési **út panelen** kereshet a Helyreállítható elem oszlopban megjelenő fájlok vagy mappák **listájában.**
    
   > [!NOTE]
   > **Az alábbi keresési lista** nem keres almappákban. Az almappákban való kereséshez kattintson duplán a mappára. A Fel **gombbal** lépjen egy gyermekmappból a szülőmappába. Több elemet (fájlokat és mappákat) is kijelölhet, de ezeknek ugyanabban a szülőmappában kell lennie. Ugyanabban a helyreállítási feladatban nem állíthatók helyre elemek több mappából.

   ![A helyreállítás kiválasztásának áttekintése](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Miután kiválasztotta az elemeket a helyreállításhoz, a felügyeleti konzol eszköz  menüszalagján válassza a Helyreállítás lehetőséget a Helyreállítási **varázsló megnyitásához.** A Helyreállítási varázsló  **Helyreállítási** kijelölés áttekintése képernyőjén a helyreállítani kijelölt elemek megjelenik.

1. A Helyreállítási **beállítások megadása képernyőn** tegye a következők egyikét:

   - Válassza **a Módosítás** lehetőséget a hálózati sávszélesség szabályozásának engedélyezéséhez. A Szabályozás **párbeszédpanelen** válassza a Hálózati sávszélesség használatának szabályozása **engedélyezése** lehetőséget a beállítás bekapcsolásához. Ha engedélyezve van, konfigurálja a **Beállítások és** **a Munka ütemezése beállítást.**
   - Kattintson **a Tovább** gombra a hálózati szabályozás letiltva hagyásán.

1. A Helyreállítási **típus kiválasztása képernyőn** válassza a Tovább **lehetőséget.** A fájlokat vagy mappákat csak hálózati mappába lehet helyreállítani.

1. A Cél **megadása képernyőn** válassza a **Tallózás** lehetőséget a fájlok vagy mappák hálózati helyének megkeresése érdekében. Azure Backup Server létrehoz egy mappát, amely az összes helyreállított elemet átmásolja. A mappanév az MABS_day-month-year előtaggal rendelkezik. Amikor kiválasztja a helyreállított fájlok vagy mappa helyét, a hely részletei meg vannak adatokat biztosítanak.

   ![A fájlok helyreállításának helye](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. A Helyreállítási **beállítások megadása képernyőn** válassza ki az alkalmazni kívánt biztonsági beállítást. Módosíthatja a hálózati sávszélesség használatának szabályozását, de a szabályozás alapértelmezés szerint le van tiltva. Emellett a **SAN-helyreállítás és** **az** értesítés nincs engedélyezve.

1. Az Összefoglalás **képernyőn** tekintse át a beállításokat, és válassza a **Helyreállítás lehetőséget** a helyreállítási folyamat elkezdéséhez. A **Helyreállítás állapota** képernyő a helyreállítási művelet előrehaladását jeleníti meg.

## <a name="next-steps"></a>Következő lépések

Most, hogy a virtuális gépek biztonsági Azure VMware Solution biztonsági Azure Backup Server, a következővel ismerkedhet meg: 

- [Hibaelhárítás a biztonsági mentések Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
- [Virtuális gépek életciklus-Azure VMware Solution kezelése](lifecycle-management-of-azure-vmware-solution-vms.md)
