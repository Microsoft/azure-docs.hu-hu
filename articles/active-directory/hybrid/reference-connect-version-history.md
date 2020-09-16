---
title: 'Azure AD Connect: verziók kiadásának előzményei | Microsoft Docs'
description: Ez a cikk a Azure AD Connect és Azure AD-szinkronizáló összes kiadását felsorolja.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fccbb84647ae9e47afc7bb36eeca97bb41a0d1d8
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604070"
---
# <a name="azure-ad-connect-version-release-history"></a>Az Azure AD Connect verziókiadásai
A Azure Active Directory (Azure AD) csapata rendszeresen frissíti Azure AD Connect új szolgáltatásokkal és funkciókkal. Nem minden kiegészítés alkalmazható minden célközönségre.

A cikk célja, hogy segítsen a közzétett verziók nyomon követésében, és hogy megtudja, mi a változás a legújabb verzióban.



Ez a táblázat a kapcsolódó témakörök listáját tartalmazza:

Témakör |  Részletek
--------- | --------- |
A Azure AD Connect frissítésének lépései | A [korábbi verzióról a legújabb](how-to-upgrade-previous-version.md) Azure ad Connect kiadásra való frissítés különböző módszerei.
Szükséges engedélyek | A frissítések alkalmazásához szükséges engedélyekért lásd: [fiókok és engedélyek](reference-connect-accounts-permissions.md#upgrade).
Letöltés| [Azure ad Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>A Azure AD Connect új verziójának kiadása egy olyan folyamat, amely több minőségellenőrzési lépést igényel a szolgáltatás működési funkcióinak biztosításához, és a folyamat során az új kiadás verziószámát, valamint a kiadási állapotot is frissíti a legújabb állapotnak megfelelően.
Amíg ezt a folyamatot elvégezjük, a kiadás verziószáma "X"-ként jelenik meg a másodlagos kiadási szám pozíciójában, ahogy az "1.3. X. 0" értéknél – ez azt jelenti, hogy a jelen dokumentumban szereplő kibocsátási megjegyzések az "1,3." kezdetű összes verzióra érvényesek. Amint elvégezte a kiadási folyamat véglegesítését, a kiadás verziószámát a rendszer frissíti a legújabb verzióra, és a kiadási állapot frissülni fog a "kiadás a letöltéshez és az automatikus frissítéshez".
Nem minden Azure AD Connect-kiadás lesz elérhető az automatikus frissítéshez. A kiadási állapot azt jelzi, hogy elérhető-e kiadás az automatikus frissítéshez vagy csak a letöltéshez. Ha az automatikus frissítés engedélyezve lett a Azure AD Connect-kiszolgálón, akkor a kiszolgáló automatikusan az automatikus frissítéshez kiadott Azure AD Connect legújabb verziójára fog frissülni. Vegye figyelembe, hogy nem minden Azure AD Connect konfiguráció jogosult az automatikus frissítésre. Az [automatikus frissítéssel](how-to-connect-install-automatic-upgrade.md) kapcsolatos további információért kövesse ezt a hivatkozást

>[!IMPORTANT]
> 2020 november 1-től kezdődően megkezdjük egy elavult folyamat megvalósítását, amelynek során a több mint 18 hónappal ezelőtt kiadott Azure AD Connect verziók elavultak lesznek. Ekkor megkezdjük ezt a folyamatot, ha elavulttá válik a (4/24/2019-es verzióban kiadott) 1.3.20.0-verzióval rendelkező Azure AD Connect összes kiadása, és az új verziók kiadásakor a rendszer minden alkalommal kiértékeli Azure AD Connect régebbi verzióinak elavult verzióját.
>
> Győződjön meg arról, hogy a Azure AD Connect legújabb verzióját futtatja, hogy optimális támogatási élményt kapjon. 
>
>Ha a Azure AD Connect elavult verzióját futtatja, előfordulhat, hogy nem rendelkezik a legújabb biztonsági javításokkal, a teljesítménnyel kapcsolatos javításokkal, a hibaelhárítással és a diagnosztikai eszközökkel, valamint a szolgáltatás fejlesztésével, és ha támogatásra van szüksége, előfordulhat, hogy a szervezet igényeinek megfelelő szolgáltatási szintet nem tudjuk biztosítani.
>
>Ha engedélyezte a Azure AD Connect for Sync szolgáltatást, hamarosan automatikusan megkezdi az olyan állapotadatok fogadását, amelyek figyelmeztetik a közelgő elavult verziókra, amikor az egyik régebbi verziót futtatja.
>
>[Ebben a cikkben](./how-to-upgrade-previous-version.md) további információt talál arról, hogyan frissítheti Azure ad Connect a legújabb verzióra.
>
>Az elavult verziókkal kapcsolatos korábbi verziókról lásd: [Azure ad Connect verziók kiadásának előzményei archívuma](reference-connect-version-history-archive.md)

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Kiadás állapota
07/29/2020: kiadva a letöltéshez

### <a name="functional-changes"></a>Funkcionális változások
Ez egy hibajavítási kiadás. Ebben a kiadásban nincsenek funkcionális változások.

### <a name="fixed-issues"></a>Megoldott problémák

- Kijavítva a hiba, ahol a rendszergazda nem tudja engedélyezni a "zökkenőmentes egyszeri bejelentkezést", ha a AZUREADSSOACC számítógépfiók már szerepel a "Active Directory".
- Kijavított egy problémát, amely átmeneti hibát okozott a v2 API-különbözeti importálás során egy olyan ütköző objektum esetében, amely az állapotfigyelő portálon keresztül lett kijavíthatva.
- Javítva lett egy probléma az importálási/exportálási konfigurációban, ahol a letiltott egyéni szabály engedélyezve lett importálva.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Kiadás állapota
07/10/2020: kiadva a letöltéshez

### <a name="functional-changes"></a>Funkcionális változások
Ez a kiadás a meglévő Azure AD Connect-kiszolgálók konfigurációjának exportálására szolgáló funkció nyilvános előzetes verzióját tartalmazza. A JSON-fájl, amely akkor használható, amikor új Azure AD Connect-kiszolgálót telepít az eredeti kiszolgáló másolatának létrehozásához.

Az új szolgáltatás részletes leírása ebben a [cikkben található.](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Megoldott problémák
- Kijavítva egy olyan hibát, amely hamis figyelmeztetést ad a honosított adatbázis-mérettel kapcsolatban a frissítés során.
- Kijavítva a hiba, ahol hamis hiba történt az alkalmazás eseményeiben a fióknév/tartománynév felcserélésekor.
- Kijavítva egy hiba, amelyben a Azure AD Connect sikertelen volt a TARTOMÁNYVEZÉRLŐn való telepítéskor, így a "tag nem található" hibaüzenet jelenik meg.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Kiadás állapota
05/07/2020: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás olyan hibát javít, amelyben a nem kiválasztott tartományok helytelenül lettek kiválasztva a varázsló felhasználói felületéről, ha csak az unoka-tárolók lettek kiválasztva.


>[!NOTE]
>Ez a verzió tartalmazza az új Azure AD Connect Sync v2 Endpoint API-t.  Ez az új v2-végpont jelenleg nyilvános előzetes verzióban érhető el.  Az új v2 Endpoint API használatához ez a verzió vagy újabb verzió szükséges.  Ennek a verziónak a telepítése azonban nem engedélyezi a v2-végpontot. Ha engedélyezi a v2-végpontot, továbbra is a v1-végpontot használja.  Az engedélyezéshez és a nyilvános előzetes verzióra való feliratkozáshoz a [Azure ad Connect Sync v2 ENDPOINT API (nyilvános előzetes verzió)](how-to-connect-sync-endpoint-api-v2.md) szakaszban leírt lépéseket kell követnie.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Kiadás állapota
04/23/2020: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-Build javít egy olyan problémát, amely olyan build 1.5.20.0 jelent meg, ahol az MFA-val rendelkező bérlői rendszergazda nem tudta engedélyezni a DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Kiadás állapota
04/20/2020: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-Build javít egy problémát a build 1.5.20.0, ha klónozotta az **ad-Group JOIN** szabályból, és nem klónozotta az **ad-Group Common** szabályból.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Kiadás állapota
04/09/2020: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák
- Ez a gyorsjavítás kijavítja a build 1.5.18.0 kapcsolatos problémát, ha engedélyezte a csoportos szűrés funkciót, és az mS-DS-ConsistencyGuid-t használja forrásként.
- Javítva lett egy probléma a ADSyncConfig PowerShell-modulban, ahol az összes set-ADSync * permissions parancsmagban használt DSACLS parancs meghívása a következő hibák valamelyikét okozhatja:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Ha klónozott a alkalmazást az **ad-Group csatlakozás** szinkronizálási szabályból, és nem klónozotta a-t az **ad-Group Common** Sync szabályból, és nem tervezi a verziófrissítést, hajtsa végre a következő lépéseket a frissítés részeként:
> 1. A frissítés során törölje a **szinkronizálási folyamat elindítása a konfiguráció befejeződése**után lehetőséget.
> 2. Szerkessze a klónozott illesztés szinkronizálási szabályát, és adja hozzá a következő két átalakítást:
>     - A közvetlen folyamat beállítása a következőre: `objectGUID` `sourceAnchorBinary` .
>     - A kifejezés folyamatának beállítása a következőre: `ConvertToBase64([objectGUID])` `sourceAnchor` .     
> 3. Engedélyezze az ütemező használatát a paranccsal `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Kiadás állapota
04/02/2020: kiadva a letöltéshez

### <a name="functional-changes-adsyncautoupgrade"></a>Funkcionális változások ADSyncAutoUpgrade 

- Az mS-DS-ConsistencyGuid szolgáltatás támogatása a csoport objektumaihoz. Ez lehetővé teszi csoportok áthelyezését az erdők között, vagy a csoportok újracsatlakozását az AD-ben az Azure AD-ba, ahol az AD-csoport objectID megváltozott, például amikor egy AD-kiszolgáló újraépítése a szerencsétlenség után történik. További információ: [csoportok áthelyezése erdők között](how-to-connect-migrate-groups.md).
- Az mS-DS-ConsistencyGuid attribútum automatikusan be van állítva az összes szinkronizált csoportra, és nem kell semmit tennie a funkció engedélyezéséhez. 
- Eltávolította a Get-ADSyncRunProfile, mert már nincs használatban. 
- Módosította a figyelmeztetést, amikor vállalati rendszergazdai vagy tartományi rendszergazdai fiókot próbál használni a AD DS Connector-fiókhoz, hogy több környezet biztosítható legyen. 
- Új parancsmag hozzáadva az objektumok eltávolításához az összekötő területéről a régi CSDelete.exe eszköz törlődik, és az új Remove-ADSyncCSObject parancsmaggal lesz lecserélve. A Remove-ADSyncCSObject parancsmag bemenetként fogadja a CsObject. Ez az objektum a Get-ADSyncCSObject parancsmag használatával kérhető le.

>[!NOTE]
>A régi CSDelete.exe eszköz el lett távolítva, és az új Remove-ADSyncCSObject parancsmaggal lett lecserélve 

### <a name="fixed-issues"></a>Megoldott problémák

- A funkció letiltása után kijavított egy hibát a csoport visszaírási erdő vagy szervezeti egység választójában a Azure AD Connect varázsló újbóli futtatásához. 
- A rendszer egy új hibaüzenetet jelenít meg, amely akkor jelenik meg, ha a szükséges DCOM beállításjegyzék-értékek hiányoznak egy új Súgó hivatkozással. Az információkat a rendszer a naplófájlok számára is megírja. 
- Az Azure Active Directory szinkronizációs fiók létrehozásával kapcsolatos probléma kijavítva, ahol a címtárszolgáltatások vagy PHS engedélyezése sikertelen lehet, mert a rendszer nem propagálta a fiókot az összes szolgáltatás-replikán a használat megkísérlése előtt. 
- Kijavított egy hibát a szinkronizálási hibák tömörítése segédprogramban, amely nem megfelelően kezeli a helyettesítő karaktereket. 
- Kijavított egy hibát az automatikus frissítésben, amely elhagyta a kiszolgálót az ütemező felfüggesztett állapotában. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Kiadás állapota
12/9/2019: kiadás letölthető. Nem érhető el az automatikus frissítéssel.
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
- Frissítettük a jelszó-kivonatok szinkronizálását a Azure AD Domain Services számára a Kerberos-kivonatok kitöltésének megfelelő kiszámításához.  Ez a szolgáltatás az Azure AD-ből a Azure AD Domain Servicesba való jelszó-szinkronizálás során javítja a teljesítményt.
- A megbízható munkamenetek támogatása a hitelesítési ügynök és a Service Bus között.
- Ez a kiadás a hitelesítési ügynök és a Cloud Services közötti kommunikációra kényszeríti a TLS 1,2-et.
- A rendszer egy DNS-gyorsítótárat adott hozzá a hitelesítési ügynök és a Cloud Services közötti WebSocket-kapcsolatokhoz.
- Felvettük a lehetőséget arra, hogy konkrét ügynököt célozjon meg a felhőből az ügynök kapcsolatának teszteléséhez.

### <a name="fixed-issues"></a>Megoldott problémák
- A kiadási 1.4.18.0 olyan hibát észlelt, amelyben a DSSO PowerShell-parancsmagja használta a bejelentkezési Windows hitelesítő adatokat a PS futtatásakor megadott rendszergazdai hitelesítő adatok helyett. Ennek eredményeképpen a Azure AD Connect felhasználói felületen keresztül nem engedélyezhető több erdő DSSO. 
- Javítás történt a DSSO egyidejű engedélyezéséhez az összes erdőben az Azure AD Connect felhasználói felületen keresztül

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Kiadás állapota
11/08/2019: kiadva a letöltéshez. Nem érhető el az automatikus frissítéssel.

>[!IMPORTANT]
>A Azure AD Connect ezen kiadásának belső sémájának változása miatt, ha AD FS megbízhatósági kapcsolat konfigurációs beállításait a MSOnline PowerShell használatával felügyeli, akkor frissítenie kell a MSOnline PowerShell-modult a 1.1.183.57 vagy újabb verzióra.

### <a name="fixed-issues"></a>Megoldott problémák

Ez a verzió a meglévő hibrid Azure AD-hez csatlakoztatott eszközök problémáját javítja ki. Ez a kiadás egy új eszköz-szinkronizálási szabályt tartalmaz, amely kijavította ezt a problémát.
Vegye figyelembe, hogy ez a szabály változása miatt törölheti az elavult eszközöket az Azure AD-ből. Ez nem okoz gondot, mivel az Azure AD nem használja fel ezeket az eszközöket a feltételes hozzáférés engedélyezésekor. Egyes ügyfelek esetében a szabály módosításával törölni kívánt eszközök száma túllépheti a törlési küszöbértéket. Ha úgy látja, hogy az Azure AD-ban az eszközök objektumainak törlése túllépi az Exportálás törlési küszöbértékét, azt javasoljuk, hogy engedélyezze a törlést. [A törlések folyamatának engedélyezése, ha túllépik a törlési küszöbértéket](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Kiadás állapota
9/28/2019: megjelent az automatikus frissítéshez a bérlők kiválasztásához. Nem érhető el letöltésre.

Ez a verzió javít egy hibát, amelyben néhány olyan kiszolgáló, amely egy korábbi verzióról automatikusan frissült, 1.4.18.0, és az önkiszolgáló jelszó-visszaállítás (SSPR) és a jelszó visszaírási miatt tapasztalt problémákat észlelt.

### <a name="fixed-issues"></a>Megoldott problémák

Bizonyos esetekben előfordulhat, hogy a 1.4.18.0 verzióra automatikusan frissített kiszolgálók nem engedélyezték újra az önkiszolgáló jelszó-visszaállítást és a jelszó visszaírási a frissítés befejeződése után. Ez az automatikus frissítési kiadás javítja az önkiszolgáló jelszó-visszaállítást és a jelszó visszaírási.

Kijavítottunk egy hibát a szinkronizálási hibák tömörítése segédprogramban, amely nem megfelelően kezeli a helyettesítő karaktereket.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Olyan incidenst vizsgálunk, amelyben egyes ügyfelek a meglévő hibrid Azure AD-hez csatlakoztatott eszközök problémáit tapasztalják a Azure AD Connect ezen verziójára való frissítés után. Javasoljuk, hogy a hibrid Azure AD-csatlakozást használó ügyfeleinket a verzióra való frissítés elhalasztása előtt végezze el, amíg a problémák alapvető oka teljesen érthető és enyhített. A lehető leghamarabb meg kell adni további információkat.

>[!IMPORTANT]
>A Azure AD Connect ezen verzióját néhány ügyfél láthatja, hogy a Windows-eszközeik némelyike vagy mindegyike eltűnik az Azure AD-ből. Ez nem okoz gondot, mivel az Azure AD nem használja fel az eszköz identitásait a feltételes hozzáférés engedélyezésekor. További információ: [Azure ad Connect 1.4. xx. x disappearnce-eszköz ismertetése](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Kiadás állapota
9/25/2019: csak automatikus frissítésre lett kiadva.

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
- Az új hibaelhárítási eszközök segítenek a "felhasználó nem szinkronizált", "csoport nem szinkronizált" vagy "csoporttag nem szinkronizált" forgatókönyvek hibaelhárításában.
- Adja hozzá a nemzeti felhők támogatását Azure AD Connect hibaelhárítási parancsfájlban.
- Az ügyfeleket tájékoztatni kell arról, hogy a MIIS_Service elavult WMI-végpontjai már el lettek távolítva. A WMI-műveleteket most PS-parancsmagok használatával kell elvégezni.
- Biztonsági fejlesztés azáltal, hogy a korlátozott delegálást alaphelyzetbe állítja a AZUREADSSOACC objektumon.
- Szinkronizálási szabály hozzáadásakor/szerkesztésekor, ha az összekötő sémában található, de az összekötőhöz nem hozzáadott attribútumok vannak, akkor az attribútumok automatikusan hozzáadódnak az összekötőhöz. Ugyanez vonatkozik a szabály által érintett objektum típusára is. Ha bármilyen adat hozzá lett adva az összekötőhöz, az összekötő a következő szinkronizálási ciklusban teljes importálásra lesz megjelölve.
- Az új Azure AD Connect központi telepítések nem támogatják a vállalati vagy tartományi rendszergazdákat, mivel az összekötő fiók már nem támogatott. A jelenlegi Azure AD Connect vállalati vagy tartományi rendszergazda használatával üzemelő központi telepítések nem érintik ezt a kiadást.
- A szinkronizálási kezelőben teljes szinkronizálás fut a szabály létrehozása/szerkesztése/törlése során. Egy előugró ablak jelenik meg minden olyan szabály változásakor, amely értesíti a felhasználót, ha teljes Importálás vagy teljes szinkronizálás fut.
- Az "összekötők > tulajdonságok > kapcsolat" lapon a hibák elhárításához hozzáadott problémák elhárítása.
- A szinkronizálási szolgáltatás kezelőjének elavult figyelmeztetést adott az összekötő tulajdonságlapján. Ez a figyelmeztetés értesíti a felhasználót, hogy a módosításokat a Azure AD Connect varázslón keresztül kell elvégezni.
- Új hiba történt a felhasználó jelszavas házirendjével kapcsolatos problémák esetén.
- A tartományok és szervezeti egységek szűrése által a csoportosítási szűrők helytelen konfigurálásának megakadályozása. A csoport szűrése hibaüzenetet jelenít meg, ha a megadott csoport tartománya/szervezeti egysége már ki van szűrve, és a felhasználó továbbra is továbblép, amíg a probléma fel nem oldódik.
- A felhasználók már nem hozhatnak létre összekötőt Active Directory tartományi szolgáltatások vagy Windows Azure Active Directoryhoz a Synchronization Service Manager felhasználói felületen.
- Az egyéni FELHASZNÁLÓIFELÜLET-vezérlők rögzített hozzáférhetősége a Synchronization Service Managerban.
- A rendszer a Azure AD Connect összes bejelentkezési módszerének hat összevonási felügyeleti feladatát engedélyezte.  (Korábban csak az "Update AD FS TLS/SSL-tanúsítvány" feladat volt elérhető az összes bejelentkezéshez.)
- A rendszer figyelmeztetést adott a bejelentkezési módszer az PHS vagy a PTA rendszerbe való módosításakor, hogy az összes Azure AD-tartomány és-felhasználó felügyelt hitelesítésre lesz konvertálva.
- A rendszer eltávolította a jogkivonat-aláíró tanúsítványokat az "Azure AD-és AD FS-megbízhatóság alaphelyzetbe állítása" feladatból, és egy külön alfeladatot adott hozzá a tanúsítványok frissítéséhez.
- Hozzáadott egy új, "tanúsítványok kezelése" nevű összevonási felügyeleti feladatot, amely altevékenységekkel frissíti a TLS-vagy jogkivonat-aláíró tanúsítványokat a AD FS farmhoz.
- Hozzáadott egy új, az "elsődleges kiszolgáló megadása" nevű összevonási felügyeleti alfeladatot, amely lehetővé teszi, hogy a rendszergazdák új elsődleges kiszolgálót adjanak meg a AD FS Farm számára.
- Hozzáadott egy új, "kiszolgálók kezelése" nevű összevonási felügyeleti feladatot, amely alfeladatokkal rendelkezik egy AD FS-kiszolgáló üzembe helyezéséhez, egy webalkalmazás-proxykiszolgáló üzembe helyezéséhez, valamint az elsődleges kiszolgáló megadásához.
- Egy új, "az összevonási konfiguráció megtekintése" nevű összevonási felügyeleti feladatot adott hozzá, amely megjeleníti az aktuális AD FS beállításait.  (Ezen túlmenően a AD FS beállítások el lettek távolítva a "megoldás áttekintése" oldalon.)

### <a name="fixed-issues"></a>Megoldott problémák
- Hiba történt a szinkronizálás hibája miatt, amikor egy felhasználói objektum átveszi a kapcsolódó kapcsolattartási objektumát (például a felhasználó saját felettese).
- A Súgó előugró ablakai mostantól a billentyűzet fókuszában jelennek meg.
- Ha az összes ütköző alkalmazás 6 órán keresztül fut, akkor a frissítés után folytassa a frissítést.
- A címtár-bővítmények kiválasztásakor az ügyfél által 100-ig kiválasztható attribútumok száma. Ez megakadályozza, hogy a hiba az exportálás során bekövetkezik, mivel az Azure-ban legfeljebb 100 kiterjesztési attribútum adható meg.
- Kijavított egy hibát, hogy az AD kapcsolódási parancsfájl robusztusabb legyen.
- Kijavított egy hibát, hogy Azure AD Connect telepítsen egy olyan gépre, amely egy meglévő nevesített Pipes WCF szolgáltatást használ.
- Továbbfejlesztett diagnosztika és hibaelhárítás a csoportházirendekben, amelyek nem teszik lehetővé, hogy a ADSync szolgáltatás az első telepítésekor induljon el.
- Kijavítva egy hiba, amelyben a Windows rendszerű számítógép megjelenítendő neve helytelenül lett írva.
- Javítsa ki a hibát, ha a Windows rendszerű számítógép operációsrendszer-típusa helytelenül lett írva.
- Kijavítva egy olyan hibát, amelyben a nem Windows 10 rendszerű számítógépek váratlanul szinkronizálva lettek. Vegye figyelembe, hogy ennek a változásnak a hatása az, hogy a korábban szinkronizált, nem Windows 10 rendszerű számítógépek most törlődnek. Ez nem befolyásolja az olyan szolgáltatásokat, mint a Windows rendszerű számítógépek szinkronizálása kizárólag hibrid Azure AD-tartományhoz való csatlakozáshoz, amely csak Windows-10 rendszerű eszközökhöz használható.
- Számos új (belső) parancsmagot adott hozzá a ADSync PowerShell-modulhoz.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Ismert probléma történt a Azure AD Connect korábbi verzióról történő frissítésével a 1.3.21.0, ahol a Microsoft 365-portál nem tükrözi a frissített verziót, noha Azure AD Connect frissítése sikeresen megtörtént.
>
> Ennek megoldásához importálnia kell a **AdSync** modult, majd futtatnia kell a `Set-ADSyncDirSyncConfiguration` PowerShell-parancsmagot a Azure ad Connect kiszolgálón.  A következő lépéseket hajthatja végre:
>
>1. Nyissa meg a PowerShellt rendszergazdai módban.
>2. Futtassa az `Import-Module "ADSync"` parancsot.
>3. Futtassa az `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` parancsot.
 
### <a name="release-status"></a>Kiadás állapota 

05/14/2019: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák 

- A Microsoft Azure Active Directory Connect build 1.3.20.0 található, a jogosultságok kiterjesztésével kapcsolatos sebezhetőség javítva.  Ez a biztonsági rés bizonyos körülmények között lehetővé teheti, hogy egy támadó egy kiemelt fiók környezetében két PowerShell-parancsmagot hajtson végre, és magas jogosultsági szintű műveleteket hajtson végre.  Ez a biztonsági frissítés a parancsmagok letiltásával oldja meg a problémát. További információ: [biztonsági frissítés](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
