---
title: 'Azure AD Connect Sync: szűrés konfigurálása | Microsoft Docs'
description: Elmagyarázza, hogyan konfigurálhatja a szűrést Azure AD Connect szinkronizálásban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996542"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Az Azure AD Connect szinkronizálása: a szűrés konfigurálása
A szűrés használatával szabályozhatja, hogy mely objektumok jelenjenek meg Azure Active Directory (Azure AD) a helyszíni címtárból. Az alapértelmezett konfiguráció a konfigurált erdők összes tartományában lévő összes objektumot átveszi. Általában ez az ajánlott konfiguráció. A Microsoft 365 számítási feladatokat, például az Exchange Online-t és a Skype vállalati verziókat használó felhasználók teljes globális címlistát használhatnak, így e-maileket küldhetnek, és meghívhatnak mindenkit. Az alapértelmezett konfigurációval ugyanazzal a tapasztalattal rendelkeznek, mint az Exchange vagy a Lync helyszíni megvalósításával.

Bizonyos esetekben azonban szükség van az alapértelmezett konfiguráció módosítására. Íme néhány példa:

* A [multi-Azure ad címtár-topológiát](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant)tervezi használni. Ezután egy szűrőt kell alkalmaznia annak szabályozására, hogy mely objektumok legyenek szinkronizálva egy adott Azure AD-címtárral.
* Az Azure-hoz vagy a Microsoft 365hoz tartozó próbaverziót futtat, és csak a felhasználók egy részhalmazát szeretné használni az Azure AD-ben. A kis pilóta esetében nem fontos, hogy a funkciók megjelenítéséhez teljes globális címlistát lehessen használni.
* Számos szolgáltatásfiókot és más olyan nem személyes fiókot tartalmaz, amelyeket nem szeretne az Azure AD-ben használni.
* Megfelelőségi okokból nem törölheti a helyszíni felhasználói fiókokat. Csak azokat kell letiltani. Az Azure AD-ben azonban csak azt szeretné, hogy az aktív fiókok jelen legyenek.

Ez a cikk a különböző szűrési módszerek konfigurálását ismerteti.

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect szinkronizálásának módosítását vagy a hivatalos dokumentumokban szereplő műveleteken kívüli használat. Ezen műveletek bármelyike inkonzisztens vagy nem támogatott állapotba Azure AD Connect szinkronizálást eredményezhet. Ennek eredményeképpen a Microsoft nem tud technikai támogatást biztosítani az ilyen üzemelő példányokhoz.

## <a name="basics-and-important-notes"></a>Alapismeretek és fontos megjegyzések
Azure AD Connect Sync szolgáltatásban bármikor engedélyezheti a szűrést. Ha a címtár-szinkronizálás alapértelmezett konfigurációját választja, és a szűrést konfigurálja, a kiszűrt objektumok már nem szinkronizálhatók az Azure AD-vel. Ennek a változásnak a következtében az Azure AD összes olyan objektuma, amely korábban szinkronizálva lett, de az Azure AD-ben törölve lett.

