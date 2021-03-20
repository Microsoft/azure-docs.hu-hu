---
title: A konfigurációs kiszolgáló üzembe helyezése Azure Site Recovery
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy konfigurációs kiszolgálót a VMware vész-helyreállításhoz Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359815"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

Ha [Azure site Recoveryt](site-recovery-overview.md) használ a VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, a helyszíni konfigurációs kiszolgálót is üzembe kell helyeznie. A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Emellett az adatreplikálást is kezeli. Ez a cikk végigvezeti a konfigurációs kiszolgáló üzembe helyezéséhez szükséges lépéseken, amikor a VMware virtuális gépeket az Azure-ba replikálja. Ha konfigurációs kiszolgálót kell beállítania a fizikai kiszolgálók replikálásához, tekintse meg [a konfigurációs kiszolgáló beállítása a fizikai kiszolgálók Azure-ba való vész-helyreállításához](physical-azure-set-up-source.md)című témakört.

> [!TIP]
> A konfigurációs kiszolgáló Azure Site Recovery architektúra részeként való szerepkörének megismeréséhez tekintse meg a [VMware – Azure vész-helyreállítási architektúrát](vmware-azure-architecture.md)ismertető témakört.

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Konfigurációs kiszolgáló üzembe helyezése PETESEJT-sablonnal

A konfigurációs kiszolgálót egy olyan, magasan elérhető VMware virtuális gépnek kell beállítani, amely bizonyos minimális hardver-és méretezési követelményekkel rendelkezik. A kényelmes és egyszerű üzembe helyezés érdekében a Site Recovery egy letölthető, nyílt virtualizációs alkalmazási (PETESEJT-) sablont biztosít a konfigurációs kiszolgáló beállításához, amely megfelel az itt felsorolt összes előírt követelménynek.

## <a name="prerequisites"></a>Előfeltételek

A konfigurációs kiszolgáló minimális hardverkövetelmények a következő részben vannak összefoglalva.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory engedélyekre vonatkozó követelmények

A konfigurációs kiszolgáló Azure Site Recovery-szolgáltatásokkal való regisztrálásához rendelkeznie kell egy olyan felhasználóval, aki a Azure Active Directory (Azure AD) beállításban szerepel a következő engedélyek egyikével.

1. Alkalmazás létrehozásához a felhasználónak alkalmazás-fejlesztői szerepkörrel kell rendelkeznie.
    - Az ellenőrzéshez jelentkezzen be a Azure Portalba.</br>
    - Lépjen **Azure Active Directory**  >  **szerepkörök és rendszergazdák** lehetőségre.</br>
    - Ellenőrizze, hogy az alkalmazás fejlesztői szerepköre hozzá van-e rendelve a felhasználóhoz. Ha nem, használja ezt az engedélyt használó felhasználót, vagy forduljon [a rendszergazdához, és engedélyezze az engedélyt](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Ha az alkalmazás fejlesztői szerepköre nem rendelhető hozzá, győződjön meg arról, hogy a **felhasználók regisztrálhatják az alkalmazások** jelölőjét **true (igaz** ) értékre állítva, ha a felhasználó identitást hoz létre. Az engedélyek engedélyezése:
    - Jelentkezzen be az Azure Portalra.
    - Lépjen **Azure Active Directory**  >  **felhasználói beállítások menüpontra**.
    - A **Alkalmazásregisztrációk** alatt a **felhasználók regisztrálhatják az alkalmazásokat**. Válassza az **Igen** lehetőséget.

      ![Azure-AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> A Active Directory összevonási szolgáltatások (AD FS) *nem támogatott*. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)használatával felügyelt fiókot használjon.

## <a name="download-the-template"></a>A sablon letöltése

1. A tárolóban váltson az **infrastruktúra előkészítése**  >  **forrásra**.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló PETESEJTJEI-sablonját.

   > [!TIP]
   >A konfigurációs kiszolgáló sablonjának legújabb verzióját közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver)is letöltheti.

