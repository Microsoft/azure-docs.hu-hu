---
title: VMware virtuális gép – több-bérlős vész-helyreállítás Azure Site Recovery
description: Áttekintést nyújt Azure Site Recovery az Azure-ba irányuló VMWare vész-helyreállítási támogatásról több-bérlős környezet (CSP) program keretében.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93145582"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Több-bérlős támogatás a VMware vész-helyreállításhoz az Azure-ban a CSP használatával – áttekintés

[Azure site Recovery](site-recovery-overview.md) támogatja a bérlői előfizetések több-bérlős környezetét. Emellett támogatja a bérlői előfizetések több-bérlős használatát is, amelyeket a Microsoft Cloud megoldás-szolgáltató (CSP) programon keresztül hoztak létre és felügyelnek.

Ez a cikk áttekintést nyújt a több-bérlős VMware-ről az Azure-ba történő replikációjának megvalósításáról és kezeléséről.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetek

Három fő több-bérlős modell létezik:

* **Megosztott üzemeltetési szolgáltatások szolgáltatója (HSP)**: a partner tulajdonosa a fizikai infrastruktúra, és megosztott erőforrásokat (vCenter, adatközpontokat, fizikai tárhelyet stb.) használ a több bérlős virtuális gép ugyanazon az infrastruktúrán való üzemeltetéséhez. A partner felügyelt szolgáltatásként is biztosíthatja a vész-helyreállítási felügyeletet, a bérlő pedig önkiszolgáló megoldásként is saját vész-helyreállítási megoldást kínál.

* **Dedikált üzemeltetési szolgáltató**: a partner a fizikai infrastruktúrát használja, de dedikált erőforrásokat (több vCenter, fizikai adattárolót stb.) használ az egyes bérlők virtuális gépei külön infrastruktúrán való üzemeltetéséhez. A partner felügyelt szolgáltatásként is biztosíthatja a vész-helyreállítási felügyeletet, a bérlő pedig önkiszolgáló megoldásként is rendelkezhet.

* **Felügyelt szolgáltató (msp)**: az ügyfél tulajdonosa a virtuális gépeket üzemeltető fizikai infrastruktúra, a partner pedig vész-helyreállítási engedélyezést és felügyeletet biztosít.

## <a name="shared-hosting-services-provider-hsp"></a>Közös üzemeltetésű szolgáltatók (HSP)

A másik két forgatókönyv a megosztott üzemeltetésű forgatókönyv részhalmaza, és ugyanazokat az elveket használják. A különbségeket a megosztott üzemeltetési útmutató végén mutatjuk be.

A több-bérlős forgatókönyv alapvető követelménye, hogy a bérlőknek elkülönítettnek kell lenniük. Az egyik bérlő nem tudja megfigyelni, hogy egy másik bérlő hol található. A partner által felügyelt környezetekben ez a követelmény nem annyira fontos, mint az önkiszolgáló környezetekben, ahol kritikus fontosságú lehet. Ez a cikk azt feltételezi, hogy a bérlő elkülönítése kötelező.

Az architektúra az alábbi ábrán látható.

