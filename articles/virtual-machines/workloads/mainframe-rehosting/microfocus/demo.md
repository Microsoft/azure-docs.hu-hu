---
title: A Micro Focus CICS BankDemo beállítása a Micro Focus Enterprise Developer 4,0 Azure-beli Virtual Machines
description: Futtassa a Micro Focus BankDemo alkalmazást az Azure Virtual Machineson (VM), és ismerkedjen meg a Micro Focus Enterprise Server és a Enterprise Developer használatával.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 9ef9dd27183b6c23762dcdc2d5a844123d35559a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045960"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>A Micro Focus CICS BankDemo beállítása a Micro Focus Enterprise Developer 4,0 Azure-beli fejlesztéséhez

Ha a Micro Focus Enterprise Server 4,0 és a Enterprise Developer 4,0 szolgáltatást az Azure-ban állítja be, akkor tesztelheti az IBM z/OS számítási feladatok üzembe helyezéseit. Ez a cikk bemutatja, hogyan állíthat be CICS-BankDemo, amely a vállalati fejlesztőhöz tartozik.

A CICs az ügyfél-ellenőrzési rendszerre, az online nagyvállalati alkalmazások számos alkalmazásában használt tranzakciós platformra áll. A BankDemo alkalmazás kiválóan használható a vállalati kiszolgáló és a nagyvállalati fejlesztők működésének megismeréséhez, valamint a tényleges alkalmazások felügyeletéhez és üzembe helyezéséhez a zöld képernyős terminálokkal.

> [!NOTE]
> Hamarosan elérhető: útmutató a [Micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) Azure-beli virtuális gépeken való beállításához.

## <a name="prerequisites"></a>Előfeltételek

- [Nagyvállalati fejlesztővel](set-up-micro-focus-azure.md)rendelkező virtuális gép. Ne feledje, hogy a nagyvállalati fejlesztő a vállalati kiszolgáló teljes példányát használja fejlesztési és tesztelési célokra. Ez a példány a bemutatóhoz használt Enterprise Server példánya.

