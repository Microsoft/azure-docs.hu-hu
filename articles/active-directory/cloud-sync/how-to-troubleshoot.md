---
title: Azure AD Connect cloud sync hibaelhárítása
description: Ez a cikk a felhőalapú kiépítési ügynökkel kapcsolatos esetleges problémák elhárítását ismerteti.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530317"
---
# <a name="cloud-sync-troubleshooting"></a>Felhőszinkronizálás hibaelhárítása

A felhőalapú szinkronizálás számos különböző területet érint, és számos különböző függőséggel rendelkezik. Ez az átfogó hatókör számos problémát eredményezhet. Ez a cikk segít elhárítani ezeket a problémákat. Bemutatja a területeket, amelyekre általában figyelmet kell fordítania, a további információk beszerzésének módját, és a különböző módszereket, amelyekkel megkeresheti a hibák forrását.


## <a name="common-troubleshooting-areas"></a>Gyakori hibaelhárítási területek

|Név|Leírás|
|-----|-----|
|[Ügynökproblémák](#agent-problems)|Ellenőrizze, hogy az ügynök megfelelően lett-e telepítve, és hogy kommunikál-e Azure Active Directory (Azure AD) használatával.|
|[Objektumszinkronizálási problémák](#object-synchronization-problems)|Az objektumszinkronizálási problémák elhárításához használjon kiépítési naplókat.|
|[Karanténba helyezett problémák kiépítése](#provisioning-quarantined-problems)|A karanténba helyezett problémák és a megoldásuk.|


## <a name="agent-problems"></a>Ügynökkel kapcsolatos problémák
Az ügynökkel elsőként a következő dolgokat szeretné ellenőrizni:

-  Telepítve van?
-  Az ügynök helyileg fut?
-  Az ügynök a portálon van?
-  Az ügynök kifogástalan állapotúként van megjelölve?

Ezek az elemek ellenőrizhetők a Azure Portal az ügynököt futtató helyi kiszolgálón.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése

Az alábbi lépésekkel ellenőrizheti, hogy az ügynök látható-e az Azure-ban, és hogy kifogástalan állapotú-e.

1. Jelentkezzen be az Azure Portalra.
1. A bal oldalon válassza a **Azure Active Directory**  >  **Azure AD Connect** lehetőséget. A középső központban válassza a **Szinkronizálás kezelése lehetőséget.**
1. A **felhőalapú Azure AD Connect képernyőn** válassza az **Összes ügynök áttekintése lehetőséget.**

   ![Az összes ügynök áttekintése](media/how-to-install/install-7.png)</br>
 
1. A **Helyszíni kiépítési ügynökök** képernyőn láthatja a telepített ügynököket. Ellenőrizze, hogy a szóban forgó ügynök ott van-e, és kifogástalan állapotúként *van-e megjelölve.*

   ![Helyszíni kiépítési ügynökök képernyő](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése

Ellenőrizze, hogy az Azure figyel-e a 443-as porton, és hogy az ügynök tud-e kommunikálni vele. 

Ez a teszt ellenőrzi, hogy az ügynökök képesek-e kommunikálni az Azure-ral a 443-as porton keresztül. Nyisson meg egy böngészőt, és nyissa meg az előző URL-címet a kiszolgálóról, ahol az ügynök telepítve van.

![A portok elérhetőságának ellenőrzése](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón

Az ügynök futásának ellenőrzéséhez kövesse az alábbi lépéseket.

1. Nyissa meg a Szolgáltatások szolgáltatásokat a kiszolgálón az ügynök telepítéséhez vagy a **Run**   >    >  **Services.msc futtatásának indításával.**
1. A **Szolgáltatások alatt** győződjön meg arról, Microsoft Azure AD Connect Agent **Updater** és **Microsoft Azure AD Connect Provisioning Agent** van, és az állapotuk *Fut.*

   ![Szolgáltatások képernyő](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Gyakori ügynöktelepítési problémák

A következő szakaszok az ügynökök telepítésével kapcsolatos gyakori problémákat és tipikus megoldásokat ismertetik.

#### <a name="agent-failed-to-start"></a>Az ügynök nem tudott elindulni

Előfordulhat, hogy a következő hibaüzenet jelenik meg:

**A "Microsoft Azure AD Connect provisioning Agent" szolgáltatás nem tudott elindulni. Ellenőrizze, hogy rendelkezik-e a rendszerszolgáltatások indításhoz szükséges jogosultságokkal.** 

Ezt a problémát általában egy olyan csoportházirend okozza, amely megakadályozta, hogy engedélyeket alkalmazzanak a telepítő által létrehozott helyi NT-szolgáltatás bejelentkezési fiókjára (NT SERVICE\AADConnectProvisioningAgent). Ezek az engedélyek szükségesek a szolgáltatás elindításához.

A probléma megoldásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal.
1. Nyissa **meg** a szolgáltatásokat: navigálhat hozzá, vagy elindíthatja **a**  >  **Run**  >  **Services.msc futtatását.**
1. A **Szolgáltatások alatt** kattintson duplán a Microsoft Azure AD Connect **Provisioning Agent (Kiépítési ügynök csatlakoztatása) elemre.**
1. A **Bejelentkezés lapon** módosítsa **ezt** a fiókot egy tartományi rendszergazdai fiókra. Ezután indítsa újra a szolgáltatást. 

   ![Bejelentkezés lap](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Az ügynök időkorrekta vagy a tanúsítvány érvénytelen

Az ügynök regisztrálása során a következő hibaüzenet jelenhet meg.

![Időkorrekta hibaüzenet](media/how-to-troubleshoot/troubleshoot-4.png)

Ezt a problémát általában az okozza, hogy az ügynök nem tud csatlakozni a hibrid identitásszolgáltatáshoz, és konfigurálnia kell egy HTTP-proxyt. A probléma megoldásához konfiguráljon egy kimenő proxyt. 

A kiépítési ügynök támogatja a kimenő proxy használatát. Konfigurálhatja a *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config.* Adja hozzá a következő sorokat a fájl vége felé, a záró címke `</configuration>` előtt.
Cserélje le a és `[proxy-server]` `[proxy-port]` változókat a proxykiszolgáló nevére és portértékére.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Az ügynök regisztrációja biztonsági hibával meghiúsul

Előfordulhat, hogy hibaüzenet jelenik meg a felhőalapú kiépítési ügynök telepítésekor.

Ezt a problémát általában az okozza, hogy az ügynök nem tudja végrehajtani a PowerShell regisztrációs szkripteket a helyi PowerShell végrehajtási szabályzatok miatt.

A probléma megoldásához módosítsa a PowerShell végrehajtási házirendeket a kiszolgálón. A számítógép- és felhasználói házirendeket *Nem* meghatározott vagy *RemoteSigned* beállításúra kell beállítani. Ha ezek *korlátlanként vannak beállítva,* ez a hiba jelenik meg. További információ: [PowerShell végrehajtási szabályzatok.](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 

### <a name="log-files"></a>Naplófájlok

Alapértelmezés szerint az ügynök minimális hibaüzeneteket és hívásláncadatokat bocsát ki. Ezek a nyomkövetési naplók a **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace** mappában találhatók.

Az ügynökkel kapcsolatos problémák elhárításával kapcsolatos további részletekért kövesse az alábbi lépéseket.

1.  Telepítse az AADCloudSyncTools PowerShell-modult az itt [leírtak szerint.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Az információk `Export-AADCloudSyncToolsLogs` rögzítéséhez használja a PowerShell-parancsmagot.  Az alábbi kapcsolók segítségével finomhangolhatja az adatgyűjtést.
      - SkipVerboseTrace : csak az aktuális naplókat exportálja részletes naplók rögzítése nélkül (alapértelmezett = hamis)
      - TracingDurationMins eltérő rögzítési időtartam megadásához (alapértelmezett = 3 perc)
      - OutputPath eltérő kimeneti elérési út megadásához (alapértelmezett = Felhasználói dokumentumok)


## <a name="object-synchronization-problems"></a>Objektumszinkronizálási problémák

A következő szakasz az objektumszinkronizálás hibaelhárításával kapcsolatos információkat tartalmaz.

### <a name="provisioning-logs"></a>Üzembehelyezési naplók

A Azure Portal a kiépítési naplók segítségével nyomon követhetők és elháríthatóak az objektumszinkronizálási problémák. A naplók megtekintéséhez válassza a Naplók **lehetőséget.**

![Naplók gomb](media/how-to-troubleshoot/log-1.png)

A kiépítési naplók rengeteg információt nyújtanak a saját környezete és az Azure helyi Active Directory szinkronizált objektumok állapotáról.

![Kiépítési naplók képernyő](media/how-to-troubleshoot/log-2.png)

Az oldal tetején található legördülő mezők használatával a nézetet adott problémák, például dátumok alapján nullára szűrheti. További információkért kattintson duplán egy adott eseményre.

![A kiépítési naplók legördülő listában található információk](media/how-to-troubleshoot/log-3.png)

Ezek az információk részletes lépéseket tartalmaznak, valamint a szinkronizálási probléma előfordulási helyeit. Így pontosan kitűzheti a probléma pontos helyét.


## <a name="provisioning-quarantined-problems"></a>Karanténba helyezett problémák kiépítése

A felhőalapú szinkronizálás figyeli a konfiguráció állapotát, és karanténba helyezi a nem megfelelő objektumokat. Ha a célrendszerre vonatkozó hívások nagy része vagy egésze egy hiba miatt (például érvénytelen rendszergazdai hitelesítő adatok miatt) folyamatosan meghiúsul, a szinkronizálási feladat karanténban lesz megjelölve.

![Karantén állapota](media/how-to-troubleshoot/quarantine-1.png)

Az állapot kiválasztásával további információkat láthat a karanténról. A hibakódot és az üzenetet is beszerezheti.

![A karanténra vonatkozó további információkat bemutató képernyőkép.](media/how-to-troubleshoot/quarantine-2.png)

Ha a jobb gombbal az állapotra kattint, további lehetőségek gombra is kattint:
    
   - kiépítési naplók megtekintése
   - ügynök megtekintése
   - karantén törlődik

![A helyi menü beállításait bemutató képernyőkép.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Karantén feloldása
A karantén feloldásának két különböző módja van.  Ezek a következők:

  - karantén jelölése – törli a vízjelet, és változásszinkronizálást futtat
  - a kiépítési feladat újraindítása – törli a vízjelet, és futtat egy kezdeti szinkronizálást

#### <a name="clear-quarantine"></a>Karantén törlődik
A vízjel törléshez és a kiépítési feladat változásszinkronizálásának futtatásához, miután ellenőrizte, egyszerűen kattintson a jobb gombbal az állapotra, és válassza a **karantén jelölését.**

Egy értesítésnek kell látnia, hogy a karantén törlése meg fog zárulni.

![Képernyőkép a karantén törlésével foglalkozó értesítésről.](media/how-to-troubleshoot/quarantine-5.png)

Ezután kifogástalan állapotúnak kell lennie az ügynöknél.

![Karantén állapotinformációi](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Az üzembe állási feladat újraindítása
A Azure Portal indítsa újra a kiépítési feladatot. Az ügynök konfigurációs oldalán válassza a **Kiépítés újraindítása lehetőséget.**

  ![Kiépítés újraindítása](media/how-to-troubleshoot/quarantine-3.png)

- A Microsoft Graph indítsa [újra a kiépítési feladatot.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) Az újraindítást teljes mértékben Ön szabályozhatja. Dönthet úgy, hogy törli a következőt:
  - A escrows parancsokkal újraindíthatja a karanténállapot felé felhalmozott escrow számlálót.
  - Karanténba gombra az alkalmazás karanténból való eltávolításához.
  - Vízjelek. 
  
  Használja az alábbi kérelmet:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>A Cloud Sync szolgáltatásfiók javítása
Ha javítania kell a felhőszinkronizálási szolgáltatás fiókját, használhatja a következőt: `Repair-AADCloudSyncToolsAccount` .  


   1.  Az itt ismertetett telepítési [lépésekkel](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) elkezdheti a munkát, majd folytathatja a további lépésekkel.
   2.  Egy rendszergazdai Windows PowerShell rendelkező munkamenetből írja vagy másolja be a következőt: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Adja meg az Azure AD globális rendszergazdai hitelesítő adatait
   4. Írja vagy másolja be a következőt: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Ha ezzel elkészült, a fiók javításának sikeresnek kell lennie.

## <a name="next-steps"></a>Következő lépések 

- [Ismert korlátozások](how-to-prerequisites.md#known-limitations)
- [Hibakódok](reference-error-codes.md)
