---
title: 'Azure AD Connect: Verzió kiadási előzményeinek | Microsoft Docs'
description: Ez a cikk felsorolja a Azure AD Connect és Azure AD-szinkronizáló.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67bc46b4f612d3d2f377070d5d8280512e0e3df
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576363"
---
# <a name="azure-ad-connect-version-release-history"></a>Az Azure AD Connect verziókiadásai
A Azure Active Directory (Azure AD) csapata rendszeresen frissíti Azure AD Connect új funkciókkal és funkciókkal. Nem minden kiegészítés alkalmazható minden célközönségre.

Ez a cikk segít nyomon követni a kiadott verziókat, és megérteni a legújabb verzió változásait.



Ez a táblázat a kapcsolódó témakörök listáját tartalmazza:

Témakör |  Részletek
--------- | --------- |
A frissítés lépései a Azure AD Connect | Különböző módszerek az [előző verzióról a](how-to-upgrade-previous-version.md) legújabb verzióra Azure AD Connect frissítéshez.
Szükséges engedélyek | A frissítés alkalmazásához szükséges engedélyekért lásd: [fiókok és engedélyek.](reference-connect-accounts-permissions.md#upgrade)
Letöltés| [Töltse le Azure AD Connect.](https://go.microsoft.com/fwlink/?LinkId=615771)

>[!NOTE]
>Az Azure AD Connect új verziójának kiadása egy olyan folyamat, amely számos minőségellenőrzési lépést igényel a szolgáltatás működésének biztosításához, és miközben végigveszi a folyamatot, egy új kiadás verziószáma és a kiadás állapota frissül, hogy az a legújabb állapotot tükrözze.
A folyamat során a kiadás verziószáma az "1.3.X.0"-hez képest kisebb verziószámú "X" betűvel jelenik meg – ez azt jelzi, hogy a dokumentum kibocsátási megjegyzései az "1.3" kezdetű összes verzióra érvényesek. Amint véglegesítettük a kiadási folyamatot, a kiadás verziószáma a legutóbb kiadott verzióra frissül, a kiadás állapota pedig "Kiadás letöltésre és automatikus frissítésre" lesz frissítve.
A Azure AD Connect nem minden kiadása lesz elérhető az automatikus frissítéshez. A kiadás állapota jelzi, hogy a kiadás elérhető-e automatikus frissítéshez vagy csak letöltéshez. Ha az automatikus frissítés engedélyezve volt a Azure AD Connect-kiszolgálón, akkor a kiszolgáló automatikusan a legújabb verzióra Azure AD Connect az automatikus frissítéshez kiadott verzióra. Vegye figyelembe, hogy Azure AD Connect konfigurációk nem jogosultak az automatikus frissítésre. 

Az automatikus frissítés használatának tisztázása érdekében az a célja, hogy minden fontos frissítést és kritikus javítást lekultáljon Önnek. Ez nem feltétlenül a legújabb verzió, mert nem minden verzió igényel/tartalmaz javítást egy kritikus biztonsági problémához (csak egy példa a sokra). Az ilyen problémát az automatikus frissítés által biztosított új verzióval lehet megoldani. Ha nincsenek ilyen problémák, nincsenek az automatikus frissítéssel lekért frissítések, és általában véve jónak kell lennie, ha a legújabb automatikus verziófrissítést használja.
Ha azonban a legújabb funkciókat és frissítéseket szeretné kapni, a legjobb módszer annak ellenőrzésére, hogy van-e ilyen, ha ellenőrzi ezt az oldalt, és a megfelelő módon telepíti őket. 

Kövesse ezt a hivatkozást az automatikus [frissítéssel kapcsolatos további információkért](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> 2024. április 1-től kivezetjük az Azure AD Connect 2018. május 1. előtti – 1.1.751.0-s és régebbi verzióit. 
>
> Az optimális támogatási élmény érdekében győződjön meg arról, hogy a Azure AD Connect legújabb verzióját futtatja. 
>
>Ha a Azure AD Connect egy visszavont verzióját futtatja, előfordulhat, hogy nem tudja a legújabb biztonsági javításokat, teljesítménybeli javításokat, hibaelhárítási és diagnosztikai eszközöket és szolgáltatásfejlesztéseket használni, és ha támogatásra van szüksége, előfordulhat, hogy nem tudjuk biztosítani a szervezet által szükséges szolgáltatásszintet.
>

>
>Ebből a [cikkből többet](./how-to-upgrade-previous-version.md) is megtudhat arról, hogyan frissítheti Azure AD Connect legújabb verzióra.
>
>A visszavont verziók verzióelőzményekkel kapcsolatos információiért tekintse meg a Azure AD Connect [kiadási előzmények archívumát.](reference-connect-version-history-archive.md)

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> A Azure AD Connect V2 végponti API mostantól elérhető ezekben az Azure-környezetekben:
> - Azure Commercial
> - Azure China-felhő
> - Azure US Government-felhő Az Azure német felhőben nem lesz elérhető

### <a name="release-status"></a>Kiadás állapota
2021.03.31. : Csak letöltésre van kiadva, automatikus frissítésre nem érhető el

### <a name="bug-fixes"></a>Hibajavítások
- Ez a kiadás kijavít egy hibát az 1.6.2.4-es verzióban, amely miatt az Azure AD Connect Health-funkció nem megfelelően lett regisztrálva, és nem működött. Az 1.6.2.4 buildet üzembe helyező ügyfeleket arra kérik, hogy ezzel a buildel frissítik Azure AD Connect-kiszolgálójukat, amely megfelelően regisztrálja az Állapot funkciót. 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> 2021. március 30-i frissítés: Ebben a buildben hibát fedeztünk fel. A build telepítése után az állapotszolgáltatások nem regisztrálva vannak. Javasoljuk, hogy ne telepítse ezt a buildet. Rövidesen kiadunk egy gyorsjavítást.
> Ha már telepítette ezt a buildet, manuálisan regisztrálhatja az állapotszolgáltatásokat a parancsmag használatával, ahogy az ebben [a cikkben is látható](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#manually-register-azure-ad-connect-health-for-sync)

>[!NOTE]
> - Ez a kiadás csak letöltésre lesz elérhető.
> - Az erre a kiadásra való frissítéshez teljes szinkronizálásra lesz szükség a szinkronizálási szabályok változásai miatt.
> - Ez a kiadás alapértelmezés szerint az AADConnect-kiszolgálót az új V2-végpontra használja. Vegye figyelembe, hogy ez a végpont nem támogatott a német országos felhőben, és [](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) ha ebben a környezetben kell üzembe helyeznie ezt a verziót, kövesse ezeket az utasításokat a V1-végpontra való visszaváltáshoz. Ha ezt nem teszi meg, szinkronizálási hibákat fog eredményezni.

### <a name="release-status"></a>Kiadás állapota
2021.03.19. : Letöltésre kiadva, automatikus frissítéshez nem érhető el

### <a name="functional-changes"></a>Funkcionális változások

 - Frissültek az alapértelmezett szinkronizálási szabályok a visszaírt csoportok tagságának 50 000 tagra való korlátozására.
   - Új alapértelmezett szinkronizálási szabályok hozzáadva a csoportvisszaírás tagságszámának korlátozására (Ki a következőre: AD – Csoportvisszaírás tagkorlátja) és csoportszinkronizálás Azure Active Directory -csoportokra (AAD- csoportírási tagok korlátja).
   - Tagattribútum hozzáadva az "Out to AD - Group SOAInAAD - Exchange" szabályhoz, amely a visszaírt csoportok tagjait 50 000-re korlátozza
 - A szinkronizálási szabályok frissítve a Group Writeback v2 támogatásához – Ha a "In from AAD - Group SOAInAAD" szabály klónozott, és az AADConnect frissül.
     – A frissített szabály alapértelmezés szerint le lesz tiltva, így a targetWritebackType null értékű lesz.
     - Az AADConnect terjesztési csoportként írja vissza az összes felhőcsoportot (beleértve Azure Active Directory a visszaírásra engedélyezett biztonsági csoportokat is).
   -Ha az "Out to AD - Group SOAInAAD" szabály klónozott, és az AADConnect frissítve van.
     - A frissített szabály alapértelmezés szerint le lesz tiltva. Az új "Out to AD - Group SOAInAAD - Exchange" szinkronizálási szabály azonban engedélyezve lesz.
     - A klónozott egyéni szinkronizálási szabály prioritásától függően az AADConnect a Mail és az Exchange attribútumot fogja továbbadni.
     - Ha a klónozott egyéni szinkronizálási szabály nem tartalmaz levél- és Exchange-attribútumokat, akkor az új Exchange Sync-szabály hozzáadja ezeket az attribútumokat.
 - A szelektív [jelszó-kivonatszinkronizálás támogatása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Hozzá van adva az új [Single Object Sync parancsmag.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync) Használja ezt a parancsmagot a szinkronizálási konfiguráció Azure AD Connect hibaelhárításhoz. 
 -  Azure AD Connect mostantól támogatja a hibrid identitás-rendszergazdai szerepkört a szolgáltatás konfigurálásához.
 - Az AADConnectHealth ügynök frissült a 3.1.83.0-sra
 - Az [ADSyncTools PowerShell-modul új verziója,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools)amely számos új vagy továbbfejlesztett parancsmagot tartalmaz. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Frissült a hibanaplózás a jogkivonatok beszerzésének sikertelenségére.
 - Frissített "További információk" hivatkozások a konfigurációs oldalon, hogy további részleteket adjanak a hivatkozott adatokról.
 - Az Explicit oszlop el lett távolítva a régi szinkronizálási felhasználói felület CS Search (CS-keresés) lapról
 - A Csoportvisszaírási folyamathoz további felhasználói felület is hozzá lett adva, amely hitelesítő adatokat kér a felhasználótól, vagy saját engedélyeket konfigurál az ADSyncConfig modullal, ha a hitelesítő adatok még nem voltak megadva egy korábbi lépésben.
 - MsA automatikus létrehozása ADSync-szolgáltatásfiókhoz egy tartományvezérlőn. 
 -  A DirSync szolgáltatáscsoport-visszaírási V2 Azure Active Directory beállításának és lehívásának lehetősége hozzáadva a meglévő parancsmagokban:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - 2 parancsmag hozzáadva az AWS API verziójának olvasásához
    - Get-ADSyncAADConnectorImportApiVersion – az AWS API-verzió importálása
    - Get-ADSyncAADConnectorExportApiVersion – az AWS API-verzió exportálása

 - A rendszer mostantól nyomon követi a szinkronizálási szabályokon végrehajtott módosításokat a szolgáltatás változásainak hibaelhárítása érdekében. A "Get-ADSyncRuleAudit" parancsmag lekéri a nyomonott módosításokat.
 - Az [ADSyncConfig PowerShell-modulban](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) frissítette az Add-ADSyncADDSConnectorAccount parancsmagot, hogy az ADSyncAdmin csoportban egy felhasználó módosítsa a AD DS Connector-fiókot. 

### <a name="bug-fixes"></a>Hibajavítások
 - A letiltott előtérszín frissült, hogy fehér háttér mellett megfeleljen a különbözőségi követelményeknek. További feltételek hozzáadva a navigációs fához az előtér szövegszínének fehérre való beállításához, ha egy letiltott oldal van kiválasztva a követelményeknek való megfeleléshez.
 - Részletesség növelése Set-ADSyncPasswordHashSyncPermissions parancsmag esetében – Frissített PHS-engedélyszk szkript (Set-ADSyncPasswordHashSyncPermissions) egy opcionális "ADobjectDN" paraméter beírása érdekében. 
 - Kisegítő lehetőségek hibajavítása. A képernyőolvasó az erdők listáját a "Forest List **list"**(Erdőlista) lista helyett "Erdőlista" elemmel írja le, amely az erdők **listáját tartalmazza.**
 - Frissült a képernyőolvasó kimenete a Azure AD Connect egyes elemeihez. Frissült a gomb rámutatási színe, hogy megfeleljen a kontrasztos követelményeknek. Frissült Synchronization Service Manager cím színe, hogy megfeleljen a kontrasztos követelményeknek.
 - Ki van javítva az AADConnect egyéni bővítményattribútumokkal rendelkező exportált konfigurációból való telepítésével kapcsolatos hiba – Hozzá van adva egy feltétel, amely kihagyja a bővítményattribútumok ellenőrzését a célsémában a szinkronizálási szabály alkalmazása során.
 - Ha a csoportvisszaírási funkció engedélyezve van, a telepítéshez megfelelő engedélyeket ad hozzá a rendszer.
 - A duplikált alapértelmezett szinkronizálási szabályok sorrendjének kijavítva importáláskor
 - Kijavítottunk egy hibát, amely átmeneti hibát okozott a 2-es verziós API különbözeti importálása során az állapotportálon kijavított ütköző objektum esetén.
 - Kijavítottunk egy hibát a szinkronizálási motorban, amely inkonzisztens kapcsolatállapotot okozott a CS-objektumoknak
 - Importálási számlálók hozzáadva Get-ADSyncConnectorStatistics kimenethez.
 - Ki van javítva a tartomány nem elérhető kiválasztásával (korábban kiválasztott) hiba néhány sarokban a pass2 varázsló során.
 - A házirend importálása és exportálása meghiúsult, ha az egyéni szabály ismétlődő sorrendben van 
 - Kijavítottunk egy hibát a tartományválasztási logikában.
 - Kijavít egy hibát az 1.5.18.0 buildel kapcsolatban, ha az mS-DS-ConsistencyGuid a forráshorgony, és klónozza a In from AD - Group Join szabályt.
 - A friss AADConnect-telepítések a felhőben tárolt exportálási törlési küszöbértéket használják, ha van ilyen, és nem érhetők el másikak.
 - Kijavítva a hiba, amely miatt az AADConnect nem olvassa be a hibrid csatlakoztatott eszközök AD displayName módosításait

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Kiadás állapota
2020.07.29. : Letöltésre kiadva

### <a name="functional-changes"></a>Funkcionális változások
Ez egy hibajavítási kiadás. Ebben a kiadásban nincsenek funkcionális módosítások.

### <a name="fixed-issues"></a>Megoldott problémák

- Kijavítottunk egy hibát, amely miatt a rendszergazda nem tudja engedélyezni a "Közvetlen egyszeri bejelentkezést", ha az AZUREADSSOACC számítógépfiók már jelen van a "Active Directory".
- Kijavítottunk egy hibát, amely átmeneti hibát okozott a 2-es verziós API különbözeti importálása során az állapotportálon kijavított ütköző objektum esetén.
- Kijavítottunk egy problémát az importálási/exportálási konfigurációban, amely miatt a letiltott egyéni szabály engedélyezve lett.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Kiadás állapota
2020.07.10.: Letöltésre kiadva

### <a name="functional-changes"></a>Funkcionális változások
Ez a kiadás tartalmazza a meglévő Azure AD Connect konfigurációjának exportálására használható funkciók nyilvános előzetesét. JSON-fájl, amely egy új Azure AD Connect telepítésekor használható az eredeti kiszolgáló másolatának létrehozásához.

Az új funkció részletes leírását ebben a [cikkben talál.](./how-to-connect-import-export-config.md)

### <a name="fixed-issues"></a>Megoldott problémák
- Kijavítottunk egy hibát, amely miatt a frissítés során téves figyelmeztetés jelenik meg a helyi adatbázis méretével kapcsolatban a honosított buildek esetében.
- Kijavítottunk egy hibát, amely miatt a fióknév/tartománynév felcserélése téves hibát jelzett az alkalmazásesemények során.
- Kijavítottunk egy hibát, Azure AD Connect tartományvezérlőre való telepítés sikertelen volt, és a "member not found" (a tag nem található) hibaüzenetet ad vissza.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Kiadás állapota
2020.05.07. : Letöltésre kiadva

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-build kijavít egy problémát, amely miatt a nem kijelölt tartományok helytelenül lett kiválasztva a varázsló felhasználói felületén, ha csak a grandfix tárolók voltak kiválasztva.


>[!NOTE]
>Ez a verzió tartalmazza az új Azure AD Connect Sync V2 végponti API-t.  Ez az új V2-végpont jelenleg nyilvános előzetes verzióban érhető el.  Ez a verzió vagy újabb szükséges az új V2 végponti API-hoz.  Ennek a verziónak a telepítése azonban nem engedélyezi a V2-végpontot. A V1 végpontot továbbra is használni fogja, kivéve, ha engedélyezi a V2-végpontot.  Az engedélyezéshez és a nyilvános előzetes verzió engedélyezéséhez Azure AD Connect V2-végpont API szinkronizálása (nyilvános előzetes verzió) a [V2 végponti API](how-to-connect-sync-endpoint-api-v2.md) szinkronizálása alatt található lépéseket kell követnie.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Kiadás állapota
2020.04.23. : Letöltésre kiadva

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-build kijavít egy, az 1.5.20.0 buildben bevezetett hibát, amelyben az MFA-val rendelkező bérlői rendszergazda nem tudta engedélyezni a DSSO-t.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Kiadás állapota
2020.04.20.: Letöltésre kiadva

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-build kijavít egy hibát az 1.5.20.0 buildben, ha klónozta a In from **AD - Group Join** szabályt, és nem klónozta a In **from AD - Group Common** szabályt.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Kiadás állapota
2020.04.09. : Letöltésre kiadva

### <a name="fixed-issues"></a>Megoldott problémák
- Ez a gyorsjavítás-build kijavít egy hibát az 1.5.18.0 buildben, ha a Csoportszűrés funkció engedélyezve van, és az mS-DS-ConsistencyGuid parancs van használatban forráshorgonyként.
- Ki lett javítva az ADSyncConfig PowerShell-modul egyik problémája, amely miatt az összes Set-ADSync* permissions parancsmagban használt DSACLS-parancs a következő hibák valamelyikét okozhatta:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Ha klónozta a In from AD - Group Join (Be az AD-ból – Csoporthoz való csatlakozás szinkronizálása) szinkronizálási szabályt, és nem klónozta a In from **AD – Group Common** sync (Be az **AD-ból** – Csoport közös szinkronizálási szabálya) szabályt, és megtervelte a frissítést, a frissítés részeként kövesse az alábbi lépéseket:
> 1. A frissítés során törölje a jelet A **szinkronizálási folyamat elindítani a konfiguráció befejezésekor jelölőnégyzetből.**
> 2. Szerkessze a klónozott illesztés szinkronizálási szabályát, és adja hozzá a következő két átalakítást:
>     - A közvetlen folyamatot állítsa `objectGUID` a `sourceAnchorBinary` beállításra.
>     - Állítsa a `ConvertToBase64([objectGUID])` kifejezésfolyamot a `sourceAnchor` következőre: .     
> 3. Engedélyezze az ütemezőt a `Set-ADSyncScheduler -SyncCycleEnabled $true` használatával.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Kiadás állapota
2020. 04. 02. : Letöltésre kiadva

### <a name="functional-changes-adsyncautoupgrade"></a>Funkcionális módosítások ADSyncAutoUpgrade 

- Az mS-DS-ConsistencyGuid szolgáltatás támogatása hozzáadva a csoportobjektumok számára. Ez lehetővé teszi csoportok áthelyezését az erdők között, vagy újracsatlakoztathatja az AD-ben található csoportokat az Azure AD-be, ahol az AD-csoport objektumazonosítója megváltozott, például ha egy AD-kiszolgálót egy adott idő után újraépít. További információ: [Csoportok mozgatása erdők között.](how-to-connect-migrate-groups.md)
- Az mS-DS-ConsistencyGuid attribútum automatikusan be van állítva az összes szinkronizált csoportra, így Önnek semmit nem kell tennie a funkció engedélyezéséhez. 
- A Get-ADSyncRunProfile el, mert már nincs használatban. 
- Módosult a figyelmeztetés, amely akkor jelenik meg, amikor vállalati rendszergazdai vagy tartományi rendszergazdai fiókot próbál használni a AD DS-összekötő fiókjához, hogy több kontextust biztosítson. 
- Új parancsmag hozzáadva, amely eltávolítja az objektumokat az összekötőtérből, a régi CSDelete.exe eszköz el lesz távolítva, és a rendszer lecseréli az új Remove-ADSyncCSObject parancsmagra. A Remove-ADSyncCSObject parancsmag bemenete egy CsObject. Ez az objektum a Get-ADSyncCSObject használatával olvasható be.

>[!NOTE]
>A régi CSDelete.exe eszköz el lett távolítva, és az új Remove-ADSyncCSObject lett lecserélve 

### <a name="fixed-issues"></a>Megoldott problémák

- Kijavítottunk egy hibát a csoportvisszaírási erdőben/szervezeti egységválasztóban a Azure AD Connect varázsló újrafuttatásakor a funkció letiltása után. 
- Új hibalap jelenik meg, amely akkor jelenik meg, ha a szükséges DCOM-beállításazonosítók hiányoznak egy új súgóhivatkozással. Az adatok naplófájlba is írva vannak. 
- Kijavítottunk egy hibát a Azure Active Directory szinkronizálási fiók létrehozásakor, amely miatt a címtárbővítmények vagy a PHS engedélyezése meghiúsulhat, mert a fiók nem lett propagálva az összes szolgáltatásreplikára a használatbavétel előtt. 
- Kijavítottunk egy hibát a szinkronizálási hibák tömörítési segédprogramján, amely nem megfelelően kezeli a helyettes karaktereket. 
- Kijavítottunk egy hibát az automatikus frissítésben, amely felfüggesztett állapotban hagyta a kiszolgálót az ütemezőben. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Kiadás állapota
2019.09.12.: Kiadás letölthető. Az automatikus frissítéssel nem érhető el.
### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
- Frissítettük a jelszó kivonatszinkronizálását a Azure AD Domain Services, hogy megfelelően figyelembe tudjuk venni a Kerberos-kivonatok kitöltését.  Ez teljesítménybeli javulást biztosít az Azure AD-ről a Azure AD Domain Services.
- A hitelesítési ügynök és a Service Bus közötti megbízható munkamenetek támogatása hozzáadva.
- Ez a kiadás kikényszeríteni a TLS 1.2-t a hitelesítési ügynök és a felhőszolgáltatások közötti kommunikációhoz.
- Hozzáadtunk egy DNS-gyorsítótárat a hitelesítési ügynök és a felhőszolgáltatások közötti websocket-kapcsolatokhoz.
- Hozzáadtunk egy képességet, hogy a felhőből adott ügynököt célozunk meg az ügynökkapcsolat teszteléséhez.

### <a name="fixed-issues"></a>Megoldott problémák
- Az 1.4.18.0-s kiadásban hiba történt, amely miatt a DSSO PowerShell-parancsmagja a bejelentkezési windowsos hitelesítő adatokat használja a ps futtatása közben megadott rendszergazdai hitelesítő adatok helyett. Ennek eredményeképpen nem lehetett engedélyezni a DSSO-t több erdőben a felhasználói Azure AD Connect keresztül. 
- Javítás történt a DSSO egyidejű engedélyezéséhez az összes erdőben a Azure AD Connect felhasználói felületén

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Kiadás állapota
2019.08.11. : Letöltésre kiadva. Az automatikus frissítéssel nem érhető el.

>[!IMPORTANT]
>Az Azure AD Connect ezen kiadásának belső sémaváltozása miatt, ha AD FS megbízhatósági kapcsolatok konfigurációs beállításait az MSOnline PowerShell használatával kezeli, akkor frissítenie kell az MSOnline PowerShell-modult az 1.1.183.57-es vagy újabb verzióra

### <a name="fixed-issues"></a>Megoldott problémák

Ez a verzió kijavítja a meglévő hibrid Azure AD-hez csatlakozott eszközökkel kapcsolatban fennálló problémát. Ez a kiadás egy új eszközszinkronizálási szabályt tartalmaz, amely kijavítja ezt a problémát.
Vegye figyelembe, hogy ez a szabályváltozás elavult eszközök törlését okozhatja az Azure AD-ból. Ez nem okoz problémát, mivel ezeket az eszközobjektumokat az Azure AD nem használja a feltételes hozzáférés engedélyezése során. Egyes ügyfelek esetében a szabályváltozás által törölt eszközök száma meghaladhatja a törlési küszöbértéket. Ha azt látja, hogy az Azure AD-beli eszközobjektumok törlése meghaladja az Exportálási törlés küszöbértékét, javasolt engedélyezni a törléseket. [A törlési folyamat engedélyezése, ha túllépik a törlési küszöbértéket](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Kiadás állapota
2019.09.28.: Kiadva a bérlők kiválasztásához az automatikus frissítéshez. Nem tölthető le.

Ez a verzió kijavít egy hibát, amely miatt egyes kiszolgálók automatikusan frissítve voltak egy korábbi verzióról az 1.4.18.0-s verzióra, és problémákat tapasztaltak az önkiszolgáló jelszóátállítás (SSPR) és a jelszóvisszaírás során.

### <a name="fixed-issues"></a>Megoldott problémák

Bizonyos körülmények között az 1.4.18.0-s verzióra automatikusan frissített kiszolgálók nem engedélyezték újra az önkiszolgáló jelszóátállítást és a jelszóvisszaírást a frissítés befejezése után. Ez az automatikus frissítés kiadás kijavítja a problémát, és újra engedélyezi az önkiszolgáló jelszóátállítást és a jelszóvisszaírást.

Kijavítottunk egy hibát a szinkronizálási hibák tömörítési segédprogramban, amely nem megfelelően kezeli a helyettes karaktereket.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Egy olyan incidenst vizsgálunk, amelyben egyes ügyfelek problémát tapasztalnak a meglévő Hibrid Azure AD-hez csatlakozott eszközökkel kapcsolatban, miután frissítettek a Azure AD Connect. Azt javasoljuk ügyfeleinknek, hogy halasztják el a hibrid Azure AD-csatlakozást, hogy halasztják el a verziófrissítést, amíg a problémák kiváltó okait nem teljesen megértik és mérsékelik. A lehető leghamarabb további információt is biztosítanak.

>[!IMPORTANT]
>A szolgáltatás ezen Azure AD Connect egyes ügyfelek windowsos eszközeik egy része vagy egésze eltűnhet az Azure AD-ból. Ez nem okoz problémát, mivel ezeket az eszköz identitásokat az Azure AD nem használja a feltételes hozzáférés engedélyezése során. További információ: [Az 1.4.xx.x Azure AD Connect eszköz eltűnéséről](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Kiadás állapota
2019.09.25.: Csak automatikus frissítésre van kiadva.

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések
- Az új hibaelhárítási eszközök segítségével elháríthatja a "felhasználó nem szinkronizálható", "a csoport szinkronizálása nem" vagy a "csoporttag nem szinkronizál" forgatókönyveket.
- Az országos felhők támogatása hozzáadva a Azure AD Connect szkriptben.
- Az ügyfeleket tájékoztatni kell arról, hogy a MIIS_Service WMI-végpontjai el vannak távolítva. Most már minden WMI-műveletet PS-parancsmagokkal kell elvégrehajtásához.
- A biztonság javítása a korlátozott delegálás alaphelyzetbe állításával az AZUREADSSOACC objektumon.
- Szinkronizálási szabály hozzáadásakor vagy szerkesztésekor, ha a szabályban olyan attribútumok vannak használva, amelyek az összekötő sémában vannak, de nincsenek hozzáadva az összekötőhöz, az attribútumok automatikusan hozzáadódnak az összekötőhöz. Ugyanez vonatkozik a szabály által érintett objektumtípusra is. Ha bármit hozzáad az összekötőhöz, az összekötő teljes importálásra lesz megjelölve a következő szinkronizálási ciklusban.
- A vállalati vagy tartományi rendszergazda összekötőfiókként való használata már nem támogatott az új Azure AD Connect telepítések során. Ez Azure AD Connect kiadás nem érinti az aktuális vállalati vagy tartományi rendszergazdai szerepkört, mint összekötőfiókot használó központi telepítéseket.
- A Szinkronizáláskezelőben a rendszer teljes szinkronizálást futtat szabály létrehozásakor/szerkesztésekor/törlésekor. Minden szabály módosításakor felugró ablak jelenik meg, amely értesíti a felhasználót, ha teljes importálást vagy teljes szinkronizálást fog futtatni.
- Új megoldási lépések hozzáadva a jelszóhibákhoz az "összekötők > tulajdonságok > kapcsolat" lapon.
- Elalasztó figyelmeztetés lett hozzáadva a szinkronizálási szolgáltatás kezelőjéhez az összekötő tulajdonságok lapján. Ez a figyelmeztetés értesíti a felhasználót arról, hogy a módosításokat a Azure AD Connect kell.
- Új hiba hozzáadva a felhasználó jelszó-szabályzatával kapcsolatos problémákhoz.
- A csoportszűrés hibás konfigurálásának megakadályozása tartomány- és szervezeti egységszűrők szerint. A csoportszűrés hibát jelez, ha a megadott csoport tartománya/szervezeti egy része már ki van szűrve, és a felhasználó nem halad tovább a probléma megoldásáig.
- A felhasználók már nem hozhatnak létre összekötőt a Active Directory Domain Services windowsos Azure Active Directory a felhasználói Synchronization Service Manager felületén.
- A felhasználói felület egyéni vezérlőinek akadálymentessége kijavítva a Synchronization Service Manager.
- Hat összevonás-felügyeleti feladat engedélyezve az összes bejelentkezési módszerhez a Azure AD Connect.  (Korábban csak a "TLS AD FS/SSL-tanúsítvány frissítése" feladat volt elérhető az összes bejelentkezéshez.)
- A bejelentkezési módszer összevonásról PHS-ről PTA-ba való módosításakor figyelmeztetés jelenik meg, amely szerint az összes Azure AD-tartomány és -felhasználó felügyelt hitelesítésre lesz konvertálva.
- A jogkivonat-aláíró tanúsítványokat eltávolítottuk az "Azure AD és AD FS megbízhatóság visszaállítása" feladatból, és hozzáadtunk egy külön alfeladatot a tanúsítványok frissítéséhez.
- Hozzá lett adva egy "Tanúsítványok kezelése" nevű új összevonás-kezelési feladat, amely alfeladatokkal rendelkezik a TLS- vagy jogkivonat-aláíró tanúsítványok frissítéséhez a AD FS farmon.
- Hozzá van adva egy "Elsődleges kiszolgáló megadása" nevű új összevonás-kezelési alfeladat, amely lehetővé teszi a rendszergazdák számára, hogy új elsődleges kiszolgálót adjanak meg a AD FS farmhoz.
- Hozzá lett adva egy új, "Kiszolgálók kezelése" nevű összevonási felügyeleti feladat, amely alfeladatokkal rendelkezik egy AD FS-kiszolgáló üzembe helyezéséhez, egy Web alkalmazásproxy-kiszolgáló telepítéséhez és az elsődleges kiszolgáló megadásához.
- Új összevonás-kezelési feladat hozzáadva "Összevonási konfiguráció megtekintése" néven, amely megjeleníti az aktuális AD FS beállításokat.  (A kiegészítés miatt a AD FS beállítások el lett távolítva a "Megoldás áttekintése" lapról.)

### <a name="fixed-issues"></a>Megoldott problémák
- Megoldottuk a szinkronizálási hibát abban a forgatókönyvben, amikor a megfelelő kapcsolatobjektumot átvesző felhasználói objektum önhivatkozással rendelkezik (például a felhasználó a saját kezelője).
- A súgó előugró ablakai most már billentyűzetfókuszban megjelenik.
- Automatikus frissítés esetén, ha az ütköző alkalmazások 6 óra után futnak, akkor le kell őket állnunk, és folytatni kell a frissítést.
- Korlátozza az ügyfél által választható attribútumok számát objektumonként 100-ra a címtárbővítmények kiválasztásakor. Ez megakadályozza, hogy a hiba az exportálás során fordul elő, mivel az Azure objektumonként legfeljebb 100 bővítményattribútummal rendelkezik.
- Kijavítottunk egy hibát, hogy az AD Connectivity szkript robusztusabb legyen.
- Kijavítottunk egy hibát, amely Azure AD Connect, hogy egy meglévő Named Pipes WCF-szolgáltatással telepítsen egy gépet.
- Továbbfejlesztett diagnosztika és hibaelhárítás olyan csoportházirendekkel, amelyek nem teszik lehetővé az ADSync szolgáltatás első telepítésekor való elindulását.
- Kijavítottunk egy hibát, amely miatt egy Windows-számítógép megjelenítendő neve helytelenül lett megírva.
- Ki lett javítva egy hiba, amely miatt egy Windows-számítógép operációsrendszer-típusa helytelenül lett megírva.
- Kijavítottunk egy hibát, amely miatt a Windows 10 számítógépek váratlanul szinkronizáltak. Vegye figyelembe, hogy ennek a módosításnak az a hatása, hogy a korábban szinkronizált, nem Windows-10 rendszerű számítógépek törlődnek. Ez nincs hatással a szolgáltatásokra, mivel a Windows rendszerű számítógépek szinkronizálása csak hibrid Azure AD-tartományhoz való csatlakozáshoz használatos, amely csak Windows-10-eszközökön működik.
- Több új (belső) parancsmag hozzáadva az ADSync PowerShell-modulhoz.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>A Azure AD Connect verzióról az 1.3.21.0-s verzióra való frissítés ismert problémája miatt a Microsoft 365 portál nem tükrözi a frissített verziót annak ellenére, hogy Azure AD Connect frissítés sikeres volt.
>
> A probléma megoldásához importálja az **AdSync** modult, majd futtassa a PowerShell-parancsmagot a `Set-ADSyncDirSyncConfiguration` Azure AD Connect kiszolgálón.  A következő lépéseket használhatja:
>
>1. Nyissa meg a PowerShellt rendszergazdai módban.
>2. Futtatja a `Import-Module "ADSync"` parancsot.
>3. Futtatja a `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""` parancsot.
 
### <a name="release-status"></a>Kiadás állapota 

2019.05.14.: Letöltésre kiadva

### <a name="fixed-issues"></a>Megoldott problémák 

- Kijavítottunk egy jogosultságszint-emelési biztonsági rést, amely az 1.3.20.0 Microsoft Azure Active Directory Connect buildben található.  Ez a biztonsági rés bizonyos körülmények között lehetővé teheti, hogy a támadó két PowerShell-parancsmagot hajtson végre egy kiemelt jogosultságú fiók környezetében, és emelt szintű műveleteket hajtson végre.  Ez a biztonsági frissítés úgy javítja ki a problémát, hogy letiltja ezeket a parancsmagokat. További információ: [biztonsági frissítés.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)


## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