- [SQL Server 2017 Express kiadás](https://www.microsoft.com/sql-server/sql-server-editions-express). Töltse le és telepítse a vállalati fejlesztői virtuális gépre. A vállalati kiszolgálónak a CICS-régiók felügyeletéhez adatbázisra van szüksége, és a BankDemo alkalmazás egy BANKDEMO nevű SQL Server adatbázist is használ. Ez a bemutató feltételezi, hogy mindkét adatbázishoz SQL Server Express használ. A telepítésekor válassza ki az alapszintű telepítést.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). A SSMS az adatbázisok felügyeletére és a T-SQL-szkriptek futtatására szolgál. Töltse le és telepítse a vállalati fejlesztői virtuális gépre.

- [Visual studio 2019](https://azure.microsoft.com/downloads/) a legújabb szervizcsomaggal vagy a [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)szolgáltatással, amely ingyenesen letölthető.

- Rumba Desktop vagy egy másik 3270 emulátor.

## <a name="configure-the-windows-environment"></a>A Windows-környezet konfigurálása

Miután telepítette a Enterprise Developer 4,0-et a virtuális gépre, konfigurálnia kell a vállalati kiszolgáló azon példányát, amely a szolgáltatáshoz tartozik. Ehhez telepítenie kell néhány további Windows-szolgáltatást az alábbiak szerint.

1. Az RDP használatával jelentkezzen be a létrehozott Enterprise Server 4,0 virtuális gépre.

2. Kattintson a **Start** gomb melletti keresés ikonra, és írja be a **Windows-szolgáltatások** **kifejezést** . Megnyílik a Kiszolgálókezelő szerepkörök és szolgáltatások hozzáadása varázslója.

3. Válassza ki a **webkiszolgáló (IIS) szerepkört**, majd jelölje be az alábbi beállításokat:

    - Webkezelési eszközök
    - AZ IIS 6 felügyeletének kompatibilitása (az összes elérhető funkció kijelölése)
    - IIS kezelése konzol
    - IIS-kezelő parancsfájlok és eszközök
    - IIS-kezelő szolgáltatás

4. Válassza ki **World Wide Web szolgáltatások** elemet, és jelölje be az alábbi beállításokat:

     Alkalmazás-fejlesztési funkciók:
    - .NET-bővíthetőség
    - ASP.NET
    - Gyakori HTTP-funkciók: az összes elérhető funkció hozzáadása
    - Állapot és diagnosztika: az összes elérhető funkció hozzáadása
    - Biztonság:
        - Alapszintű hitelesítés
        - Windows-hitelesítés

5. Válassza ki a **Windows folyamataktivációs szolgáltatást** és annak minden gyermekét.

6. A **szolgáltatások** esetében a **Microsoft .NET-keretrendszer 3.5.1**-es és a következő lehetőségeket kell ellenőriznie:

    - HTTP-aktiválás Windows Communication Foundation
    - Nem HTTP-aktiválás Windows Communication Foundation

7. A **szolgáltatások** esetében a **Microsoft .NET Framework 4,6**-es és a következő lehetőségeket kell ellenőriznie:

   - Nevesített cső aktiválása
   - TCP-aktiválás
   - TCP-port megosztása

     ![Szerepkörök és szolgáltatások hozzáadása varázsló: szerepkör-szolgáltatások](media/01-demo-roles.png)

8. Miután kiválasztotta az összes beállítást, kattintson a **tovább** gombra a telepítéshez.

9. A Windows-funkciók után lépjen a **Vezérlőpult \> rendszer és biztonság \> felügyeleti eszközök** pontjára, és válassza a **szolgáltatások** lehetőséget. Görgessen lefelé, és ellenőrizze, hogy a következő szolgáltatások futnak-e, és állítsa be az **automatikus** lehetőséget:

    - **NetTcpPortSharing**
    - **Net. pipe figyelő adapter**
    - **Net. TCP figyelő adapter**

10. Az IIS konfigurálásához és támogatásához a menüben keresse meg a **Micro Focus Enterprise Developer Command Prompt (64 bit)** és a Futtatás **rendszergazdaként** lehetőséget.

11. Írja be a **wassetup – i** értéket, és nyomja le az **ENTER** billentyűt

12. A szkript futtatása után lezárhatja az ablakot.

## <a name="configure-the-local-system-account-for-sql-server"></a>A SQL Server helyi rendszerfiókjának konfigurálása

Egyes vállalati kiszolgálói folyamatoknak be kell jelentkezniük SQL Server és adatbázisokat és egyéb objektumokat kell létrehozniuk. Ezek a folyamatok a helyi rendszerfiókot használják, ezért meg kell adnia a rendszergazdai jogosultságokat a fióknak.

1. Indítsa el a **SSMS** , és kattintson a **Kapcsolódás** LEHETŐSÉGre a helyi SQLExpress-kiszolgálóhoz való kapcsolódáshoz Windows-hitelesítés használatával. A **kiszolgáló neve** listában elérhetőnek kell lennie.

2. A bal oldalon bontsa ki a **biztonsági** mappát, és válassza a **bejelentkezések** lehetőséget.

3. Válassza az **NT Authority \\ rendszer** lehetőséget, majd válassza a **Tulajdonságok** lehetőséget.

4. Válassza ki a **kiszolgálói szerepköröket** , és jelölje be a **sysadmin** elemet

     ![SSMS Object Explorer ablak: bejelentkezési tulajdonságok](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>A BankDemo-adatbázis és az összes objektum létrehozása

1. Nyissa meg a **Windows Intézőt** , és navigáljon a **C: \\ felhasználók \\ nyilvános \\ dokumentumok \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo \\ SQL**.

2. Másolja a **BankDemoCreateAll. SQL** fájl tartalmát a vágólapra.

3. Nyissa meg a **SSMS**. A jobb oldalon kattintson a **kiszolgáló** elemre, és válassza az **Új lekérdezés** elemet.

4. Illessze be a vágólap tartalmát az **Új lekérdezés** mezőbe.

5. Futtassa az SQL-t úgy, hogy a lekérdezés fölötti **parancs** lapon a **végrehajtás** gombra kattint.

A lekérdezésnek hibák nélkül kell futnia. Ha elkészült, a BankDemo alkalmazáshoz tartozó mintaadatbázis szerepel.

![SQLQuery1. SQL kimenet](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Ellenőrizze, hogy létrejöttek-e az adatbázis táblái és objektumai

1. Kattintson a jobb gombbal a **BANKDEMO** -adatbázisra, és válassza a **frissítés** lehetőséget.

2. Bontsa ki az **adatbázist** , és válassza a **táblák** lehetőséget. A következőhöz hasonlónak kell megjelennie.

     ![A BANKDEMO táblázat kibontva Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Az alkalmazás létrehozása nagyvállalati fejlesztőknek

1. Nyissa meg a Visual studiót, és jelentkezzen be.

2. A **fájl** menüben válassza a **projekt/megoldás megnyitása** lehetőséget, navigáljon a **C: \\ Users \\ Public \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo**, és válassza ki a **SLN** fájlt.

3. Szánjon némi időt az objektumok vizsgálatára. A COBOL programok Megoldáskezelőban jelennek meg a CBL bővítménnyel, valamint a CopyBooks (CPY) és a JCL.

4. Kattintson a jobb gombbal a **BankDemo2** projektre, és válassza a **beállítás indítási projektként** lehetőséget.

    > [!NOTE]
    > A BankDemo projekt a HCOSS (a gazdagép kompatibilitási beállítását használja a SQL Server), amely nem használható ehhez a bemutatóhoz.

5. A **megoldáskezelő** kattintson a jobb gombbal a **BankDemo2** projektre, és válassza a **Létrehozás** lehetőséget.

    > [!NOTE]
    > A megoldási szint kiépítése hibákat eredményez, mivel a HCOSS nincs konfigurálva.

6. A projekt felépítésekor vizsgálja meg a **kimeneti** ablakot. Az alábbi képen láthatóhoz hasonlóan kell kinéznie.

     ![A sikeres buildet bemutató kimeneti ablak](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>A BankDemo alkalmazás üzembe helyezése a régió adatbázisába

1. Nyisson meg egy vállalati fejlesztői parancssort (64 bites) rendszergazdaként.

2. Navigáljon a **(z)% Public% \\ Documents \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo**.

3. A parancssorban futtassa a **bankdemodbdeploy** , és adja meg a paramétert a telepítendő adatbázishoz, például:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Ügyeljen arra, hogy a perjel (/) ne legyen visszafelé perjel ( \\ ). Ez a szkript egy ideig fut.

![Felügyelet: Enterprise Developer parancssori ablak](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>A BankDemo régió létrehozása a .NET-es vállalati rendszergazda számára

1. Nyissa meg az **Enterprise Server for .net felügyeleti** felhasználói felületét.

2. Az MMC beépülő modul elindításához a Windows **Start** menüjében válassza a **Micro Focus Enterprise fejlesztői \> konfiguráció \> Enterprise Server for .net admin** elemet. (A Windows Server esetében válassza a **Micro Focus Enterprise Developer \> Enterprise Server for .net admin** lehetőséget).

3. Bontsa ki a **régiók** tárolót a bal oldali ablaktáblán, majd kattintson a jobb gombbal a **CICS** elemre.

4. Válassza a **régió meghatározása** lehetőséget, hogy létrehozzon egy új, **BANKDEMO** nevű CICS-régiót, amelyet a (helyi) adatbázisban üzemeltet.

5. Adja meg az adatbázis-kiszolgáló példányát, kattintson a **tovább** gombra, majd írja be a régió nevét **BANKDEMO**.

     ![Régió definiálása párbeszédpanel](media/07-demo-cics.png)

6. A régiók közötti adatbázis régió-definíciós fájljának kiválasztásához keresse meg a **\_ bankdemo régió \_db.config** **C: \\ felhasználók \\ nyilvános \\ dokumentumok \\ Micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ DotNet \\ bankdemo**.

     ![Régió megadása – régió neve: BANKDEMO](media/08-demo-cics.png)

7. Kattintson a **Finish** (Befejezés) gombra.

## <a name="create-xa-resource-definitions"></a>XA-erőforrás-definíciók létrehozása

1. Az **Enterprise Server for .net felügyeleti** felhasználói felület bal oldali ablaktábláján bontsa ki a **rendszer**, majd az **XA-erőforrás-definíciók** elemet. Ez a beállítás határozza meg, hogy a régió hogyan működjön együtt a vállalati kiszolgálóval és az alkalmazás adatbázisaival.

2. Kattintson a jobb gombbal az **XA erőforrás-definíciók** elemre, és válassza a **kiszolgálópéldány hozzáadása** elemet.

3. A legördülő listában válassza ki az **adatbázis-szolgáltatás példánya** elemet. Ez lesz a helyi gép SQLEXPRESS.

4. Válassza ki a példányt az **XA erőforrás-definíciók (számítógépnév \\ SQLExpress)** tárolóból, és kattintson a **Hozzáadás** gombra.

5. Válassza ki az **adatbázis XA-erőforrás-definícióját** , majd írja be a **BANKDEMO** **nevet** és a **régiót**.

     ![Új adatbázis XA erőforrás-definíciós képernyő](media/09-demo-xa.png)

6. Kattintson a három pontra (**...**) a kapcsolódási karakterlánc varázsló létrehozásához. A **kiszolgálónév** mezőbe írja be a **(helyi) \\ SQLExpress**. A **bejelentkezéshez** válassza a **Windows-hitelesítés** lehetőséget. Az adatbázis neve mezőbe írja be a következőt: **BANKDEMO**

     ![A kapcsolatok karakterláncának szerkesztése képernyő](media/10-demo-string.png)

7. A kapcsolatok tesztelése.

## <a name="start-the-bankdemo-region"></a>A BANKDEMO régió elindítása

> [!NOTE]
> Az első lépés fontos: a régiót úgy kell beállítani, hogy az imént létrehozott XA-erőforrás-definíciót használja.

1. Keresse meg a **BANDEMO CICS régiót** a **régiók tárolóban**, majd válassza a **régió indítási fájljának szerkesztése** lehetőséget a **műveletek** ablaktáblán. Görgessen le az SQL tulajdonságaihoz, és adja meg a **bankdemo** az **XA-erőforrás neveként**, vagy használja a három pontot a kiválasztásához.

2. A módosítások mentéséhez kattintson a **Save (Mentés** ) ikonra.

3. Kattintson a jobb gombbal a **BANKDEMO CICS-régió** elemre a **konzol** ablaktáblán, és válassza a **régió indítása/leállítása** lehetőséget.

4. A középső ablaktáblán megjelenő **Start/Stop (régió indítása/leállítása** ) mező alján válassza az **Indítás** lehetőséget. Néhány másodperc elteltével a régió elindul.

     ![SQL Start/leállítás Box](media/11-demo-sql.png)

     ![CICS régió BANKDEMO – megkezdett képernyő](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Figyelő létrehozása

Hozzon létre egy figyelőt a BankDemo alkalmazást elérő TN3270-munkamenetek számára.

1. A bal oldali ablaktáblán bontsa ki a **konfigurációs szerkesztők** csomópontot, és válassza a **figyelő** lehetőséget.

2. Kattintson a **fájl megnyitása** ikonra, és válassza ki a **seelistener.exe.config** fájlt. Ezt a fájlt a rendszer minden alkalommal betölti, amikor a vállalati kiszolgáló elindul.

3. Figyelje meg a két korábban definiált régiót (ESDEMO és JCLDEMO).

4. Új régió BANKDEMO való létrehozásához kattintson a jobb gombbal a **régiók** elemre, majd válassza a **régió hozzáadása** lehetőséget.

5. Válassza ki a **BANKDEMO régiót**.

6. Adjon hozzá egy TN3270-csatornát úgy, hogy a jobb gombbal a **BANKDEMO régióra** kattint, és kiválasztja a **csatorna hozzáadása** elemet.

7. A **név** mezőbe írja be a következőt: **TN3270**. A **port** mezőben adja meg a **9024** értéket. A ESDEMO alkalmazás a 9230-es portot használja, ezért másik portot kell használnia.

8. A fájl mentéséhez kattintson a Save ( **Mentés** ) ikonra, vagy válassza a **fájl** \> **Mentés** lehetőséget.

9. A figyelő elindításához kattintson a **figyelő indítása** ikonra, vagy válassza a  \> **figyelő** indítása lehetőséget.

     ![Figyelő – konfigurációs szerkesztő Windows](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>A Rumba konfigurálása a BankDemo alkalmazás eléréséhez

A végső teendő a 3270-munkamenet konfigurálása a rumba, a 3270 Emulator használatával. Ez a lépés lehetővé teszi a BankDemo alkalmazás elérését a létrehozott figyelőn keresztül.

1. A Windows **Start** menüjében indítsa el a Rumba Desktopot.

2. A **kapcsolatok** menüpont alatt válassza a **TN3270** elemet.

3. Kattintson a **Beszúrás** gombra, és írja be a **127.0.0.1** értéket az IP-címhez és a **9024** -es típushoz a felhasználó által megadott porthoz.

4. A párbeszédpanel alján kattintson a **kapcsolat** elemre. Megjelenik egy fekete CICS képernyő.

5. Írja be a **bankot** a BankDemo alkalmazás kezdeti 3270 képernyőjének megjelenítéséhez.

6. A User ID (felhasználói azonosító) mezőbe írja be a **B0001** és a jelszót, és írjon be bármit. Megnyílik az első képernyő BANK20.

![Nagyszámítógépes megjelenítés – az üdvözlőképernyőn látható ](media/14-demo.png)
 ![ nagyszámítógépek megjelenítése – rumba-alrendszer bemutató képernyő](media/15-demo.png)

Gratulálunk! Mostantól egy CICS alkalmazást futtat az Azure-ban a Micro Focus Enterprise Server használatával.

## <a name="next-steps"></a>Következő lépések

- [Vállalati kiszolgáló futtatása Docker-tárolókban az Azure-ban](run-enterprise-server-container.md)
- [Nagyszámítógép-áttelepítés – portál](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [Hibaelhárítás](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Demystifying mainframe – Azure Migrálás](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)