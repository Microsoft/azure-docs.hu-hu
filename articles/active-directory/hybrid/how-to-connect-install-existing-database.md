---
title: Azure AD Connect telepítése meglévő ADSync-adatbázis használatával | Microsoft Docs
description: Ez a témakör egy meglévő ADSync-adatbázis használatát ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8324b82a05d7e78772e0b0b6de3a9bfaa183411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91265391"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával
A Azure AD Connect SQL Server adatbázis tárolására van szükség az adattároláshoz. Használhatja a Azure AD Connect telepített alapértelmezett SQL Server 2012 Express LocalDB, vagy használhatja a saját teljes SQL-verzióját. Korábban a Azure AD Connect telepítésekor a rendszer a ADSync nevű új adatbázist mindig létrehozta. A Azure AD Connect verzió 1.1.613.0 (vagy azt követően) lehetősége van a Azure AD Connect telepítésére, ha egy meglévő ADSync-adatbázisra mutat.

## <a name="benefits-of-using-an-existing-adsync-database"></a>A meglévő ADSync-adatbázisok használatának előnyei
Egy meglévő ADSync-adatbázisra mutat:

- A hitelesítő adatok kivételével a ADSync-adatbázisban tárolt szinkronizálási konfigurációt (beleértve az egyéni szinkronizálási szabályokat, az összekötőket, a szűrést és a választható funkciók konfigurációját) a rendszer automatikusan helyreállítja, és a telepítés során használja. A Azure AD Connect által a helyszíni AD-vel és az Azure AD-vel végzett módosítások szinkronizálásához használt hitelesítő adatok titkosítottak, és csak az előző Azure AD Connect-kiszolgáló érhetik el.
- A rendszer a ADSync-adatbázisban tárolt összes Identity (összekötő-és metaverse-) és szinkronizálási cookie-t is helyreállítja. Az újonnan telepített Azure AD Connect-kiszolgáló továbbra is képes szinkronizálni az előző Azure AD Connect kiszolgálóról, és nem kell teljes szinkronizálást végeznie.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Olyan forgatókönyvek, amelyekben egy meglévő ADSync-adatbázis használata hasznos
Ezek az előnyök a következő esetekben hasznosak:


- Meglévő Azure AD Connect üzemelő példánya van. A meglévő Azure AD Connect-kiszolgáló már nem működik, de a ADSync-adatbázist tartalmazó SQL Server továbbra is működik. Telepíthet új Azure AD Connect-kiszolgálót, és rámutathat a meglévő ADSync-adatbázisra. 
- Meglévő Azure AD Connect üzemelő példánya van. A ADSync-adatbázist tartalmazó SQL Server-kiszolgáló már nem működik. Van azonban egy nemrég készült biztonsági másolat az adatbázisról. Először a ADSync-adatbázist állíthatja vissza egy új SQL Server-kiszolgálóra. Ezután telepíthet egy új Azure AD Connect-kiszolgálót, és rámutathat a visszaállított ADSync-adatbázisra.
- Meglévő Azure AD Connect üzemelő példánya van, amely a LocalDB-t használja. A LocalDB által kiszabott 10 GB-os korlát miatt a teljes SQL-re szeretne áttelepíteni. Biztonsági mentést készíthet a ADSync-adatbázisról a LocalDB-ből, és visszaállíthatja azt egy SQL Server-kiszolgálóra. Ezután újratelepítheti az új Azure AD Connect-kiszolgálót, és rámutathat a visszaállított ADSync-adatbázisra.
- Egy átmeneti kiszolgálót próbál beállítani, és meg szeretné győződni arról, hogy a konfigurációja megegyezik az aktuális aktív kiszolgálóval. Biztonsági mentést készíthet a ADSync-adatbázisról, és visszaállíthatja azt egy másik SQL Server-kiszolgálóra. Ezután újratelepítheti az új Azure AD Connect-kiszolgálót, és rámutathat a visszaállított ADSync-adatbázisra.

## <a name="prerequisite-information"></a>Előfeltételekre vonatkozó információk

Fontos megjegyzések a folytatás előtt jegyezze fel a következőt:

- Győződjön meg arról, hogy a Azure AD Connect telepítésének előfeltételei a hardver és az előfeltételek, valamint a Azure AD Connect telepítéséhez szükséges fiókok és engedélyek. A "meglévő adatbázis használata" módban a Azure AD Connect telepítéséhez szükséges engedélyek megegyeznek az "egyéni" telepítéssel.
- A Azure AD Connect meglévő ADSync-adatbázison való üzembe helyezése csak a teljes SQL-támogatással támogatott. Az SQL Express LocalDB használata nem támogatott. Ha meglévő ADSync-adatbázissal rendelkezik a használni kívánt LocalDB, először készítsen biztonsági másolatot a ADSync-adatbázisról (LocalDB), és állítsa vissza a teljes SQL-verzióra. Ezt követően a metódus használatával telepítheti Azure AD Connect a visszaállított adatbázison.
- A telepítéshez használt Azure AD Connect verziójának meg kell felelnie a következő feltételeknek:
    - 1.1.613.0 vagy újabb, valamint
    - A Azure AD Connect a ADSync-adatbázissal utoljára használt verziójával megegyező vagy annál nagyobb. Ha a telepítéshez használt Azure AD Connect verziója magasabb, mint a ADSync-adatbázishoz legutóbb használt verzió, akkor szükség lehet teljes szinkronizálásra.  Teljes szinkronizálásra van szükség, ha a séma vagy a szinkronizálási szabály a két verzió között változik. 