Mielőtt megkezdené a szűrést, győződjön meg arról, hogy [letiltotta az ütemezett feladatot](#disable-the-scheduled-task) , így nem kell véletlenül exportálnia azokat a módosításokat, amelyeket még nem ellenőrzött, hogy helyesek legyenek.

Mivel a szűrés egyszerre több objektumot is eltávolíthat, meg kell győződnie arról, hogy az új szűrők helyesek, mielőtt megkezdené az Azure AD-beli módosítások exportálását. A konfigurációs lépések elvégzése után erősen ajánlott az [ellenőrzési lépések](#apply-and-verify-changes) követése az Azure ad exportálása és módosítása előtt.

A "[véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkció alapértelmezés szerint be van kapcsolva, hogy megakadályozza a sok objektum törlését. Ha a szűrés miatt sok objektumot töröl (500 alapértelmezés szerint), akkor a cikk lépéseit követve engedélyezheti a törlést az Azure AD-be való átugráshoz.

Ha november 2015 ([1.0.9125](reference-connect-version-history.md)) előtti buildet használ, módosítsa a szűrő konfigurációját, és használja a jelszó-kivonatolási szinkronizálást, majd a konfiguráció befejezése után minden jelszó teljes szinkronizálását el kell indítania. A jelszó teljes szinkronizálásának elindítására vonatkozó lépésekért lásd: az [összes jelszó teljes szinkronizálásának elindítása](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Ha a build 1.0.9125 vagy újabb verzióját használja, akkor a normál **teljes szinkronizálási** művelet azt is kiszámítja, hogy szinkronizálva legyenek-e a jelszavak, és ha ez a további lépés már nem szükséges.

Ha egy szűrési hiba miatt véletlenül törölte a **felhasználói** objektumokat az Azure ad-ben, akkor az Azure ad-ben újra létrehozhatja a felhasználói objektumokat a szűrési konfigurációk eltávolításával. Ezután szinkronizálhatja a címtárakat. Ez a művelet visszaállítja a felhasználókat a Lomtárból az Azure AD-ben. Más objektumtípusok törlését azonban nem lehet visszavonni. Ha például véletlenül töröl egy biztonsági csoportot, és az erőforrás-HOZZÁFÉRÉSre volt használva, a csoport és az ACL-ek nem állíthatók helyre.

Azure AD Connect csak azokat az objektumokat törli, amelyeknek a hatókörében szerepelnek. Ha vannak olyan objektumok az Azure AD-ben, amelyeket egy másik szinkronizáló motor hozott létre, és ezek az objektumok nem tartoznak a hatókörbe, a szűrés hozzáadásával nem távolítja el azokat. Ha például egy olyan Azure-kiszolgálóval indul, amely létrehozta a teljes könyvtárat az Azure AD-ben, és egy új Azure AD Connect szinkronizáló kiszolgálót telepít, párhuzamosan, az elején lévő szűréssel, Azure AD Connect nem távolítja el az rSync által létrehozott további objektumokat.

A szűrési konfiguráció megmarad, ha a Azure AD Connect újabb verziójára telepíti vagy frissíti. Az első szinkronizálási ciklus futtatása előtt mindig érdemes ellenőrizni, hogy a konfigurációt véletlenül nem módosították-e az újabb verzióra való frissítés után.

Ha egynél több erdővel rendelkezik, a jelen témakörben ismertetett szűrési beállításokat minden erdőre alkalmaznia kell (feltéve, hogy mindegyikhez ugyanazt a konfigurációt szeretné használni).

### <a name="disable-the-scheduled-task"></a>Az ütemezett feladat letiltása
Az alábbi lépéseket követve letilthatja a szinkronizálási ciklust 30 percenként indító beépített Feladatütemezőt:

1. Lépjen a PowerShell-parancssorba.
2. Futtassa `Set-ADSyncScheduler -SyncCycleEnabled $False` a parancsot az ütemező letiltásához.
3. Végezze el a cikkben leírt módosításokat.
4. Futtassa `Set-ADSyncScheduler -SyncCycleEnabled $True` ismét a parancsot az ütemező engedélyezéséhez.

**Ha a 1.1.105.0 előtt Azure AD Connect buildet használ**  
Az alábbi lépéseket követve letilthatja a szinkronizálási ciklust három óránként indító ütemezett feladatot:

1. Indítsa el a **Feladatütemezőt** a **Start** menüből.
2. Közvetlenül a Feladatütemező **könyvtár** alatt keresse meg **Azure ad-szinkronizáló Scheduler** nevű feladatot, kattintson a jobb gombbal, és válassza a **Letiltás** lehetőséget.  
   ![Feladatütemező](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Most már elvégezheti a konfigurációs módosításokat, és manuálisan futtathatja a Szinkronizáló motort a **synchronization Service Manager** -konzolról.

Miután elvégezte az összes szűrési módosítást, ne felejtse el visszatérni, és **engedélyezze** újra a feladatot.

## <a name="filtering-options"></a>Szűrési beállítások
A következő szűrési konfigurációs típusokat alkalmazhatja a címtár-szinkronizáló eszközre:

* [**Csoportos**](#group-based-filtering): egyetlen csoport alapján történő szűrés csak a telepítési varázslóval állítható be a kezdeti telepítésre.
* [**Tartományalapú**](#domain-based-filtering): ezzel a beállítással kiválaszthatja, hogy mely tartományok szinkronizálhatók az Azure ad-vel. A szinkronizálási motor konfigurációjától is hozzáadhat és eltávolíthat tartományokat, amikor a Azure AD Connect Sync telepítése után módosításokat végez a helyszíni infrastruktúrában.
* [**Szervezeti egység (OU) – alapú**](#organizational-unitbased-filtering): ezzel a beállítással kiválaszthatja, hogy mely szervezeti egységek legyenek szinkronizálva az Azure ad szolgáltatásba. Ez a beállítás a kiválasztott szervezeti egységekben lévő összes objektumtípus esetében használható.
* [**Attribútum-alapú**](#attribute-based-filtering): Ha ezt a lehetőséget választja, az objektumok attribútumai alapján is szűrheti az objektumokat. Különböző típusú szűrőket is használhat.

Egyszerre több szűrési lehetőséget is használhat. A OU-alapú szűrés használatával például csak egy szervezeti egységben lévő objektumokat tartalmazhat. Ugyanakkor attribútum-alapú szűrést is használhat az objektumok szűréséhez. Több szűrési módszer használata esetén a szűrők logikai "és" kifejezést használnak a szűrők között.

## <a name="domain-based-filtering"></a>Tartományalapú szűrés
Ez a szakasz a tartományi szűrő konfigurálásának lépéseit ismerteti. Ha a Azure AD Connect telepítése után adott hozzá vagy távolított el tartományokat az erdőben, akkor a szűrési konfigurációt is frissítenie kell.

A tartományalapú szűrés módosításának előnyben részesített módja a telepítővarázsló futtatása, valamint a [tartomány és a szervezeti egység szerinti szűrés](how-to-connect-install-custom.md#domain-and-ou-filtering)módosítása. A telepítővarázsló automatizálja a jelen témakörben ismertetett feladatokat.

Ezeket a lépéseket csak akkor hajtsa végre, ha valamilyen okból nem tudja futtatni a telepítési varázslót.

A tartományalapú szűrési konfiguráció a következő lépésekből áll:

1. Válassza ki a szinkronizálásba felvenni kívánt tartományokat.
2. Minden hozzáadott és eltávolított tartomány esetében módosítsa a futtatási profilokat.
3. [A módosítások alkalmazása és ellenőrzése](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Válassza ki a szinkronizálandó tartományokat
Kétféleképpen lehet kiválasztani a szinkronizálandó tartományokat:
    - A szinkronizálási szolgáltatás használata
    - A Azure AD Connect varázsló használata.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Válassza ki a szinkronizálni kívánt tartományokat a szinkronizálási szolgáltatás használatával.
A tartományi szűrő beállításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be Azure AD Connect szinkronizálást futtató kiszolgálóra egy olyan fiókkal, amely a **ADSyncAdmins** biztonsági csoport tagja.
2. Indítsa el a **szinkronizálási szolgáltatást** a **Start** menüből.
3. Válassza az **Összekötők** lehetőséget, majd az **Összekötők** listából válassza ki az összekötőt, amelynek a típusa **Active Directory Domain Services**. A **műveletek** területen válassza a **Tulajdonságok** lehetőséget.  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson a címtárpartíciók **konfigurálása** elemre.
5. A **Select Directory Partitions (címtárpartíciók kiválasztása** ) listában válassza ki és válassza ki a kívánt tartományokat. Győződjön meg arról, hogy csak a szinkronizálni kívánt partíciók vannak kiválasztva.  
   ![A "tulajdonságok" ablakban található címtárpartíciókat megjelenítő képernyőkép.](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Ha módosította a helyszíni Active Directory infrastruktúráját, és hozzáadta vagy eltávolította a tartományokat az erdőből, kattintson a **frissítés** gombra egy frissített lista lekéréséhez. A frissítésekor a rendszer a hitelesítő adatok megadását kéri. Adjon meg olyan hitelesítő adatokat, amelyek olvasási hozzáféréssel rendelkeznek a Windows Server Active Directoryhoz. Nem kell a párbeszédpanelen előre feltöltött felhasználónak lennie.  
   ![Frissítés szükséges](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Ha elkészült, a **Tulajdonságok** párbeszédpanel bezárásához kattintson **az OK gombra**. Ha eltávolított tartományokat az erdőből, egy üzenet jelenik meg, amely szerint a tartomány el lett távolítva, és a rendszer törli a konfigurációt.
7. Folytassa a futtatási profilok módosításával.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Válassza ki a szinkronizálni kívánt tartományokat a Azure AD Connect varázsló segítségével
A tartományi szűrő beállításához hajtsa végre a következő lépéseket:

1.  A Azure AD Connect varázsló elindítása
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza a **szinkronizálási beállítások testreszabása lehetőséget** , majd kattintson a **tovább** gombra.
4.  Adja meg Azure AD hitelesítő adatait
5.  A **csatlakoztatott könyvtárak** képernyőn kattintson a **tovább** gombra.
6.  A **tartomány és szervezeti egység szűrése lapon** kattintson a **frissítés** gombra.  Ekkor megjelennek az új tartományok, és a törölt tartományok el fognak tűnni.
   ![Partíciók](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>A futtatási profilok frissítése
Ha frissítette a tartományi szűrőt, a futtatási profilokat is frissítenie kell.

1. Az **Összekötők** listájában ellenőrizze, hogy az előző lépésben módosított összekötő ki van-e választva. A **műveletek** területen válassza a **futtatási profilok konfigurálása** lehetőséget.  
   ![Összekötő-futtatási profilok 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Keresse meg és azonosítsa a következő profilokat:
    * Teljes importálás
    * Teljes szinkronizálás
    * Különbözeti importálás
    * Különbözeti szinkronizálás
    * Exportálás
3. Az egyes profilok esetében módosítsa a **hozzáadott** és az **eltávolított** tartományokat.
    1. Mindegyik öt profil esetében hajtsa végre a következő lépéseket az egyes **hozzáadott** tartományokhoz:
        1. Válassza ki a futtatási profilt, és kattintson az **új lépés** gombra.
        2. A **lépés konfigurálása** oldalon, a **típus** legördülő menüben válassza ki a lépés típusát ugyanazzal a névvel, mint a konfigurálni kívánt profillal. Ezután kattintson a **Tovább** gombra.  
        ![Összekötő-profilok futtatása 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Az **összekötő konfigurációja** lapon, a **partíció** legördülő menüben válassza ki a tartomány szűrőhöz hozzáadott tartomány nevét.  
        ![Összekötőn futó profilok 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. A **futtatási profil konfigurálása** párbeszédpanel bezárásához kattintson a **Befejezés** gombra.
    2. Minden öt profil esetében hajtsa végre a következő lépéseket az egyes **eltávolított** tartományokhoz:
        1. Válassza ki a futtatási profilt.
        2. Ha a **Partition** ATTRIBÚTUM **értéke** GUID, válassza a Futtatás lépést, és kattintson a **lépés törlése** gombra.  
        ![Összekötőn futó profilok 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Ellenőrizze a változást. Minden szinkronizálni kívánt tartománynak szerepelnie kell az egyes futtatási profilokban szereplő lépésként.
4. A **futtatási profilok konfigurálása** párbeszédpanel bezárásához kattintson **az OK** gombra.
5.  A konfiguráció befejezéséhez **teljes importálást** és **különbözeti szinkronizálást** kell futtatnia. Folytassa a szakasz olvasásával [, és ellenőrizze a módosításokat](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Szervezeti egység – alapú szűrés
A szervezeti egység szerinti szűrés elsődleges módja a telepítővarázsló futtatása, valamint a [tartomány és a szervezeti egység szűrésének](how-to-connect-install-custom.md#domain-and-ou-filtering)módosítása. A telepítővarázsló automatizálja a jelen témakörben ismertetett feladatokat.

Ezeket a lépéseket csak akkor hajtsa végre, ha valamilyen okból nem tudja futtatni a telepítési varázslót.

A szervezeti egység-alapú szűrés konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be Azure AD Connect szinkronizálást futtató kiszolgálóra egy olyan fiókkal, amely a **ADSyncAdmins** biztonsági csoport tagja.
2. Indítsa el a **szinkronizálási szolgáltatást** a **Start** menüből.
3. Válassza az **Összekötők** lehetőséget, majd az **Összekötők** listából válassza ki az összekötőt, amelynek a típusa **Active Directory Domain Services**. A **műveletek** területen válassza a **Tulajdonságok** lehetőséget.  
   ![Összekötő tulajdonságai](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Kattintson a címtárpartíciók **konfigurálása** elemre, jelölje ki a konfigurálni kívánt tartományt, majd kattintson a **tárolók** elemre.
5. Ha a rendszer kéri, adjon meg a helyszíni Active Directoryhoz olvasási hozzáféréssel rendelkező hitelesítő adatokat. Nem kell a párbeszédpanelen előre feltöltött felhasználónak lennie.
6. A **tárolók kiválasztása** párbeszédpanelen törölje azokat a szervezeti egységeket, amelyeket nem szeretne szinkronizálni a felhő címtárával, majd kattintson **az OK** gombra.  
   ![Szervezeti egységek a tárolók kiválasztása párbeszédpanelen](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * A **számítógépek** tárolót ki kell választani ahhoz, hogy a Windows 10 rendszerű számítógépek sikeresen szinkronizálva legyenek az Azure ad-vel. Ha a tartományhoz csatlakoztatott számítógépek más szervezeti egységekben találhatók, győződjön meg róla, hogy ezek ki vannak választva.
   * Ha több, megbízhatósági kapcsolattal rendelkező erdővel rendelkezik, használja a **ForeignSecurityPrincipals** tárolót. Ez a tároló lehetővé teszi az erdők közötti biztonsági csoporttagságok feloldását.
   * Ha engedélyezte az eszköz visszaírási funkcióját, válassza ki az **RegisteredDevices** szervezeti egységet. Ha más visszaírási funkciót használ, például a csoport visszaírási, győződjön meg róla, hogy ezek a helyszínek vannak kiválasztva.
   * Válassza ki az egyéb szervezeti egységeket, ahol a felhasználók, az iNetOrgPerson, a csoportok, a névjegyek és a számítógépek találhatók. A képen az összes ilyen szervezeti egység a ManagedObjects szervezeti egységben található.
   * Ha Group-alapú szűrést használ, akkor azt a szervezeti egységet kell tartalmaznia, amelyben a csoport található.
   * Vegye figyelembe, hogy beállíthatja, hogy a szűrési konfiguráció befejeződése után hozzáadott új szervezeti egységek szinkronizálva legyenek vagy nincsenek szinkronizálva. A részleteket a következő szakaszban találhatja meg.
7. Ha elkészült, a **Tulajdonságok** párbeszédpanel bezárásához kattintson **az OK gombra**.
8. A konfiguráció befejezéséhez **teljes importálást** és **különbözeti szinkronizálást** kell futtatnia. Folytassa a szakasz olvasásával [, és ellenőrizze a módosításokat](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Új szervezeti egységek szinkronizálása
A szűrés konfigurálását követően létrehozott új szervezeti egységeket a rendszer alapértelmezés szerint szinkronizálja. Ezt az állapotot jelölte be a jelölőnégyzet. Néhány alszervezeti egység kijelölését is törölheti. Ennek a viselkedésnek a beszerzéséhez kattintson a mezőre, amíg fehér színű pipa (az alapértelmezett állapota) be nem válik. Ezután törölje a nem szinkronizálni kívánt alszervezeti egységek kijelölését.

Ha az összes alszervezeti egység szinkronizálva van, a mező fehér színnel van jelölve.  
![Szervezeti egység az összes kijelölt mezővel](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Ha egyes alhálózatok nem lettek kiválasztva, akkor a doboz szürke színű Pipa jel.  
![Szervezeti egység néhány nem kijelölt alhálózattal](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Ezzel a konfigurációval a ManagedObjects alatt létrehozott új szervezeti egység szinkronizálva van.

A Azure AD Connect telepítővarázsló mindig létrehozza ezt a konfigurációt.

### <a name="dont-synchronize-new-ous"></a>Ne szinkronizáljon új szervezeti egységeket
A szinkronizálási motor úgy is beállítható, hogy a szűrési konfiguráció befejeződése után ne szinkronizálja az új szervezeti egységeket. Ezt az állapotot a felhasználói felületen a négyzet jelzi, ha a jelölőnégyzet nincs bejelölve. Ennek a viselkedésnek a beszerzéséhez kattintson a mezőre, amíg be nem fejeződik a pipa jel. Ezután válassza ki a szinkronizálni kívánt alszervezeti egységeket.

![Nincs kiválasztva a legfelső szintű szervezeti egység](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Ezzel a konfigurációval a ManagedObjects alatt létrehozott új szervezeti egység nincs szinkronizálva.

## <a name="attribute-based-filtering"></a>Attribútum-alapú szűrés
Győződjön meg arról, hogy a következő lépések végrehajtásához használja a november 2015 ([1.0.9125](reference-connect-version-history.md)) vagy újabb buildet.

> [!IMPORTANT]
>A Microsoft azt javasolja, hogy ne módosítsa a **Azure ad Connect** által létrehozott alapértelmezett szabályokat. Ha módosítani kívánja a szabályt, majd klónozást végez, és letiltja az eredeti szabályt. Végezze el a klónozott szabály módosításait. Vegye figyelembe, hogy ezzel (az eredeti szabály letiltásával) az adott szabályon keresztül engedélyezett hibajavítások vagy funkciók hiányoznak.

Az attribútum-alapú szűrés a legrugalmasabb módszer az objektumok szűrésére. A [deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md) hatékonyságát használhatja arra, hogy szinte minden aspektusát vezérelje, amikor egy objektumot szinkronizálnak az Azure ad-vel.

[Beérkező](#inbound-filtering) szűrést alkalmazhat Active Directoryról a metaverse-ra, és a metaverse-ből az Azure ad-be történő [kimenő](#outbound-filtering) szűrést. Javasoljuk, hogy alkalmazza a bejövő szűrést, mert ez a legkönnyebb a karbantartáshoz. A kiértékelés megkezdése előtt csak akkor használjon kimenő szűrést, ha egynél több erdő objektumaihoz kell csatlakoznia.

### <a name="inbound-filtering"></a>Bejövő szűrés
A bejövő szűrés az alapértelmezett konfigurációt használja, ahol az Azure AD-hez hozzáférő objektumoknak a metaverse attribútummal kell cloudFiltered a szinkronizálandó értékre. Ha az attribútum értéke **true (igaz**) értékre van állítva, akkor az objektum nincs szinkronizálva. Nem állítható be **hamis** értékre a tervezés szerint. Annak biztosításához, hogy más szabályok képesek legyenek hozzájárulni egy értékhez, ez az attribútum csak **igaz** vagy **Null** értékkel rendelkezhet (hiányzik).

A bejövő szűrés során a **hatókör** hatékonyságát használva határozza meg, hogy mely objektumokat szinkronizálni vagy ne szinkronizálni. Itt végezheti el a saját szervezet igényeinek megfelelő módosításokat. A hatókör modul tartalmaz egy **csoportot** és egy **záradékot** annak meghatározásához, hogy egy szinkronizálási szabály hatókörben van-e. Egy csoport egy vagy több záradékot tartalmaz. Létezik egy logikai "és" kifejezés több záradék között, valamint logikai "vagy" több csoport között.

Nézzünk egy példát:  
![Egy képernyőkép, amely a hatóköri szűrők hozzáadására mutat példát.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Ennek a következőnek kell lennie: **(részleg = IT) vagy (részleg = értékesítés és c = US)**.

A következő példákban és lépésekben példaként használja a felhasználói objektumot, de ezt minden objektumtípus esetében használhatja.

A következő mintákban a sorrend értéke 50. Ezt a számot nem lehet használni, de a 100-nál kisebbnek kell lennie.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatív szűrés: "ne szinkronizálja ezeket"
A következő példában kiszűrjük (nem szinkronizáljuk) az összes olyan felhasználót, ahol a **extensionAttribute15** értéke nem **szinkronizált**.

1. Jelentkezzen be Azure AD Connect szinkronizálást futtató kiszolgálóra egy olyan fiókkal, amely a **ADSyncAdmins** biztonsági csoport tagja.
2. Indítsa el a **szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. Győződjön meg arról, hogy a **bejövő** lehetőség van kiválasztva, majd kattintson az **új szabály hozzáadása** lehetőségre.
4. Adjon egy leíró nevet a szabálynak, például *: "in from ad-User DoNotSyncFilter*". Válassza ki a megfelelő erdőt, válassza a **felhasználó** lehetőséget a **cs objektumtípus** elemnél, és válassza a **személy** elemet az **MV Objektumtípus mezőben**. A **kapcsolat típusa** területen válassza a **Csatlakozás** lehetőséget. A **prioritás** mezőben olyan értéket adjon meg, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 50), majd kattintson a **tovább** gombra.  
   ![1. bejövő Leírás](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. A **hatókör szűrőben** kattintson a **Csoport hozzáadása** elemre, majd kattintson a **záradék hozzáadása** elemre. Az **attribútum** területen válassza a **ExtensionAttribute15** lehetőséget. Győződjön meg arról, hogy az **operátor** értéke **egyenlő**, és írja be az **Érték mezőbe a** **desync** értéket. Kattintson a **Tovább** gombra.  
   ![2. bejövő hatókör](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Hagyja üresen az **illesztési** szabályokat, majd kattintson a **tovább** gombra.
7. Kattintson az **átalakítás hozzáadása** lehetőségre, válassza ki a **FlowType** **állandóként**, majd válassza a **cloudFiltered** lehetőséget a **cél attribútumként**. A **forrás** szövegmezőbe írja be az **igaz** értéket. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
   ![3. bejövő átalakítás](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. A konfigurálás befejezéséhez **teljes szinkronizálást** kell futtatnia. Folytassa a szakasz olvasásával [, és ellenőrizze a módosításokat](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitív szűrés: "csak szinkronizálja ezeket"
A pozitív szűrés kinyilvánítása nagyobb kihívást jelenthet, mivel olyan objektumokat is meg kell fontolnia, amelyek nem egyértelműek a szinkronizáláshoz, például a konferenciatermeket. Emellett felülbírálja az alapértelmezett szűrőt is az **ad-User JOIN** utasításban lévő out-of-box szabályban. Az egyéni szűrő létrehozásakor ügyeljen arra, hogy ne szerepeljenek a kritikus rendszerobjektumok, a replikálási ütközési objektumok, a speciális postaládák és a Azure AD Connect tartozó szolgáltatásfiókok.

A pozitív szűrési beállításhoz két szinkronizálási szabály szükséges. Szükség van egy szabályra (vagy többre) a szinkronizálandó objektumok megfelelő hatókörével. Szüksége lesz egy második catch-all szinkronizálási szabályra is, amely kiszűri az összes olyan objektumot, amely még nem lett azonosítva a szinkronizálandó objektumként.

A következő példában csak azokat a felhasználói objektumokat szinkronizálja, amelyekben a Department attribútum értéke **Sales**.

1. Jelentkezzen be Azure AD Connect szinkronizálást futtató kiszolgálóra egy olyan fiókkal, amely a **ADSyncAdmins** biztonsági csoport tagja.
2. Indítsa el a **szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. Győződjön meg arról, hogy a **bejövő** lehetőség van kiválasztva, majd kattintson az **új szabály hozzáadása** lehetőségre.
4. Adjon egy leíró nevet a szabálynak, például *: "in from ad-User Sales Sync*". Válassza ki a megfelelő erdőt, válassza a **felhasználó** lehetőséget a **cs objektumtípus** elemnél, és válassza a **személy** elemet az **MV Objektumtípus mezőben**. A **kapcsolat típusa** területen válassza a **Csatlakozás** lehetőséget. A **prioritás** mezőben olyan értéket adjon meg, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 51), majd kattintson a **tovább** gombra.  
   ![4. bejövő Leírás](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. A **hatókör szűrőben** kattintson a **Csoport hozzáadása** elemre, majd kattintson a **záradék hozzáadása** elemre. Az **attribútum** területen válassza a **részleg** elemet. Győződjön meg arról, hogy az operátor értéke **egyenlő**, majd írja be az **értékesítés** értéket az **érték** mezőbe. Kattintson a **Tovább** gombra.  
   ![5. bejövő hatókör](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Hagyja üresen az **illesztési** szabályokat, majd kattintson a **tovább** gombra.
7. Kattintson az **átalakítás hozzáadása** lehetőségre, válassza az **állandó** lehetőséget a **FlowType**, majd válassza ki a **cloudFiltered** a **cél attribútumként**. A **forrás** mezőbe írja be a **false értéket**. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
   ![Bejövő 6 átalakítás](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Ez egy különleges eset, amikor explicit módon beállította a cloudFiltered **hamis** értékre.
8. Most létre kell hoznia a catch-all szinkronizálási szabályt. Adjon egy leíró nevet a szabálynak, például *: "in from ad – felhasználói catch-all Filter*". Válassza ki a megfelelő erdőt, válassza a **felhasználó** lehetőséget a **cs objektumtípus** elemnél, és válassza a **személy** elemet az **MV Objektumtípus mezőben**. A **kapcsolat típusa** területen válassza a **Csatlakozás** lehetőséget. A **prioritás** mezőben olyan értéket adjon meg, amelyet jelenleg nem használ egy másik szinkronizálási szabály (például 99). Az előző szinkronizálási szabálynál magasabb (alacsonyabb prioritású) prioritási értéket jelölt ki. Azonban egy kis termet is hagyott, hogy később további szűrési szinkronizálási szabályokat lehessen felvenni, amikor meg szeretné kezdeni a további részlegek szinkronizálását. Kattintson a **Tovább** gombra.  
   ![Inbound 7 – Leírás](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Hagyja üresen a **hatókör-szűrőt** , és kattintson a **tovább** gombra. Az üres szűrő azt jelzi, hogy a szabályt az összes objektumra alkalmazni kell.
10. Hagyja üresen az **illesztési** szabályokat, majd kattintson a **tovább** gombra.
11. Kattintson az **átalakítás hozzáadása** lehetőségre, válassza az **állandó** lehetőséget a **FlowType**, és válassza a **cloudFiltered** lehetőséget a **cél attribútumként**. A **forrás** mezőbe írja be az **igaz** értéket. A szabály mentéséhez kattintson a **Hozzáadás** gombra.  
    ![3. bejövő átalakítás](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. A konfigurálás befejezéséhez **teljes szinkronizálást** kell futtatnia. Folytassa a szakasz olvasásával [, és ellenőrizze a módosításokat](#apply-and-verify-changes).

Ha szüksége van a szolgáltatásra, létrehozhat több szabályt az első típushoz, ahol további objektumokat is tartalmaz a szinkronizálásban.

### <a name="outbound-filtering"></a>Kimenő szűrés
Bizonyos esetekben a szűrés csak akkor szükséges, ha az objektumok a metaverse-ben csatlakoztak. Előfordulhat például, hogy a mail attribútumot az erőforrás-erdőben, a userPrincipalName attribútumot pedig a fiók erdőből kell megvizsgálnia annak megállapításához, hogy egy objektumot szinkronizálni kell-e. Ezekben az esetekben a szűrést a Kimenő szabály alapján hozza létre.

Ebben a példában úgy módosítja a szűrést, hogy csak azok a felhasználók legyenek szinkronizálva, akiknek a levelezését és userPrincipalName is befejezte @contoso.com :

1. Jelentkezzen be Azure AD Connect szinkronizálást futtató kiszolgálóra egy olyan fiókkal, amely a **ADSyncAdmins** biztonsági csoport tagja.
2. Indítsa el a **szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
3. A **szabályok típusa** alatt kattintson a **kimenő** elemre.
4. Az Ön által használt csatlakozási verziótól függően keresse meg az **Azure ad – User JOIN** vagy **Az Azure ad-User JOIN SOAInAD** nevű szabályt, és kattintson a **Szerkesztés** gombra.
5. Az előugró ablakban válaszoljon az **Igen** gombra a szabály másolatának létrehozásához.
6. A **Leírás** lapon módosítsa a **sorrendet** egy nem használt értékre (például 50).
7. A bal oldali navigációs sávon kattintson a **hatókör szűrő** elemre, majd kattintson a **záradék hozzáadása** lehetőségre. Az **attribútum** területen válassza az **e-mail** lehetőséget. A **kezelőben** válassza a **ENDSWITH** lehetőséget. Az **érték** mezőbe írja be a **\@ contoso.com**, majd kattintson a **záradék hozzáadása** elemre. Az **attribútum** területen válassza a **userPrincipalName** lehetőséget. A **kezelőben** válassza a **ENDSWITH** lehetőséget. Az **érték** mezőbe írja be a következőt: **\@ contoso.com**.
8. Kattintson a **Mentés** gombra.
9. A konfigurálás befejezéséhez **teljes szinkronizálást** kell futtatnia. Folytassa a szakasz olvasásával [, és ellenőrizze a módosításokat](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Módosítások alkalmazása és ellenőrzése
A konfigurációs módosítások elvégzése után azokat az objektumokra kell alkalmaznia, amelyek már jelen vannak a rendszeren. Az is előfordulhat, hogy a szinkronizálási motorban jelenleg nem szereplő objektumokat fel kell dolgozni (és a szinkronizálási motornak újra kell olvasnia a forrásoldali rendszerét a tartalom ellenőrzéséhez).

Ha a konfigurációt **tartomány** vagy **szervezeti egység** szerinti szűrés használatával módosította, akkor **teljes importálást** kell végrehajtania, amelyet a **különbözeti szinkronizálás** követ.

Ha a konfigurációt **attribútum** -szűrés használatával módosította, akkor **teljes szinkronizálást** kell végeznie.

Hajtsa végre a következő lépéseket:

1. Indítsa el a **szinkronizálási szolgáltatást** a **Start** menüből.
2. Válassza az **Összekötők** lehetőséget. Az **Összekötők** listában válassza ki azt az összekötőt, amelyben korábban a konfigurációs módosítást hajtotta végre. A **műveletek** területen válassza a **Futtatás** lehetőséget.  
   ![Összekötő futtatása](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. A **Run profiles (futtatási profilok**) területen válassza ki az előző szakaszban említett műveletet. Ha két műveletet kell futtatnia, futtassa a másodikat az első befejeződése után. (Az **állapot** oszlopban a kiválasztott összekötő **inaktív** .)

A szinkronizálás után a rendszer az összes módosítást exportálja. Mielőtt megkezdené a módosításokat az Azure AD-ben, ellenőrizni kívánja, hogy a módosítások helyesek-e.

1. Indítson el egy parancssort, és nyissa meg a következőt: `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Futtassa az `csexport "Name of Connector" %temp%\export.xml /f:x` parancsot.  
   Az összekötő neve a szinkronizációs szolgáltatásban található. Az Azure AD esetében az "contoso.com – Azure AD" kifejezéshez hasonló név szerepel.
3. Futtassa az `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` parancsot.
4. Most már van egy fájlja a (z)% Temp% megnevezett export.csvban, amely megvizsgálható a Microsoft Excelben. Ez a fájl tartalmazza az exportálandó összes módosítást.
5. Végezze el a szükséges módosításokat az adatokon vagy a konfiguráción, majd futtassa újra ezeket a lépéseket (importálás, szinkronizálás és ellenőrzés), amíg az exportálandó módosítások elvártak lesznek.

Ha elégedett, exportálja a módosításokat az Azure AD-be.

1. Válassza az **Összekötők** lehetőséget. Az **Összekötők** listából válassza ki az Azure ad-összekötőt. A **műveletek** területen válassza a **Futtatás** lehetőséget.
2. A **futtatási profilok** területen válassza az **Exportálás** lehetőséget.
3. Ha a konfiguráció sok objektum törlését módosítja, akkor az exportálás során hibaüzenet jelenik meg, ha a szám meghaladja a beállított küszöbértéket (alapértelmezés szerint 500). Ha ezt a hibát látja, átmenetileg le kell tiltania a "[véletlen törlés megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)" funkciót.

Itt az ideje, hogy ismét engedélyezze az ütemező.

1. Indítsa el a **Feladatütemezőt** a **Start** menüből.
2. Közvetlenül a Feladatütemező **könyvtár** alatt keresse meg **Azure ad-szinkronizáló Scheduler** nevű feladatot, kattintson a jobb gombbal, majd válassza az **Engedélyezés** lehetőséget.

## <a name="group-based-filtering"></a>Csoport alapú szűrés
Csoportházirend-alapú szűrést úgy konfigurálhat, hogy a Azure AD Connect első telepítésekor [Egyéni telepítéssel](how-to-connect-install-custom.md#sync-filtering-based-on-groups)telepítse a rendszert. Olyan kísérleti üzembe helyezésre szolgál, ahol csak kis mennyiségű objektumot szeretne szinkronizálni. Ha letiltja a csoport alapú szűrést, nem engedélyezhető újra. A csoport-alapú szűrés *nem* használható egyéni konfigurációban. Ezt a funkciót csak a telepítővarázsló használatával lehet konfigurálni. Ha befejezte a próbaverziót, használja a jelen témakör más szűrési lehetőségei közül a megfelelőt. Ha OU-alapú szűrést használ a csoport alapú szűréssel együtt, a csoportot és annak tagjait tartalmazó szervezeti egység (ek) et is tartalmaznia kell.

Több AD-erdő szinkronizálásakor a csoport-alapú szűrést egy másik csoport megadásával állíthatja be az egyes AD-összekötők számára. Ha egy felhasználót szeretne szinkronizálni egy AD-erdőben, és ugyanaz a felhasználó rendelkezik egy vagy több megfelelő objektummal más AD-erdőkben, meg kell győződnie arról, hogy a felhasználói objektum és a hozzá tartozó összes objektum a csoport alapú szűrési hatókörön belül van. Példák:

* Az egyik erdőben van egy olyan felhasználó, amely egy másik erdőben található megfelelő FSP (külső rendszerbiztonsági tag) objektummal rendelkezik. Mindkét objektumnak Group-alapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.

* Egy erdőben van egy olyan felhasználó, amely egy másik erdőben található megfelelő erőforrás-fiókkal (például csatolt postaládával) rendelkezik. Továbbá úgy konfigurálta Azure AD Connect, hogy összekapcsolja a felhasználót az erőforrás-fiókkal. Mindkét objektumnak Group-alapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.

* Az egyik erdőben van egy olyan felhasználó, amely egy másik erdőben található e-mail-kapcsolattal rendelkezik. Továbbá úgy konfigurálta Azure AD Connect, hogy összekapcsolja a felhasználót a levelezési kapcsolattal. Mindkét objektumnak Group-alapú szűrési hatókörön belül kell lennie. Ellenkező esetben a felhasználó nem lesz szinkronizálva az Azure AD-vel.


## <a name="next-steps"></a>Következő lépések
- További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.
- További információ a helyszíni [identitások Azure ad-vel való integrálásáról](whatis-hybrid-identity.md).