![Megosztott HSP egyetlen vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Megosztott üzemeltetés egyetlen vCenter-kiszolgálóval**

A diagramon minden ügyfél külön felügyeleti kiszolgálóval rendelkezik. Ez a konfiguráció korlátozza a bérlőhöz tartozó virtuális gépek bérlői hozzáférését, és lehetővé teszi a bérlők elkülönítését. A VMware VM-replikáció a konfigurációs kiszolgáló használatával észleli a virtuális gépeket, és telepíti az ügynököket. Ugyanazok az alapelvek vonatkoznak a több-bérlős környezetekre is, a virtuálisgép-felderítés korlátozásával a vCenter hozzáférés-vezérlés használatával.

Az adatelkülönítési követelmény azt jelenti, hogy az összes bizalmas infrastruktúra-információ (például a hozzáférési hitelesítő adatok) a bérlők számára nyilvánosságra is marad. Ezért javasoljuk, hogy a felügyeleti kiszolgáló összes összetevője a partner kizárólagos felügyelete alatt maradjon. A felügyeleti kiszolgáló összetevői a következők:

* Konfigurációs kiszolgáló
* Folyamatkiszolgáló
* Fő célkiszolgáló

A partner által felügyelt különálló felskálázású folyamat-kiszolgáló is.

## <a name="configuration-server-accounts"></a>Konfigurációs kiszolgálói fiókok

A több-bérlős forgatókönyvben szereplő összes konfigurációs kiszolgáló két fiókot használ:

- **vCenter hozzáférési fiók**: Ez a fiók a bérlői virtuális gépek felderítésére szolgál. Hozzá van rendelve vCenter hozzáférési engedélyekkel. Ha el szeretné kerülni a hozzáférési szivárgások elkerülését, javasoljuk, hogy a partnerek ezeket a hitelesítő adatokat saját maguk adja meg a konfigurációs eszközben.

- **Virtuális gép hozzáférési fiókja**: Ez a fiók használható a mobilitási szolgáltatás ügynökének a bérlői virtuális gépeken való telepítésére, automatikus leküldéses küldéssel. Általában egy olyan tartományi fiók, amelyet a bérlő egy partnernek vagy egy olyan fióknak ad meg, amelyet a partner közvetlenül kezelhet. Ha a bérlő nem szeretné közvetlenül megosztani az adatokat a partnerrel, a konfigurációs kiszolgálóhoz való korlátozott hozzáféréssel megadhatja a hitelesítő adatokat. Vagy a partner segítségével manuálisan is telepíthetik a mobilitási szolgáltatás ügynökét.

## <a name="vcenter-account-requirements"></a>vCenter-fiókra vonatkozó követelmények

Konfigurálja a konfigurációs kiszolgálót egy olyan fiókkal, amelyhez hozzá van rendelve egy speciális szerepkör.

- A szerepkör-hozzárendelést az egyes vCenter-objektumok vCenter-hozzáférési fiókjára kell alkalmazni, és a rendszer nem propagálja a gyermekobjektumok számára. Ez a konfiguráció biztosítja a bérlők elkülönítését, mivel a hozzáférés-propagálás más objektumokhoz való véletlen hozzáféréshez vezethet.

    ![A propagálás gyermekobjektum számára beállítás](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Az alternatív módszer a felhasználói fiók és a szerepkör társítása az adatközpont-objektumhoz, majd a gyermekobjektumok továbbítása. Ezután adja meg a fióknak, hogy minden objektumhoz (például más bérlőhöz tartozó virtuális gépekhez) **ne legyen hozzáférési** szerepkör, amely nem érhető el egy adott bérlő számára. Ez a konfiguráció nehézkes. Lehetővé teszi a véletlen hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést is. Ezért javasoljuk, hogy az első módszert használja.

### <a name="create-a-vcenter-account"></a>VCenter-fiók létrehozása

1. Hozzon létre egy új szerepkört az előre meghatározott *írásvédett* szerepkör klónozásával, majd adjon meg egy kényelmes nevet (például Azure_Site_Recovery, ahogy az ebben a példában látható).
2. Rendelje hozzá a következő engedélyeket ehhez a szerepkörhöz:

   * **Adattár**: tárhely kiosztása, adattár tallózása, alacsony szintű fájl műveletei, fájl eltávolítása, virtuálisgép-fájlok frissítése
   * **Hálózat**: hálózati hozzárendelés
   * **Erőforrás**: virtuális gép kiosztása erőforráskészlet számára, áttelepítési virtuális gép, Migrálás virtuális gépen
   * **Feladatok**: létrehozási feladat, feladat frissítése
   * **Virtuális gép – konfiguráció**: mind
   * **Virtuális gép – interakció** > válasz kérdés, eszköz kapcsolat, CD-adathordozó konfigurálása, hajlékonylemez-adathordozó konfigurálása, kikapcsolás, bekapcsolás, VMware-eszközök telepítése
   * **Virtuális gépek – leltár** > létrehozás meglévőből, új létrehozása, regisztráció, regisztráció törlése
   * Virtuálisgép **-kiépítés** > a virtuális gépek letöltésének engedélyezése, a virtuális gépek fájljainak feltöltése
   * **Virtuális gép – Pillanatképek kezelése** > Pillanatképek eltávolítása

       ![A szerepkör szerkesztése párbeszédpanel](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Rendeljen hozzáférési szinteket a vCenter-fiókhoz (amelyet a bérlői konfigurációs kiszolgálón használ) különböző objektumokhoz, az alábbiak szerint:

>| Objektum | Szerepkör | Megjegyzések |
>| --- | --- | --- |
>| vCenter | Read-Only | Csak a különböző objektumok kezeléséhez szükséges vCenter-hozzáférés engedélyezésére van szükség. Ezt az engedélyt akkor távolíthatja el, ha a fiókot soha nem fogja megadni a bérlőnek, vagy semmilyen felügyeleti művelethez nem használja a vCenter. |
>| Adatközpont | Azure_Site_Recovery |  |
>| Gazdagép és gazda fürt | Azure_Site_Recovery | Gondoskodjon arról, hogy a hozzáférés az objektum szintjén legyen, így csak az elérhető gazdagépek rendelkeznek a feladatátvétel előtt és a feladat-visszavétel után. |
>| Adattár-és adattár-fürt | Azure_Site_Recovery | Ugyanaz, mint az előző. |
>| Network (Hálózat) | Azure_Site_Recovery |  |
>| Felügyeleti kiszolgáló | Azure_Site_Recovery | A CS-gépen kívül minden összetevő (CS, PS és MT) elérését tartalmazza. |
>| Bérlői virtuális gépek | Azure_Site_Recovery | Gondoskodik arról, hogy egy adott bérlő új bérlői virtuális gépei is megkapják ezt a hozzáférést, vagy a Azure Portalon keresztül nem lesznek felderíthetők. |

A vCenter fiókhoz való hozzáférés most már befejeződött. Ez a lépés teljesíti a feladat-visszavételi műveletek végrehajtásához szükséges minimális engedélyeket. Ezeket a hozzáférési engedélyeket használhatja a meglévő szabályzatokhoz is. Csak módosítsa a meglévő engedélyeket úgy, hogy a 2. lépésből származó szerepkör-engedélyeket tartalmazza, korábban részletezve.

### <a name="failover-only"></a>Csak feladatátvétel
Ha a vész-helyreállítási műveleteket csak feladatátvételre szeretné korlátozni (azaz a feladat-visszavételi képességek nélkül), használja az előző eljárást a következő kivételekkel:

- Ahelyett, hogy az *Azure_Site_Recovery* szerepkört a vCenter-hozzáférési fiókhoz rendelje, csak *olvasási* szerepkört rendeljen ehhez a fiókhoz. Ez az engedélyezési beállítás engedélyezi a virtuális gépek replikálását és feladatátvételét, és nem engedélyezi a feladat-visszavételt.
- Az előző folyamat során minden más más marad. A bérlők elkülönítésének és a virtuálisgép-felderítés korlátozásának biztosításához minden engedély csak az objektum szintjén van hozzárendelve, és nem terjed ki a gyermek objektumokra.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Erőforrások üzembe helyezése a bérlői előfizetésben

1. A Azure Portal hozzon létre egy erőforráscsoportot, majd telepítsen egy Recovery Services-tárolót a szokásos folyamat alapján.
2. Töltse le a tároló regisztrációs kulcsát.
3. Regisztrálja a CS-t a bérlőhöz a tároló regisztrációs kulcsának használatával.
4. Adja meg a két hozzáférési fiók hitelesítő adatait, a vCenter-kiszolgáló eléréséhez használt fiókot és a virtuális gép eléréséhez szükséges fiókot.

    ![Kezelői konfigurációs kiszolgáló fiókjai](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Kiszolgálók regisztrálása a tárolóban

1. A Azure Portal a korábban létrehozott tárolóban regisztrálja a vCenter-kiszolgálót a konfigurációs kiszolgálón a létrehozott vCenter-fiók használatával.
2. Fejezze be az "infrastruktúra előkészítése" folyamatot Site Recovery a szokásos folyamat során.
3. A virtuális gépek most már készen állnak a replikálásra. Győződjön meg arról, hogy csak a bérlő virtuális gépei jelennek meg a **replikálás**  >  **területen válassza a virtuális gépek lehetőséget**.

## <a name="dedicated-hosting-solution"></a>Dedikált üzemeltetési megoldás

Ahogy az a következő ábrán is látható, az építészeti különbség egy dedikált üzemeltetési megoldásban az, hogy minden bérlői infrastruktúra csak az adott bérlőre van beállítva.

![A dedikált üzemeltetési megoldás építészeti különbségét ábrázoló diagram az, hogy minden bérlői infrastruktúra csak az adott bérlőhöz van beállítva.](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikált üzemeltetési forgatókönyv több vCenter**

## <a name="managed-service-solution"></a>Felügyelt szolgáltatási megoldás

Ahogy az a következő ábrán is látható, a felügyelt szolgáltatási megoldás építészeti különbsége az, hogy minden bérlői infrastruktúra fizikailag el van különítve a többi bérlői infrastruktúrától. Ez a forgatókönyv általában akkor fordul elő, ha a bérlő tulajdonosa az infrastruktúra, és a megoldás szolgáltatója szeretné kezelni a vész-helyreállítást.

![architektúra – közös – HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Felügyelt szolgáltatás forgatókönyve több vCenter**

## <a name="next-steps"></a>Következő lépések
- [További](site-recovery-role-based-linked-access-control.md) információ a site Recovery szerepköralapú hozzáférés-vezérléséről.
- Ismerje meg, hogyan [állíthatja be a VMWare virtuális gépek vész-helyreállítását az Azure-](vmware-azure-tutorial.md)ba.
- További információ a [VMWare virtuális gépekhez készült CSP-vel rendelkező többszörös bérletről](vmware-azure-multi-tenant-csp-disaster-recovery.md).