- A használt ADSync-adatbázisnak egy viszonylag új szinkronizálási állapotot kell tartalmaznia. A meglévő ADSync-adatbázis utolsó szinkronizálási tevékenységének az elmúlt három hétben kell lennie.
- Azure AD Connect a "meglévő adatbázis használata" metódus használatával történő telepítésekor a rendszer nem őrzi meg az előző Azure AD Connect-kiszolgálón konfigurált bejelentkezési módszert. Emellett a telepítés során nem konfigurálhatja a bejelentkezési módszert. A bejelentkezési módszert csak a telepítés befejezése után lehet konfigurálni.
- Nem rendelkezhet több Azure AD Connect kiszolgálóval ugyanazzal a ADSync-adatbázissal. A "meglévő adatbázis használata" módszer lehetővé teszi egy meglévő ADSync-adatbázis újrafelhasználását egy új Azure AD Connect-kiszolgálóval. Nem támogatja a megosztást.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>A Azure AD Connect telepítésének lépései a "meglévő adatbázis használata" módban
1.  Töltse le Azure AD Connect telepítőt (AzureADConnect.MSI) a Windows Serverre. A Azure AD Connect telepítésének megkezdéséhez kattintson duplán a Azure AD Connect Installer elemre.
2.  Miután az MSI-telepítés befejeződött, az Azure AD Connect varázslója elindítja az expressz módú telepítést. A Kilépés ikonra kattintva zárja be a képernyőt.
![Képernyőkép, amely az "Üdvözöljük az Azure-ba A D kapcsolaton" lapot mutatja, és a bal oldali menüben az "expressz beállítások" elem jelenik meg.](./media/how-to-connect-install-existing-database/db1.png)
3.  Indítson új parancssort vagy PowerShell-munkamenetet. Navigáljon a "C:\Program Files\Microsoft Azure Active Directory Connect" mappába. Az Azure AD Connect-varázsló meglévő adatbázist használó módban való elindításához futtassa az .\AzureADConnect.exe /useexistingdatabase parancsot.

> [!NOTE]
> A kapcsoló **/UseExistingDatabase** csak akkor használja, ha az adatbázis már tartalmaz egy korábbi Azure ad Connect telepítésből származó adatokkal. Ha például egy helyi adatbázisból egy teljes SQL Server adatbázisba helyez át, vagy amikor a Azure AD Connect-kiszolgálót újraépítette, és visszaállította a ADSync-adatbázis SQL-biztonsági másolatát a Azure AD Connect korábbi telepítésével. Ha az adatbázis üres, azaz nem tartalmaz a korábbi Azure AD Connect telepítésből származó összes adatforrást, hagyja ki ezt a lépést.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Megjelenik az Azure AD Connect üdvözlőképernyője. A licencfeltételek és az adatvédelmi nyilatkozat elfogadása után kattintson a **Folytatás** gombra.
   ![Képernyőfelvétel: "Üdvözöljük az Azure A D-vel" oldal](./media/how-to-connect-install-existing-database/db3.png)
1. **A szükséges összetevők telepítése** képernyőn a **Meglévő SQL Server használata** lehetőség engedélyezve van. Adja meg az ADSync-adatbázist futtató SQL Server nevét. Ha az ADSync-adatbázist futtató SQL-motorpéldány nem az alapértelmezett példány az SQL Serveren, meg kell adnia az SQL-motorpéldány nevét. Ha az SQL-böngészés nincs engedélyezve, meg kell adnia az SQL-motorpéldány portszámát is. Például:         
   ![A "szükséges összetevők telepítése" lapot megjelenítő képernyőkép.](./media/how-to-connect-install-existing-database/db4.png)           

1. A **Azure AD-hez való csatlakozásra** szolgáló képernyőn meg kell adnia az Azure AD-címtár globális rendszergazdájának hitelesítő adatait. Javasoljuk, hogy az alapértelmezett onmicrosoft.com tartományban található fiókot használjon. Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.
   ![Kapcsolódás](./media/how-to-connect-install-existing-database/db5.png)
 
1. A **címtárak csatlakozására** szolgáló képernyőn a címtár-szinkronizáláshoz konfigurált meglévő AD-erdő mellett egy piros kereszt ikon látható. Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Az Azure AD Connect varázslója nem tudja lekérni az ADSync-adatbázisban tárolt AD DS-fiók hitelesítő adatait, mert azok titkosítva vannak, és a titkosítás kizárólag az előző Azure AD Connect-kiszolgálóval oldható fel. Kattintson a **hitelesítő adatok módosításának** lehetőségére az AD-erdőhöz tartozó AD DS-fiók megadásához.
   ![Címtárak](./media/how-to-connect-install-existing-database/db6.png)
 
