---
title: 'Azure AD Connect: kiadási előzmények archívuma | Microsoft Docs'
description: Ez a cikk a Azure AD Connect és Azure AD-szinkronizáló összes archivált kiadását felsorolja
services: active-directory
author: billmath
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 859a4f199e65dd0c3aee9424029f6060683d5fbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836087"
---
# <a name="azure-ad-connect-version-release-history-archive"></a>Azure AD Connect: kiadási előzmények archiválása

A Azure Active Directory (Azure AD) csapata rendszeresen frissíti Azure AD Connect új szolgáltatásokkal és funkciókkal. Nem minden kiegészítés alkalmazható minden célközönségre.

>[!NOTE] 
> Ez a cikk az Azure AD-1.3.20.0 és régebbi verzióinak összes archivált verziójára vonatkozó hivatkozási információt tartalmaz.  A jelenlegi kiadásokkal kapcsolatban lásd a [Azure ad Connect verziójának kiadási előzményeit](reference-connect-version-history.md) .

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Kiadás állapota 

04/24/2019: kiadva a letöltéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 

- Támogatás hozzáadása a tartomány frissítéséhez 
- Exchange-levelezés nyilvános mappái szolgáltatás 
- A varázsló hibáinak kezelése a szolgáltatás hibái esetén 
- Figyelmeztető hivatkozás hozzáadva Synchronization Service Manager felhasználói felületen az összekötő tulajdonságai lapon. 
- Az egyesített csoportok visszaírási szolgáltatás most már a GA 
- Továbbfejlesztett SSPR hibaüzenet, ha a TARTOMÁNYVEZÉRLŐhöz hiányzik egy LDAP-vezérlő 
- A DCOM beállításjegyzék-hibákhoz tartozó diagnosztika hozzáadva a telepítés során  
- A PHS RPC-hibák jobb nyomon követése 
- EA-jogcímek engedélyezése alárendelt tartományból 
- Az adatbázis nevének megadása a telepítés során (alapértelmezett név ADSync)
- Frissítsen a ADAL 3.19.8, és vegyen fel egy WS-Trust javítást a pingeléshez, és adja hozzá az új Azure-példányok támogatását 
- A csoportok szinkronizálási szabályainak módosítása a samAccountName, a DomainNetbios és a DomainFQDN-re a szükséges igényekhez 
- Módosítva az alapértelmezett szinkronizálási szabály kezelését – [itt](how-to-connect-fix-default-rules.md)olvashat bővebben.
- Új, Windows-szolgáltatásként futó ügynök lett hozzáadva. A "felügyeleti ügynök" nevű ügynök lehetővé teszi a Azure AD Connect-kiszolgáló mélyebb távoli diagnosztizálását, hogy segítséget nyújtson a Microsoft-mérnököknek a támogatási esetek megnyitásakor. Ez az ügynök nincs telepítve, és alapértelmezés szerint nincs engedélyezve.  Az ügynök telepítésével és engedélyezésével kapcsolatos további információkért tekintse meg a [Mi a Azure ad Connect felügyeleti ügynök?](whatis-aadc-admin-agent.md)című témakört. 
- Frissítette a végfelhasználói licencszerződést (EULA) 
- A rendszer a bejelentkezési típusként AD FS használó központi telepítések esetében hozzáadott automatikus frissítési támogatást.  Ez azt is megszüntette, hogy a frissítési folyamat részeként a AD FS Azure AD függő entitás megbízhatóságának frissítésére van szükség. 
- Egy Azure AD-megbízhatósági felügyeleti feladat hozzáadva, amely két lehetőséget kínál: a megbízhatóság elemzése/frissítése és a megbízhatóság alaphelyzetbe állítása. 
- Módosította a AD FS Azure AD függő entitás megbízhatósági viselkedését, hogy mindig a-SupportMultipleDomain kapcsolót használja (beleértve a megbízhatóságot és az Azure AD-tartomány frissítéseit is). 
- Módosította az új AD FS Farm telepítésének telepítését úgy, hogy egy. pfx-tanúsítványt igényel az előre telepített tanúsítvány használatával történő eltávolításával.
- Frissítve lett az új AD FS Farm-munkafolyamat telepítése, hogy az csak 1 AD FS és egy WAP-kiszolgáló telepítését engedélyezze.  Az összes további kiszolgáló a kezdeti telepítés után lesz végrehajtva. 

### <a name="fixed-issues"></a>Megoldott problémák 

- A ADSync szolgáltatáshoz tartozó SQL recsatlakoztatható logika javítása 
- Javítás a tiszta telepítés engedélyezéséhez üres SQL ALAPSZABÁLYÁBAN-adatbázis használatával 
- A PS-engedélyek parancsfájljának javítása a GWB engedélyeinek pontosításához 
- VSS-hibák kijavítása a LocalDB  
- Félrevezető hibaüzenet kijavítása, ha az Objektumtípus nincs a hatókörben 
- Kijavítva az a probléma, amikor az Azure AD PowerShell telepítése egy kiszolgálón lehetséges, hogy a szerelvény ütközik Azure AD Connectval. 
- Rögzített PHS hiba az átmeneti kiszolgálón, ha az összekötő hitelesítő adatai frissülnek a Synchronization Service Manager felhasználói felületen. 
- Rögzített memória-szivárgás 
- Egyéb frissítési javítások 
- Az exportáláshoz és a nem megerősített importáláshoz szükséges egyéb javítások 
- Hiba javítva egy fordított perjel a tartományban és a szervezeti egységben történő szűrésével 
- Kijavított egy problémát, amelyben a ADSync szolgáltatás több mint 2 percet vesz igénybe, és hibát okoz a frissítési időpontnál. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Kiadás állapota

12/18/2018: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák

