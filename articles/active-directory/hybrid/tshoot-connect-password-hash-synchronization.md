---
title: Jelszó-kivonatolási szinkronizálás hibáinak megoldása Azure AD Connect szinkronizálással | Microsoft Docs
description: Ez a cikk a jelszó-kivonatolási szinkronizálással kapcsolatos problémák elhárításához nyújt információt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247352"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>A jelszókivonat szinkronizálásának hibaelhárítása Azure AD Connect-szinkronizálással

Ez a témakör a jelszó-kivonatok szinkronizálásával kapcsolatos hibák elhárításának lépéseit ismerteti. Ha a jelszavak nem a várt módon vannak szinkronizálva, a felhasználók egy részhalmaza vagy az összes felhasználó számára lehet.

A (z) Azure Active Directory (Azure AD) 1.1.614.0 vagy későbbi verzióval való összekapcsolásához használja a varázsló hibaelhárítási feladatát a jelszó-kivonatolási szinkronizálással kapcsolatos problémák elhárításához:

* Ha olyan problémáról van szó, amely nem szinkronizálja a jelszavakat, tekintse meg a [nem szinkronizált jelszavakat: hibaelhárítás a hibaelhárítási feladat szakasz használatával](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) .

* Ha problémája van az egyes objektumokkal kapcsolatban, akkor az [egyik objektum nem szinkronizálja a jelszavakat: hibaelhárítás a hibaelhárítási feladat szakasz használatával](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) .

A 1.1.524.0 vagy újabb verzióval való üzembe helyezéshez egy diagnosztikai parancsmag használható, amely a jelszó-kivonatolási szinkronizálási problémák elhárítására szolgál:

* Ha olyan problémáról van szó, amely nem szinkronizálja a jelszavakat, tekintse meg a [nem szinkronizált jelszavakat: hibakeresés a diagnosztikai parancsmag használatával](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) című szakaszt.

* Ha az egyes objektumokkal kapcsolatban probléma merül fel, az [egyik objektum nem szinkronizálja a jelszavakat: hibakeresés a diagnosztikai parancsmaggal szakasz használatával](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) .

Azure AD Connect üzemelő példány régebbi verziói esetén:

