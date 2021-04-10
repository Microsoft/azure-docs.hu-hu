---
title: Szelektív jelszó kivonatának szinkronizálása Azure AD Connect
description: Ez a cikk azt ismerteti, hogyan lehet beállítani és konfigurálni a szelektív jelszó-kivonatok szinkronizálását, hogy az a Azure AD Connect használatával használható legyen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 248d5e163eb046edd130d69307a1c553d434b92d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604668"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Szelektív jelszó kivonatának szinkronizálási konfigurációja Azure AD Connect

A [jelszó-kivonatolás szinkronizálása](whatis-phs.md) a hibrid identitások megvalósításához használt bejelentkezési módszerek egyike. Azure AD Connect szinkronizálja egy felhasználó jelszavának kivonatát egy helyszíni Active Directory példányból egy felhőalapú Azure AD-példányba.  Alapértelmezés szerint a beállítása után a jelszó-kivonat szinkronizálása a szinkronizált összes felhasználónál megtörténik.

Ha azt szeretné, hogy a felhasználók egy részhalmaza ne szinkronizálja a jelszavukat az Azure AD-ba, beállíthatja a szelektív jelszó-kivonatok szinkronizálását a jelen cikkben ismertetett útmutatás alapján.

>[!Important]
> A Microsoft nem támogatja az olyan konfigurációkon vagy műveleteken kívüli Azure AD Connect-szinkronizálások módosítását vagy működtetését, amelyek hivatalosan dokumentálva vannak. Ezen konfigurációk vagy műveletek bármelyike inkonzisztens vagy nem támogatott állapotba Azure AD Connect szinkronizálást eredményezhet. Ennek eredményeképpen a Microsoft nem tudja garantálni, hogy hatékony technikai támogatást biztosítson az ilyen üzembe helyezésekhez. 