> [!NOTE]
> A petesejtek sablonnal megadott licenc egy 180 napos próbaverziós licenc. Ezen időszak után be kell szereznie egy licencet.

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre az VMware vSphere-ügyfél használatával.
2. A **fájl** menüben válassza a **OVF-sablon telepítése** lehetőséget a OVF- **sablon központi telepítése** varázsló elindításához.

     ![OVF-sablon üzembe helyezése](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben. A dinamikus kiépítési beállítás a konfigurációs kiszolgáló teljesítményére is hatással lehet.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * Ha az alapértelmezett beállításokkal szeretné beállítani a virtuális gépet, válassza a **bekapcsolás lehetőséget az üzembe helyezés**  >  **befejezése** után.
    * További hálózati adapter hozzáadásához törölje **a bekapcsolást a telepítés után**, majd kattintson a **Befejezés gombra**. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

> [!IMPORTANT]
> Ne módosítsa az erőforrás-konfigurációkat, például a memóriát, a magokat és a CPU-korlátozást, vagy a telepítés után módosítsa vagy törölje a telepített szolgáltatásokat vagy fájlokat a konfigurációs kiszolgálón. Az ilyen típusú változások hatással vannak a konfigurációs kiszolgáló Azure-szolgáltatásokkal való regisztrálására és a konfigurációs kiszolgáló teljesítményére.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

> [!NOTE]
> Két hálózati adapterre van szükség, ha azt tervezi, hogy megtartja a feladatátvételhez használt forrásoldali gépek IP-címeit, és később vissza szeretné állítani a helyszíni feladatokat. Az egyik hálózati adapter csatlakoztatva van a forrásoldali gépekhez, és a másik hálózati adaptert használja az Azure-kapcsolathoz.

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, adja hozzá azt a tárolóban lévő kiszolgáló regisztrálása előtt. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. Ha a virtuális hálózati adaptert a virtuális gép bekapcsolása után szeretné összekapcsolni, válassza a **Csatlakozás a Power-on** lehetőséget. Ezután válassza a **következő**  >  **Befejezés**  >  **OK** elemet.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>A konfigurációs kiszolgáló regisztrálása Azure Site Recovery szolgáltatásokkal

1. A VMware vSphere-ügyfél konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Amikor először jelentkezik be, néhány másodpercen belül elindul a Azure Site Recovery konfigurációs eszköz.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.</br>
    a. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.</br>
    b. Győződjön meg arról, hogy a kiválasztott felhasználói fiók rendelkezik engedéllyel az Azure-beli alkalmazás létrehozásához. A szükséges engedélyek engedélyezéséhez kövesse az [Azure Active Directory engedélyekkel kapcsolatos követelmények](#azure-active-directory-permission-requirements)című szakaszban található útmutatást.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodperc múlva automatikusan elindul.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **kapcsolat beállítása** lehetőséget. A legördülő listából válassza ki azt a hálózati adaptert, amelyet a beépített Process Server használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásoldali gépeken. Ezután válassza ki azt a hálózati adaptert, amelyet a konfigurációs kiszolgáló használ az Azure-hoz való kapcsolódáshoz. Kattintson a **Mentés** gombra. Ez a beállítás a konfigurálás után nem módosítható. Ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt IP-cím statikus IP-cím, nem DHCP-IP-cím.
2. A **Recovery Services**-tároló kiválasztása lapon jelentkezzen be a Microsoft Azure a [konfigurációs kiszolgáló regisztrálása a Azure site Recovery Services szolgáltatással](#register-the-configuration-server-with-azure-site-recovery-services)című 6. lépésében használt hitelesítő adatokkal.
3. Bejelentkezés után válassza ki az Azure-előfizetését, valamint a megfelelő erőforráscsoportot és tárolót.

    > [!NOTE]
    > A regisztráció után nincs rugalmasság a Recovery Services-tároló módosítására.
    > A Recovery Services-tárolók módosítása a konfigurációs kiszolgáló leállítását igényli az aktuális tárból, és a konfigurációs kiszolgáló alatt lévő összes védett virtuális gép replikálása leállt. További információ: [a konfigurációs kiszolgáló kezelése a VMWare virtuális gép vész-helyreállításához](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. **Külső gyártótól származó szoftverek telepítése**:

    |Eset   |Követendő lépések  |
    |---------|---------|
    |Letölthetem és telepíthetem a MySQL-t manuálisan?     |  Igen. Töltse le a MySQL-alkalmazást, helyezze azt a **C:\Temp\ASRSetup** mappába, majd telepítse manuálisan. A feltételek elfogadása és a **letöltés és telepítés** kiválasztása után a portál *már telepítve van*. Folytassa a következő lépéssel.       |
    |El tudom kerülni a MySQL online letöltését?     |   Igen. Helyezze a MySQL Installer-alkalmazást a **C:\Temp\ASRSetup** mappába. Fogadja el a feltételeket, válassza a **letöltés és telepítés** lehetőséget, és a portál az alkalmazás telepítéséhez hozzáadott telepítőt használja. A telepítés befejeződése után folytassa a következő lépéssel.    |
    |A MySQL-t a Azure Site Recovery használatával szeretném letölteni és telepíteni.    |  Fogadja el a licencszerződést, és válassza a **letöltés és telepítés** lehetőséget. A telepítés befejeződése után folytassa a következő lépéssel.       |

5. A **berendezés konfigurációjának ellenőrzésekor** a folytatás előtt ellenőrizze az előfeltételek ellenőrzését.
6. A **vCenter Server/vSphere ESXi-kiszolgáló konfigurálása** területen adja meg annak a vCenter-kiszolgálónak vagy vSphere-állomásnak a teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**  >  **Folytatás** lehetőséget. Az itt megadott hitelesítő adatok helyileg mentve.
8. A **virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a virtuális gépek felhasználónevét és jelszavát, hogy a replikáció során automatikusan telepítse a mobilitási szolgáltatást. **Windows rendszerű** gépek esetén a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. **Linux** esetén adja meg a legfelső szintű fiók részleteit.
9. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
10. A regisztráció befejeződése után nyissa meg a Azure Portal, és ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel-e a **Recovery Services**-tárolóban  >    >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** kezelése.

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló legújabb verzióra történő frissítésével kapcsolatban lásd: [a konfigurációs kiszolgáló kezelése a VMWare virtuális gép vész-helyreállításához](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Az összes Site Recovery-összetevő frissítésével kapcsolatos utasításokért tekintse meg a [site Recovery szolgáltatás frissítései](service-updates-how-to.md)című témakört.

## <a name="manage-the-configuration-server"></a>A konfigurációs kiszolgáló kezelése

A folyamatban lévő replikáció megszakításának elkerülése érdekében győződjön meg arról, hogy a konfigurációs kiszolgáló IP-címe nem változik, miután a konfigurációs kiszolgáló regisztrálva van egy tárolóban. A gyakori konfigurációs kiszolgáló felügyeleti feladatokkal kapcsolatos további tudnivalókért lásd: [a konfigurációs kiszolgáló kezelése a VMWare virtuális](vmware-azure-manage-configuration-server.md)gépek vész-helyreállítási feladataihoz.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

Az üzembe helyezési & kapcsolódási problémák megoldásához tekintse meg a [hibaelhárítással](vmware-azure-troubleshoot-configuration-server.md) foglalkozó cikket.

## <a name="faqs"></a>Gyakori kérdések

* Mennyi ideig érvényes a licenc a OVF-en keresztül üzembe helyezett konfigurációs kiszolgálón? Mi történik, ha nem tudom újraaktiválni a licencet?

    A petesejtek sablonnal megadott licenc 180 napig érvényes próbaverzió. A lejárat előtt aktiválnia kell a licencet. Ellenkező esetben előfordulhat, hogy a konfigurációs kiszolgáló gyakori leállítása és a replikálási tevékenységek akadályozása. További információ: [a konfigurációs kiszolgáló kezelése VMWare virtuális gép](vmware-azure-manage-configuration-server.md#update-windows-license)vész-helyreállítási feladatához.

* Használhatom azt a virtuális gépet, amelyen a konfigurációs kiszolgáló különböző célokra van telepítve?

    Nem. A virtuális gépet a konfigurációs kiszolgáló kizárólagos céljára használhatja. Győződjön meg arról, hogy a vész-helyreállítás hatékony kezelésének [előfeltételei](#prerequisites) között szereplő összes specifikációt követi.
* Átválthatom a tárolót a konfigurációs kiszolgálón már regisztrált új tárolóval?

    Nem. Miután regisztrálta a tárolót a konfigurációs kiszolgálóval, nem módosítható.
* Használhatom ugyanazt a konfigurációs kiszolgálót mind a fizikai, mind a virtuális gépek elleni védelemhez?

    Igen. Ugyanazt a konfigurációs kiszolgálót használhatja a fizikai és a virtuális gépek replikálásához. A fizikai gép azonban csak a VMware virtuális gépeken végezhető el.
* Mi a konfigurációs kiszolgáló célja, és hol van használatban?

    Ha többet szeretne megtudni a konfigurációs kiszolgálóról és annak működéséről, olvassa el a [VMware – Azure replikálási architektúra](vmware-azure-architecture.md)című témakört.
* Hol találhatom meg a konfigurációs kiszolgáló legújabb verzióját?

    A konfigurációs kiszolgáló portálon keresztüli frissítésének lépéseiért lásd: [a konfigurációs kiszolgáló frissítése](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Az összes Site Recovery-összetevő frissítésével kapcsolatos utasításokért tekintse meg a [site Recovery szolgáltatás frissítései](./service-updates-how-to.md)című témakört.
* Honnan tölthetők le a konfigurációs kiszolgáló jelszava?

    A jelszó letöltéséhez lásd: [a konfigurációs kiszolgáló kezelése a VMWare virtuális gép vész-helyreállítási szolgáltatásához](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Módosítható a jelszó?

    Nem. Ne módosítsa a konfigurációs kiszolgáló jelszavát. A jelszó módosítása megszakítja a védett gépek replikálását, és kritikus állapotba vezet.
* Honnan tölthetők le a tároló regisztrációs kulcsai?

    **Recovery Services**-tárolóban válassza a  >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** kezelése lehetőséget. A **kiszolgálók** területen válassza a **regisztrációs kulcs letöltése** lehetőséget a tár hitelesítő adatainak fájljának letöltéséhez.
* Megtehetem a meglévő konfigurációs kiszolgáló klónozását és a replikálási folyamathoz való használatát?

    Nem. A klónozott konfigurációs kiszolgáló összetevő használata nem támogatott. Egy kibővíthető folyamat kiszolgálójának klónozása nem támogatott forgatókönyv is. A klónozás Site Recovery összetevők hatással vannak a folyamatos replikációra.

* Módosíthatom a konfigurációs kiszolgáló IP-címét?

    Nem. Ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-címek, nem pedig DHCP-IP-címek.
* Állíthatok be egy konfigurációs kiszolgálót az Azure-ban?

    Állítsa be a konfigurációs kiszolgálót egy helyszíni környezetben közvetlen, a v-központtal ellátott, és az adatátviteli késések csökkentése érdekében. A konfigurációs kiszolgáló ütemezett biztonsági mentését feladat- [visszavételi célokra](vmware-azure-manage-configuration-server.md#failback-requirements)is elvégezheti.

* Módosíthatom a gyorsítótár-illesztőprogramot a konfigurációs kiszolgálón vagy a kibővíthető folyamat kiszolgálóján?

    Nem, a gyorsítótár-illesztőprogram nem módosítható a beállítás befejeződése után.

További gyakori kérdések a konfigurációs kiszolgálókról: [konfigurációs kiszolgáló gyakori kérdései](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Következő lépések

Állítsa be a [VMWare virtuális gépek](vmware-azure-tutorial.md) vész-helyreállítását az Azure-ba.