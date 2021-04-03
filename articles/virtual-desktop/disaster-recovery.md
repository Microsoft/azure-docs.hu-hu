---
title: A Windows rendszerű virtuális asztali vész-helyreállítási terv beállítása – Azure
description: Üzletmenet-folytonossági és vész-helyreállítási terv beállítása a Windows rendszerű virtuális asztali környezethez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91935704"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Üzletmenet-folytonossági és vész-helyreállítási terv beállítása

A szervezet adatai biztonságának megőrzése érdekében szükség lehet egy üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia elfogadására. A hangalapú BCDR stratégia a tervezett és nem tervezett szolgáltatások, illetve az Azure-kimaradások során folyamatosan működik az alkalmazások és a számítási feladatok.

A Windows rendszerű virtuális asztali szolgáltatások BCDR biztosít a Windows rendszerű virtuális asztali szolgáltatás számára, hogy megőrizze az ügyfelek metaadatait az kimaradások során. Ha egy adott régióban áramkimaradás lép fel, a szolgáltatás-infrastruktúra összetevői átveszik a feladatátvételt a másodlagos helyre, és a szokásos módon folytatják a működést. Továbbra is hozzáférhet a szolgáltatással kapcsolatos metaadatokhoz, és a felhasználók továbbra is csatlakozhatnak az elérhető gazdagépekhez. A végfelhasználói kapcsolatok online állapotban maradnak, amíg a bérlői környezet vagy a gazdagépek továbbra is elérhetők maradnak.

Annak érdekében, hogy a felhasználók továbbra is kapcsolódhatnak a régió meghibásodása során, egy másik helyen kell replikálni a virtuális gépeket (VM). A leállások során az elsődleges hely feladatátvételt hajt végre a másodlagos helyen található replikált virtuális gépeken. A felhasználók megszakítás nélkül is hozzáférhetnek az alkalmazásokhoz a másodlagos helyről. A virtuális gépek replikálásához a felhasználói identitásokat a másodlagos helyen kell megtartani. Ha profil-tárolókat használ, a replikálást is el kell végeznie. Végezetül gondoskodjon arról, hogy az elsődleges helyen lévő adatokra támaszkodó üzleti alkalmazások feladatátvételt végezzenek a többi adat használatával.

Ha szeretné összefoglalni, hogy a felhasználók a leállás során is csatlakoztatva maradjanak, a következő műveleteket kell végrehajtania ebben a sorrendben:

- Replikálja a virtuális gépeket egy másodlagos helyen.
- Ha profil-tárolókat használ, állítsa be az adatreplikálást a másodlagos helyen.
- Győződjön meg arról, hogy az elsődleges helyen beállított felhasználói identitások elérhetők a másodlagos helyen.
- Győződjön meg arról, hogy az elsődleges helyen található adatra támaszkodó üzletági alkalmazások feladatátvétele a másodlagos helyre történik.

## <a name="vm-replication"></a>VM-replikáció

Először replikálnia kell a virtuális gépeket a másodlagos helyre. Az ehhez szükséges lehetőségek a virtuális gépek konfigurálásának módjától függenek:

- Az összes virtuális gépet beállíthatja úgy, hogy mind a készletezett, mind a személyes gazdagépek készletét Azure Site Recovery. Ezzel a módszerrel csak egy gazdagépet és a hozzá tartozó alkalmazás-csoportokat és munkaterületeket kell beállítania.
- Létrehozhat egy új címkészletet a feladatátvételi régióban, miközben a feladatátvételi hely összes erőforrása ki van kapcsolva. Ennél a módszernél új alkalmazás-csoportokat és munkaterületeket kell beállítania a feladatátvételi régióban. Ezután használhat egy Azure Site Recovery tervet a gazdagép-készletek bekapcsolásához.
- Létrehozhat egy olyan gazdagépet, amelyet az elsődleges és feladatátvételi régióban létrehozott virtuális gépek töltenek fel, miközben a virtuális gépeket a feladatátvételi régióban kikapcsolva tartja. Ebben az esetben csak egy gazdagépet és a hozzá tartozó alkalmazás-csoportokat és munkaterületeket kell beállítania. Ezzel a módszerrel a gazdagép-készletek bekapcsolásához Azure Site Recovery tervet használhat.