* Ha olyan problémáról van szó, amely nem szinkronizálja a jelszavakat, tekintse meg a [nem szinkronizált jelszavakat: manuális hibaelhárítási lépések](#no-passwords-are-synchronized-manual-troubleshooting-steps) szakaszt.

* Ha problémája van az egyes objektumokkal kapcsolatban, az [egyik objektum nem szinkronizálja a jelszavakat: manuális hibaelhárítási lépések](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) szakasz.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>A rendszer nem szinkronizálja a jelszavakat: hibaelhárítás a hibaelhárítási feladat használatával

A hibaelhárítási feladat segítségével megtalálhatja, hogy a rendszer miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A hibaelhárítási feladat csak Azure AD Connect vagy újabb verziójú 1.1.614.0 érhető el.

### <a name="run-the-troubleshooting-task"></a>A hibaelhárítási feladat futtatása

A jelszavak szinkronizálása nélküli hibák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .

3. Indítsa el a Azure AD Connect varázslót.

4. Navigáljon a **További feladatok** lapra, válassza a **hibakeresés** lehetőséget, majd kattintson a **tovább** gombra.

5. A hibaelhárítás lapon kattintson a **Launch (indítás** ) elemre a PowerShell hibaelhárítási menüjének elindításához.

6. A főmenüben válassza a **jelszó kivonatának szinkronizálása** lehetőséget.

7. Az almenüben válassza a **jelszó kivonat szinkronizálása egyáltalán nem működik** lehetőséget.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése

A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

* Ellenőrzi, hogy az Azure AD-bérlőhöz engedélyezve van-e a jelszó-kivonat szinkronizálása funkció.

* Ellenőrzi, hogy a Azure AD Connect-kiszolgáló nem átmeneti módban van-e.

* Minden meglévő helyszíni Active Directory összekötőhöz (amely egy meglévő Active Directory erdőnek felel meg):

   * Ellenőrzi, hogy a jelszó-kivonat szinkronizációs funkciója engedélyezve van-e.
   
   * Megkeresi a jelszó-kivonatolás szinkronizálásának szívverési eseményeit a Windows alkalmazás eseménynaplói között.

   * Minden Active Directory tartományhoz a helyszíni Active Directory-összekötő alatt:

      * Ellenőrzi, hogy a tartomány elérhető-e a Azure AD Connect-kiszolgálóról.

      * Ellenőrzi, hogy a helyszíni Active Directory-összekötő által használt Active Directory Domain Services (AD DS) fiókok helyes felhasználónevet, jelszót és engedélyeket igényelnek-e a jelszó kivonatának szinkronizálásához.

Az alábbi ábrán egy egytartományos, helyszíni Active Directory topológia parancsmagjának eredményei láthatók:

![Diagnosztikai kimenet a jelszó-kivonatolási szinkronizáláshoz](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

A szakasz további része a feladat által visszaadott eredményeket és a hozzájuk tartozó problémákat ismerteti.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>nincs engedélyezve a jelszó-kivonat szinkronizálása funkció

Ha nem engedélyezte a jelszó-kivonatoló szinkronizálást a Azure AD Connect varázsló használatával, a következő hibaüzenetet kapja:

![nincs engedélyezve a jelszó-kivonat szinkronizálása](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect kiszolgáló átmeneti módban van

Ha a Azure AD Connect-kiszolgáló átmeneti módban van, a jelszó-kivonatolási szinkronizálás átmenetileg le van tiltva, és a következő hibaüzenetet adja vissza:

![Azure AD Connect kiszolgáló átmeneti módban van](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Nincs jelszó-kivonatoló szinkronizálási szívverési esemény

Minden helyszíni Active Directory-összekötőhöz tartozik egy saját jelszó-kivonat szinkronizációs csatornája. A jelszó-kivonat szinkronizációs csatornájának létrehozásakor és a jelszó módosításainak szinkronizálásakor a rendszer 30 percenként egyszer generál szívverési eseményt (Napszállta 654) a Windows-alkalmazás eseménynaplójában. Az egyes helyszíni Active Directory összekötők esetében a parancsmag az elmúlt három órában megkeresi a megfelelő szívverési eseményeket. Ha nem található szívverési esemény, a következő hibaüzenetet adja vissza:

![Nincs jelszó-kivonatoló szinkronizációs szívverési esemény](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS fiók nem rendelkezik megfelelő engedélyekkel

Ha a helyszíni Active Directory-összekötő által a jelszó-kivonatok szinkronizálásához használt AD DS fiók nem rendelkezik a megfelelő engedélyekkel, a rendszer a következő hibaüzenetet adja vissza:

![A AD DS-fiók helytelen felhasználónevével vagy jelszavával visszaadott hibaüzenetet bemutató képernyőkép.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Helytelen AD DS fiók felhasználóneve vagy jelszava

Ha a helyszíni Active Directory-összekötő által a jelszó-kivonatok szinkronizálásához használt AD DS fiók helytelen felhasználónevet vagy jelszót tartalmaz, a rendszer a következő hibaüzenetet adja vissza:

![Helytelen hitelesítő adat](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Az egyik objektum nem szinkronizálja a jelszavakat: hibaelhárítás a hibaelhárítási feladat használatával

A hibaelhárítási feladat segítségével meghatározhatja, hogy az egyik objektum miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A hibaelhárítási feladat csak Azure AD Connect vagy újabb verziójú 1.1.614.0 érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

Egy adott felhasználói objektum problémáinak elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .

3. Indítsa el a Azure AD Connect varázslót.

4. Navigáljon a **További feladatok** lapra, válassza a **hibakeresés** lehetőséget, majd kattintson a **tovább** gombra.

5. A hibaelhárítás lapon kattintson a **Launch (indítás** ) elemre a PowerShell hibaelhárítási menüjének elindításához.

6. A főmenüben válassza a **jelszó kivonatának szinkronizálása** lehetőséget.

7. Az almenüben válassza a **jelszó nincs szinkronizálva beállítást egy adott felhasználói fiókhoz**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése

A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

* Megvizsgálja a Active Directory objektum állapotát a Active Directory-összekötő területéről, a metaverse és az Azure AD-összekötő területéről.

* Ellenőrzi, hogy vannak-e olyan szinkronizálási szabályok, amelyeken engedélyezve van a jelszó-kivonatoló szinkronizálás, és hogy azok a Active Directory objektumra vonatkoznak.

* Megkísérli beolvasni és megjeleníteni az objektum jelszavának utolsó szinkronizálási kísérletének eredményét.

Az alábbi ábrán a parancsmag eredményei láthatók, amikor a jelszó-kivonatolási szinkronizálást egy adott objektumra vonatkozó hibaelhárítás során hajtja végre:

![Diagnosztikai kimenet a jelszó kivonatának szinkronizálásához – egyetlen objektum](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

A szakasz további része a parancsmag által visszaadott eredményeket és a hozzájuk tartozó problémákat ismerteti.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Az Active Directory objektum nincs exportálva az Azure AD-be

a helyszíni Active Directory fiók jelszavas kivonatának szinkronizálása meghiúsul, mert nincs megfelelő objektum az Azure AD-bérlőben. A rendszer a következő hibaüzenetet adja vissza:

![Hiányzik az Azure AD-objektum](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>A felhasználónak ideiglenes jelszava van

A Azure AD Connect jelenleg nem támogatja az ideiglenes jelszavak Azure AD-vel történő szinkronizálását. A jelszó ideiglenesnek tekintendő, ha a **következő bejelentkezéskor a jelszó módosítása** lehetőség be van állítva a helyszíni Active Directory felhasználóra. A rendszer a következő hibaüzenetet adja vissza:

![Az ideiglenes jelszó nincs exportálva](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>A jelszó Legutóbbi szinkronizálási kísérletének eredményei nem érhetők el

Alapértelmezés szerint a Azure AD Connect hét napig tárolja a jelszó-kivonat szinkronizációs kísérletének eredményét. Ha a kiválasztott Active Directory objektumhoz nem érhetők el eredmények, a rendszer a következő figyelmeztetést adja vissza:

![Egyetlen objektum diagnosztikai kimenete – jelszó-szinkronizálási előzmények nélkül](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>A rendszer nem szinkronizálja a jelszavakat: hibakeresés a diagnosztikai parancsmag használatával

A `Invoke-ADSyncDiagnostics` parancsmag segítségével kiderítheti, hogy a rendszer miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag csak Azure ad Connect vagy újabb verziójú 1.1.524.0 érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

A jelszavak szinkronizálása nélküli hibák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .

3. Futtatja a `Import-Module ADSyncDiagnostics` parancsot.

4. Futtatja a `Invoke-ADSyncDiagnostics -PasswordSync` parancsot.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Az egyik objektum nem szinkronizálja a jelszavakat: hibakeresés a diagnosztikai parancsmag használatával

A `Invoke-ADSyncDiagnostics` parancsmag segítségével meghatározhatja, hogy az egyik objektum miért nem szinkronizálja a jelszavakat.

> [!NOTE]
> A `Invoke-ADSyncDiagnostics` parancsmag csak Azure ad Connect vagy újabb verziójú 1.1.524.0 érhető el.

### <a name="run-the-diagnostics-cmdlet"></a>A diagnosztikai parancsmag futtatása

A felhasználók számára nem szinkronizált jelszavakkal kapcsolatos hibák elhárítása:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a **Futtatás rendszergazdaként** beállítással.

2. Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .

3. Futtassa az `Import-Module ADSyncDiagnostics` parancsot.

4. Futtassa a következő parancsmagot:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Például:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>A rendszer nem szinkronizálja a jelszavakat: manuális hibaelhárítási lépések

A következő lépések végrehajtásával megállapíthatja, hogy a rendszer miért nem szinkronizálja a jelszavakat:

1. A csatlakozási kiszolgáló [átmeneti módban](how-to-connect-sync-staging-server.md)van? Az átmeneti üzemmódú kiszolgálók nem szinkronizálják a jelszavakat.

2. Futtassa a parancsfájlt a [jelszó-szinkronizálási beállítások állapotának beolvasása](#get-the-status-of-password-sync-settings) szakaszban. Áttekintést nyújt a jelszó-szinkronizálási konfigurációról.  

    ![PowerShell-parancsfájl kimenete a jelszó-szinkronizálási beállításokból](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Ha a szolgáltatás nincs engedélyezve az Azure AD-ben, vagy ha a szinkronizálási csatorna állapota nincs engedélyezve, futtassa a telepítővarázsló összekapcsolása varázslót. Válassza a **szinkronizálási beállítások testreszabása lehetőséget**, és törölje a jelszó-szinkronizálás kijelölését. Ez a módosítás átmenetileg letiltja a szolgáltatást. Ezután futtassa újra a varázslót, és engedélyezze újra a jelszó-szinkronizálást. Futtassa újra a parancsfájlt annak ellenőrzéséhez, hogy a konfiguráció megfelelő-e.

4. Az Eseménynaplóban keresse meg a hibákat. Keresse meg a következő eseményeket, amelyek a problémát jelezhetik:
    * Forrás: "címtár-szinkronizálás" azonosító: 0, 611, 652, 655 ha ezeket az eseményeket látja, kapcsolódási probléma van. Az eseménynapló-üzenet olyan erdő-információkat tartalmaz, amelyekben probléma van. További információ: [kapcsolódási probléma](#connectivity problem).

5. Ha nem lát szívverést, vagy ha más nem működött, futtassa [az trigger teljes szinkronizálását az összes jelszóval](#trigger-a-full-sync-of-all-passwords). Csak egyszer futtassa a parancsfájlt.

6. Tekintse meg a jelszavakat nem szinkronizáló egyik objektum hibáit ismertető szakaszt.

### <a name="connectivity-problems"></a>Csatlakozási problémák

Van kapcsolata az Azure AD-vel?

Rendelkezik a fiók a jelszó-kivonatok összes tartományban való olvasásához szükséges engedélyekkel? Ha az expressz beállítások használatával telepítette a kapcsolatot, az engedélyeknek már helyesnek kell lenniük. 

Ha egyéni telepítést használt, manuálisan állítsa be az engedélyeket a következő módon:
    
1. Az Active Directory-összekötő által használt fiók megkereséséhez indítsa el a **synchronization Service Manager**. 
 
2. Válassza az **Összekötők** lehetőséget, majd keressen rá a helyszíni Active Directory erdőre, amelyről hibaelhárítást végez. 
 
3. Válassza ki az összekötőt, majd kattintson a **Tulajdonságok** elemre. 
 
4. Lépjen a **kapcsolódás Active Directory erdőhöz**.  
    
    ![Active Directory-összekötő által használt fiók](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Jegyezze fel a felhasználónevet és a tartományt, ahol a fiók található.
    
5. Indítsa el **Active Directory felhasználókat és számítógépeket**, majd ellenőrizze, hogy a korábban megtalált fiók rendelkezik-e az erdő összes tartományának gyökerében a következő engedélyekkel:
    * Címtárbeli módosítások replikálása
    * A címtár összes módosításának replikálása

6. Elérhetők a tartományvezérlők Azure AD Connect? Ha a csatlakozási kiszolgáló nem tud csatlakozni az összes tartományvezérlőhöz, konfigurálja a **csak az előnyben részesített tartományvezérlőt**.  
    
    ![Active Directory-összekötő által használt tartományvezérlő](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Lépjen vissza a **synchronization Service Managerra** , és **konfigurálja a címtárpartíciót**. 
 
8. Válassza ki a tartományt a címtárpartíciók **kijelölése** területen, jelölje be a **csak az előnyben részesített tartományvezérlők használata** jelölőnégyzetet, majd kattintson a **Konfigurálás** elemre. 

9. A listában adja meg, hogy a csatlakoztatott tartományvezérlők a jelszó-szinkronizálást használják. Ugyanez a lista az importáláshoz és az exportáláshoz is használatos. Hajtsa végre ezeket a lépéseket az összes tartományban.

> [!NOTE]
> A módosítások alkalmazásához indítsa újra a **Microsoft Azure ad Sync** (AdSync) szolgáltatást.

10. Ha a parancsfájl azt mutatja, hogy nincs szívverés, futtassa a parancsfájlt az [összes jelszó teljes szinkronizálásának elindításával](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Az egyik objektum nem szinkronizálja a jelszavakat: manuális hibaelhárítási lépések

Az objektumok állapotának áttekintésével könnyedén elháríthatja a jelszó-kivonatolás szinkronizálásával kapcsolatos problémákat.

1. **Active Directory felhasználók és számítógépek** területen keresse meg a felhasználót, majd ellenőrizze, hogy a **következő bejelentkezéskor a felhasználónak meg kell-e változtatnia a jelszót** jelölőnégyzet jelölését.  

    ![Active Directory produktív jelszavak](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Ha a jelölőnégyzet be van jelölve, kérje meg a felhasználót, hogy jelentkezzen be, és módosítsa a jelszót. Az ideiglenes jelszavak nincsenek szinkronizálva az Azure AD-vel.

2. Ha a jelszó helyesnek tűnik Active Directoryban, kövesse a felhasználót a Szinkronizáló motorban. A helyszíni Active Directoryról az Azure AD-be való beküldést követően láthatja, hogy van-e leíró hiba az objektumon.

    a. Indítsa el a [synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Kattintson az **Összekötők** elemre.

    c. Válassza ki azt a **Active Directory-összekötőt** , ahol a felhasználó található.

    d. Válassza a **Keresés összekötő terület** lehetőséget.

    e. A **hatókör** mezőben válassza a **megkülönböztető név vagy a horgony** lehetőséget, majd adja meg a hibaelhárításhoz szükséges felhasználó teljes megkülönböztető nevét.

    ![Felhasználó keresése az összekötői térben a DN-vel](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Keresse meg a keresett felhasználót, majd kattintson a **Tulajdonságok** elemre az összes attribútum megjelenítéséhez. Ha a felhasználó nem szerepel a keresési eredmények között, ellenőrizze a [szűrési szabályokat](how-to-connect-sync-configure-filtering.md) , és győződjön meg arról, hogy az alkalmazás fut, [és ellenőrizze](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) , hogy a felhasználó a kapcsolódás területen megjelenjen-e.

    : Ha meg szeretné tekinteni az elmúlt hét objektumának jelszó-szinkronizálási részleteit, kattintson a **napló** gombra.  

    ![Objektum naplójának részletei](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Ha az objektum naplója üres, Azure AD Connect nem tudta beolvasni a jelszó-kivonatot a Active Directoryból. A hibaelhárítást a kapcsolódási hibákkal folytathatja. Ha a **sikernél** más értéket lát, tekintse meg a jelszó- [szinkronizálási naplóban](#password-sync-log)található táblázatot.

    h. Válassza ki a **Lineage** fület, és győződjön meg arról, hogy a **PasswordSync** oszlopban legalább egy szinkronizálási szabály **igaz értékű**. Az alapértelmezett konfigurációban a szinkronizálási szabály neve az **ad-User AccountEnabled** található.  

    ![A felhasználóra vonatkozó kifejlődési információ](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. A felhasználói attribútumok listájának megjelenítéséhez kattintson a **metaverse-objektum tulajdonságai** elemre.  

    ![A metaverse-objektum tulajdonságainak felhasználói attribútumainak listáját megjelenítő képernyőkép.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Ellenőrizze, hogy nincs-e **cloudFiltered** attribútum. Győződjön meg arról, hogy a tartományi attribútumok (domainFQDN és domainNetBios) a várt értékekkel rendelkeznek.

    j. Kattintson az **Összekötők** fülre. Győződjön meg arról, hogy az összekötők a helyszíni Active Directory és az Azure AD-vel egyaránt láthatók.

    ![Metaverse-információk](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Válassza ki az Azure AD-t jelölő sort, kattintson a **Tulajdonságok** elemre, majd kattintson a **Lineage** (leválasztás) fülre. Az összekötő terület objektumnak rendelkeznie kell egy kimenő szabállyal a **PasswordSync** oszlopban az **igaz** értékre állítva. Az alapértelmezett konfigurációban a szinkronizálási szabály neve nem **HRE – felhasználó illesztés**.  

    ![Összekötő terület objektumának tulajdonságai párbeszédpanel](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Jelszó-szinkronizálási napló

Az Állapot oszlopban a következő értékek szerepelhetnek:

| Állapot | Leírás |
| --- | --- |
| Siker |A jelszó szinkronizálása sikeresen megtörtént. |
| FilteredByTarget |A jelszó beállítása a **következő bejelentkezéskor a felhasználónak kell megváltoztatnia a jelszót**. A jelszó nincs szinkronizálva. |
| NoTargetConnection |Nincs objektum a metaverse-ban vagy az Azure AD-összekötőben. |
| SourceConnectorNotPresent |Nem található objektum a helyszíni Active Directory-összekötő területén. |
| TargetNotExportedToDirectory |Az Azure AD-összekötő területének objektuma még nem lett exportálva. |
| MigratedCheckDetailsForMoreInfo |A naplóbejegyzés a 1.0.9125.0 létrehozása előtt lett létrehozva, és a korábbi állapotukban jelenik meg. |
| Hiba |A szolgáltatás ismeretlen hibát adott vissza. |
| Ismeretlen |Hiba történt a jelszó-kivonatok kötegének feldolgozására tett kísérlet során.  |
| MissingAttribute |A Azure AD Domain Services által igényelt adott attribútumok (például Kerberos-kivonatok) nem érhetők el. |
| RetryRequestedByTarget |A Azure AD Domain Services által igényelt konkrét attribútumok (például Kerberos-kivonatok) korábban nem voltak elérhetők. Kísérlet történt a felhasználó jelszavas kivonatának újraszinkronizálására. |

## <a name="scripts-to-help-troubleshooting"></a>Hibaelhárítást segítő parancsfájlok

### <a name="get-the-status-of-password-sync-settings"></a>Jelszó-szinkronizálási beállítások állapotának beolvasása

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Az összes jelszó teljes szinkronizálásának elindítása

> [!NOTE]
> Ezt a parancsfájlt csak egyszer futtassa. Ha többször kell futtatnia, valami más a probléma. A probléma elhárításához forduljon a Microsoft ügyfélszolgálatához.

Az összes jelszó teljes szinkronizálását a következő parancsfájl használatával aktiválhatja:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Következő lépések

* [Jelszó-kivonatolási szinkronizálás megvalósítása Azure AD Connect szinkronizálással](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect Sync: szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
