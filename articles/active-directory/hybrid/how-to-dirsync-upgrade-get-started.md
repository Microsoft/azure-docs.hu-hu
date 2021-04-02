---
title: 'Azure AD Connect: Frissítés a DirSyncről | Microsoft Docs'
description: Tudja meg, hogyan frissíthet a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra. Ez a cikk a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra való frissítés lépéseit ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996525"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Frissítés a DirSync szolgáltatásról
Az Azure AD Connect a DirSync utóda. A DirSync szolgáltatásról való frissítés lehetőségeit találja meg ebben a témakörben. Ezek lépések nem használhatóak az Azure AD Connect másik kiadásáról vagy az Azure AD Syncről való frissítéshez.

Mielőtt elkezdené telepíteni az Azure AD Connect szolgáltatást, bizonyosodjon meg róla, hogy [letöltötte az Azure AD Connectet](https://go.microsoft.com/fwlink/?LinkId=615771), és elvégezte az [Azure AD Connect: Hardware and prerequisites](how-to-connect-install-prerequisites.md) (Azure AD Connect: hardver és előfeltételek) témakörben leírt lépéseket. Különösen, ha a következőkről kíván olvasni, mivel ezek a területek különböznek a DirSynctől:

* A .NET és a PowerShell szükséges verziója. A kiszolgálón újabb verzióknak kell lenniük, mint amelyek a DirSynchez szükségesek.
* A proxyiszolgáló konfigurálása. A frissítés előtt konfigurálnia kell ezt a beállítást, ha proxykiszolgálóval csatlakozik az internethez. A DirSnyc mindig a telepítést végző felhasználóhoz konfigurált proxykiszolgálót használta, de az Azure AD Connect ehelyett a gép beállításait használja.
* Az URL-eket a proxykiszolgálón keresztül kell megnyitni. A DirSync által is támogatott alapszintű forgatókönyvekben a követelmények ugyanezek. Ha az Azure AD Connectben található új szolgáltatások valamelyikét szeretné használni, néhány új URL címet kell megnyitnia.

> [!NOTE]
> Miután engedélyezte, hogy az új Azure AD Connect-kiszolgáló megkezdje a módosítások szinkronizálását az Azure AD-ba, nem kell visszaállnia az rSync vagy a Azure AD-szinkronizáló használatára. A Azure AD Connectról örökölt ügyfelekre való visszalépés nem támogatott, beleértve a következőt: az rSync és a Azure AD-szinkronizáló, és olyan problémákhoz vezethet, mint például az adatvesztés az Azure AD-ben.

Ha nem a DirSync szolgáltatásról frissít, tekintse meg az egyéb forgatókönyvek vonatkozó dokumentációját.

## <a name="upgrade-from-dirsync"></a>Frissítés a DirSync szolgáltatásról
A DirSync jelenleg aktív üzemelő példányától függően több lehetőség is létezik a frissítésre. Ha a várt frissítési idő kevesebb mint három óra, akkor a helyben történő frissítés javasolt. Ha a várt frissítési idő több mint három óra, akkor a párhuzamos üzembe helyezés javasolt egy másik kiszolgálón. A becslések szerint 50 000-nél több objektum esetén a frissítés több mint három órát vesz igénybe.

| Eset |
| --- |
| [Frissítés helyben](#in-place-upgrade) |
| [Párhuzamos üzembe helyezés](#parallel-deployment) |

> [!NOTE]
> Amikor a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra frissít, ne telepítse a DirSyncet a frissítés előtt. Az Azure AD Connect beolvassa és áttelepíti a konfigurációt a DirSyncről, és eltávolítja azt a kiszolgáló vizsgálatát követően.

**Frissítés helyben**  
A varázsló mutatja a frissítés befejeztének várható idejét. Ez a becslés azon a feltételezésen alapul, hogy egy 50 000 objektumot (felhasználókat, névjegyeket és csoportokat) tartalmazó adatbázis frissítése három órát vesz igénybe. Ha az adatbázis 50 000-nél kevesebb objektumot tartalmaz, az Azure AD Connect helyben történő frissítést javasol. Ha a folytatást választja, az aktuális beállítások automatikusan alkalmazva lesznek a frissítés folyamán, és a kiszolgáló automatikusan folytatja az aktív szinkronizálást.

Amennyiben konfiguráció-áttelepítést és párhuzamos üzembe helyezést kíván végezni, felülbírálhatja a helyben történő frissítésre tett javaslatot. Például ez remek alkalom lehet arra, hogy frissítse a hardvert és az operációs rendszert. További információért lásd a [párhuzamos üzembe helyezés](#parallel-deployment) szakaszt.

**Párhuzamos üzembe helyezés**  
Ha 50 000-nél több objektummal rendelkezik, párhuzamos üzembe helyezés ajánlott. Ezt az üzembehelyezési módszert választva a felhasználók nem tapasztalnak semmilyen késedelmet a rendszer működésében. Az Azure AD Connect telepítés megkísérli megbecsülni a frissítés hátralévő idejét, azonban amennyiben már végzett korábban is DirSync-frissítést, saját tapasztalatai alapján valószínűleg jobb becsléseket adhat.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Támogatott frissíthető DirSync-konfigurációk
A frissített DirSync az alábbi konfigurációmódosításokat támogatja:

* Tartományok és szervezeti egységek szűrése
* Másik azonosító (UPN)
* Jelszó-szinkronizálás és Exchange hibrid beállítások
* Erdők/tartományok és az Azure AD beállítási
* Szűrés felhasználói attribútumok alapján

Az alábbi módosítás nem frissíthető. Amennyiben rendelkezik ezzel a konfigurációval, a frissítés blokkolva lesz:

* Nem támogatott DirSync-módosítások, például eltávolított attribútumok, vagy egyedi bővítmény-DLL használata

![Frissítés blokkolva](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

Ezekben az esetekben az ajánlott megoldás egy új Azure AD Connect kiszolgáló telepítése [átmeneti módban](how-to-connect-sync-staging-server.md), és a régi DirSync és az új Azure AD Connect konfiguráció ellenőrzése. Alkalmazza újra az összes módosítást egyéni konfiguráció használatával az [Azure AD Connect Sync custom configuration](how-to-connect-sync-whatis.md) (Azure AD Connect szinkronizálása egyéni konfigurációval) szakaszban foglaltak szerint.

A DirSync által a szolgáltatásfiókokhoz használt jelszavak nem kérhetőek le, és nem lesznek áttelepítve. A jelszavak vissza lesznek állítva a frissítés során.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>A DirSync szolgáltatásról az Azure AD Connect szolgáltatásra való frissítés magas szintű lépései
1. Üdvözli az Azure AD Connect
2. Az aktuális DirSync-konfiguráció elemzése
3. Az Azure AD globális rendszergazdai jelszavának begyűjtése
4. Egy vállalati rendszergazdai fiók hitelesítő adatainak begyűjtése (csak az Azure AD Connect telepítése során való használatra)
5. Az Azure AD Connect telepítése
   * A DirSync eltávolítása (vagy ideiglenes letiltása)
   * Az Azure AD Connect telepítése
   * Választhatóan a szinkronizálás indítása

További lépések végrehajtása szükséges, ha:

* Jelenleg teljes SQL Server rendszert használ – akár helyit, akár távolit
* 50 000-nél több objektummal rendelkezik a szinkronizálás hatókörében

## <a name="in-place-upgrade"></a>Frissítés helyben
1. Indítsa el az Azure AD Connect telepítőt (MSI).
2. Tekintse át és fogadja el a licencfeltételeket és az adatvédelmi nyilatkozatot.  
   ![Üdvözli az Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Kattintson a Tovább gombra a meglévő DirSync telepítés elemzésének indításához.  
   ![A meglévő Directory Sync telepítés elemzése](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Az elemzés végeztével a rendszer ajánlásokat tesz a következő lépésekre.  
   * Ha SQL Server Expresst használ, és kevesebb mint 50 000 objektummal rendelkezik, a következő képernyő jelenik meg:  
     ![Elemzés befejezve, készen áll a frissítésre a DirSyncről](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Ha teljes SQL Servert használ a DirSynchez, ehelyett a következő képernyő jelenik meg:  
     ![a meglévő SQL Database-kiszolgálót bemutató képernyőkép.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     A DirSync által használt meglévő SQL Server adatbázis-kiszolgálóra vonatkozó információk megjelennek. Amennyiben szükséges, végezze el a megfelelő módosításokat. A telepítés folytatásához kattintson a **Next** (Tovább) gombra.
   * Ha 50 000-nél több objektummal rendelkezik, ehelyett a következő képernyő jelenik meg:  
     ![Képernyőfelvétel: Ez a képernyő jelenik meg, ha több mint 50 000 objektumot szeretne frissíteni.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Amennyiben helyben történő frissítést kíván végezni, kattintson a jelölőnégyzetre az üzenet mellett: **Continue upgrading DirSync on this computer** (A DirSync frissítésének folytatása ezen a számítógépen).
     Amennyiben inkább [párhuzamos üzembe helyezést](#parallel-deployment) kíván végrehajtani, exportálnia kell a DirSync konfigurációs beállításait, majd áttelepítenie azokat az új kiszolgálóra.
5. Adja meg a fiók jelszavát, amellyel jelenleg az Azure AD szolgáltatáshoz csatlakozik. Azt a fiókot kell használnia, amelyet jelenleg a DirSync használ.  
   ![Adja meg Azure AD hitelesítő adatait](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Ha hibaüzenetet kap, és problémák adódnak a kapcsolódással, tekintse meg a [Troubleshoot connectivity problems](tshoot-connect-connectivity.md) (Kapcsolati problémák elhárítása) szakaszt.
6. Adjon meg egy vállalati rendszergazdai fiókot az Active Directory szolgáltatáshoz.  
   ![Adja meg ADDS hitelesítő adatait](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Készen áll a konfigurálására. Amikor az **Upgrade** (Frissítés) gombra kattint, megtörténik a DirSync eltávolítása, az Azure AD Connect konfigurálása és a szinkronizálás indítása.  
   ![Konfigurálásra kész](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Miután a telepítés befejeződött, jelentkezzen ki majd ismét be a Windowsba, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná, vagy bármely egyéb konfigurációmódosítást próbálna végrehajtani.

## <a name="parallel-deployment"></a>Párhuzamos üzembe helyezés
### <a name="export-the-dirsync-configuration"></a>DirSync-konfiguráció exportálása
**Párhuzamos üzembe helyezés 50 000-nél több objektum esetén**

Ha 50 000-nél több objektummal rendelkezik, az Azure AD Connect telepítés párhuzamos üzembe helyezést javasol.

A következőhöz hasonló képernyő jelenik meg:  
![Elemzés befejeződött](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Amennyiben párhuzamos üzembe helyezést kíván végrehajtani, az alábbi lépéseket kell végrehajtania:

* Kattintson az **Export settings** (Beállítások exportálása) gombra. Ha az Azure AD Connectet egy különálló kiszolgálón telepíti, ezek a beállítások át lesznek telepítve a jelenlegi DirSyncből az új Azure AD Connect-telepítésre.

Miután a beállítások exportálása sikeresen megtörtént, kiléphet az Azure AD Connect varázslóból a DirSync kiszolgálón. Folytassa a következő lépéssel, ha a Azure AD Connectt külön kiszolgálóra szeretné telepíteni

**Párhuzamos üzembe helyezés 50 000-nél kevesebb objektummal**

Ha 50 000-nél kevesebb objektummal rendelkezik, azonban mégis párhuzamos üzembe helyezést kíván végrehajtani, tegye a következőket:

1. Futtassa az Azure AD Connect telepítőt (MSI).
2. Amikor megjelenik a **Welcome to Azure AD Connect** (Üdvözli az Azure AD Connect) képernyő, lépjen ki a telepítővarázslóból az „X” gombra kattintva az ablak jobb felső sarkában.
3. Nyisson meg egy parancssort.
4. Az Azure AD Connect telepítési helyéről (alapértelmezett hely: C:\Program Files\Microsoft Azure Active Directory Connect) hajtsa végre a következő parancsot: `AzureADConnect.exe /ForceExport`.
5. Kattintson az **Export settings** (Beállítások exportálása) gombra. Ha az Azure AD Connectet egy különálló kiszolgálón telepíti, ezek a beállítások át lesznek telepítve a jelenlegi DirSyncből az új Azure AD Connect-telepítésre.

![Képernyőfelvétel: az exportálási beállítások lehetőség a beállítások új Azure AD Connect telepítésre való áttelepítéséhez.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Miután a beállítások exportálása sikeresen megtörtént, kiléphet az Azure AD Connect varázslóból a DirSync kiszolgálón. Lépjen tovább a következő lépésre az Azure AD Connect különálló kiszolgálóra telepítéséhez.

### <a name="install-azure-ad-connect-on-separate-server"></a>Az Azure AD Connect telepítése különálló kiszolgálóra
Ha az Azure AD Connectet egy új kiszolgálón telepíti, a rendszer feltételezi, hogy az Azure AD Connect tiszta telepítését kívánja végrehajtani. Mivel a DirSync-konfigurációt szeretné alkalmazni, néhány további lépést is végre kell hajtania:

1. Futtassa az Azure AD Connect telepítőt (MSI).
2. Amikor megjelenik a **Welcome to Azure AD Connect** (Üdvözli az Azure AD Connect) képernyő, lépjen ki a telepítővarázslóból az „X” gombra kattintva az ablak jobb felső sarkában.
3. Nyisson meg egy parancssort.
4. Az Azure AD Connect telepítési helyéről (alapértelmezett hely: C:\Program Files\Microsoft Azure Active Directory Connect) hajtsa végre a következő parancsot: `AzureADConnect.exe /migrate`.
   Az Azure AD Connect telepítővarázslója elindul, és a következő képernyőt jeleníti meg:  
   ![Képernyőkép, amely bemutatja, hová kell importálni a beállításokat tartalmazó fájlt a Verziófrissítéskor.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Válassza ki a DirSync-telepítésből exportált beállítási fájlt.
6. Konfigurálja az esetleges speciális beállításokat, beleértve a következőket:
   * Egyedi telepítési helyet adhat meg az Azure AD Connect számára.
   * Megadhatja az SQL Server egy meglévő példányát (alapértelmezett beállítás: az Azure AD Connect telepíti az SQL Server 2012 Expresst). Ne használja ugyanazt az adatbázispéldányt, mint a DirSync kiszolgáló.
   * Megadhat az SQL Serverhez való csatlakozáshoz használt szolgáltatásfiókot (ha az SQL Server adatbázis távoli, a fióknak tartományi szolgáltatásfióknak kell lennie).
     Ezek a beállítások láthatók ezen a képernyőn:  
     ![Képernyőkép, amely az előre megadott konfigurációs beállításokat jeleníti meg a-től az rSync-ről.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Kattintson a **Tovább** gombra.
8. A **Ready to configure** (Konfigurálásra kész) oldalon hagyja a **Start the synchronization process as soon as the configuration completes** (Szinkronizálási folyamat indítása a konfiguráció befejeztével) beállítást bejelölve. A kiszolgáló [átmeneti módban](how-to-connect-sync-staging-server.md) van, így a módosítások nem lesznek exportálva az Azure AD-be.
9. Kattintson az **Install** (Telepítés) gombra.
10. Miután a telepítés befejeződött, jelentkezzen ki majd ismét be a Windowsba, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná, vagy bármely egyéb konfigurációmódosítást próbálna végrehajtani.

> [!NOTE]
> Megkezdődik a szinkronizálás a Windows Server Active Directory és az Azure Active Directory között, a módosítások azonban nem lesznek exportálva az Azure AD-be. Egy időben csupán egyetlen szinkronizálási eszköz exportálhat aktívan módosításokat. Ezt az állapotot nevezzük [átmeneti módnak](how-to-connect-sync-staging-server.md).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Ellenőrizze, hogy az Azure AD Connect készen áll a szinkronizálás megkezdésére
Annak ellenőrzéséhez, hogy az Azure AD Connect kész-e átvenni a DirSync feladatait, meg kell nyitnia a **Synchronization Service Managert** (Szinkronizálási szolgáltatás kezelője) a Start menü **Azure AD Connect** csoportjában.

Az alkalmazásban lépjen az **Operations (műveletek** ) lapra. Ezen a lapon ellenőrizze, hogy a következő műveletek befejeződtek-e:

* Importálás az AD Connectoron
* Importálás az Azure AD Connectoron
* Teljes szinkronizálás az AD Connectoron
* Teljes szinkronizálás az Azure AD Connectoron

![Importálás és szinkronizálás befejezve](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Tekintse át ezeknek a műveleteknek az eredményét, és győződjön meg róla, hogy nincsenek hibák.

Ha meg kívánja tekinteni és vizsgálni az Azure AD szolgáltatásba exportálni szándékolt módosításokat, olvassa el, hogy ellenőrizhető a konfiguráció az [átmeneti módban](how-to-connect-sync-staging-server.md). Hajtsa végre a szükséges módosításokat, amíg semmi váratlant nem lát már.

Akkor áll készen a DirSyncről Azure AD-re váltásra, ha végrehajtotta ezeket a lépéseket, és elégedett az eredménnyel.

### <a name="uninstall-dirsync-old-server"></a>A DirSync eltávolítása (régi kiszolgáló)
* A **Programok és szolgáltatások** felületen keresse meg a **Windows Azure Active Directory sync tool** eszközt
* Távolítsa el a **Windows Azure Active Directory sync tool** eszközt
* Az eszköz eltávolítása akár 15 percet is igénybe vehet.

Ha később szeretné eltávolítani a DirSyncet, ideiglenesen leállíthatja a kiszolgálót vagy letilthatja a szolgáltatást. Ha hiba lép fel, ez a módszer lehetővé teszi az ismételt engedélyezését. Azonban nem valószínű, hogy a következő lépés hibával ér véget, ezért erre feltehetőleg nem lesz szükség.

A DirSync eltávolítása vagy letiltása után nem marad aktív kiszolgáló, amely exportálna az Azure AD-be. Végre kell hajtania a következő lépést az Azure AD Connect engedélyezéséhez, még mielőtt folytatódna a helyszíni Active Directory módosításainak szinkronizálása az Azure AD-be.

### <a name="enable-azure-ad-connect-new-server"></a>Az Azure AD Connect engedélyezése (új kiszolgáló)
A telepítést követően az Azure AD Connect újraindításával további konfigurációmódosításokat hajthat végre. Indítsa el az **Azure AD Connectet** a Start menüből vagy a parancsikonnal az asztalról. Győződjön meg róla, hogy nem a telepítő MSI-t próbálja ismét futtatni.

A következőnek kell megjelennie:  
![További feladatok](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Válassza a **Configure staging mode** (Átmeneti mód konfigurálása) lehetőséget.
* Kapcsolja ki az átmeneti módot az **Enabled staging mode** (Átmeneti mód engedélyezve) jelölőnégyzet jelölésének törlésével.

![Képernyőkép, amely az átmeneti üzemmód engedélyezésének lehetőségét mutatja.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Kattintson a **Next** (Tovább) gombra
* A jóváhagyást kérő lapon kattintson az **Install** (Telepítés) gombra.

Mostantól az Azure AD Connect az aktív kiszolgáló, és nem szabad visszaváltania a meglévő DirSync-kiszolgáló használatára.

## <a name="next-steps"></a>Következő lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett új szolgáltatásokkal: az [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md), a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