Javasoljuk, hogy az [Azure-to-Azure vész-helyreállítási architektúrában](../site-recovery/azure-to-azure-architecture.md)ismertetett módon kezelje a virtuális gépek replikálását más Azure-helyszíneken található [Azure site Recovery](../site-recovery/site-recovery-overview.md) használatával. Különösen ajánljuk a Azure Site Recovery használatát a személyes gazdagépek számára, mivel a Azure Site Recovery [kiszolgáló-és ügyfél-alapú SKU-](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)ket is támogat.

Ha Azure Site Recovery használ, nem kell manuálisan regisztrálnia ezeket a virtuális gépeket. A másodlagos virtuális gépen a Windows rendszerű virtuális asztali ügynök automatikusan a legújabb biztonsági jogkivonat használatával csatlakozik a hozzá legközelebb eső szolgáltatási példányhoz. A másodlagos helyen található virtuális gép (munkamenet-gazdagép) automatikusan a gazdagép részévé válik. A végfelhasználónak újra csatlakoznia kell a folyamat során, de ettől függetlenül nincs más manuális művelet.

Ha a leállás során meglévő felhasználói kapcsolatok vannak, mielőtt a rendszergazda el tudja indítani a feladatátvételt a másodlagos régióba, le kell állítania a felhasználói kapcsolatokat az aktuális régióban.

Ha le szeretné bontani a felhasználókat a Windows Virtual Desktopban (klasszikus), futtassa a következő parancsmagot:

```powershell
Invoke-RdsUserSessionLogoff
```

Ha le szeretné bontani a felhasználókat a Windows rendszerű virtuális asztal Azure-integrált verziójában, futtassa a következő parancsmagot:

```powershell
Remove-AzWvdUserSession
```