## <a name="consider-your-implementation"></a>Vegye fontolóra a megvalósítást  
A konfiguráció felügyeleti tevékenységének csökkentése érdekében először vegye figyelembe, hogy hány felhasználói objektumot szeretne kizárni a jelszó-kivonatolási szinkronizálásból. Győződjön meg arról, hogy az alábbi forgatókönyvek közül melyek kölcsönösen kizárják egymást, és igazodjon az igényeinek megfelelő konfigurációs beállítás kiválasztásához.
- Ha a **kizárni** kívánt felhasználók száma **kisebb** , mint a **felvenni** kívánt felhasználók száma, kövesse az ebben a [szakaszban](#excluded-users-is-smaller-than-included-users)ismertetett lépéseket.
- Ha a **kizárni** kívánt felhasználók száma **nagyobb** , mint a **felvenni** kívánt felhasználók száma, kövesse az ebben a [szakaszban](#excluded-users-is-larger-than-included-users)ismertetett lépéseket.

> [!Important]
> A kiválasztott konfigurációs beállítással a módosítások alkalmazásához szükséges kezdeti szinkronizálás (teljes szinkronizálás) automatikusan elvégzi a következő szinkronizálási ciklust.

> [!Important]
> A szelektív jelszavas kivonatok szinkronizálásának konfigurálása közvetlenül befolyásolja a jelszó visszaírási. A jelszó-változtatások vagy a jelszó-ÁTÁLLÍTÁSOK, amelyeket a rendszer kezdeményez Azure Active Directory a helyszíni Active Directoryre való visszaíráskor, ha a felhasználó a jelszó-kivonatolási szinkronizálás hatókörében van. 

### <a name="the-admindescription-attribute"></a>A adminDescription attribútum
Mindkét forgatókönyv arra támaszkodik, hogy a felhasználók adminDescription-attribútumát egy adott értékre állítja be.  Ez lehetővé teszi, hogy a szabályok alkalmazhatók legyenek, és mi teszi a szelektív PHS munkát.

|Eset|adminDescription-érték|
|-----|-----|
|A kizárt felhasználók kisebbek, mint a belefoglalt felhasználók|PHSFiltered|
|A kizárt felhasználók nagyobb méretűek, mint a tartalmazott felhasználók|PHSIncluded|

Ezt az attribútumot a következőkre lehet beállítani:

- a Active Directory felhasználók és számítógépek felhasználói felületének használata
- `Set-ADUser`PowerShell-parancsmag használata.  További információ: [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>A szinkronizálási ütemező letiltása:
A forgatókönyvek elindítása előtt le kell tiltania a szinkronizálási ütemezést a szinkronizálási szabályok módosításakor.
 1. Indítsa el a Windows PowerShellt.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  A következő parancsmag futtatásával erősítse meg, hogy az ütemező le van tiltva:
     
    ```Get-ADSyncScheduler```

További információ az ütemező szolgáltatásról: [Azure ad Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>A kizárt felhasználók kisebbek, mint a belefoglalt felhasználók
A következő szakasz azt ismerteti, hogyan engedélyezhető a szelektív jelszó-kivonatok szinkronizálása, ha a **kizárni** kívánt felhasználók száma **kisebb** , mint a **felvenni** kívánt felhasználók száma.

>[!Important]
> Mielőtt továbblépne, győződjön meg arról, hogy a szinkronizálási ütemező le van tiltva a fent ismertetett módon.

- Hozzon létre egy szerkeszthető másolatot a **alkalmazásból az ad-User AccountEnabled** , és **engedélyezze a jelszó-kivonat szinkronizálásának mellőzését** , és adja meg annak hatókör-szűrőjét. 
- Hozzon létre egy másik szerkeszthető másolatot az alapértelmezett értékről az **ad-User AccountEnabled** , és válassza a **jelszó-kivonat szinkronizálásának engedélyezése** lehetőséget, és adja meg annak hatókör-szűrőjét. 
- A szinkronizálási ütemező újbóli engedélyezése 
- Állítsa be az Active Directoryban az attribútum értékét, amely hatókör attribútumként van definiálva azon felhasználóknál, akik számára engedélyezni kívánja a jelszó-kivonat szinkronizálását. 

>[!Important]
>A szelektív jelszó-kivonatok szinkronizálásának konfigurálásához megadott lépések csak olyan felhasználói objektumokat érintenek, amelyekben a **adminDescription** attribútum Active Directory értékkel van feltöltve **PHSFiltered**.
Ha ez az attribútum nincs feltöltve, vagy az értéke nem **PHSFiltered** , akkor a rendszer nem alkalmazza ezeket a szabályokat a felhasználói objektumokra.


### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurálja a szükséges szinkronizálási szabályokat:

 1. Indítsa el a szinkronizálási szabályok szerkesztőjét, és állítsa a szűrők **jelszavas szinkronizálás** a következőre és **a** **szabály típusra** **standard** értéket.
     ![Szinkronizálási szabályok szerkesztő indítása](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Válassza ki az **ad – felhasználói AccountEnabled** tartozó szabályt a szelektív jelszót konfigurálni kívánt Active Directory erdő összekötőhöz, és kattintson a **Szerkesztés** gombra. A következő párbeszédpanelen válassza az **Igen** lehetőséget az eredeti szabály szerkeszthető példányának létrehozásához.
     ![Szabály kiválasztása](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. Az első szabály letiltja a jelszó-kivonatok szinkronizálását. Adja meg a következő nevet az új egyéni szabály számára: **az ad-User AccountEnabled – felhasználók szűrése a PHS**.
 Módosítsa a sorrend értékét a 100-nál alacsonyabb értékre (például **90** vagy attól függően, hogy melyik a legalacsonyabb érték a környezetben).
 Győződjön meg róla, hogy a **Jelszó-szinkronizálás engedélyezése** és a **Letiltva** jelölőnégyzet be van jelölve, és c.
 Kattintson a **Tovább** gombra.
  ![Bejövőek szerkesztése](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. A **hatókör szűrőben** kattintson a **Hozzáadás záradék** elemre.
 Válassza a **adminDescription** elemet az attribútum oszlopban, amely **egyenlő** a kezelő oszlopban, és adja meg a **PHSFiltered** értéket.
     ![Hatókör-szűrő](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Nincs szükség további módosításokra. Az **összekapcsolási szabályokat** és **átalakításokat** az alapértelmezett másolási beállításokkal kell hagyni, hogy a **Mentés** most lehetőségre kattintson.
 Az összekötő következő szinkronizálási ciklusában a teljes szinkronizálást jelző figyelmeztető párbeszédablakban kattintson az **OK gombra** .
     ![Szabály mentése](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Ezután hozzon létre egy másik egyéni szabályt, amelynél engedélyezve van a jelszó-kivonatoló szinkronizálás. Válassza újra az alapértelmezett szabályt a **from ad – felhasználói AccountEnabled** azon Active Directory erdőben, amelyhez be szeretné állítani a szelektív jelszó szinkronizálását, és kattintson a **Szerkesztés** gombra. A következő párbeszédpanelen válassza az **Igen** lehetőséget az eredeti szabály szerkeszthető példányának létrehozásához.
     ![Egyéni szabály](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Adja meg a következő nevet az új egyéni szabály számára: **az ad-User AccountEnabled-ben a PHS-hoz tartozó felhasználók**.
 Módosítsa a sorrend értékét a korábban létrehozott szabálynál alacsonyabb értékre (ebben a példában a **89** lesz).
 Győződjön meg arról, hogy a **Jelszó-szinkronizálás engedélyezése** jelölőnégyzet be van jelölve, és a **letiltott** jelölőnégyzet nincs bejelölve.
 Kattintson a **Tovább** gombra.  
     ![Új szabály szerkesztése](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. A **hatókör szűrőben** kattintson a **Hozzáadás záradék** elemre.
 Válassza a **adminDescription** lehetőséget az attribútum oszlopban, a **NOTEQUAL** az operátor oszlopban, és adja meg a **PHSFiltered** értéket.
     ![Hatóköri szabály](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Nincs szükség további módosításokra. Az **összekapcsolási szabályokat** és **átalakításokat** az alapértelmezett másolási beállításokkal kell hagyni, hogy a **Mentés** most lehetőségre kattintson.
 Az összekötő következő szinkronizálási ciklusában a teljes szinkronizálást jelző figyelmeztető párbeszédablakban kattintson az **OK gombra** .
     ![Csatlakozási szabályok](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Erősítse meg a szabályok létrehozását. Távolítsa el a szűrők **jelszavas szinkronizálást** **és a** **szabály típusa** **standard** értéket. Ekkor az imént létrehozott új szabályokat is látnia kell.
     ![Szabályok megerősítése](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>A szinkronizálási ütemező újbóli engedélyezése:  
Miután végrehajtotta a szükséges szinkronizálási szabályok konfigurálásának lépéseit, engedélyezze újra a szinkronizálási Feladatütemezőt a következő lépésekkel:
 1. A Windows PowerShellben futtassa a következőket:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Ezt követően ellenőrizze, hogy sikeresen engedélyezte-e a futtatását:

     ```Get-ADSyncScheduler```

További információ az ütemező szolgáltatásról: [Azure ad Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Felhasználói **adminDescription** -attribútum szerkesztése:
Ha az összes konfiguráció elkészült, akkor az összes olyan felhasználóhoz szerkesztenie kell a **adminDescription** -attribútumot, amelyet szeretne **kizárni** a jelszó-kivonatolási szinkronizálásból a Active Directory, és hozzá kell adnia a hatókör-szűrőben használt karakterláncot: **PHSFiltered**.
   
  ![Attribútum szerkesztése](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

A felhasználó **adminDescription** -attribútumának szerkesztéséhez a következő PowerShell-parancsot is használhatja:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>A kizárt felhasználók nagyobb méretűek, mint a tartalmazott felhasználók
A következő szakasz azt ismerteti, hogyan engedélyezhető a szelektív jelszó-kivonatok szinkronizálása, ha a **kizárni** kívánt felhasználók száma **nagyobb** , mint a **felvenni** kívánt felhasználók száma.

>[!Important]
> Mielőtt továbblépne, győződjön meg arról, hogy a szinkronizálási ütemező le van tiltva a fent ismertetett módon.

Az alábbiakban az alábbi lépésekben végrehajtandó műveletek összegzése látható:

- Hozzon létre egy szerkeszthető másolatot a **alkalmazásból az ad-User AccountEnabled** , és **engedélyezze a jelszó-kivonat szinkronizálásának mellőzését** , és adja meg annak hatókör-szűrőjét. 
- Hozzon létre egy másik szerkeszthető másolatot az alapértelmezett értékről az **ad-User AccountEnabled** , és válassza a **jelszó-kivonat szinkronizálásának engedélyezése** lehetőséget, és adja meg annak hatókör-szűrőjét. 
- A szinkronizálási ütemező újbóli engedélyezése 
- Állítsa be az Active Directoryban az attribútum értékét, amely hatókör attribútumként van definiálva azon felhasználóknál, akik számára engedélyezni kívánja a jelszó-kivonat szinkronizálását. 

>[!Important]
>A szelektív jelszó-kivonatok szinkronizálásának konfigurálásához megadott lépések csak olyan felhasználói objektumokat érintenek, amelyekben a **adminDescription** attribútum Active Directory értékkel van feltöltve **PHSIncluded**.
Ha ez az attribútum nincs feltöltve, vagy az értéke nem **PHSIncluded** , akkor a rendszer nem alkalmazza ezeket a szabályokat a felhasználói objektumokra.


### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurálja a szükséges szinkronizálási szabályokat:

 1. Indítsa el a szinkronizálási szabályok szerkesztőjét, és állítsa be a **Jelszó-szinkronizálás** **szűrése és a** **szabály típusa** **standard** értéket.
     ![Szabály típusa](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Válassza ki az **Active Directory – felhasználói AccountEnabled** tartozó szabályt azon Active Directory erdőben, amelyhez a szelektív jelszót konfigurálni szeretné, majd kattintson a **Szerkesztés** gombra. A következő párbeszédpanelen válassza az **Igen** lehetőséget az eredeti szabály szerkeszthető példányának létrehozásához.
     ![Az AD-ből](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. Az első szabály letiltja a jelszó-kivonatok szinkronizálását. Adja meg a következő nevet az új egyéni szabály számára: **az ad-User AccountEnabled – felhasználók szűrése a PHS**.
 Módosítsa a sorrend értékét a 100-nál alacsonyabb értékre (például **90** vagy attól függően, hogy melyik a legalacsonyabb érték a környezetben).
 Győződjön meg róla, hogy a **Jelszó-szinkronizálás engedélyezése** és a **Letiltva** jelölőnégyzet be van jelölve.
 Kattintson a **Tovább** gombra.
  ![Prioritás beállítása](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. A **hatókör szűrőben** kattintson a **Hozzáadás záradék** elemre.
Válassza a **adminDescription** lehetőséget az attribútum oszlopban, a **NOTEQUAL** az operátor oszlopban, és adja meg a **PHSIncluded** értéket.
     ![Záradék hozzáadása](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Nincs szükség további módosításokra. Az **összekapcsolási szabályokat** és **átalakításokat** az alapértelmezett másolási beállításokkal kell hagyni, hogy a **Mentés** most lehetőségre kattintson.
 Az összekötő következő szinkronizálási ciklusában a teljes szinkronizálást jelző figyelmeztető párbeszédablakban kattintson az **OK gombra** .
     ![Átalakítás](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Ezután hozzon létre egy másik egyéni szabályt, amelynél engedélyezve van a jelszó-kivonatoló szinkronizálás. Válassza újra az alapértelmezett szabályt a **from ad – felhasználói AccountEnabled** azon Active Directory erdőben, amelyhez be szeretné állítani a szelektív jelszó szinkronizálását, és kattintson a **Szerkesztés** gombra. A következő párbeszédpanelen válassza az **Igen** lehetőséget az eredeti szabály szerkeszthető példányának létrehozásához.
     ![Felhasználói AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Adja meg a következő nevet az új egyéni szabály számára: **az ad-User AccountEnabled-ben a PHS-hoz tartozó felhasználók**.
 Módosítsa a sorrend értékét a korábban létrehozott szabálynál alacsonyabb értékre (ebben a példában a **89** lesz).
 Győződjön meg arról, hogy a **Jelszó-szinkronizálás engedélyezése** jelölőnégyzet be van jelölve, és a **letiltott** jelölőnégyzet nincs bejelölve.
 Kattintson a **Tovább** gombra.
     ![Jelszó-szinkronizálás engedélyezése](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. A **hatókör szűrőben** kattintson a **Hozzáadás záradék** elemre.
 Válassza a **adminDescription** elemet az attribútum oszlopban, amely **egyenlő** a kezelő oszlopban, és adja meg a **PHSIncluded** értéket.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Nincs szükség további módosításokra. Az **összekapcsolási szabályokat** és **átalakításokat** az alapértelmezett másolási beállításokkal kell hagyni, hogy a **Mentés** most lehetőségre kattintson.
 Az összekötő következő szinkronizálási ciklusában a teljes szinkronizálást jelző figyelmeztető párbeszédablakban kattintson az **OK gombra** .
     ![Mentés most](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Erősítse meg a szabályok létrehozását. Távolítsa el a szűrők **jelszavas szinkronizálást** **és a** **szabály típusa** **standard** értéket. Ekkor az imént létrehozott új szabályokat is látnia kell.
     ![Szinkronizálás bekapcsolva](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>A szinkronizálási ütemező újbóli engedélyezése:  
Miután végrehajtotta a szükséges szinkronizálási szabályok konfigurálásának lépéseit, engedélyezze újra a szinkronizálási Feladatütemezőt a következő lépésekkel:
 1. A Windows PowerShellben futtassa a következőket:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Ezt követően ellenőrizze, hogy sikeresen engedélyezte-e a futtatását:

     ```Get-ADSyncScheduler```

További információ az ütemező szolgáltatásról: [Azure ad Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Felhasználói **adminDescription** -attribútum szerkesztése:
Az összes konfiguráció befejezését követően szerkesztenie kell a **adminDescription** attribútumot az összes olyan felhasználó számára, amelyet a jelszó-kivonatolási szinkronizáláshoz szeretne **szerepeltetni** Active Directoryban, és adja hozzá a hatóköri szűrőben használt karakterláncot: **PHSIncluded**.

  ![Attribútumok szerkesztése](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 A felhasználó **adminDescription** -attribútumának szerkesztéséhez a következő PowerShell-parancsot is használhatja:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Következő lépések
- [Mi az a jelszókivonat-szinkronizálás?](whatis-phs.md)
- [A jelszó-kivonatolási szinkronizálás működése](how-to-connect-password-hash-synchronization.md)