Ez a Build frissíti a nem szabványos összekötőket (például az általános LDAP-összekötőt és az általános SQL-összekötőt) Azure AD Connect. További információ a megfelelő összekötők használatáról: a verzió 1.1.911.0 az [összekötő verziójának korábbi kiadásaiban](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Kiadás állapota
12/11/2018: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák
Ez a gyorsjavítás-összeállítás lehetővé teszi, hogy a felhasználó kiválasszon egy RegisteredDevices-tárolót a megadott erdőben, az eszköz visszaírási engedélyezésekor.  Az új eszközbeállítások funkcióit (1.1.819.0 – 1.2.68.0) tartalmazó korábbi verziókban a RegisteredDevices-tároló helye az erdő gyökerére korlátozódik, és nem engedélyezte a gyermektartomány tartományát.  Ez a korlátozás csak az új központi telepítések esetében nyilvánult meg – a helyben végzett frissítés nem érintett.  

Ha a frissített eszközbeállítások funkcióit tartalmazó buildek egy új kiszolgálóra lettek telepítve, és az eszköz visszaírási engedélyezve lett, akkor manuálisan kell megadnia a tároló helyét, ha nem szeretné az erdő gyökerében használni.  Ehhez le kell tiltania az eszköz visszaírási, és újra engedélyeznie kell, ami lehetővé teszi a tároló helyének megadását a "visszaírási erdő" lapon.



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Kiadás állapota 

11/30/2018: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák

Ez a gyorsjavítás olyan ütközést javít, amelyben a MSOnline PowerShell-galéria modul független jelenléte miatt előfordulhat hitelesítési hiba a szinkronizálási kiszolgálón.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Kiadás állapota 

11/19/2018: kiadva a letöltéshez

### <a name="fixed-issues"></a>Megoldott problémák

Ez a gyorsjavítás-összeállítás az előző összeállításban javít egy regressziót, ahol a jelszó visszaírási meghiúsul, ha Windows Server 2008/R2 rendszerű tartományvezérlőt használ.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Kiadás állapota 

10/25/2018: kiadva a letöltéshez


### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 


- Módosult az attribútum működésének visszaírása annak érdekében, hogy a szolgáltatott hangposta a várt módon működjön.  Bizonyos esetekben az Azure AD felülírta a msExchUcVoicemailSettings attribútumot a visszaírás során NULL értékkel.  Az Azure AD mostantól többé nem törli az attribútum helyszíni értékét, ha a felhő értéke nincs beállítva.
- A diagnosztika hozzáadva a Azure AD Connect varázslóban a kapcsolódási problémák vizsgálatához és azonosításához az Azure AD-ben. Ugyanezen diagnosztika közvetlenül a PowerShellen keresztül is futtatható a test-AdSyncAzureServiceConnectivity parancsmag használatával. 
- A diagnosztika hozzáadva a Azure AD Connect varázslóban az AD-kapcsolati problémák vizsgálatához és azonosításához. Ugyanez a diagnosztika közvetlenül a PowerShellen keresztül is futtatható a ADConnectivityTools PowerShell-modul Start-ConnectivityValidation funkciójával.  További információ: [Mi a ADConnectivityTool PowerShell-modul?](how-to-connect-adconnectivitytools.md)
- AD-séma verziójának előzetes keresése a hibrid Azure Active Directory csatlakoztatásához és az eszköz visszaírásához 
- A címtár-kiterjesztés oldal attribútumának keresési értéke nem lehet kis-és nagybetűk megkülönböztetése.
-   Teljes támogatás hozzáadva a TLS 1,2-hez. Ez a kiadás támogatja az összes többi olyan protokollt, amely le van tiltva, és csak a TLS 1,2 engedélyezve van azon a gépen, amelyen a Azure AD Connect telepítve van.  További információ: [TLS 1,2 kényszerítés Azure ad Connect](reference-connect-tls-enforcement.md)



### <a name="fixed-issues"></a>Megoldott problémák   

- Kijavítva a hiba, ahol a Azure AD Connect frissítés meghiúsul, ha az SQL always on volt használatban. 
- Kijavított egy hibát, hogy helyesen elemezze a továbbítási perjelet tartalmazó szervezeti egységek nevét. 
- Kijavított egy problémát, amelyben a Pass-Through hitelesítése le lesz tiltva a tiszta telepítéshez átmeneti módban. 
- Kijavítva egy hiba, amely megakadályozta a PowerShell-modul betöltését a hibaelhárítási eszközök futtatásakor 
- Kijavítva egy olyan hibát, amely megakadályozza, hogy az ügyfelek numerikus értékeket használjanak az állomásnév első karakterében. 
- Kijavítva a hiba, ahol a Azure AD Connect érvénytelen partíciók és tárolók kijelölését tenné lehetővé 
- A "érvénytelen jelszó" hibaüzenet megoldódott, ha engedélyezve van az asztali egyszeri bejelentkezés. 
- A AD FS megbízhatóságának kezeléséhez szükséges különböző hibajavítások  
- Az eszköz visszaírási konfigurálásakor – rögzítette a séma-ellenőrzési eszközt, hogy megkeresse az msDs-DeviceContainer Object osztályt (a WS2012 R2-ben bevezetve)


## <a name="118820"></a>1.1.882.0  

9/7/2018: megjelent a letöltéshez, nem lesz kiadás az automatikus frissítéshez 

### <a name="fixed-issues"></a>Megoldott problémák  

Azure AD Connect frissítés meghiúsul, ha az SQL always on rendelkezésre állás konfigurálva van a ADSync-ADATBÁZIShoz. Ez a gyorsjavítás megoldja ezt a problémát, és lehetővé teszi a frissítés sikerességét. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Kiadás állapota

8/21/2018: megjelent a letöltéshez és az automatikus frissítéshez. 

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

- A Azure AD Connect ping összevonása-integrációja mostantól elérhető az általános rendelkezésre állás érdekében. [További információ az összevont Azure AD és a ping összevonása](./plan-connect-user-signin.md#federation-with-pingfederate)
- Azure AD Connect most létrehozza az Azure AD-megbízhatóság biztonsági mentését AD FS minden alkalommal, amikor egy frissítést végeznek, és egy külön fájlban tárolja az egyszerű visszaállításhoz, ha szükséges. [További információ az új funkciókról és az Azure ad megbízhatósági kezeléséről Azure ad Connectban](./how-to-connect-azure-ad-trust.md).
- Az új hibaelhárítási eszközök segítségével elháríthatja az elsődleges e-mail-cím módosítását és a fiók elrejtését a globális címlistában
- Azure AD Connect frissült, hogy tartalmazza a legújabb SQL Server 2012 natív ügyfelet
- Ha a felhasználói bejelentkezést a jelszó-kivonat szinkronizálására vagy átmenő hitelesítésre vált a "felhasználói bejelentkezés módosítása" feladatban, a zökkenőmentes egyszeri Sign-On jelölőnégyzet alapértelmezés szerint engedélyezve van.
- A Windows Server Essentials 2019 támogatása
- A Azure AD Connect Health ügynök frissítve lett a legújabb verzió 3.1.7.0
- Ha a frissítés során a telepítő észleli az alapértelmezett szinkronizálási szabályokat, a rendszer figyelmeztetést küld a rendszergazdának a módosított szabályok felülírása előtt. Ez lehetővé teszi, hogy a felhasználó javítsa a lépéseket, és később folytassa a műveletet. Régi viselkedés: Ha módosította a beépített szabályt, akkor a manuális frissítés felülírta ezeket a szabályokat anélkül, hogy figyelmeztetést adna a felhasználónak, és az ütemező le lett tiltva a felhasználó értesítése nélkül. Új viselkedés: a rendszer figyelmeztetést kér a felhasználótól a módosított beépített szinkronizálási szabályok felülírása előtt. A felhasználó dönthet úgy, hogy leállítja a frissítési folyamatot, és később folytatja a javítási művelet elvégzése után.
- Hatékonyabban kezelheti a FIPS-megfelelőségi problémát, és hibaüzenetet ad az MD5-kivonatok létrehozásához egy FIPS-kompatibilis környezetben, valamint egy, a probléma megoldására szolgáló dokumentációra mutató hivatkozást.
- A felhasználói felület frissítése a varázsló összevonási feladatainak fejlesztéséhez, amelyek mostantól egy különálló alcsoportba tartoznak az összevonáshoz. 
- Az összevonás további feladatai mostantól egyetlen almenübe vannak csoportosítva a könnyű használat érdekében.
- Új, a régi ADSyncPrep. psm1 (amely rövidesen elavult) új AD-ADSyncConfig (AdSyncConfig. psm1).

### <a name="fixed-issues"></a>Megoldott problémák 

- Kijavítva a hiba, ha a Azure AD Connect-kiszolgáló magas CPU-használatot mutat a .NET-4.7.2 való frissítés után.
- Kijavítva egy hiba, amely időszakosan előállít egy hibaüzenetet egy automatikusan megoldott SQL holtpont-probléma esetén
- A szinkronizálási szabályok szerkesztője és a szinkronizálási Service Manager számos kisegítő lehetőség javítva  
- Kijavítva a hiba, ahol a Azure AD Connect nem tudja beolvasni a beállításjegyzék beállításával kapcsolatos információkat
- Kijavítva egy olyan hibát, amely a felhasználó által a varázslóban továbbított vagy visszalépéskor felmerülő problémákat hozta létre
- Kijavított egy hibát, amely megakadályozza, hogy hiba történt a varázsló helytelen többszálas átadása miatt.
- Ha a csoportos szinkronizálás szűrése lap LDAP-hibát észlel a biztonsági csoportok feloldásakor, Azure AD Connect most a teljes hűséggel visszaadja a kivételt.  Az átirányítási kivétel kiváltó oka továbbra is ismeretlen, és egy másik hiba fogja megoldani.
-  Kijavítva egy hiba, ahol a STK és az NGC-kulcsok (ms-DS-KeyCredentialLink attribútum a WHfB-ben) helytelenül lettek beállítva.     
- Kijavítva egy olyan hibát, amelyben a "set-ADSyncRestrictedPermissions" metódus helytelenül lett meghívva
-  Támogatás engedélyezése az Azure ADConnect telepítővarázslójának visszaírási csoportjának engedélyezéséhez
- A bejelentkezési módszer a jelszó-kivonatos szinkronizálásról AD FSra való módosításakor a jelszó-kivonat szinkronizálása nem volt letiltva.
- IPv6-címek ellenőrzése AD FS konfigurációban
- Frissült az értesítési üzenet, amely tájékoztatja arról, hogy létezik egy meglévő konfiguráció.
- Az eszköz visszaírási nem ismeri fel a nem megbízható erdőben lévő tárolót. Ez a frissítés lehetővé teszi a jobb hibaüzenetet és a megfelelő dokumentációra mutató hivatkozást.
- Egy szervezeti egység kijelölésének, majd a szervezeti egységnek megfelelő szinkronizálás/visszaírási általános szinkronizálási hibát ad. Ez úgy módosult, hogy érthetőbb hibaüzenetet hozzon létre.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Kiadás állapota

5/14/2018: megjelent az automatikus frissítéshez és a letöltéshez.

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

Új funkciók és Újdonságok

- Ez a kiadás tartalmazza a PingFederate integrációjának nyilvános előzetes verzióját Azure AD Connect-ben. Ebben a kiadásban az ügyfelek könnyedén és megbízhatóan konfigurálhatják Azure Active Directory környezetét, hogy PingFederate használják az összevonási szolgáltatóként. Ha többet szeretne megtudni az új funkció használatáról, tekintse meg az [online dokumentációt](plan-connect-user-signin.md#federation-with-pingfederate). 
- Frissítettük a Azure AD Connect varázsló hibaelhárítási segédprogramját, ahol most már több hiba történik, például a csatolt postaládák és az AD dinamikus csoportok elemzése. A hibaelhárítási segédprogramról [itt](tshoot-connect-objectsync.md)olvashat bővebben.
- Az eszköz visszaírási konfigurációja mostantól kizárólag a Azure AD Connect varázslóban felügyelhető.
- Egy új, ADSyncTools. psm1 nevű PowerShell-modult ad hozzá, amely az SQL-kapcsolati problémák és a különböző hibaelhárítási segédeszközök hibaelhárítására használható. További információ a [ADSyncTools modulról](tshoot-connect-tshoot-sql-connectivity.md). 
- Új "eszközbeállítások konfigurálása" nevű további feladat lett hozzáadva. A feladatot a következő két művelet konfigurálására használhatja: 
  - **Hibrid Azure ad-csatlakozás**: Ha a környezet helyszíni ad-lábnyomot használ, és a Azure Active Directory által biztosított képességek előnyeit is szeretné kihasználni, a hibrid Azure ad-hez csatlakoztatott eszközöket is megvalósíthatja. Ezek olyan eszközök, amelyek a helyszíni Active Directoryhoz és az Azure Active Directoryhoz is csatlakoznak.
  - **Eszköz visszaírási**: az eszköz visszaírási lehetővé teszi a feltételes hozzáférés engedélyezését az eszközökön alapuló AD FS (2012 R2 vagy újabb) védett eszközökre

    >[!NOTE] 
    > - Az eszköz visszaírási a szinkronizálási beállítások testreszabásával történő engedélyezésének lehetősége szürkén jelenik meg. 
    > -  Az ADPrep PowerShell-modulja elavult ezzel a kiadással.


### <a name="fixed-issues"></a>Megoldott problémák 

- Ez a kiadás frissíti a SQL Server Express telepítést SQL Server 2012 SP4-re, amely többek között a biztonsági rések javítását is lehetővé teszi.  A SQL Server 2012 SP4-vel kapcsolatos további információkért tekintse meg [itt](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) .
- Szinkronizálási szabály feldolgozása: az illesztési feltételt nem tartalmazó kimenő illesztési szinkronizálási szabályok nem érvényesek, ha a szülő-szinkronizálási szabály már nem alkalmazható.
- Több kisegítő lehetőség is lett alkalmazva a Synchronization Service Manager felhasználói felületre és a szinkronizálási szabályok szerkesztőjére
- Azure AD Connect varázsló: hiba az AD Connector-fiók létrehozásakor, ha Azure AD Connect munkacsoportban van
- Azure AD Connect varázsló: az Azure AD bejelentkezési oldalán az ellenőrzés jelölőnégyzet jelenik meg, ha az AD-tartományok és az Azure AD által ellenőrzött tartományok nem egyeznek
- Automatikusan frissítheti a PowerShell-javítást úgy, hogy bizonyos esetekben megfelelően állítsa be az automatikus frissítési állapotot az automatikus frissítés megkísérlése után.
- Azure AD Connect varázsló: frissített telemetria a korábban hiányzó adatok rögzítéséhez
- Azure AD Connect varázsló: az alábbi módosítások történtek, amikor a **felhasználói bejelentkezési feladat módosítása** lehetőséget használja a ad FSról átmenő hitelesítésre való váltáshoz:
    - Az áteresztő hitelesítési ügynök telepítve van a Azure AD Connect-kiszolgálón, és az átmenő hitelesítés funkció engedélyezve van, mielőtt a tartomány (ok) t átalakítja az összevontról a felügyelt értékre.
    - A felhasználókat a rendszer már nem konvertálja összevontról felügyelt értékre. Csak a tartomány (ok) lett konvertálva.
- Azure AD Connect varázsló: AD FS többtartományos regex nem megfelelő, ha a felhasználó UPN-je speciális karakterek támogatásához a speciális karakterekkel rendelkező regex frissítése
- Azure AD Connect varázsló: "a forrás horgony attribútumának konfigurálása" üzenet eltávolítása, ha nincs változás 
- Azure AD Connect varázsló: AD FS támogatás a kettős összevonási forgatókönyvhöz
- Azure AD Connect varázsló: a felügyelt tartomány összevontra konvertálásakor a AD FS jogcímek nem frissülnek a hozzáadott tartományhoz.
- Azure AD Connect varázsló: a telepített csomagok észlelése során elavult az rsync/Azure AD-szinkronizáló/Azure AD Connect kapcsolódó termékek. Most megpróbáljuk eltávolítani az elavult termékeket.
- Azure AD Connect varázsló: az áteresztő hitelesítési ügynök telepítésekor nem sikerül kijavítani a hibaüzenetek hozzárendelését
- Azure AD Connect varázsló: a "Configuration" tároló el lett távolítva a tartomány szervezeti egységének szűrése lapról
- A szinkronizálási motor telepítése: távolítsa el a szükségtelen örökölt logikát, amely esetenként sikertelen volt a Sync Engine MSI telepítése során
- Azure AD Connect varázsló: az előugró szöveg javítása a választható funkciók lapon a jelszó-kivonatolási szinkronizáláshoz
- Szinkronizáló motor futtatókörnyezete: javítsa ki azt a forgatókönyvet, amelyben a CS objektum importált törlési és szinkronizálási szabályokkal próbálja meg újból kiépíteni az objektumot.
- Szinkronizáló motor futtatókörnyezete: az importálási hiba esetén az eseménynaplóhoz kapcsolódó online kapcsolat hibaelhárítási útmutatója
- Szinkronizáló motor futtatókörnyezete: a szinkronizálási ütemező használatának csökkentése az összekötők számbavétele során
- Azure AD Connect varázsló: javítsa ki az egyéni szinkronizálási szolgáltatásfiók feloldásával kapcsolatos problémát, amely nem rendelkezik AD olvasási jogosultságokkal
- Azure AD Connect varázsló: a tartomány-és OU-szűrési beállítások naplózásának javítása
- Azure AD Connect varázsló: AD FS az MFA-forgatókönyvhöz létrehozott összevonási megbízhatósági kapcsolatok alapértelmezett jogcímeinek hozzáadása
- Azure AD Connect varázsló: AD FS a WAP üzembe helyezése: a kiszolgáló hozzáadása nem sikerül új tanúsítványt használni
- Azure AD Connect varázsló: DSSO kivétel, ha a onPremCredentials nincs inicializálva egy tartományhoz 
- Az Active Directory distinguishedName attribútumot az aktív felhasználói objektumból előnyben kell átvezetni.
- Rögzített egy kozmetikai hiba volt az első OOB szinkronizálási szabály elsőbbsége 99 helyett 100



## <a name="117510"></a>1.1.751.0
4/12/2018-es állapot: kiadás csak letöltésre

>[!NOTE]
>Ez a kiadás a Azure AD Connect gyorsjavítása
### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Megoldott problémák
Kijavítva a probléma a kínai bérlők számára az Azure-példányok automatikus észlelése miatt sikertelen volt.  

### <a name="ad-fs-management"></a>AD FS-kezelés
#### <a name="fixed-issues"></a>Megoldott problémák

Probléma merült fel a konfiguráció újrapróbálkozási logikájában, ami egy olyan ArgumentException eredményez, amely egy azonos kulccsal rendelkező elem már hozzá lett adva.  Ennek hatására az összes újrapróbálkozási művelet sikertelen lesz.

## <a name="117500"></a>1.1.750.0
3/22/2018 állapot: az automatikus frissítéshez és a letöltéshez jelent meg.
>[!NOTE]
>Ha az új verzióra való frissítés befejeződik, a automatikusan teljes szinkronizálást és teljes importálást indít el az Azure AD-összekötőhöz, és teljes szinkronizálást végez az AD-összekötőhöz. Mivel ez eltarthat egy ideig a Azure AD Connect-környezet méretétől függően, győződjön meg arról, hogy elvégezte a szükséges lépéseket, hogy támogassa ezt, vagy a frissítés után is elvégezze a frissítést, amíg meg nem találja a megfelelő pillanatot.
>[!NOTE]
>"Az autoupgrade funkció nem volt megfelelően letiltva néhány olyan bérlőnél, akik üzembe helyezettek a 1.1.524.0-nél később. A következő PowerShell-parancsmag futtatásával biztosíthatja, hogy a Azure AD Connect-példány továbbra is jogosult legyen az autoupgrade-re: "set-ADSyncAutoUpgrade-AutoupGradeState enabled"

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Megoldott problémák

* A Set-ADSyncAutoUpgrade parancsmag korábban az automatikus frissítést blokkolta, ha a frissítés állapota felfüggesztve értékre van állítva. Ez a funkció már megváltozott, így nem blokkolja a jövőbeli buildek újrafrissítését.
* Módosította a **felhasználói bejelentkezési** oldal "jelszó-szinkronizálás" beállítását "jelszó-kivonatolási szinkronizálás" értékre.  Azure AD Connect szinkronizálja a jelszavakat, nem pedig a jelszavakat, így az a ténylegesen előforduló dolgokhoz igazodik.  További információ: [jelszó-kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Állapot: kiadva az ügyfelek kiválasztásához

>[!NOTE]
>Ha az új verzióra való frissítés befejeződik, a automatikusan teljes szinkronizálást és teljes importálást indít el az Azure AD-összekötőhöz, és teljes szinkronizálást végez az AD-összekötőhöz. Mivel ez eltarthat egy ideig a Azure AD Connect-környezet méretétől függően, győződjön meg arról, hogy elvégezte a szükséges lépéseket, hogy támogassa ezt, vagy a frissítés után is elvégezze a frissítést, amíg meg nem találja a megfelelő pillanatot.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Megoldott problémák
* A következő lapra váltáskor javítsa ki az időzítési időszakot a particionálási feladatok oldalának háttérben végzett feladatainál.

* Kijavítva egy olyan hibát, amely a configdb elemre egyéni művelet során hozzáférési szabálysértést okozott.

* Kijavított egy hibát az SQL-kapcsolat időtúllépése miatti helyreállításhoz.

* Kijavítva egy olyan hibát, amelyben a SAN helyettesítő karakterekkel rendelkező tanúsítványok előfeltétel-ellenőrzés miatt sikertelenek voltak.

* Kijavítva egy olyan hibát, amely miiserver.exe összeomlását okozza az Azure AD-összekötő exportálásakor.

* Kijavítva egy olyan hibát, amely helytelen jelszóval jelentkezett be a TARTOMÁNYVEZÉRLŐn a konfiguráció módosításához a Azure AD Connect varázsló futtatásakor.


#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Adatvédelmi beállítások hozzáadása a Általános adatvédelmi rendelethoz (GDPR).  További információért lásd [a cikket.](reference-connect-user-privacy.md)

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* alkalmazás-telemetria – a rendszergazda a következő időpontban állíthatja be az adatosztályt:

* Azure AD Health-adatkezelés – a rendszergazdának meg kell keresnie az állapotfigyelő portált az állapotadatok szabályozása érdekében.
   A szolgáltatási házirend megváltozása után az ügynökök beolvassák és érvényesítik azt.

* Hozzáadott eszköz írási-visszaállítási konfigurációs műveletei és folyamatjelző sáv az oldal inicializálásához

* Továbbfejlesztett általános diagnosztika a HTML-jelentéssel és a teljes adatgyűjtéssel egy ZIP-Text/HTML-jelentésben

* Továbbfejlesztettük az automatikus frissítés megbízhatóságát, és további telemetria bővült a kiszolgáló állapotának megállapítása érdekében

* Jogosultsági szintű fiókok számára elérhető engedélyek korlátozása az AD Connector-fiókban

  * Új telepítések esetén a varázsló a MSOL-fiók létrehozása után korlátozza a Kiemelt fiókok által a MSOL fiókra vonatkozó engedélyeket.

A módosítások a következő lépéseket teszik:
1. Expressz telepítések
2. Egyéni telepítések automatikus létrehozási fiókkal
3. Módosította a telepítőt, hogy a Azure AD Connect tiszta telepítésekor ne legyen szükség rendszergazdai jogosultságokra.

* Új segédprogram hozzáadva egy adott objektum szinkronizálási problémáinak elhárításához. A Azure AD Connect varázsló "objektum-szinkronizálásának hibakeresése" lehetőségével további feladatokat is elháríthat. A segédprogram jelenleg a következőket ellenőrzi:

  * UserPrincipalName eltérés a szinkronizált felhasználói objektum és az Azure AD-bérlő felhasználói fiókja között.
  * Ha a rendszer tartományi szűrés miatt szűri az objektumot a szinkronizálásból
  * Ha a rendszer a szervezeti egység (OU) szűrése miatt szűri az objektumot a szinkronizálásból

* Egy új segédprogrammal szinkronizálhatja a helyszíni Active Directoryban tárolt aktuális jelszó-kivonatot egy adott felhasználói fiókhoz.

A segédprogram nem igényli a jelszó módosítását. A (z) Azure AD Connect varázsló jelszó-kivonatolási szinkronizálásának hibakeresése lehetőség alatt a további feladattal kapcsolatos további feladatok című részében található.






## <a name="116540"></a>1.1.654.0
Állapot: december 12., 2017

>[!NOTE]
>Ez a kiadás a Azure AD Connect biztonsággal kapcsolatos gyorsjavítása
### <a name="azure-ad-connect"></a>Azure AD Connect
A Azure AD Connect verzió 1.1.654.0 (és azután) bővült, hogy a rendszer automatikusan alkalmazza a [AD DS-fiókhoz való hozzáférés zárolása](#lock) című szakaszban ismertetett javasolt engedélyek változásait, amikor a Azure ad Connect létrehozza a AD DS fiókot. 

- A Azure AD Connect beállításakor a rendszergazda megadhat egy meglévő AD DS fiókot, vagy engedélyezheti Azure AD Connect automatikusan létrehozni a fiókot. Az engedélyek módosításait a rendszer automatikusan alkalmazza a Azure AD Connect által a telepítés során létrehozott AD DS fiókra. Ezeket nem alkalmazza a telepítő rendszergazdája által biztosított meglévő AD DS fiókra.
- Azon ügyfelek esetében, akik a Azure AD Connect régebbi verziójáról 1.1.654.0 (vagy azután) lettek frissítve, az engedélyek módosításait a rendszer nem alkalmazza visszamenőlegesen a frissítés előtt létrehozott meglévő AD DS fiókokra. Csak a frissítés után létrehozott új AD DS fiókokra lesznek alkalmazva. Ez akkor fordul elő, ha új AD-erdőket ad hozzá az Azure AD-vel való szinkronizáláshoz.

>[!NOTE]
>Ez a kiadás csak olyan új telepítések esetén távolítja el a biztonsági réseket, amelyeken a telepítési folyamat hozza létre a szolgáltatásfiókot a Azure AD Connect. Meglévő telepítések esetén, vagy ha saját maga adja meg a fiókot, győződjön meg arról, hogy a biztonsági rés nem létezik.
#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a> A AD DS fiók hozzáférésének zárolása
Zárja le a AD DS fiók hozzáférését a helyszíni AD-ben a következő engedélyek módosításának végrehajtásával:  

*   Öröklés letiltása a megadott objektumon
*   Távolítsa el az összes Ace-t az adott objektumon, kivéve az önmagukhoz tartozó Ace-ket. Azt szeretnénk, hogy az alapértelmezett engedélyek érintetlenek maradjanak, ha önmagukban is megmaradnak.
*   Rendelje hozzá ezeket a konkrét engedélyeket:

Típus     | Name                          | Access               | Érvényesség
---------|-------------------------------|----------------------|--------------|
Engedélyezés    | RENDSZER                        | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati rendszergazdák             | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Tartományi rendszergazdák                 | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Rendszergazdák                | Teljes hozzáférés         | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Tartalom listázása        | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Vállalati tartományvezérlők | Olvasási engedélyek     | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Tartalom listázása        | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Az összes tulajdonság olvasása  | Ez az objektum  |
Engedélyezés    | Hitelesített felhasználók           | Olvasási engedélyek     | Ez az objektum  |

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-parancsfájl egy már meglévő szolgáltatásfiók szigorításához

Ha a PowerShell-szkriptet szeretné használni a beállítások alkalmazásához egy meglévő AD DS-fiókra, (a szervezet által biztosított vagy a Azure AD Connect korábbi telepítésével létrehozott), töltse le a parancsfájlt a fent megadott hivatkozásból.

##### <a name="usage"></a>Használat

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Ahol 

**$ObjectDN** = a Active Directory fiók, amelynek az engedélyeit meg kell szigorítani.

**$Credential** = rendszergazdai hitelesítő adat, amely rendelkezik a $ObjectDN fiók engedélyeinek korlátozásához szükséges jogosultságokkal. Ezeket a jogosultságokat általában a vállalati vagy a tartományi rendszergazda tartja fenn. A fiókok keresési hibáinak elkerülése érdekében használja a rendszergazdai fiók teljes tartománynevét. Példa: contoso. com\admin.

>[!NOTE] 
>$credential. A felhasználónévnek FQDN\username formátumúnak kell lennie. Példa: contoso. com\admin 
##### <a name="example"></a>Példa:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>A biztonsági rést a jogosulatlan hozzáférés megszerzéséhez használták?

Ha szeretné megtudni, hogy a biztonsági rést a Azure AD Connect konfigurációjának sérülése okozta, ellenőrizze a szolgáltatásfiók utolsó jelszó-visszaállítási dátumát.  Ha a rendszer váratlan időbélyeget, további vizsgálatot végez az Eseménynaplón keresztül, akkor a jelszó-visszaállítási esemény esetében el kell végezni.

További információ: [Microsoft biztonsági tanácsadó 4056318](/security-updates/securityadvisories/2017/4056318)

## <a name="116490"></a>1.1.649.0
Állapot: október 27 2017

>[!NOTE]
>Ez a Build nem érhető el az ügyfelek számára a Azure AD Connect automatikus frissítési funkcióján keresztül.
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Javítottuk a kompatibilitási problémát Azure AD Connect és Azure AD Connect Health ügynök között (szinkronizáláshoz). Ez a probléma azokat az ügyfeleket érinti, akik Azure AD Connect helyszíni frissítést végeznek a verzió 1.1.647.0, de jelenleg az állapotfigyelő 3.0.127.0 verziója van telepítve. A frissítés után az állapotfigyelő ügynök többé nem küldhet egészségügyi információkat Azure AD Connect szinkronizálási szolgáltatásról az Azure AD-Állapotfigyelő szolgáltatásba. Ezzel a javítással az állapotfigyelő ügynök verziója 3.0.129.0 van telepítve Azure AD Connect helyben történő frissítés során. Az állapotfigyelő ügynök 3.0.129.0 verziója nem rendelkezik kompatibilitási problémával a Azure AD Connect verzió 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Állapot: október 19 2017

> [!IMPORTANT]
> Ismert kompatibilitási probléma van a Azure AD Connect verzió 1.1.647.0 és a Azure AD Connect Health Agent (szinkronizálási) verzió 3.0.127.0. Ez a probléma megakadályozza, hogy az állapotfigyelő ügynök adatokat küldjön a Azure AD Connect szinkronizációs szolgáltatásról (beleértve az objektum-szinkronizálási hibákat és a futtatási előzmények adatait) az Azure AD-Állapotfigyelő szolgáltatás. Mielőtt manuálisan frissítené Azure AD Connect központi telepítést a 1.1.647.0 verzióra, ellenőrizze a Azure AD Connect-kiszolgálón telepített Azure AD Connect Health-ügynök aktuális verzióját. Ehhez nyissa meg a *Vezérlőpultot* , és keresse meg a programok eltávolítása lehetőséget, és keressen rá az alkalmazás *Microsoft Azure ad kapcsolódási ügynök szinkronizáláshoz* lehetőségre. Ha a verziója 3.0.127.0, javasoljuk, hogy a frissítés előtt várja meg a következő Azure AD Connect verzió elérhetővé tételét. Ha az állapotfigyelő ügynök verziója nem 3.0.127.0, a manuális, helyben történő frissítés folytatható. Vegye figyelembe, hogy ez a probléma nem érinti a swing frissítést vagy a Azure AD Connect új telepítését végző ügyfeleket.
>
>
### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Megoldott problémák
* A Azure AD Connect varázsló *felhasználói bejelentkezés módosítása* feladatának hibája javítva:

  * A probléma akkor fordul elő, ha a jelszó-szinkronizálással rendelkező meglévő Azure AD Connect-telepítést **engedélyezte**, és a felhasználói bejelentkezési módszert az *átmenő hitelesítésre* próbálja beállítani. A módosítás alkalmazása előtt a varázsló helytelenül jeleníti meg a "*Jelszó-szinkronizálás letiltása*" üzenetet. A jelszó-szinkronizálás azonban továbbra is engedélyezve marad a módosítás alkalmazása után. Ezzel a javítással a varázsló már nem jeleníti meg a kérdést.

  * A varázsló nem tiltja le a jelszó-szinkronizálást, ha a felhasználó bejelentkezési módszerét a *felhasználói bejelentkezés módosítása* feladat használatával frissíti. Ezzel elkerülhető, hogy a jelszó-szinkronizálást megtartó ügyfelek ne legyenek megszakítva annak ellenére, hogy engedélyezve vannak az átmenő hitelesítés vagy az összevonás elsődleges felhasználói bejelentkezési módszerként.

  * Ha a felhasználói bejelentkezési módszer frissítése után le szeretné tiltani a jelszó-szinkronizálást, akkor a varázslóban végre kell hajtania a *szinkronizálási konfiguráció testreszabása* feladatot. Ha megnyitja a *választható funkciók* lapot, törölje a jelszó- *szinkronizálás* lehetőséget.

  * Vegye figyelembe, hogy ugyanez a probléma akkor is előfordulhat, ha a zökkenőmentes egyszeri bejelentkezés engedélyezését vagy letiltását kísérli meg. A jelszó-szinkronizálás engedélyezve van egy meglévő Azure AD Connect-telepítéssel, és a felhasználói bejelentkezési módszer már be van állítva *átmenő hitelesítésként*. A *felhasználói bejelentkezés módosítása* feladat használatával megpróbálhatja bejelölni/kitörölni a *zökkenőmentes egyszeri bejelentkezés engedélyezése* lehetőséget, miközben a felhasználói bejelentkezési módszer "átmenő hitelesítésként" lesz konfigurálva. A módosítás alkalmazása előtt a varázsló helytelenül jeleníti meg a "*Jelszó-szinkronizálás letiltása*" üzenetet. A jelszó-szinkronizálás azonban továbbra is engedélyezve marad a módosítás alkalmazása után. Ezzel a javítással a varázsló már nem jeleníti meg a kérdést.

* A Azure AD Connect varázsló *felhasználói bejelentkezés módosítása* feladatának hibája javítva:

  * A probléma akkor fordul elő, ha a jelszó-szinkronizálással meglévő Azure AD Connect üzemelő példánya **le van tiltva**, és a felhasználó bejelentkezési módszerét át szeretné állítani *átmenő hitelesítésként*. A módosítás alkalmazása esetén a varázsló az átmenő hitelesítést és a jelszó-szinkronizálást is lehetővé teszi. Ezzel a javítással a varázsló már nem engedélyezi a jelszó-szinkronizálást.

  * Korábban a jelszó-szinkronizálás előfeltétel volt az átmenő hitelesítés engedélyezéséhez. Ha a felhasználói bejelentkezési módszert *átmenő hitelesítésként* állítja be, a varázsló az áteresztő hitelesítést és a jelszó-szinkronizálást is lehetővé teszi. A közelmúltban a jelszó-szinkronizálás el lett távolítva az előfeltételként. Azure AD Connect 1.1.557.0 verziójának részeként módosult a Azure AD Connect, hogy ne engedélyezze a jelszó-szinkronizálást, amikor a felhasználó bejelentkezési módszerét *átmenő hitelesítésként* állítja be. A módosítás azonban csak Azure AD Connect telepítésre lett alkalmazva. Ezzel a javítással ugyanezt a módosítást is alkalmazza a rendszer a felhasználó bejelentkezési feladatának *módosítására* .

  * Vegye figyelembe, hogy ugyanez a probléma akkor is előfordulhat, ha a zökkenőmentes egyszeri bejelentkezés engedélyezését vagy letiltását kísérli meg. A jelszó-szinkronizálással meglévő Azure AD Connect üzemelő példánya le van tiltva, és a felhasználói bejelentkezési módszer már be van állítva *átmenő hitelesítésként*. A *felhasználói bejelentkezés módosítása* feladat használatával megpróbálhatja bejelölni/kitörölni a *zökkenőmentes egyszeri bejelentkezés engedélyezése* lehetőséget, miközben a felhasználói bejelentkezési módszer "átmenő hitelesítésként" lesz konfigurálva. A módosítás alkalmazása után a varázsló engedélyezi a jelszó-szinkronizálást. Ezzel a javítással a varázsló már nem engedélyezi a jelszó-szinkronizálást. 

* Kijavított egy problémát, amely miatt Azure AD Connect frissítés sikertelen volt, hiba: "*nem sikerült frissíteni a szinkronizációs szolgáltatást*". A szinkronizálási szolgáltatás többé nem kezdődhet "a szolgáltatás nem indítható el" hibaüzenettel,*mert az adatbázis verziója újabb, mint a telepített bináris fájlok verziója*. A probléma akkor fordul elő, ha a frissítést végrehajtó rendszergazda nem rendelkezik rendszergazdai jogosultsággal az Azure AD Connect által használt SQL-kiszolgálóhoz. Ezzel a javítással a Azure AD Connect csak a rendszergazdának kell db_owner jogosultsággal rendelkeznie a ADSync-adatbázishoz a frissítés során.

* Kijavított egy Azure AD Connect frissítési problémát, amelyet a [zökkenőmentes egyszeri bejelentkezést](./how-to-connect-sso.md)engedélyező ügyfelek érintettek. Azure AD Connect frissítése után a zökkenőmentes önálló Sign-On helytelenül jelenik meg a Azure AD Connect varázslóban, még akkor is, ha a funkció engedélyezve van és teljes mértékben működőképes marad. Ezzel a javítással a funkció mostantól helyesen jelenik meg a varázslóban.

* Kijavított egy problémát, amely azt eredményezte, hogy Azure AD Connect varázsló mindig megjeleníti a "*forrás-horgony konfigurálása*" kérést a *konfigurálásra kész* lapon, még akkor is, ha nem történt változás a forrás-rögzítéssel kapcsolatban.

* Azure AD Connect manuális helyi frissítésének végrehajtásakor az ügyfélnek meg kell adnia a megfelelő Azure AD-bérlő globális rendszergazdai hitelesítő adatait. Korábban a frissítés akkor is folytatható, ha a globális rendszergazda hitelesítő adatai egy másik Azure AD-bérlőhöz tartoztak. Ha a frissítés sikeresen befejeződött, bizonyos konfigurációk nem maradnak meg megfelelően a frissítéssel. Ezzel a módosítással a varázsló megakadályozza a frissítés folytatását, ha a megadott hitelesítő adatok nem egyeznek az Azure AD-Bérlővel.

* Eltávolított redundáns logika, amely szükségtelenül újraindította Azure AD Connect Health szolgáltatást a manuális frissítés elején.


#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* Új logika a Microsoft Germany Cloudtel való Azure AD Connect beállításához szükséges lépések egyszerűsítéséhez. Korábban a jelen cikkben leírtak szerint frissítenie kell bizonyos beállításkulcsokat a Azure AD Connect-kiszolgálón ahhoz, hogy megfelelően működjön a Microsoft Germany Cloudtel. A Azure AD Connect mostantól képes automatikusan megállapítani, hogy a bérlő a Microsoft Germany Cloud-ban van-e a telepítés során megadott globális rendszergazdai hitelesítő adatok alapján.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
> [!NOTE]
> Megjegyzés: a szinkronizálási szolgáltatás egy WMI-felülettel rendelkezik, amely lehetővé teszi saját egyéni ütemező fejlesztését. Ez az interfész már elavult, és el lesz távolítva a Azure AD Connect későbbi verzióiból, amelyeket a 2018. június 30-ig szállítanak. Azok a felhasználók, akik szeretnék a szinkronizálási ütemezést testreszabni, a [beépített Feladatütemezőt](./how-to-connect-sync-feature-scheduler.md)kell használniuk.
#### <a name="fixed-issues"></a>Megoldott problémák
* Ha Azure AD Connect varázsló létrehozza a helyszíni Active Directory változásainak a szinkronizálásához szükséges AD Connector-fiókot, akkor nem rendeli hozzá megfelelően a fiókot a PublicFolder-objektumok olvasásához szükséges engedéllyel. Ez a probléma az expressz telepítést és az Egyéni telepítést is érinti. Ez a változás javítja a problémát.

* Kijavított egy problémát, amely miatt a Azure AD Connect varázsló hibaelhárítási lapja nem megfelelően jelenik meg a Windows Server 2016 rendszerű rendszergazdák számára.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* A jelszó-szinkronizálás hibaelhárítása a Azure AD Connect varázsló hibaelhárítási lapján a hibaelhárítási oldalon a tartományra vonatkozó állapotot adja vissza.

* Korábban, ha megpróbálta engedélyezni a jelszó-kivonatolási szinkronizálást, Azure AD Connect nem ellenőrzi, hogy az AD-összekötő fiókja rendelkezik-e a jelszó-kivonatok helyszíni AD-vel való szinkronizálásához szükséges engedélyekkel. Most Azure AD Connect varázsló ellenőrzi és figyelmezteti, ha az AD Connector-fiók nem rendelkezik megfelelő engedélyekkel.

### <a name="ad-fs-management"></a>AD FS-kezelés
#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Kijavítottuk az [MS-DS-ConsistencyGuid forrás-rögzítési](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) funkciójának használatával kapcsolatos problémát. Ez a probléma azokat az ügyfeleket érinti, akik a *ad FSval való összevonást* konfigurálták felhasználói bejelentkezési módszerként. Ha a varázslóban végrehajtja a *forrás-rögzítés konfigurálása* feladatot, Azure ad Connect a * MS-DS-ConsistencyGuid as Source attribútumot használja a immutableId. Ennek a változásnak a részeként Azure AD Connect megkísérli frissíteni a ImmutableId vonatkozó jogcímeket a AD FSban. Azonban ez a lépés nem sikerült, mert Azure AD Connect nem rendelkezett a AD FS konfigurálásához szükséges rendszergazdai hitelesítő adatokkal. Ezzel a javítással a Azure AD Connect most megkéri, hogy adja meg a AD FS rendszergazdai hitelesítő adatait, amikor végrehajtja a *configure Source Anchor (forrás konfigurálása* ) feladatot.



## <a name="116140"></a>1.1.614.0
Állapot: szeptember 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Ismert problémák
* Létezik egy ismert probléma, amely miatt Azure AD Connect frissítés sikertelen a következő hiba miatt: "*nem sikerült frissíteni a szinkronizációs szolgáltatást*". A szinkronizálási szolgáltatás többé nem kezdődhet "a szolgáltatás nem indítható el" hibaüzenettel,*mert az adatbázis verziója újabb, mint a telepített bináris fájlok verziója*. A probléma akkor fordul elő, ha a frissítést végrehajtó rendszergazda nem rendelkezik rendszergazdai jogosultsággal az Azure AD Connect által használt SQL-kiszolgálóhoz. A dbo engedélyei nem elegendőek.

* Ismert probléma van Azure AD Connect frissítéssel kapcsolatban, amely hatással van a [zökkenőmentes egyszeri bejelentkezést](how-to-connect-sso.md)engedélyező ügyfelekre. Azure AD Connect frissítése után a szolgáltatás letiltottként jelenik meg a varázslóban, még akkor is, ha a funkció engedélyezve marad. A probléma megoldása a következő kiadásban lesz elérhető. Azok az ügyfelek, akik aggódnak erről a megjelenítési hibáról, manuálisan is kijavíthatják azt a varázsló zökkenőmentes egyetlen Sign-Onének engedélyezésével.

#### <a name="fixed-issues"></a>Megoldott problémák
* Kijavítva egy olyan problémát, amely megakadályozta, hogy Azure AD Connect a helyi AD FS a jogcímek szabályainak frissítését, miközben az [MS-DS-ConsistencyGuid forrás-szerkesztőpontként](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) való engedélyezését engedélyezte. A probléma akkor fordul elő, ha egy meglévő Azure AD Connect üzemelő példányra próbálja engedélyezni a szolgáltatást, AD FS a bejelentkezési módszerként van konfigurálva. A probléma azért fordul elő, mert a varázsló nem kéri az ADFS hitelesítő adatait, mielőtt megpróbálja frissíteni a jogcímek szabályait a AD FSban.
* Kijavított egy problémát, amely a Azure AD Connect sikertelen telepítését eredményezte, ha a helyszíni AD-erdőben az NTLM le van tiltva. A probléma oka, hogy Azure AD Connect varázsló nem biztosít teljesen minősített hitelesítő adatokat a Kerberos-hitelesítéshez szükséges biztonsági környezetek létrehozásakor. Ennek hatására a Kerberos-hitelesítés sikertelen lesz, és Azure AD Connect varázsló visszaesik az NTLM használatára.

### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása
#### <a name="fixed-issues"></a>Megoldott problémák
* Kijavítva a hiba, amely során nem hozható létre új szinkronizálási szabály, ha a címke attribútum nincs feltöltve.
* Kijavítva egy olyan problémát, amely Azure AD Connectt okozott a jelszó-szinkronizáláshoz a helyszíni AD-hez az NTLM használatával, bár a Kerberos elérhető. Ez a probléma akkor fordul elő, ha a helyszíni AD-topológia egy vagy több, biztonsági másolatból visszaállított tartományvezérlővel rendelkezik.
* Kijavított egy problémát, amely miatt a teljes szinkronizálási lépések szükségtelenek a frissítés után. A frissítés után általában a teljes szinkronizálás lépéseinek futtatására van szükség, ha a rendszer a beépített szinkronizálási szabályokat módosítja. A probléma oka a változás észlelési logikájának hibája volt, amely helytelenül észlelt változást a szinkronizálási szabály kifejezésének sortörési karakterekkel való találkozásakor. A sortörési karakterek bekerülnek a szinkronizálási szabály kifejezésbe az olvashatóság javítása érdekében.
* Javítva egy probléma, amely miatt a Azure AD Connect-kiszolgáló nem működik megfelelően az automatikus frissítés után. Ez a probléma a 1.1.443.0 (vagy korábbi) verzióval rendelkező Azure AD Connect-kiszolgálókat érinti. A hibával kapcsolatos részletekért tekintse meg a következő cikket: [Azure ad Connect az automatikus frissítés után nem működik megfelelően](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Javítva egy probléma, amely miatt az automatikus frissítés 5 percenként újrapróbálkozhat a hibák előfordulásakor. A javítást követően az automatikus frissítés újrapróbálkozik az exponenciális visszalépéssel, ha hiba történt.
* Kijavítva egy probléma, amelyben a 611-es jelszó-szinkronizálási esemény helytelenül jelenik meg a Windows-alkalmazás eseménynaplójában a **hiba** helyett **tájékoztatóként** . A 611-es esemény akkor jön létre, amikor a jelszó-szinkronizálás hibát észlel. 
* Kijavított egy problémát a Azure AD Connect varázslóban, amely lehetővé teszi a csoportos visszaírási funkció engedélyezését anélkül, hogy kijelöl egy szervezeti egység szükséges a visszaírási csoport számára.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* A további feladatok alatt Azure AD Connect varázslóhoz hozzáadott egy hibakeresési feladatot. Az ügyfelek ezt a feladatot felhasználhatják a jelszó-szinkronizálással kapcsolatos problémák elhárításához és az általános diagnosztika összegyűjtéséhez. A későbbiekben a hibaelhárítási feladat ki lesz bővítve a címtár-szinkronizálással kapcsolatos egyéb problémákra is.
* A Azure AD Connect mostantól támogatja a **meglévő adatbázis használata** nevű új telepítési módot. Ez a telepítési mód lehetővé teszi, hogy az ügyfelek olyan Azure AD Connect telepítsenek, amelyek egy meglévő ADSync-adatbázist határoznak meg. A szolgáltatással kapcsolatos további információkért tekintse meg a [meglévő adatbázis használata](how-to-connect-install-existing-database.md)című cikket.
* A fokozott biztonság érdekében a Azure AD Connect mostantól alapértelmezés szerint a TLS 1.2-t használja az Azure AD-hez való kapcsolódáshoz a címtár-szinkronizáláshoz. Korábban az alapértelmezett volt a TLS 1.0.
* A jelszó-szinkronizálási ügynök Azure AD Connect indításakor megpróbál csatlakozni az Azure AD Well-known endpointhoz a jelszó-szinkronizáláshoz. A sikeres csatlakozás után a rendszer átirányítja a régióhoz tartozó végpontra. Korábban a jelszó-szinkronizálási ügynök gyorsítótárazza a régióra jellemző végpontot, amíg újra nem indítja. Az ügynök most törli a gyorsítótárat, és újrapróbálkozik a jól ismert végponttal, ha kapcsolódási probléma lép fel a régióra vonatkozó végponttal. Ez a módosítás biztosítja, hogy a jelszó-szinkronizálás feladatátvételt hajthat végre egy másik régióspecifikus végponton, ha a gyorsítótárazott régióspecifikus végpont már nem érhető el.
* Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Saját maga is létrehozhatja a AD DS fiókot, és megadhatja a hitelesítő adatait Azure AD Connect, vagy (II) adja meg a vállalati rendszergazda hitelesítő adatait, és Azure AD Connect hozza létre az AD DS fiókot. Korábban, (i) az alapértelmezett beállítás a Azure AD Connect varázslóban. Most, (II) az alapértelmezett beállítás.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* A Microsoft Azure Government Cloud és Microsoft Cloud Germany támogatása.

### <a name="ad-fs-management"></a>AD FS-kezelés
#### <a name="fixed-issues"></a>Megoldott problémák
* Az AD PREP PowerShell-modul Initialize-ADSyncNGCKeysWriteBack parancsmagja helytelenül alkalmazza az ACL-eket az eszköz regisztrációs tárolójára, ezért csak a meglévő engedélyeket örökli.  Ez frissült, így a szinkronizálási szolgáltatás fiókja rendelkezik a megfelelő engedélyekkel.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* Az Azure AD Connect ellenőrzi, hogy az ADFS bejelentkezési feladata frissítve lett-e, hogy a bejelentkezéseket a Microsoft online-on keresztül ellenőrzi, nem csak a jogkivonat-lekérést az ADFS-ből.
* Amikor új ADFS-farmot állít be a Azure AD Connect használatával, a rendszer áthelyezte az ADFS hitelesítő adatait kérő lapot, hogy a felhasználó az ADFS-és a WAP-kiszolgálók megadását megelőzően megkérdezzen.  Ez lehetővé teszi Azure AD Connect számára annak ellenőrzését, hogy a megadott fiók rendelkezik-e a megfelelő engedélyekkel.
* Azure AD Connect frissítés során a frissítés nem fog sikerülni, ha az ADFS Azure AD-beli megbízhatósága nem frissül.  Ha ez történik, a felhasználó egy megfelelő figyelmeztető üzenet jelenik meg, és a Azure AD Connect további feladattal folytathatja a megbízhatóság alaphelyzetbe állítását.

### <a name="seamless-single-sign-on"></a>Zökkenőmentes egyszeri Sign-On
#### <a name="fixed-issues"></a>Megoldott problémák
* Kijavított egy hibát, amely miatt Azure AD Connect varázsló hibát adott vissza, ha megpróbálják engedélyezni a [zökkenőmentes egyszeri bejelentkezést](how-to-connect-sso.md). A hibaüzenet a *következő: Microsoft Azure ad csatlakozási hitelesítési ügynök konfigurációja sikertelen.* Ez a probléma olyan meglévő ügyfelekre is hatással van, akik a jelen [cikkben](how-to-connect-pta-upgrade-preview-authentication-agents.md)ismertetett lépések alapján manuálisan frissítették a hitelesítési ügynökök előzetes verzióját az [áteresztő hitelesítéshez](how-to-connect-sso.md) .


## <a name="115610"></a>1.1.561.0
Állapot: július 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 

* Kijavított egy olyan problémát, amely miatt a rendszer eltávolítja a kifelé irányuló szinkronizálási szabályt az AD-User ImmutableId:

  * A probléma akkor fordul elő, ha a Azure AD Connect frissül, vagy ha a Azure AD Connect varázslóban a feladat- *szinkronizálási konfiguráció frissítése* lehetőséggel frissíti Azure ad Connect szinkronizálási konfigurációját.

  * Ez a szinkronizálási szabály azon ügyfelekre vonatkozik, akik engedélyezték az [MS-DS-ConsistencyGuid forrás-szerkesztőpontként](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)való használatát. Ez a funkció a 1.1.524.0 és későbbi verziókban lett bevezetve. A szinkronizálási szabály eltávolításakor Azure AD Connect már nem tudja feltölteni a helyszíni AD MS-DS-ConsistencyGuid attribútumot a ObjectGuid attribútum értékével. Nem akadályozza meg, hogy az új felhasználók kiépítsék az Azure AD-t.

  * A javítás biztosítja, hogy a szinkronizálási szabály a frissítés során ne legyen eltávolítva vagy a konfiguráció módosításakor, ha a szolgáltatás engedélyezve van. A probléma által érintett meglévő ügyfelek esetében a javítás azt is biztosítja, hogy a szinkronizálási szabály a Azure AD Connect ezen verziójára való frissítés után ismét felkerüljön.

* Javítva a hiba, amely miatt a beépített szinkronizálási szabályok nem rendelkeznek 100-nál kisebb prioritási értékkel:

  * Általánosságban a 0-99 elsőbbségi értékek az egyéni szinkronizálási szabályok számára vannak fenntartva. A frissítés során a rendszer frissíti a beépített szinkronizálási szabályok prioritási értékeit a szinkronizálási szabály változásainak megfelelően. Ennek a hibának köszönhetően a beépített szinkronizálási szabályok a 100-nál kisebb prioritási értékhez rendelhetők.

  * A javítás megakadályozza a probléma előfordulását a frissítés során. Azonban nem állítja vissza a probléma által érintett meglévő ügyfelek elsőbbségi értékeit. A jövőben egy külön javítást is biztosítunk, amely segít a helyreállításban.

* Kijavítva a probléma, hogy a [tartomány és a szervezeti egység szűrési képernyője](how-to-connect-install-custom.md#domain-and-ou-filtering) a Azure ad Connect varázslóban az *összes tartomány és szervezeti egység szinkronizálása* beállítást mutatja, még akkor is, ha az OU-alapú Szűrés engedélyezve van.

*   Kijavított egy problémát, amely a Synchronization Service Manager [Directory-partíciók konfigurálása képernyőt](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) eredményezett, ha a *refresh (frissítés* ) gombra kattint, hibaüzenetet ad vissza. A hibaüzenet *"hiba történt a tartományok frissítése közben:" System. Collections. ArrayList "típusú objektum nem konvertálható" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject "típusúra.* A hiba akkor fordul elő, ha új AD-tartomány lett hozzáadva egy meglévő AD-erdőhöz, és a frissítés gombra kattintva frissíti Azure AD Connect.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Az [automatikus frissítési funkció](how-to-connect-install-automatic-upgrade.md) kibővült az ügyfelek támogatására a következő konfigurációkkal:
  * Engedélyezte az eszköz visszaírási funkcióját.
  * Engedélyezte a csoport visszaírási funkcióját.
  * A telepítés nem expressz beállítások vagy az rSync frissítése.
  * A metaverse több mint 100 000 objektumot tartalmaz.
  * Több erdőhöz csatlakozik. Az expressz beállítás csak egyetlen erdőhöz csatlakozik.
  * Az AD Connector-fiók már nem az alapértelmezett MSOL_ fiók.
  * A kiszolgáló átmeneti módban van beállítva.
  * Engedélyezte a felhasználói visszaírási funkciót.

  >[!NOTE]
  >Az automatikus frissítési funkció hatókör-bővítése hatással van az ügyfelekre Azure AD Connect build 1.1.105.0 és utána. Ha nem szeretné, hogy a Azure AD Connect-kiszolgáló automatikusan legyen frissítve, a következő parancsmagot kell futtatnia a Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Az automatikus frissítés engedélyezésével és letiltásával kapcsolatos további információkért tekintse meg a [Azure ad Connect: automatikus frissítés](how-to-connect-install-automatic-upgrade.md)című cikket.
## <a name="115580"></a>1.1.558.0
Állapot: nem jelenik meg. A Build módosításait a 1.1.561.0 verzió tartalmazza.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 

* Kijavított egy olyan problémát, amely miatt a rendszer eltávolítja a kifelé irányuló szinkronizálási szabályt az AD-User ImmutableId, ha a OU-alapú szűrési konfiguráció frissül. Ez a szinkronizálási szabály szükséges az [MS-DS-ConsistencyGuid forrás-rögzítési szolgáltatásához](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Kijavítva a probléma, hogy a [tartomány és a szervezeti egység szűrési képernyője](how-to-connect-install-custom.md#domain-and-ou-filtering) a Azure ad Connect varázslóban az *összes tartomány és szervezeti egység szinkronizálása* beállítást mutatja, még akkor is, ha az OU-alapú Szűrés engedélyezve van.

*   Kijavított egy problémát, amely a Synchronization Service Manager [Directory-partíciók konfigurálása képernyőt](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) eredményezett, ha a *refresh (frissítés* ) gombra kattint, hibaüzenetet ad vissza. A hibaüzenet *"hiba történt a tartományok frissítése közben:" System. Collections. ArrayList "típusú objektum nem konvertálható" Microsoft. DirectoryServices. MetadirectoryServices. UI. PropertySheetBase. MaPropertyPages. PartitionObject "típusúra.* A hiba akkor fordul elő, ha új AD-tartomány lett hozzáadva egy meglévő AD-erdőhöz, és a frissítés gombra kattintva frissíti Azure AD Connect.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Az [automatikus frissítési funkció](how-to-connect-install-automatic-upgrade.md) kibővült az ügyfelek támogatására a következő konfigurációkkal:
  * Engedélyezte az eszköz visszaírási funkcióját.
  * Engedélyezte a csoport visszaírási funkcióját.
  * A telepítés nem expressz beállítások vagy az rSync frissítése.
  * A metaverse több mint 100 000 objektumot tartalmaz.
  * Több erdőhöz csatlakozik. Az expressz beállítás csak egyetlen erdőhöz csatlakozik.
  * Az AD Connector-fiók már nem az alapértelmezett MSOL_ fiók.
  * A kiszolgáló átmeneti módban van beállítva.
  * Engedélyezte a felhasználói visszaírási funkciót.

  >[!NOTE]
  >Az automatikus frissítési funkció hatókör-bővítése hatással van az ügyfelekre Azure AD Connect build 1.1.105.0 és utána. Ha nem szeretné, hogy a Azure AD Connect-kiszolgáló automatikusan legyen frissítve, a következő parancsmagot kell futtatnia a Azure AD Connect-kiszolgálón: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled` . Az automatikus frissítés engedélyezésével és letiltásával kapcsolatos további információkért tekintse meg a [Azure ad Connect: automatikus frissítés](how-to-connect-install-automatic-upgrade.md)című cikket.
## <a name="115570"></a>1.1.557.0
Állapot: július 2017

>[!NOTE]
>Ez a Build nem érhető el az ügyfelek számára a Azure AD Connect automatikus frissítési funkcióján keresztül.
### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Hiba kijavítva: 
* Javítva lett a Initialize-ADSyncDomainJoinedComputerSync parancsmaggal kapcsolatos probléma, amely miatt a rendszer megváltoztatta a meglévő Service kapcsolódási pont objektumon konfigurált ellenőrzött tartományt, még akkor is, ha az még érvényes tartomány. Ez a probléma akkor fordul elő, ha az Azure AD-bérlő több ellenőrzött tartománnyal rendelkezik, amelyek a szolgáltatás kapcsolódási pontjának konfigurálására használhatók.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* A visszaírási mostantól elérhető az előzetes verzióhoz Microsoft Azure Government Cloud és Microsoft Cloud Germany esetében. A különböző szolgáltatási példányok Azure AD Connect támogatásával kapcsolatos további információkért tekintse meg a cikk [Azure ad Connect: a példányokra vonatkozó különleges megfontolásokat](reference-connect-instances.md).

* A Initialize-ADSyncDomainJoinedComputerSync parancsmaghoz egy új, AzureADDomain nevű választható paraméter tartozik. Ezzel a paraméterrel megadhatja, hogy melyik ellenőrzött tartományt szeretné használni a szolgáltatás kapcsolódási pontjának konfigurálásához.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* Az áteresztő hitelesítéshez szükséges ügynök neve *Microsoft Azure ad alkalmazásproxy-összekötőről* *Microsoft Azure ad csatlakozás hitelesítési ügynökre* módosult.

* Az átmenő hitelesítés engedélyezése a továbbiakban nem engedélyezi a jelszó-kivonatok szinkronizálását alapértelmezés szerint.


## <a name="115530"></a>1.1.553.0
Állapot: június 2017

> [!IMPORTANT]
> Ebben a buildben a séma-és szinkronizálási szabályok változásai is bekerültek. Azure AD Connect szinkronizációs szolgáltatás a frissítés után teljes importálási és teljes szinkronizálási lépéseket indít el. A módosítások részletei az alábbiakban olvashatók. Ha átmenetileg el szeretné halasztani a teljes importálást és a teljes szinkronizálást a frissítés után, tekintse meg a cikk a [teljes szinkronizálás késleltetése a frissítés után](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)című cikket.
>
>
### <a name="azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálása

#### <a name="known-issue"></a>Ismert probléma
* Probléma merül fel, amely a [szervezeti egységen alapuló szűrést](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) használó ügyfeleket Azure ad Connect szinkronizálással érinti. Amikor megnyitja a Azure AD Connect varázsló [tartomány és szervezeti egység szűrés lapját](how-to-connect-install-custom.md#domain-and-ou-filtering) , a következő viselkedés várható:
  * Ha a szervezeti egységen alapuló Szűrés engedélyezve van, akkor a **kiválasztott tartományok és szervezeti egységek szinkronizálása** lehetőség van kiválasztva.
  * Ellenkező esetben a **minden tartomány és szervezeti egység szinkronizálása** beállítás van kiválasztva.

A felmerülő probléma az, hogy a varázsló futtatásakor a **minden tartomány és szervezeti egység szinkronizálása lehetőség** mindig ki van választva.  Ez akkor is előfordul, ha a OU-alapú szűrést előzőleg konfigurálták. A Azure AD Connect konfigurációjának módosítása előtt győződjön meg arról, hogy a **kiválasztott tartományok és szervezeti egységek szinkronizálása beállítás be van jelölve** , és ellenőrizze, hogy az összes szinkronizálandó szervezeti egység engedélyezve van-e. Ellenkező esetben a OU-alapú szűrés le lesz tiltva.

#### <a name="fixed-issues"></a>Megoldott problémák

* A jelszó-visszaírási kapcsolatos probléma javítva, amely lehetővé teszi, hogy az Azure AD-rendszergazda alaphelyzetbe állítsa a helyszíni AD privilegizált felhasználói fiók jelszavát. A probléma akkor fordul elő, ha Azure AD Connect megkapja a jelszó alaphelyzetbe állítása engedélyt a Kiemelt fiókon. A probléma a Azure AD Connect jelen verziójában nem teszi lehetővé az Azure AD-rendszergazdák számára, hogy egy tetszőleges helyszíni AD-jogosultságú felhasználói fiók jelszavát állítsa alaphelyzetbe, kivéve, ha a rendszergazda a fiók tulajdonosa. További információkért tekintse meg a [4033453-es biztonsági tanácsadót](/security-updates/SecurityAdvisories/2017/4033453).

* Kijavított egy olyan problémát, amely az [MS-DS-ConsistencyGuid forrás-Anchor](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) szolgáltatáshoz kapcsolódik, ahol a Azure ad Connect nem visszaírási a helyszíni ad ms-DS-ConsistencyGuid attribútumhoz. A probléma akkor fordul elő, ha több helyszíni AD-erdőt adnak hozzá a Azure AD Connecthoz, és a *felhasználói identitások több könyvtár között* vannak kiválasztva. Ha ezt a konfigurációt használja, az eredő szinkronizálási szabályok nem töltik fel a sourceAnchorBinary attribútumot a metaverse-ban. A sourceAnchorBinary attribútum az MS-DS-ConsistencyGuid attribútum forrásoldali attribútuma. Ennek eredményeképpen az MS-DSConsistencyGuid attribútum visszaírási nem történik meg. A probléma megoldásához a következő szinkronizálási szabályok frissültek, hogy a metaverse sourceAnchorBinary attribútuma mindig fel legyen töltve:
  * Az AD-InetOrgPerson AccountEnabled.xmlból
  * Az AD-InetOrgPerson Common.xmlból
  * Az AD-User AccountEnabled.xml
  * Az AD-User Common.xml
  * Az AD-User JOIN SOAInAAD.xml

* Korábban, még akkor is, ha az [MS-DS-ConsistencyGuid as forrás-szerkesztőpont](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) funkció nincs engedélyezve, a "ki az ad-User ImmutableId" szinkronizálási szabályt a rendszer továbbra is hozzáadja a Azure ad Connecthoz. A hatás jóindulatú, és nem okoz visszaírási az MS-DS-ConsistencyGuid attribútumhoz. A félreértések elkerülése érdekében a rendszer logika hozzáadásával biztosítja, hogy a szinkronizálási szabály csak akkor legyen hozzáadva, ha a szolgáltatás engedélyezve van.

* Kijavított egy problémát, amely miatt a jelszó-kivonat szinkronizálása meghiúsult a 611-es hiba esetén. Ez a probléma akkor fordul elő, ha egy vagy több tartományvezérlő el lett távolítva a helyszíni AD-ből. A jelszó-szinkronizálási ciklus végén a helyszíni AD által kiadott szinkronizációs cookie a 0 értékkel rendelkező eltávolított tartományvezérlők Meghívási azonosítóit tartalmazza (frissítési sorszám). A jelszó-szinkronizálási kezelő nem tudja megőrizni a 0 USN-értéket tartalmazó szinkronizációs cookie-t, és a 611-es hiba esetén meghiúsul. A következő szinkronizálási ciklusban a jelszó-szinkronizálási kezelő az utolsó megőrzött szinkronizálási cookie-t használja, amely nem tartalmazza a 0 USN-értéket. Ez azt eredményezi, hogy a jelszó módosításait a rendszer újraszinkronizálja. Ezzel a javítással a jelszó-szinkronizálási kezelő megfelelően megőrzi a szinkronizálási cookie-t.

* Korábban, még akkor is, ha az automatikus frissítés le van tiltva az Set-ADSyncAutoUpgrade parancsmaggal, az automatikus frissítési folyamat rendszeres időközönként ellenőrzi a frissítést, és a letöltött telepítőre támaszkodik, hogy tiszteletben tartsák a használatot. Ezzel a javítással az automatikus frissítési folyamat már nem ellenőrzi rendszeresen a frissítést. A javítást a rendszer automatikusan alkalmazza, amikor a Azure AD Connect verziójának frissítését hajtja végre.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Korábban az [MS-DS-ConsistencyGuid as Source Anchor](./plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) funkció csak az új központi telepítések számára érhető el. Most már elérhető a meglévő telepítések. Pontosabban:
  * A szolgáltatás eléréséhez indítsa el a Azure AD Connect varázslót, és válassza a *frissítési forrás horgonya* lehetőséget.
  * Ez a beállítás csak olyan meglévő központi telepítések esetében látható, amelyek a objectGuid sourceAnchor attribútumként használják.
  * A beállítás konfigurálásakor a varázsló érvényesíti az MS-DS-ConsistencyGuid attribútum állapotát a helyszíni Active Directoryban. Ha az attribútum nincs konfigurálva a címtár egyik felhasználói objektumán sem, a varázsló az MS-DS-ConsistencyGuid használja sourceAnchor attribútumként. Ha az attribútum egy vagy több felhasználói objektumon van konfigurálva a címtárban, a varázsló arra a következtetésre jut, hogy az attribútumot más alkalmazások használják, és nem alkalmas sourceAnchor attribútumként, és nem engedélyezi a forrás-rögzítési módosítást a folytatáshoz. Ha biztos abban, hogy a meglévő alkalmazások nem használják az attribútumot, forduljon a támogatási szolgálathoz, és tájékozódjon arról, hogyan tilthatja le a hibát.

* Az **userCertificate** attribútumra jellemző, Azure ad Connect most a [tartományhoz csatlakoztatott eszközök Azure ad-hez való csatlakoztatásához szükséges tanúsítvány-értékeket keres a Windows 10 rendszerhez](../devices/hybrid-azuread-join-plan.md) , és kiszűri a TÖBBit az Azure ad-be való szinkronizálás előtt. Ennek a viselkedésnek az engedélyezéséhez frissült a "kimenő HRE-eszköz csatlakoztatása SOAInAD" nevű beépített szinkronizálási szabály.

* A Azure AD Connect mostantól támogatja a visszaírási Exchange Online **cloudPublicDelegates** attribútumot a helyszíni ad **publicDelegates** attribútumhoz. Ez lehetővé teszi, hogy az Exchange Online-postaláda SendOnBehalfTo jogokat biztosítson a helyszíni Exchange-postaládával rendelkező felhasználók számára. Ennek a funkciónak a támogatásához új, "az AD – User Exchange Hybrid PublicDelegates visszaírási" nevű, beépített szinkronizálási szabályt adott hozzá. Ez a szinkronizálási szabály csak akkor kerül Azure AD Connecthoz, ha engedélyezve van az Exchange Hybrid szolgáltatás.

* Azure AD Connect mostantól támogatja a **altRecipient** attribútum szinkronizálását az Azure ad-ből. Ennek a változásnak a támogatásához a beépített szinkronizálási szabályok frissültek, hogy tartalmazzák a szükséges attribútum-folyamatot:
  * A from AD – felhasználói Exchange
  * HRE – felhasználói ExchangeOnline

* A metaverse **cloudSOAExchMailbox** attribútuma azt jelzi, hogy egy adott felhasználó rendelkezik-e Exchange Online-postaládával vagy sem. A definíciója frissítve lett, így többek között az Exchange Online RecipientDisplayTypes, mint például a berendezések és a konferenciatermek postaládái. Ennek a módosításnak az engedélyezéséhez a cloudSOAExchMailbox attribútum definíciója, amely a (z) HRE – User Exchange Hybrid nevű beépített szinkronizálási szabályban található, a következő címről frissült:

    ```
    CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
    ```

    ... a következőkhöz:

    ```
    CBool(
      IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
        IIF([cloudMSExchRecipientDisplayType]=0,True,(
          IIF([cloudMSExchRecipientDisplayType]=2,True,(
            IIF([cloudMSExchRecipientDisplayType]=7,True,(
              IIF([cloudMSExchRecipientDisplayType]=8,True,(
                IIF([cloudMSExchRecipientDisplayType]=10,True,(
                  IIF([cloudMSExchRecipientDisplayType]=16,True,(
                    IIF([cloudMSExchRecipientDisplayType]=17,True,(
                      IIF([cloudMSExchRecipientDisplayType]=18,True,(
                        IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                          IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))
    ```

* A következő X509certificate2)-kompatibilis függvények lettek hozzáadva a szinkronizációs szabály kifejezések létrehozásához a userCertificate attribútumban lévő tanúsítványok értékeinek kezeléséhez:
  * CertSubject 
  * CertIssuer
  * CertKeyAlgorithm
  * CertSubjectNameDN
  * CertIssuerOid
  * CertNameInfo
  * CertSubjectNameOid
  * CertIssuerDN
  * IsCert
  * CertFriendlyName
  * CertThumbprint
  * CertExtensionOids
  * CertFormat
  * CertNotAfter
  * CertPublicKeyOid 
  * CertSerialNumber
  * CertNotBefore
  * CertPublicKeyParametersOid
  * CertVersion
  * CertSignatureAlgorithmOid
  * Válassza ezt:
  * CertKeyAlgorithmParams
  * CertHashString
  * Ahol
  * With

* A következő séma-változások bevezetése lehetővé teszi, hogy az ügyfelek egyéni szinkronizálási szabályokat hozzanak létre a sAMAccountName, a domainNetBios és a domainFQDN, valamint a felhasználói objektumok distinguishedName:

  * A következő attribútumok lettek hozzáadva az MV-sémához:
    * Csoport: AccountName
    * Csoport: domainNetBios
    * Csoport: domainFQDN
    * Személy: distinguishedName

  * A következő attribútumok lettek hozzáadva az Azure AD Connector sémához:
    * Csoport: OnPremisesSamAccountName
    * Csoport: NetBiosName
    * Csoport: DnsDomainName
    * Felhasználó: OnPremisesDistinguishedName

* A ADSyncDomainJoinedComputerSync-parancsmag parancsfájlja mostantól egy új, AzureEnvironment nevű paramétert is tartalmaz. A paraméter segítségével meghatározható, hogy a megfelelő Azure Active Directory bérlő melyik régiót futtatja. Az érvényes értékek a következők:
  * AzureCloud (alapértelmezett)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment

* A szinkronizálási szabály szerkesztőjének frissítése a szinkronizálási szabály létrehozásakor a csatlakozás (nem a kiépítés helyett) alapértelmezett értékének megadására szolgál.

### <a name="ad-fs-management"></a>AD FS-kezelés

#### <a name="issues-fixed"></a>Kijavított problémák

* A következő URL-címek az Azure AD által bevezetett új WS-Federation végpontok, amelyek javítják a hitelesítési leállás elleni rugalmasságot, és a rendszer hozzáadja a helyszíni AD FS válaszul szolgáló fél megbízhatósági konfigurációhoz:
  * https: \/ /ests.login.microsoftonline.com/login.SRF
  * https: \/ /stamp2.login.microsoftonline.com/login.SRF
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf

* Kijavított egy problémát, amely miatt a AD FS helytelen jogcím-értéket generált a IssuerID. A probléma akkor fordul elő, ha több ellenőrzött tartomány van az Azure AD-bérlőben, és a IssuerID-jogcím létrehozásához használt userPrincipalName attribútum tartományi utótagja legalább 3 szinten mély (például: johndoe@us.contoso.com ). A probléma megoldásához frissítse a jogcím-szabályok által használt regexet.

#### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok
* Korábban a Azure AD Connect által biztosított ADFS-tanúsítványkezelő szolgáltatás csak Azure AD Connect által felügyelt ADFS-farmokkal használható. Mostantól használhatja a szolgáltatást olyan ADFS-farmokkal, amelyeket nem a Azure AD Connect használatával felügyel.

## <a name="115240"></a>1.1.524.0
Kiadás dátuma: 2017. május

> [!IMPORTANT]
> Ebben a buildben a séma-és szinkronizálási szabályok változásai is bekerültek. Azure AD Connect szinkronizációs szolgáltatás a frissítés után teljes importálási és teljes szinkronizálási lépéseket indít el. A módosítások részletei az alábbiakban olvashatók.
>
>
**Kijavított problémák:**

Az Azure AD Connect szinkronizálása

* Kijavított egy olyan problémát, amely az automatikus frissítést okozza a Azure AD Connect-kiszolgálón, még akkor is, ha az ügyfél letiltotta a szolgáltatást a Set-ADSyncAutoUpgrade parancsmag használatával. Ezzel a javítással a kiszolgáló automatikus frissítési folyamata továbbra is rendszeresen ellenőrzi a frissítést, de a letöltött telepítő tiszteletben tartja az automatikus frissítési konfigurációt.
* A helyi rSync frissítése során a Azure AD Connect létrehoz egy Azure ad-szolgáltatásfiókot, amelyet az Azure ad-összekötő használ az Azure AD-vel való szinkronizáláshoz. A fiók létrehozása után Azure AD Connect hitelesíti magát az Azure AD-vel a fiók használatával. Előfordulhat, hogy az átmeneti problémák miatt nem sikerül a hitelesítés, mert a (z) "hiba történt a (z)" a (z) "a (z) rendszerbeli, a (z)" hibát okozó helyi frissítés *aad-szinkronizáló végrehajtása során: AADSTS50034: az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a xxx.onmicrosoft.com könyvtárához. "* Az rSync-frissítés rugalmasságának javítása érdekében Azure AD Connect most újrapróbálkozik a hitelesítési lépéssel.
* Probléma merült fel a Build 443-es verzióban, amely a (helyi), a címtár-szinkronizáláshoz szükséges futtató profilok sikeres, de nem hozható létre. A gyógyító logikát a Azure AD Connect ezen buildje tartalmazza. Amikor az ügyfél frissíti erre a buildre, Azure AD Connect észleli a hiányzó futtatási profilokat, és létrehozza azokat.
* Kijavított egy problémát, amely miatt a jelszó-szinkronizálási folyamat nem indul el a 6900-as AZONOSÍTÓJÚ eseménnyel, a következő hiba miatt: *"az azonos kulccsal rendelkező elem már hozzá lett adva"*. Ez a probléma akkor fordul elő, ha a szervezeti egység szűrési konfigurációját úgy frissíti, hogy tartalmazza az AD konfigurációs partíciót. A probléma megoldásához a jelszó-szinkronizálási folyamat most csak az AD tartományi partíciók jelszavas változásait szinkronizálja. A nem tartományi partíciókat (például a konfigurációs partíciót) a rendszer kihagyja.
* Az expressz telepítés során Azure AD Connect létrehoz egy helyszíni AD DS fiókot, amelyet az AD-összekötő használ a helyszíni AD-vel való kommunikációhoz. Korábban a rendszer létrehozta a fiókot a felhasználói fiók-vezérlő attribútumhoz beállított PASSWD_NOTREQD jelzővel, és a fiókhoz egy véletlenszerű jelszót állít be. Most Azure AD Connect explicit módon eltávolítja a PASSWD_NOTREQD jelzőt, miután beállította a jelszót a fiókhoz.
* Kijavított egy olyan problémát, amely miatt az rSync frissítése sikertelen *"az SQL Serverben olyan holtpont történt, amely egy alkalmazás zárolását próbálta elérni"* , ha a mailNickname attribútum a helyszíni ad-sémában található, de nincs KÖTVE az ad felhasználói objektum osztályhoz.
* Javítva egy probléma, amely miatt az eszköz visszaírási szolgáltatása automatikusan le lesz tiltva, amikor egy rendszergazda frissíti Azure AD Connect szinkronizálási konfigurációt a Azure AD Connect varázslóval. Ezt a problémát a varázsló okozta, hogy előfeltétel-ellenőrzést hajt végre a meglévő eszköz visszaírási-konfigurációjában a helyszíni AD-ben, és az ellenőrzés sikertelen. A javítás az ellenőrzés kihagyása, ha az eszköz visszaírási már engedélyezve van.
* A szervezeti egység szűrésének konfigurálásához használhatja a Azure AD Connect varázslót vagy a Synchronization Service Manager. Korábban, ha a Azure AD Connect varázslót használja a szervezeti egység szűrésének konfigurálásához, akkor az ezután létrehozott új szervezeti egységek a címtár-szinkronizálás részét képezik. Ha nem szeretné, hogy az új szervezeti egységek is szerepeljenek, a Synchronization Service Manager használatával konfigurálnia kell a OU-szűrést. Most már ugyanezt a viselkedést Azure AD Connect varázsló használatával is elérheti.
* Kijavított egy olyan problémát, amely miatt a Azure AD Connect által igényelt tárolt eljárások a dbo séma alatt hozhatók létre a rendszergazda telepítésének sémájában.
* Kijavított egy olyan problémát, amely miatt az Azure AD által visszaadott TrackingId attribútumot a rendszer kihagyja a Azure AD Connect Server eseménynaplókban. A probléma akkor fordul elő, ha Azure AD Connect kap egy átirányítási üzenetet az Azure AD-től, és Azure AD Connect nem tud csatlakozni a megadott végponthoz. A támogatási mérnökök a TrackingId használják, hogy a hibaelhárítás során összekapcsolják a szolgáltatás naplófájljait.
* Ha Azure AD Connect LargeObject hibaüzenetet kap az Azure AD-től, Azure AD Connect Napszállta 6941 nevű eseményt hoz létre, és *"a kiépített objektum túl nagy" üzenet jelenik meg. Az objektum attribútumai számának levágása. "* Ugyanakkor Azure AD Connect egy megtévesztő eseményt is generál a Napszállta 6900 és a következő üzenettel *: "Microsoft. online. együttélés. ProvisionRetryException: nem lehet kommunikálni a Windows Azure Active Directory szolgáltatással."* A félreértések csökkentése érdekében Azure AD Connect már nem hozza létre az utóbbi eseményt, amikor LargeObject hiba érkezik.
* Kijavított egy problémát, amely miatt a Synchronization Service Manager nem válaszol, amikor megkísérli frissíteni az általános LDAP-összekötő konfigurációját.

**Új funkciók/Újdonságok:**

Az Azure AD Connect szinkronizálása
* Szinkronizálási szabály módosításai – a következő szinkronizálási szabály módosításai teljesültek:
  * A frissített alapértelmezett szinkronizálási szabály úgy van beállítva, hogy ne exportálja az attribútumokat **userCertificate** és **userSMIMECertificate** , ha az attribútumok több mint 15 értékkel rendelkeznek.
  * Az **AlkalmazottKód** és a **msExchBypassModerationLink** ad-attribútumok már szerepelnek az alapértelmezett szinkronizálási szabály készletében.
  * Az Active Directory-attribútum **fényképe** el lett távolítva az alapértelmezett szinkronizálási szabály készletből.
  * A **preferredDataLocation** hozzáadva a metaverse-sémához és az Azure ad Connector sémához. Azok az ügyfelek, akik az Azure AD-ben szeretnék frissíteni az attribútumokat, egyéni szinkronizálási szabályokat hozhatnak létre. 
  * A **userType** hozzáadva a metaverse-sémához és az Azure ad Connector sémához. Azok az ügyfelek, akik az Azure AD-ben szeretnék frissíteni az attribútumokat, egyéni szinkronizálási szabályokat hozhatnak létre.

* A Azure AD Connect mostantól automatikusan engedélyezi a ConsistencyGuid attribútum használatát a helyszíni AD-objektumokhoz tartozó forrás-Anchor attribútumként. Ha üres, Azure AD Connect a ConsistencyGuid attribútumot a objectGuid attribútum értékével tölti fel. Ez a funkció csak az új központi telepítésre vonatkozik. Ha többet szeretne megtudni erről a szolgáltatásról, tekintse meg a cikk [Azure ad Connect: tervezési fogalmak – az MS-DS-ConsistencyGuid as SourceAnchor használatával](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)című cikket.
* Új hibaelhárítási parancsmag Invoke-ADSyncDiagnostics lett hozzáadva a jelszó-kivonatolással kapcsolatos problémák diagnosztizálásához. A parancsmag használatával kapcsolatos további információkért tekintse meg a [jelszó-kivonatolás szinkronizálása Azure ad Connect szinkronizálással](tshoot-connect-password-hash-synchronization.md)című cikket.
* Azure AD Connect mostantól támogatja Mail-Enabled nyilvános mappák objektumainak a helyszíni AD-ből az Azure AD-be való szinkronizálását. A funkciót Azure AD Connect varázslóval engedélyezheti a választható szolgáltatások területen. Ha többet szeretne megtudni erről a szolgáltatásról, tekintse [meg az Office 365 Directory-alapú Edge blokkolja a helyszíni levelezésre engedélyezett nyilvános mappák használatát](https://techcommunity.microsoft.com/t5/exchange/office-365-directory-based-edge-blocking-support-for-on-premises/m-p/74218)ismertető cikket.
* Azure AD Connect AD DS-fiókot kell szinkronizálnia a helyszíni AD-ből. Korábban, ha a Azure AD Connect Express mód használatával telepítette, akkor megadhatja a vállalati rendszergazdai fiók hitelesítő adatait, és Azure AD Connect hozza létre a szükséges AD DS fiókot. Azonban az egyéni telepítéshez és az erdők meglévő központi telepítéshez való hozzáadásához a AD DS fiókot kell megadnia. Most lehetősége van arra is, hogy a vállalati rendszergazdai fiók hitelesítő adatait egy egyéni telepítés során adja meg, Azure AD Connect hozza létre a szükséges AD DS fiókot.
* Azure AD Connect mostantól támogatja az SQL ALAPSZABÁLYT. Azure AD Connect telepítése előtt engedélyeznie kell az SQL ALAPSZABÁLYT-t. A telepítés során a Azure AD Connect észleli, hogy a megadott SQL-példány engedélyezve van-e az SQL ALAPSZABÁLYÁBAN vagy sem. Ha az SQL ALAPSZABÁLYT engedélyezve van, Azure AD Connect további adatokat, ha az SQL ALAPSZABÁLYT szinkron replikálás vagy aszinkron replikáció használatára van konfigurálva. A rendelkezésre állási csoport figyelő beállításakor ajánlott a RegisterAllProvidersIP tulajdonságot 0-ra állítani. Ez a javaslat azért van, mert Azure AD Connect jelenleg SQL Native Client használ az SQL-hez való kapcsolódáshoz, és SQL Native Client nem támogatja a MultiSubNetFailover tulajdonság használatát.
* Ha a LocalDB-t használja a Azure AD Connect-kiszolgáló adatbázisához, és elérte a 10 GB-os mérethatárt, a szinkronizálási szolgáltatás már nem indul el. Korábban a LocalDB ShrinkDatabase műveletet kell végrehajtania ahhoz, hogy a szinkronizálási szolgáltatás elindításához elegendő adatbázis álljon rendelkezésre. Ezt követően a Synchronization Service Manager használatával törölheti a futtatási előzményeket, hogy további adatbázis-területet igényeljen. Mostantól Start-ADSyncPurgeRunHistory parancsmaggal törölheti a LocalDB származó futtatási előzményeket, és visszaigényelheti az adatbázis területét. Emellett a parancsmag egy offline üzemmódot is támogat (az-offline paraméter megadásával), amely akkor használható, ha a szinkronizációs szolgáltatás nem fut. Megjegyzés: az offline mód csak akkor használható, ha a szinkronizációs szolgáltatás nem fut, és a használt adatbázis LocalDB.
* A szükséges tárterület méretének csökkentése érdekében Azure AD Connect a LocalDB/SQL-adatbázisokban való tárolás előtt tömöríti a szinkronizálási hibák részleteit. Ha a Azure AD Connect egy régebbi verziójára frissít erre a verzióra, Azure AD Connect egy egyszeri tömörítést hajt végre a meglévő szinkronizálási hiba részletein.
* Korábban, a szervezeti egység szűrési konfigurációjának frissítése után manuálisan kell futtatnia a teljes importálást annak biztosításához, hogy a meglévő objektumok megfelelően szerepeljenek/ki legyenek zárva a címtár-szinkronizálásból. Most Azure AD Connect automatikusan elindítja a teljes importálást a következő szinkronizálási ciklusban. Ezen túlmenően a teljes importálás csak a frissítés által érintett AD-összekötők esetében érvényes. Megjegyzés: Ez a fejlesztés csak a Azure AD Connect varázsló használatával készített szervezeti egységek szűrésére vonatkozik. Nem alkalmazható a Synchronization Service Manager használatával készített OU-szűrési frissítésre.
* Korábban a csoport alapú szűrés csak a felhasználókat, a csoportokat és a kapcsolattartási objektumokat támogatja. A csoport alapú szűrés most is támogatja a számítógép-objektumokat.
* Korábban a Azure AD Connect szinkronizálási ütemező letiltása nélkül törölheti az összekötő területét. A Synchronization Service Manager mostantól letiltja az összekötő területének adattörlését, ha azt észleli, hogy az ütemező engedélyezve van. Továbbá a rendszer figyelmeztetést küld, hogy tájékoztassa az ügyfeleket a lehetséges adatvesztésről, ha az összekötő területének adatait törlik.
* Korábban le kell tiltania a PowerShell átírását a Azure AD Connect varázsló megfelelő futtatásához. Ez a probléma részben fel van oldva. Ha Azure AD Connect varázslót használ a szinkronizálási konfiguráció kezelésére, engedélyezheti a PowerShell átírását. Ha Azure AD Connect varázslót használ az ADFS-konfiguráció kezeléséhez, le kell tiltania a PowerShell átírását.



## <a name="114860"></a>1.1.486.0
Kiadás dátuma: 2017 április

**Kijavított problémák:**
* Kijavítva a probléma, amelyben a Azure AD Connect nem települ a Windows Server honosított verziójában.

## <a name="114840"></a>1.1.484.0
Kiadás dátuma: 2017 április

**Ismert problémák:**

* A Azure AD Connect ezen verziója nem települ sikeresen, ha az alábbi feltételek mindegyike teljesül:
   1. A helyi vagy az rSync helyben történő frissítését vagy a Azure AD Connect friss telepítését is végrehajtja.
   2. A Windows Server honosított verzióját használja, ahol a kiszolgálón a beépített Rendszergazda csoport neve nem "rendszergazdák".
   3. Az alapértelmezett SQL Server 2012 Express LocalDB használja Azure AD Connect a saját teljes SQL megadása helyett.

**Kijavított problémák:**

Az Azure AD Connect szinkronizálása
* Kijavítva egy probléma, amelyben a szinkronizálási ütemező kihagyja a teljes szinkronizálási lépést, ha egy vagy több összekötő hiányzik a futtatási profil az adott szinkronizálási lépéshez. Például manuálisan hozzáadott egy összekötőt a Synchronization Service Manager használatával, és nem kell létrehoznia különbözeti importálási profilt. Ez a javítás biztosítja, hogy a szinkronizálási ütemező továbbra is futtasson különbözeti importálást más összekötők esetében.
* Kijavított egy hibát, amelyben a szinkronizációs szolgáltatás azonnal leállítja a futtatási profil feldolgozását, ha a futtatási lépések egyikével probléma merül fel. Ez a javítás biztosítja, hogy a szinkronizálási szolgáltatás kihagyja a futtatási lépést, és folytatja a REST feldolgozását. Például van egy különbözeti importálási profilja az AD-összekötőhöz több futtatási lépéssel (egy minden helyszíni AD-tartományhoz). A szinkronizálási szolgáltatás a különbözeti importálást a többi AD-tartománnyal is futtatja, még akkor is, ha az egyikük hálózati kapcsolattal rendelkezik.
* Kijavítva egy olyan hibát, amely miatt az Azure AD Connector frissítését a rendszer kihagyja az automatikus frissítés során.
* Kijavított egy olyan problémát, amely miatt a Azure AD Connect helytelenül határozzák meg, hogy a kiszolgáló tartományvezérlő-e a telepítés során, ami miatt a rendszerállapot-frissítés sikertelen lesz.
* Kijavított egy olyan problémát, amelynek hatására az rSync helyben történő frissítése nem hoz létre futtatási profilt az Azure AD Connectorhoz.
* Kijavított egy hibát, amelyben az Synchronization Service Manager felhasználói felülete nem válaszol az általános LDAP-összekötő konfigurálására tett kísérlet során.

AD FS-kezelés
* Kijavított egy hibát, amikor a Azure AD Connect varázsló meghibásodik, ha a AD FS elsődleges csomópont át lett helyezve egy másik kiszolgálóra.

Asztali egyszeri bejelentkezés
* Kijavított egy problémát a Azure AD Connect varázslóban, ahol a Sign-In képernyő nem teszi lehetővé az asztali egyszeri bejelentkezés használatát, ha az új telepítés során a jelszó-szinkronizálás lehetőséget választotta Sign-In lehetőségként.

**Új funkciók/Újdonságok:**

Az Azure AD Connect szinkronizálása
* Azure AD Connect Sync mostantól támogatja a virtuális szolgáltatásfiók, a felügyelt szolgáltatásfiók és a csoportosan felügyelt szolgáltatásfiók használatát a szolgáltatási fiókként. Ez csak a Azure AD Connect új telepítésére vonatkozik. Azure AD Connect telepítésekor:
    * Alapértelmezés szerint Azure AD Connect varázsló létrehoz egy virtuális szolgáltatásfiókot, és azt használja szolgáltatásfiókként.
    * Ha tartományvezérlőre végzi a telepítést, Azure AD Connect visszatér az előző működésre, ahol tartományi felhasználói fiókot hoz létre, és ehelyett a szolgáltatás fiókját használja.
    * Az alapértelmezett viselkedést felülbírálhatja a következők egyikének megadásával:
      * Csoportosan felügyelt szolgáltatásfiók
      * Felügyelt szolgáltatásfiók
      * Tartományi felhasználói fiók
      * Helyi felhasználói fiók
* Korábban, ha az összekötők frissítési vagy szinkronizálási szabályának módosítását tartalmazó Azure AD Connect új buildre frissít, Azure AD Connect a teljes szinkronizálási ciklust indít el. Most Azure AD Connect a teljes importálás lépést csak a frissítéssel rendelkező összekötők esetében, és a teljes szinkronizálás lépést csak a szinkronizálási szabály módosításával rendelkező összekötők esetében.
* Korábban az Exportálás törlési küszöbértéke csak a szinkronizálási ütemező által aktivált exportálásokra vonatkozik. Mostantól a szolgáltatás ki van bővítve, hogy belefoglalja az ügyfél által manuálisan aktivált exportálásokat a Synchronization Service Manager használatával.
* Az Azure AD-bérlőn van egy szolgáltatás konfigurációja, amely azt jelzi, hogy engedélyezve van-e a jelszó-szinkronizálási funkció a bérlő számára. Korábban a szolgáltatás konfigurációját a Azure AD Connect helytelenül konfigurálja, ha aktív és átmeneti kiszolgálóval rendelkezik. A Azure AD Connect most a szolgáltatás konfigurációját a csak az aktív Azure AD Connect-kiszolgálóval konzisztensen fogja végrehajtani.
* Azure AD Connect varázsló most észleli és visszaadja a figyelmeztetést, ha a helyszíni AD nem rendelkezik engedélyezve az AD Lomtár szolgáltatással.
* Korábban az Azure AD-ba való exportálás időtúllépést eredményezett, és sikertelen lesz, ha a kötegben lévő objektumok összesített mérete meghaladja az adott küszöbértéket. A szinkronizálási szolgáltatás most újra megkísérli az objektumok újraküldését külön, kisebb kötegekben, ha a probléma előfordul.
* A szinkronizálási szolgáltatás kulcskezelő alkalmazása el lett távolítva a Windows Start menüjéből. A titkosítási kulcs kezelése a miiskmu.exe használatával a parancssori felületen továbbra is támogatott lesz. A titkosítási kulcs kezelésével kapcsolatos információkért tekintse meg [a Azure ad Connect szinkronizálási titkosítási kulcsának felhagyása](./how-to-connect-sync-change-serviceacct-pass.md#abandoning-the-adsync-service-account-encryption-key)című cikket.
* Korábban, ha módosítja a Azure AD Connect Sync szolgáltatás fiókjának jelszavát, a szinkronizálási szolgáltatás nem fog megfelelően elindulni, amíg el nem hagyta a titkosítási kulcsot, és újrainicializálta a Azure AD Connect Sync szolgáltatás fiókjának jelszavát. Ez a folyamat már nem szükséges.

Asztali egyszeri bejelentkezés

* Azure AD Connect a varázslónak többé nem kell megnyitnia a 9090-es portot a hálózaton az átmenő hitelesítés és az asztali egyszeri bejelentkezés konfigurálása során. Csak a 443-es port szükséges.

## <a name="114430"></a>1.1.443.0
Kiadás dátuma: március 2017

**Kijavított problémák:**

Az Azure AD Connect szinkronizálása
* Kijavított egy problémát, amely Azure AD Connect varázsló meghibásodását okozza, ha az Azure AD-összekötő megjelenített neve nem tartalmazza az Azure AD-bérlőhöz hozzárendelt kezdeti onmicrosoft.com tartományt.
* Kijavítva egy olyan hibát, amely Azure AD Connect varázsló hibáját okozza az SQL Database-hez való kapcsolódáskor, ha a szinkronizálási szolgáltatás fiókjának jelszava speciális karaktereket tartalmaz, például aposztrófot, kettőspontot és szóközt.
* Kijavítva egy olyan hibát, amely miatt a "The rendszerkép rendelkezik egy, a rendszerképtől eltérő horgonysal", hogy egy Azure AD Connect-kiszolgálón átmeneti módban történjen, miután átmenetileg kizárta a helyszíni AD-objektumot a szinkronizálásból, majd ismét belefoglalta a szinkronizáláshoz.
* Kijavított egy olyan problémát, amely "a DN által elhelyezkedő objektum fantomja" hibát okoz a Azure AD Connect-kiszolgáló átmeneti módban való előfordulásakor, miután átmenetileg kizárta a helyszíni AD-objektumot a szinkronizálásból, majd ismét belefoglalta a szinkronizáláshoz.

AD FS-kezelés
* Kijavított egy problémát, amelyben Azure AD Connect varázsló nem frissíti AD FS konfigurációt, és a függő entitás megbízhatóságának megfelelő jogcímeket állítja be a másik bejelentkezési azonosító konfigurálása után.
* Kijavított egy problémát, amelyben Azure AD Connect varázsló nem tudja megfelelően kezelni azokat AD FS kiszolgálókat, amelyek sAMAccountName formátuma helyett userPrincipalName formátumban vannak konfigurálva.

Átmenő hitelesítés
* Kijavítva egy olyan hibát, amely Azure AD Connect varázsló meghibásodását okozza, ha a hitelesítés átadása beállítás be van jelölve, de az összekötő regisztrációja sikertelen.
* Javítva egy probléma, amely miatt a Azure AD Connect varázsló megkerüli az érvényesítési ellenőrzéseket a bejelentkezési módszer beállításnál, ha engedélyezve van az asztali egyszeri bejelentkezés funkció.

Új jelszó kérése
* A probléma kijavítva, ami miatt előfordulhat, hogy az Azure Azure AD Connect-kiszolgáló nem próbálkozik újra a kapcsolódással, ha a kapcsolatot tűzfal vagy proxy törölte.

**Új funkciók/Újdonságok:**

Az Azure AD Connect szinkronizálása
* A Get-ADSyncScheduler parancsmag mostantól egy új, SyncCycleInProgress nevű logikai tulajdonságot ad vissza. Ha a visszaadott érték igaz, az azt jelenti, hogy folyamatban van egy ütemezett szinkronizálási ciklus.
* Azure AD Connect telepítési és telepítési naplók tárolására szolgáló célmappa át lett helyezve a%localappdata%\AADConnect-ből a%programdata%\AADConnect-be a naplófájlok hozzáférhetőségének javítása érdekében.

AD FS-kezelés
* A AD FS Farm TLS/SSL-tanúsítványának frissítésével kapcsolatos támogatás hozzáadva.
* A AD FS 2016 felügyeletének támogatása.
* Most már megadhat meglévő gMSA (csoportosan felügyelt szolgáltatásfiók) a AD FS telepítésekor.
* Mostantól az SHA-256-et az Azure AD függő entitás megbízhatóságához tartozó aláírási kivonatoló algoritmusként is konfigurálhatja.

Új jelszó kérése
* Bevezetett Újdonságok, amelyek lehetővé teszik, hogy a termék szigorúbb tűzfalszabályok használatával működjön a környezetekben.
* Jobb kapcsolódási megbízhatóság Azure Service Bushoz.

## <a name="113800"></a>1.1.380.0
Kiadás dátuma: 2016. december

**Kijavítva a probléma:**

* Kijavítva a probléma, amelyben a issuerid jogcím szabálya Active Directory összevonási szolgáltatások (AD FS) (AD FS) hiányzik ebben a buildben.

>[!NOTE]
>Ez a Build nem érhető el az ügyfelek számára a Azure AD Connect automatikus frissítési funkcióján keresztül.
## <a name="113710"></a>1.1.371.0
Kiadás dátuma: 2016. december

**Ismert probléma:**

* A AD FS issuerid-jogcím szabálya hiányzik ebben a buildben. A issuerid-jogcím szabályának megadása kötelező, ha Azure Active Directory (Azure AD-val) több tartományt is egyesítő. Ha Azure AD Connectt használ a helyszíni AD FS központi telepítésének kezeléséhez, a buildre való frissítés eltávolítja a meglévő issuerid-jogcím szabályát a AD FS-konfigurációból. A probléma megkerüléséhez adja hozzá a issuerid jogcím szabályt a telepítés/frissítés után. A issuerid-jogcím szabályának hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure ad-vel való egyesítő több tartományon belüli támogatásával](how-to-connect-install-multiple-domains.md)kapcsolatban.

**Kijavítva a probléma:**

* Ha a 9090-as port nincs megnyitva a kimenő kapcsolathoz, akkor a Azure AD Connect telepítés vagy a frissítés meghiúsul.

>[!NOTE]
>Ez a Build nem érhető el az ügyfelek számára a Azure AD Connect automatikus frissítési funkcióján keresztül.
## <a name="113700"></a>1.1.370.0
Kiadás dátuma: 2016. december

**Ismert problémák:**

* A AD FS issuerid-jogcím szabálya hiányzik ebben a buildben. Az issuerid-jogcím szabálya akkor szükséges, ha több tartományt is egyesítő az Azure AD-vel. Ha Azure AD Connectt használ a helyszíni AD FS központi telepítésének kezeléséhez, a buildre való frissítés eltávolítja a meglévő issuerid-jogcím szabályát a AD FS-konfigurációból. A probléma megkerüléséhez adja hozzá a issuerid jogcím szabályt a telepítés/frissítés után. A issuerid-jogcím hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure ad-vel való egyesítő több tartományi támogatásáról](how-to-connect-install-multiple-domains.md)szóló cikkben.
* A telepítés befejezéséhez a 9090-es portnak nyitott kimenőnek kell lennie.

**Új funkciók:**

* Átmenő hitelesítés (előzetes verzió).

>[!NOTE]
>Ez a Build nem érhető el az ügyfelek számára a Azure AD Connect automatikus frissítési funkcióján keresztül.
## <a name="113430"></a>1.1.343.0
Kiadás dátuma: november 2016

**Ismert probléma:**

* A AD FS issuerid-jogcím szabálya hiányzik ebben a buildben. Az issuerid-jogcím szabálya akkor szükséges, ha több tartományt is egyesítő az Azure AD-vel. Ha Azure AD Connectt használ a helyszíni AD FS központi telepítésének kezeléséhez, a buildre való frissítés eltávolítja a meglévő issuerid-jogcím szabályát a AD FS-konfigurációból. A probléma megkerüléséhez adja hozzá a issuerid jogcím szabályt a telepítés/frissítés után. A issuerid-jogcím hozzáadásával kapcsolatos részletekért tekintse meg ezt a cikket az [Azure ad-vel való egyesítő több tartományi támogatásáról](how-to-connect-install-multiple-domains.md)szóló cikkben.

**Kijavított problémák:**

* Előfordulhat, hogy a Azure AD Connect telepítése sikertelen, mert nem tud létrehozni olyan helyi szolgáltatásfiókot, amelynek jelszava megfelel a szervezet jelszóházirend által meghatározott bonyolultsági szintnek.
* Kijavított egy problémát, amelyben a csatlakozási szabályok nem lesznek újraértékelve, ha az összekötő terület egyik objektuma egy csatlakozási szabály hatókörén kívül esik, és egy másikra is érvényes lesz. Ez akkor fordulhat elő, ha két vagy több olyan csatlakozási szabálya van, amelynek csatlakozási feltételei kölcsönösen kizárják egymást.
* A probléma kijavítva, amikor a bejövő szinkronizálási szabályok (az Azure AD-ből) nem tartalmaznak csatlakoztatási szabályokat, nem lesznek feldolgozva, ha alacsonyabb prioritási értékekkel rendelkeznek, mint az illesztési szabályok.

**Fejlesztések:**

* A Azure AD Connect telepítésének támogatása a Windows Server 2016 standard vagy újabb verzióra.
* A SQL Server 2016 távoli adatbázisként való használatának támogatása a Azure AD Connecthoz.

## <a name="112810"></a>1.1.281.0
Kiadás dátuma: augusztus 2016

**Kijavított problémák:**

* A szinkronizálási időköz módosításai csak a következő szinkronizálási ciklus befejeződése után lépnek érvénybe.
* Azure AD Connect varázsló nem fogad el olyan Azure AD-fiókot, amelynek a felhasználóneve aláhúzásjel () karakterrel kezdődik \_ .
* Azure AD Connect varázsló nem tudja hitelesíteni az Azure AD-fiókot, ha a fiók jelszava túl sok speciális karaktert tartalmaz. Hibaüzenet: "nem sikerült érvényesíteni a hitelesítő adatokat. Váratlan hiba történt. " részében talál.”
* Az átmeneti kiszolgáló eltávolításával letiltja a jelszó-szinkronizálást az Azure AD-bérlőben, és a jelszó-szinkronizálást nem sikerül az Active Serverrel.
* A jelszó-szinkronizálás nem gyakori esetekben sikertelen, ha a felhasználó nem tárol jelszó-kivonatot.
* Ha Azure AD Connect kiszolgáló átmeneti üzemmódra van beállítva, a jelszó visszaírási nem átmenetileg le van tiltva.
* Azure AD Connect varázsló nem jeleníti meg a jelszó-szinkronizálás és a jelszó tényleges visszaírási konfigurációját, ha a kiszolgáló átmeneti módban van. Mindig letiltottként jeleníti meg őket.
* A jelszó-szinkronizálás és a jelszó visszaírási konfigurációs módosításait Azure AD Connect varázsló nem őrzi meg, ha a kiszolgáló átmeneti módban van.

**Fejlesztések:**

* Frissítette a Start-ADSyncSyncCycle parancsmagot annak jelzésére, hogy képes-e sikeresen elindítani egy új szinkronizálási ciklust.
* A Stop-ADSyncSyncCycle parancsmag hozzáadva a szinkronizálási ciklus és a művelet megszakításához, amely jelenleg folyamatban van.
* A jelenleg folyamatban lévő szinkronizálási ciklus és művelet megszakításához frissítse a Stop-ADSyncScheduler parancsmagot.
* A Azure AD Connect varázslóban a [címtár-bővítmények](how-to-connect-sync-feature-directory-extensions.md) konfigurálásakor a "Teletex string" típusú Azure ad-attribútum most kiválasztható.

## <a name="111890"></a>1.1.189.0
Kiadás dátuma: június 2016

**Rögzített problémák és javítás:**

* A Azure AD Connect mostantól egy FIPS-kompatibilis kiszolgálóra telepíthető.
  * A jelszó-szinkronizálással kapcsolatban lásd: [jelszó-kivonat szinkronizálása és FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Kijavított egy problémát, ha a NetBIOS-név nem oldható fel a teljes tartománynévre a Active Directory-összekötőben.

## <a name="111800"></a>1.1.180.0
Kiadás dátuma: 2016. május

**Új funkciók:**

* Figyelmezteti, és segít a tartományok ellenőrzésében, ha a Azure AD Connect futtatása előtt nem tette meg.
* A [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany)támogatása.
* A legújabb [Microsoft Azure Government felhőalapú](reference-connect-instances.md#microsoft-azure-government) infrastruktúra támogatása új URL-követelményekkel.

**Rögzített problémák és javítás:**

* A szinkronizálási szabály szerkesztőjének szűrésével könnyen megtalálhatja a szinkronizálási szabályokat.
* Jobb teljesítmény az összekötők területének törlésekor.
* Kijavított egy problémát, ha ugyanaz az objektum törölve lett, és hozzá lett adva ugyanebben a futtatásban (Törlés/Hozzáadás néven).
* Egy letiltott szinkronizálási szabály már nem teszi lehetővé a befoglalt objektumok és attribútumok frissítéssel vagy címtár-séma frissítéssel való újbóli engedélyezését.

## <a name="111300"></a>1.1.130.0
Kiadás dátuma: 2016 április

**Új funkciók:**

* Többértékű attribútumok támogatása a [címtár-bővítmények](how-to-connect-sync-feature-directory-extensions.md)számára.
* Az [automatikus verziófrissítés](how-to-connect-install-automatic-upgrade.md) további konfigurációs változatait is támogatja, hogy azok a frissítésre jogosultak legyenek.
* Néhány parancsmagot adott hozzá az [Egyéni ütemező](how-to-connect-sync-feature-scheduler.md#custom-scheduler)szolgáltatáshoz.

## <a name="111190"></a>1.1.119.0
Kiadás dátuma: március 2016

**Kijavított problémák:**

* Az expressz telepítés nem használható a Windows Server 2008 (pre-R2) rendszeren, mert ez az operációs rendszer nem támogatja a jelszó-szinkronizálást.
* Az egyéni szűrő-konfigurációval való frissítés nem a várt módon működik.
* Ha újabb verzióra frissít, és a konfiguráció nem módosul, a teljes importálás/szinkronizálás nem ütemezhető.

## <a name="111100"></a>1.1.110.0
Kiadás dátuma: február 2016

**Kijavított problémák:**

* A korábbi verziókról való frissítés nem működik, ha a telepítés nem szerepel az alapértelmezett C:\Program Files mappában.
* Ha telepíti és törli **a szinkronizálási folyamatot** a telepítővarázsló végén, a telepítővarázsló második alkalommal történő futtatása nem teszi lehetővé a Feladatütemezőt.
* Az ütemező nem a várt módon működik olyan kiszolgálókon, amelyeken nincs használatban az USA-beli dátum/idő formátum. Emellett `Get-ADSyncScheduler` a helyes időpontok visszaküldését is letiltja.
* Ha a Azure AD Connect korábbi kiadását telepítette AD FS a bejelentkezési lehetőséggel és a frissítéssel, a telepítővarázsló nem futtatható újra.

## <a name="111050"></a>1.1.105.0
Kiadás dátuma: február 2016

**Új funkciók:**

* [Automatikus frissítési](how-to-connect-install-automatic-upgrade.md) funkció az expressz beállítások ügyfelei számára.
* A globális rendszergazda támogatása az Azure AD Multi-Factor Authentication és Privileged Identity Management használatával a telepítővarázsló segítségével.
  * Engedélyeznie kell, hogy a proxy a Multi-Factor Authentication használata esetén is engedélyezze a forgalmat https://secure.aadcdn.microsoftonline-p.com .
  * A https://secure.aadcdn.microsoftonline-p.com multi-Factor Authentication megfelelő működéséhez hozzá kell adnia a megbízható helyek listájához.
* A felhasználói bejelentkezési módszer módosításának engedélyezése a kezdeti telepítés után.
* A [tartomány és a szervezeti egység szűrésének](how-to-connect-install-custom.md#domain-and-ou-filtering) engedélyezése a telepítővarázsló számára. Ez lehetővé teszi az olyan erdőkhöz való csatlakozást is, ahol nem minden tartomány érhető el.
* A [Scheduler](how-to-connect-sync-feature-scheduler.md) a Szinkronizáló motorba van beépítve.

**Az előzetes verzióról a GA-ra előléptetett funkciók:**

* [Eszköz visszaírási](how-to-connect-device-writeback.md).
* [Directory-bővítmények](how-to-connect-sync-feature-directory-extensions.md).

**Új előzetes verziójú funkciók:**

* Az új alapértelmezett szinkronizálási ciklus időköze 30 perc. Az összes korábbi kiadás esetében három órát vett igénybe. Támogatást nyújt az [ütemező](how-to-connect-sync-feature-scheduler.md) működésének módosításához.

**Kijavított problémák:**

* A DNS-tartományok ellenőrzése lap nem ismerte fel a tartományokat.
* AD FS konfigurálásakor a rendszer tartományi rendszergazdai hitelesítő adatokat kér.
* A telepítővarázsló nem ismeri fel a helyszíni AD-fiókokat, ha olyan tartományban található, amely a gyökértartomány eltérő DNS-fájával rendelkezik.

## <a name="1091310"></a>1.0.9131.0
Kiadás dátuma: 2015. december

**Kijavított problémák:**

* Előfordulhat, hogy a jelszó-szinkronizálás nem működik Active Directory Domain Services (AD DS) jelszavainak módosításakor, de a jelszó beállításakor is működik.
* Ha proxykiszolgáló van, előfordulhat, hogy az Azure AD-hitelesítés sikertelen a telepítés során, vagy ha a konfiguráció lapon megszakítják a frissítést.
* Ha nem SQL Server rendszergazda (SA), a Azure AD Connect korábbi, teljes SQL Server példánnyal rendelkező verziójáról való frissítés meghiúsul.
* A Azure AD Connect korábbi kiadásáról egy távoli SQL Server a "nem sikerült hozzáférni a ADSync SQL Database-hez" hibaüzenet jelenik meg.

## <a name="1091250"></a>1.0.9125.0
Kiadás dátuma: november 2015

**Új funkciók:**

* A AD FS újrakonfigurálható az Azure AD-beli megbízhatóságra.
* Frissítheti a Active Directory sémát, és újragenerálta a szinkronizálási szabályokat.
* Letilthatja a szinkronizálási szabályt.
* A "AuthoritativeNull" definiálható új literálként egy szinkronizálási szabályban.

**Új előzetes verziójú funkciók:**

* [Azure ad Connect Health szinkronizáláshoz](how-to-connect-health-sync.md).
* [Azure ad Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) jelszó-szinkronizálás támogatása.

**Új támogatott forgatókönyv:**

* Több helyszíni Exchange-szervezetet is támogat. További információ: [hibrid központi telepítések több Active Directory erdővel](/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Kijavított problémák:**

* Jelszó-szinkronizálási problémák:
  * A hatókörön kívülről a hatókörre áthelyezett objektum nem fogja szinkronizálni a jelszavát. Ez magában foglalja a szervezeti egység és az attribútumok szűrését is.
  * A szinkronizálni kívánt új szervezeti egység kiválasztása nem igényel teljes jelszó-szinkronizálást.
  * Ha egy letiltott felhasználó engedélyezve van, a jelszó nem szinkronizálódik.
  * A jelszó-újrapróbálkozási várólista végtelen, és a rendszer eltávolítja az előző korlátot a kivonni kívánt 5 000 objektumról.
* Nem lehet csatlakozni a Active Directoryhoz a Windows Server 2016-erdő működési szintjén.
* Nem lehet módosítani a csoport szűréséhez használt csoportot a kezdeti telepítés után.
* Már nem hoz létre új felhasználói profilt a Azure AD Connect-kiszolgálón minden felhasználó számára, ha a jelszó visszaírási engedélyezve van.
* Nem lehet hosszú egész értékeket használni a szinkronizálási szabályok hatókörében.
* Ha nem érhető el tartományvezérlő, az "eszköz visszaírási" jelölőnégyzet le lesz tiltva.

## <a name="1086670"></a>eszközök 1.0.8667.0
Kiadás dátuma: augusztus 2015

**Új funkciók:**

* A Azure AD Connect telepítővarázsló mostantól honosítva van a Windows Server összes nyelvére.
* A fiók feloldásának támogatása az Azure AD jelszavas kezelésének használatakor.

**Kijavított problémák:**

* Azure AD Connect telepítővarázsló összeomlik, ha egy másik felhasználó folytatja a telepítést, nem pedig a telepítést első alkalommal indító személy.
* Ha a Azure AD Connect korábbi eltávolítása nem tudja eltávolítani a Azure AD Connect szinkronizálást, nem lehet újratelepíteni.
* Az expressz telepítéssel nem telepíthető Azure AD Connect, ha a felhasználó nem az erdő gyökértartományában található, vagy ha a Active Directory nem angol nyelvű verziója van használatban.
* Ha a Active Directory felhasználói fiók teljes tartományneve nem oldható fel, akkor a "nem sikerült véglegesíteni a sémát" hibaüzenet jelenik meg.
* Ha a Active Directory-összekötőn használt fiók a varázslón kívül módosul, a varázsló a későbbi futtatások során sikertelen lesz.
* Azure AD Connect időnként nem lehet tartományvezérlőre telepíteni.
* Az "átmeneti üzemmód" nem engedélyezhető és nem tiltható le, ha a bővítmény attribútumai hozzá lettek adva.
* Bizonyos konfigurációk esetén a jelszó visszaírási sikertelen, mert az Active Directory-összekötőn rossz jelszó van.
* A (z) nem frissíthető, ha megkülönböztető nevet (DN) használ az attribútumok szűréséhez.
* Túlzott CPU-használat a jelszó alaphelyzetbe állításakor.

**Az előzetes verzió funkcióinak eltávolítása:**

* Az előzetes verziójú funkció [felhasználói visszaírási](how-to-connect-preview.md#user-writeback) átmenetileg el lett távolítva az előzetes verziójú ügyfeleink visszajelzései alapján. Később ismét hozzá lesz adva a visszajelzések megadásához.

## <a name="1086410"></a>1.0.8641.0
Kiadás dátuma: június 2015

**Azure AD Connect kezdeti kiadása.**

A név Azure AD-szinkronizálóról Azure AD Connectra módosult.

**Új funkciók:**

* [Expressz beállítások](how-to-connect-install-express.md) telepítése
* [Konfigurálható AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* [Frissíthet a](how-to-dirsync-upgrade-get-started.md)
* [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* [Átmeneti üzemmód](how-to-connect-sync-staging-server.md) bevezetése

**Új előzetes verziójú funkciók:**

* [Felhasználói visszaírási](how-to-connect-preview.md#user-writeback)
* [Eszközvisszaíró](how-to-connect-device-writeback.md)
* [Címtárbővítmények](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Kiadás dátuma: 2015. május

**Új követelmény:**

* Azure AD-szinkronizáló most a .NET-keretrendszer 4.5.1-es verziójának telepítése szükséges.

**Kijavított problémák:**

* Az Azure AD-ből származó jelszó-visszaírási Azure Service Bus kapcsolódási hibával meghiúsul.

## <a name="104910413"></a>1.0.491.0413
Kiadás dátuma: 2015 április

**Rögzített problémák és javítás:**

* Az Active Directory-összekötő nem dolgozza fel megfelelően a törlést, ha a Lomtár engedélyezve van, és az erdőben több tartomány található.
* Az importálási műveletek teljesítménye a Azure Active Directory-összekötő esetében javult.
* Ha egy csoport túllépte a tagsági korlátot (alapértelmezés szerint a korlát értéke 50 000 objektum), a csoport törölve lett Azure Active Directoryban. Az új viselkedéssel a csoport nem törlődik, a rendszer hibát jelez, és az új tagsági változások nem lesznek exportálva.
* Nem lehet új objektumot kiépíteni, ha az összekötő térben már szerepel azonos DN-vel rendelkező előkészített törlés.
* Egyes objektumok szinkronizálásra vannak megjelölve a különbözeti szinkronizálás során, annak ellenére, hogy az objektumon nem történt változás.
* A jelszó-szinkronizálás kényszerítése eltávolítja az előnyben részesített TARTOMÁNYVEZÉRLŐk listáját is.
* A CSExportAnalyzer problémákba ütköznek bizonyos objektumok állapotával.

**Új funkciók:**

* Egy illesztéssel az MV-ban az "ANY" objektumtípus is csatlakozhat.

## <a name="104850222"></a>1.0.485.0222
Kiadás dátuma: február 2015

**Fejlesztések:**

* Továbbfejlesztett importálási teljesítmény.

**Kijavított problémák:**

* A jelszó-szinkronizálás tiszteletben tartja a cloudFiltered attribútumot, amelyet az attribútumok szűrése használ. A szűrt objektumok már nem szerepelnek a jelszó-szinkronizálás hatókörében.
* Ritka helyzetekben, amikor a topológia sok tartományvezérlővel rendelkezett, a jelszó-szinkronizálás nem működik.
* A "leállított kiszolgáló" az Azure AD-Összekötőből való importálás után, az Azure AD-ben és az Intune-ban történő eszközkezelés után.
* A külső rendszerbiztonsági tag (FSP) ugyanahhoz az erdőben található több tartományhoz való csatlakoztatása nem egyértelmű illesztési hibát okoz.

## <a name="104751202"></a>1.0.475.1202
Kiadás dátuma: 2014. december

**Új funkciók:**

* Mostantól támogatott a jelszó-szinkronizálás az attribútum-alapú szűréssel. További információ: jelszó- [szinkronizálás szűréssel](how-to-connect-sync-configure-filtering.md).
* Az MS-DS-ExternalDirectoryObjectID attribútum Active Directoryba íródik vissza. Ez a funkció a Microsoft 365 alkalmazások támogatását adja meg. A OAuth2 használatával fér hozzá az online és a helyszíni postaládákhoz egy hibrid Exchange-telepítésben.

**Rögzített frissítési problémák:**

* A Bejelentkezési segéd újabb verziója érhető el a kiszolgálón.
* A Azure AD-szinkronizáló telepítéséhez egyéni telepítési útvonal lett használva.
* Egy érvénytelen egyéni illesztési feltétel blokkolja a frissítést.

**Egyéb javítások:**

* Kijavította az Office Pro Plus sablonjait.
* Egy kötőjeltel kezdődő felhasználónevek által okozott rögzített telepítési problémák.
* A sourceAnchor-beállítás elvesztése a telepítővarázsló második alkalommal történő futtatásakor.
* Rögzített ETW-nyomkövetés a jelszó-szinkronizáláshoz.

## <a name="104701023"></a>1.0.470.1023
Kiadás dátuma: október 2014

**Új funkciók:**

* Több helyszíni Active Directoryról az Azure AD-be való jelszó-szinkronizálás.
* Honosított telepítési KEZELŐFELÜLET az összes Windows Server-nyelvre.

**Verziófrissítés a AADSync 1,0 GA-ból**

Ha már telepítve van Azure AD-szinkronizáló, egy további lépésre van szüksége, ha módosította a beépített szinkronizálási szabályok bármelyikét. A 1.0.470.1023-kiadásra való frissítés után a módosított szinkronizálási szabályok duplikálva vannak. Minden módosított szinkronizálási szabály esetében tegye a következőket:

1. Keresse meg a módosított szinkronizálási szabályt, és jegyezze fel a módosításokat.
1. Törölje a szinkronizálási szabályt.
1. Keresse meg Azure AD-szinkronizáló által létrehozott új szinkronizálási szabályt, majd alkalmazza újra a módosításokat.

**A Active Directory fiók engedélyei**

A Active Directory fióknak további engedélyekkel kell rendelkeznie ahhoz, hogy el tudja olvasni a jelszó-kivonatokat a Active Directoryból. A megadható engedélyek neve "címtárbeli módosítások replikálása" és "a címtár változásainak replikálása". Mindkét engedély szükséges ahhoz, hogy el tudja olvasni a jelszó-kivonatokat.

## <a name="104190911"></a>1.0.419.0911
Kiadás dátuma: szeptember 2014

**Azure AD-szinkronizáló kezdeti kiadása.**

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).