Az elsődleges régió összes felhasználójának kijelentkezését követően az elsődleges régióban lévő virtuális gépeket átadhatja, és lehetővé teheti a felhasználók számára a másodlagos régióban lévő virtuális gépekhez való kapcsolódást. A folyamat működésével kapcsolatos további információkért lásd: [Azure-beli virtuális gépek replikálása egy másik Azure-régióba](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Virtuális hálózat

Ezután vegye figyelembe a hálózati kapcsolatot a leállás során. Győződjön meg arról, hogy beállította a virtuális hálózatot (VNET) a másodlagos régióban. Ha a felhasználóknak hozzá kell férniük a helyszíni erőforrásokhoz, konfigurálnia kell ezt a VNET az eléréséhez. A helyszíni kapcsolatokat VPN-, ExpressRoute-vagy virtuális WAN-kapcsolattal is létrehozhatja.

Javasoljuk, hogy az Azure Site Recovery használatával állítsa be a VNET a feladatátvételi régióban, mert megőrzi az elsődleges hálózat beállításait, és nincs szükség a társításra.

## <a name="user-identities"></a>Felhasználói identitások

Ezután ellenőrizze, hogy a tartományvezérlő elérhető-e a másodlagos helyen. 

A tartományvezérlőt három módon lehet megőrizni:

   - Active Directory-tartomány vezérlő a másodlagos helyen
   - Helyszíni Active Directory-tartomány vezérlő használata
   - Active Directory-tartomány vezérlő replikálása [Azure site Recovery](../site-recovery/site-recovery-active-directory.md) használatával

## <a name="user-and-app-data"></a>Felhasználói és alkalmazásadatok

Ha profil-tárolókat használ, a következő lépés az adatreplikálás beállítása a másodlagos helyen. A FSLogix-profilok tárolására öt lehetőség áll rendelkezésre:

   - Közvetlen tárolóhelyek (S2D)
   - Hálózati meghajtók (virtuális gép extra meghajtókkal)
   - Azure Files
   - Azure NetApp Files
   - Felhőbeli gyorsítótár replikációhoz

További információkért tekintse meg a [FSLogix-profilok tárolási beállításait a Windows rendszerű virtuális asztalon](store-fslogix-profile.md).

Ha a profilok esetében vész-helyreállítást állít be, a következő lehetőségek közül választhat:

   - Állítsa be a natív Azure-replikációt (például Azure Files a standard Storage-fiók replikálását, Azure NetApp Files replikálást vagy a fájlkiszolgálók Azure Files szinkronizálását).
    
     >[!NOTE]
     >A NetApp-replikáció az első beállítása után automatikusan megtörténik. A Azure Site Recovery-csomagokkal a nem VM-erőforrások feladatátvétele érdekében az Azure Storage-erőforrások replikálásához parancsfájlokat és parancsfájl-közzétételi feladatokat adhat hozzá.

   - Állítsa be az FSLogix Cloud cache-t az alkalmazás-és a felhasználói adatértékekhez.
   - Állítsa be a vész-helyreállítást csak az alkalmazásadatok számára, hogy mindig biztosítsa az üzleti szempontból kritikus fontosságú adatszolgáltatásokhoz való hozzáférést. Ezzel a módszerrel lekérheti a felhasználói adatvesztést a leállás után.

Vessünk egy pillantást a FSLogix konfigurálására az egyes lehetőségek vész-helyreállításának beállításához.

### <a name="fslogix-configuration"></a>FSLogix-konfiguráció

A FSLogix-ügynök több Profilos helyet is támogat, ha a FSLogix beállításjegyzékbeli bejegyzéseit konfigurálja.

A beállításjegyzék bejegyzéseinek konfigurálása:

1. Nyissa meg a **Beállításszerkesztőt**.
2. Nyissa meg a **számítógép**  >  **HKEY_LOCAL_MACHINE**  >  **szoftver**  >  **FSLogix**  >  **profilokat**.
   
     > [!div class="mx-imgBorder"]
     > ![A Rendszerleíróadatbázis-szerkesztő profilok ablakának képernyőképe. A VHDLocation ki van választva.](media/regedit-profiles.png)

3. Kattintson a jobb gombbal a **VHDLocations** elemre, és válassza a **többszörös karakterlánc szerkesztése** lehetőséget.

     > [!div class="mx-imgBorder"]
     > ![A többszörös karakterlánc szerkesztése ablak képernyőképe. Az érték mező az USA és az USA keleti régiójának Centrual sorolja fel.](media/multi-string-edit.png)

4. Az **érték** mezőben adja meg a használni kívánt helyet.
5. Amikor elkészült, válassza az **OK** lehetőséget.

Ha az első hely nem érhető el, a FSLogix-ügynök automatikusan átadja a feladatátvételt a második felé, és így tovább.

Javasoljuk, hogy a FSLogix-ügynököt a fő régió másodlagos helyének elérési útjával konfigurálja. Az elsődleges hely leállítása után a FLogix-ügynök a virtuális gép Azure Site Recovery replikációjának részeként replikálja a rendszer. Ha a replikált virtuális gépek készen állnak, az ügynök automatikusan megkísérli a másodlagos régió elérését.

Tegyük fel például, hogy az elsődleges munkamenet-gazda virtuális gépek az USA középső régiójában vannak, de a profil tárolója az USA középső régiójában található, teljesítménybeli okokból.

Ebben az esetben a FSLogix-ügynököt az USA középső régiójában található tároló elérési útjával konfigurálja. A munkamenet-gazda virtuális gépeket úgy kell konfigurálni, hogy az USA nyugati régiójában replikálódjon. Miután az USA középső régiójának elérési útja sikertelen, az ügynök megpróbál létrehozni egy új elérési utat a Storage-ban az USA nyugati régiójában.

### <a name="s2d"></a>S2D

Mivel a S2D belsőleg kezeli a régiók közötti replikációt, nem kell manuálisan beállítania a másodlagos elérési utat.

### <a name="network-drives-vm-with-extra-drives"></a>Hálózati meghajtók (virtuális gép extra meghajtókkal)

Ha a hálózati tároló virtuális gépeket olyan Azure Site Recovery használatával replikálja, mint a munkamenet-gazda virtuális gépek, akkor a helyreállítás ugyanezt az elérési utat tartja, ami azt jelenti, hogy nem kell újrakonfigurálnia a FSlogix.

### <a name="azure-files"></a>Azure Files

Azure Files támogatja a régiók közötti aszinkron replikálást, amelyet a Storage-fiók létrehozásakor megadhat. Ha a Azure Files aszinkron jellege már tartalmazza a vész-helyreállítási célokat, akkor nem kell további konfigurálást végeznie.

Ha szinkron replikálásra van szüksége az adatvesztés csökkentése érdekében, javasoljuk, hogy használjon helyette a FSLogix Cloud cache-t.

>[!NOTE]
>Ez a szakasz nem fedi le Azure Files feladatátvételi hitelesítési mechanizmusát.

### <a name="azure-netapp-files"></a>Azure NetApp Files

További információ a Azure NetApp Files a [replikálási társítás létrehozása a Azure NetApp Files számára](../azure-netapp-files/cross-region-replication-create-peering.md)című témakörben található.

## <a name="app-dependencies"></a>Alkalmazás függőségei

Végezetül győződjön meg arról, hogy az elsődleges régióban található adatokra támaszkodó üzleti alkalmazások feladatátvételt végezhetnek a másodlagos helyre. Győződjön meg arról is, hogy az alkalmazások új helyen való működéséhez szükséges beállításokat konfigurálja. Ha például az egyik alkalmazás az SQL-háttértől függ, győződjön meg arról, hogy az SQL-t replikálja a másodlagos helyen. Az alkalmazást úgy kell beállítani, hogy a másodlagos helyet használja a feladatátvételi folyamat részeként, vagy az alapértelmezett konfigurációként. Azure Site Recovery csomagokban modellezheti az alkalmazások függőségeit. További információt a [helyreállítási tervekről szóló](../site-recovery/recovery-plan-overview.md)témakörben talál.

## <a name="disaster-recovery-testing"></a>Vész-helyreállítási tesztelés

Miután végzett a vész-helyreállítás beállításával, tesztelje a tervet, és győződjön meg róla, hogy működik.

Íme néhány javaslat a terv teszteléséhez:

- Ha a teszt virtuális gépek rendelkeznek internet-hozzáféréssel, a rendszer átveszi a meglévő munkamenet-gazdagépet az új kapcsolatokhoz, de az eredeti munkamenet-gazdagéphez való összes meglévő kapcsolat aktív marad. Győződjön meg arról, hogy a tesztet futtató rendszergazda az összes aktív felhasználót aláírja a terv tesztelése előtt. 
- A karbantartási időszakokban csak a teljes vész-helyreállítási teszteket kell végrehajtania, hogy ne zavarja a felhasználókat. A teszteléshez az ellenőrzési környezetben is használhat egy gazdagépet. 
- Győződjön meg arról, hogy a teszt az összes üzletileg kritikus alkalmazást magában foglalja.
- Javasoljuk, hogy egyszerre csak a 100 virtuális gépek feladatátvételét hajtsa végre. Ha ennél több virtuális gép van, akkor azt javasoljuk, hogy a kötegekben 10 percen belül feladatátvételt hajtson végre.

## <a name="next-steps"></a>Következő lépések

Ha kérdése van az adatok biztonságának megőrzéséhez az kimaradások megtervezése mellett, tekintse meg a [biztonsági útmutatót](security-guide.md).