1. Az előugró párbeszédpanelen a következő lehetőségek közül választhat: (i) megad vállalati rendszergazdai hitelesítő adatokat, és engedi, hogy az Azure AD Connect hozza létre az AD DS-fiókot, vagy (ii) létrehozza az AD DS-fiókot, és megadja annak hitelesítő adatait az Azure AD Connectnek. Miután kiválasztotta az egyik lehetőséget és megadta a szükséges hitelesítő adatokat, kattintson az **OK** gombra az előugró párbeszédpanel bezáráshoz.
   ![Képernyőfelvétel: a "D erdő fiók" nevű előugró párbeszédpanel, amely az "új D fiók létrehozása" elemet mutatja.](./media/how-to-connect-install-existing-database/db7.png)
 
1. A hitelesítő adatok megadása után a piros kereszt ikon egy zöld pipa ikonra változik. Kattintson a **Tovább** gombra.
   ![Képernyőfelvétel: "a címtárak összekapcsolása" oldal megjelenítése.](./media/how-to-connect-install-existing-database/db8.png)
 
1. A **konfigurálásra kész** képernyőn kattintson a **telepítés** elemre.
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db9.png)
 
1. Miután a telepítés befejeződött, az Azure AD Connect-kiszolgáló automatikusan engedélyezve lesz az átmeneti módhoz. Javasoljuk, hogy az átmeneti mód letiltása előtt ellenőrizze a kiszolgáló konfigurációját és a függőben lévő exportálásokat, nehogy nem várt módosításokkal kelljen számolnia. 

## <a name="post-installation-tasks"></a>Telepítés utáni feladatok
A 1.2.65.0 előtti Azure AD Connect-verzióval létrehozott adatbázis biztonsági másolatának visszaállításakor az átmeneti kiszolgáló automatikusan kiválasztja a **nem konfigurált** bejelentkezési módszert. Amíg a jelszó-kivonat szinkronizálása és a jelszó visszaírási beállításai visszaállnak, ezt követően módosítania kell a bejelentkezési módszert, hogy az megfeleljen az aktív szinkronizációs kiszolgálóra érvényes egyéb házirendeknek.  A lépések elvégzésének elmulasztása megakadályozhatja, hogy a felhasználók bejelentkezzenek, ha a kiszolgáló aktívvá válik.  

Az alábbi táblázat segítségével ellenőrizheti a szükséges további lépéseket.

|Szolgáltatás|Lépések|
|-----|-----|
|Jelszó-kivonat szinkronizálása| a 1.2.65.0-től kezdődően a jelszó-kivonat szinkronizálása és a jelszó visszaírási beállításai teljes mértékben visszaállíthatók a Azure AD Connect verzióihoz.  Ha a Azure AD Connect egy régebbi verzióját használja, tekintse át ezeket a funkciókat a szinkronizálási beállításokkal, hogy azok megfeleljenek az aktív szinkronizálási kiszolgálónak.  Nincs szükség további konfigurációs lépésekre.|
|Összevonás az AD FS rendszerrel|Az Azure-hitelesítések továbbra is az aktív szinkronizálási kiszolgálóhoz konfigurált AD FS szabályzatot használják.  Ha Azure AD Connectt használ a AD FS Farm felügyeletéhez, igény szerint megváltoztathatja a bejelentkezési módszert, hogy a rendszer a készenléti kiszolgáló aktív szinkronizálási példányának előkészítésekor AD FS összevonást is megváltoztassa.   Ha az eszköz beállításai engedélyezve vannak az aktív szinkronizációs kiszolgálón, konfigurálja ezeket a beállításokat ezen a kiszolgálón az "eszközbeállítások konfigurálása" feladat futtatásával.|
|Átmenő hitelesítés és asztali egyszeri Sign-On|Frissítse a bejelentkezési metódust, hogy az megfeleljen az aktív szinkronizációs kiszolgáló konfigurációjának.  Ha ezt a kiszolgálót az elsődlegesnek való előléptetés előtt nem követi, a zökkenőmentes egyszeri bejelentkezés mellett a rendszer letiltja a bérlőt, és előfordulhat, hogy a bérlő zárolva van, ha nem rendelkezik a jelszó-kivonatok szinkronizálása biztonsági mentéssel lehetőséggel. Azt is vegye figyelembe, hogy az átmenő hitelesítés átmeneti módban való engedélyezésekor az új hitelesítési ügynök települ, regisztrálva lesz, és magas rendelkezésre állású ügynökként fog futni, amely elfogadja a bejelentkezési kérelmeket.|
|Összevonás a PingFederate-tel|Az Azure-hitelesítések továbbra is az aktív szinkronizálási kiszolgálóhoz konfigurált PingFederate szabályzatot használják.  Igény szerint módosíthatja a bejelentkezési módszert, hogy PingFederate a készenléti kiszolgáló aktív szinkronizálási példányként való előkészítését.  Ez a lépés elhalasztható, amíg további tartományokat nem kell összevonása a PingFederate-mel.|

## <a name="next-steps"></a>Következő lépések

- Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).
- Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).
- Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).
- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
