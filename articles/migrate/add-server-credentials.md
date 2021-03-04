---
title: Adja meg a kiszolgálói hitelesítő adatokat az alkalmazások, függőségek és SQL Server példányok és adatbázisok felderítéséhez
description: Megtudhatja, hogyan biztosíthat kiszolgálói hitelesítő adatokat a készülék Configuration Managerben
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 2359855ce3949eb022a03f6e8e2dbc05f98907db
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054774"
---
# <a name="provide-server-credentials-to-discover-applications-dependencies-and-sql-server-instances-and-databases"></a>Adja meg a kiszolgálói hitelesítő adatokat az alkalmazások, függőségek és SQL Server példányok és adatbázisok felderítéséhez

Ebből a cikkből megtudhatja, hogyan adhat hozzá több kiszolgálói hitelesítő adatot a készülék Configuration Managerben a szoftverek leltározása (telepített alkalmazások felderítése), az ügynök nélküli függőségek elemzése és SQL Server példányok és adatbázisok felderítése céljából.

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A szolgáltatás kipróbálásához használja ezt a [**hivatkozást**](https://aka.ms/AzureMigrate/SQL) **Kelet-Ausztrália** régióban található projekt létrehozásához. Ha már rendelkezik egy projekttel Kelet-Ausztráliaban, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy végrehajtotta-e az [**előfeltételeket**](how-to-discover-sql-existing-project.md) a portálon.

A [Azure Migrate készülék](migrate-appliance.md) egy könnyű berendezés, amelyet a Azure Migrate használ: a kiszolgáló értékelése a VMware-környezetben futó helyszíni kiszolgálók felderítéséhez, valamint a kiszolgáló konfigurációjának és teljesítményének metaadatoknak az Azure-ba való küldéséhez. A készülék használható a szoftverek leltározására, az ügynök nélküli függőségek elemzésére és a SQL Server példányok és adatbázisok felderítésére is.

Ha ezeket a funkciókat szeretné használni, az alábbi lépésekkel megadhatja a kiszolgálói hitelesítő adatokat. A készülék megkísérli automatikusan leképezni a hitelesítő adatokat a kiszolgálók számára a felderítési funkciók végrehajtásához.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Hitelesítő adatok hozzáadása a VMware-környezetben futó kiszolgálókhoz

### <a name="types-of-server-credentials-supported"></a>A kiszolgálói hitelesítő adatok típusai támogatottak

Több kiszolgálói hitelesítő adatot is hozzáadhat a készülék Configuration Managerben, amely lehet tartomány, nem tartomány (Windows vagy Linux) vagy SQL Server hitelesítési hitelesítő adat.

A kiszolgáló által támogatott hitelesítő adatok típusai az alábbi táblázatban láthatók:

Hitelesítő adatok típusa | Leírás
--- | ---
**Tartományi hitelesítő adatok** | **Tartományi hitelesítő adatokat** adhat hozzá a **hitelesítő adatok hozzáadása** modális lehetőség kiválasztásával a legördülő menüben. <br/><br/> Tartományi hitelesítő adatok megadásához meg kell adnia azt a **tartománynevet** , amelyet a teljes tartománynév (FQDN) formátumban kell megadni (pl. prod.Corp.contoso.com). <br/><br/> A hitelesítő adatok, a Felhasználónév és a jelszó rövid nevét is meg kell adnia. <br/><br/> A hozzáadott tartományi hitelesítő adatok automatikusan érvényesítve lesznek a tartomány Active Directory. Ennek célja, hogy megakadályozza a fiókok zárolását, amikor a készülék megkísérli leképezni a tartomány hitelesítő adatait a felderített kiszolgálókon. <br/><br/> A készülék nem kísérli meg leképezni a sikertelen ellenőrzéssel rendelkező tartományi hitelesítő adatokat. Legalább egy sikeresen érvényesített tartományi hitelesítő adatot vagy legalább egy nem tartományi hitelesítő adatot kell megadnia a szoftver leltárának folytatásához.<br/><br/>A rendszer a Windows-kiszolgálókon automatikusan leképezett tartományi hitelesítő adatokat fogja használni a szoftverek leltározásához, és felhasználhatja SQL Server példányok és adatbázisok felderítésére is _(ha a Windows-hitelesítési módot konfigurálta az SQL-kiszolgálókon)_.<br/> [További](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) információ az SQL-kiszolgálókon támogatott hitelesítési módok típusairól.
**Nem tartományi hitelesítő adatok (Windows/Linux)** | **Windows (nem tartományi)** vagy **Linux (nem tartományi)** rendszer hozzáadásához válassza a szükséges lehetőséget a **hitelesítő adatok hozzáadása** modális legördülő listából. <br/><br/> A hitelesítő adatok, a Felhasználónév és a jelszó rövid nevét kell megadnia.
**Hitelesítő adatok SQL Server** | **SQL Server** hitelesítő adatokat a **hitelesítő adatok hozzáadása** modális lehetőség kiválasztásával adhat hozzá. <br/><br/> A hitelesítő adatok, a Felhasználónév és a jelszó rövid nevét kell megadnia. <br/><br/> Ilyen típusú hitelesítő adatokat adhat hozzá a VMware-környezetben futó SQL Server-példányok és adatbázisok felderítéséhez, ha SQL Server hitelesítési módot konfigurált az SQL-kiszolgálókon.<br/> [További](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) információ az SQL-kiszolgálókon támogatott hitelesítési módok típusairól.<br/><br/> Legalább egy sikeresen érvényesített tartományi hitelesítő adatot vagy legalább egy Windows-(nem tartományi) hitelesítő adatot kell megadnia ahhoz, hogy a készülék el tudja végezni a szoftver leltárát a kiszolgálókon telepített SQL felderítéséhez, mielőtt a SQL Server hitelesítési hitelesítő adatokat felderíteni a SQL Server példányok és adatbázisok felderítéséhez.

Győződjön meg arról, hogy a Windows-/Linux-hitelesítő adatok szükségesek a szoftver leltározásához, az ügynök nélküli függőségek elemzéséhez és a SQL Server példányok és adatbázisok felderítéséhez.

### <a name="required-permissions"></a>Szükséges engedélyek

Az alábbi táblázat felsorolja azokat az engedélyeket, amelyek szükségesek a készüléken a megfelelő funkciók elvégzéséhez megadott kiszolgálói hitelesítő adatokhoz:

Szolgáltatás | windowsos azonosító adatait | Linux hitelesítő adatok
---| ---| ---
**Szoftverleltár** | Vendég felhasználói fiók | Normál/normál felhasználói fiók (nem sudo hozzáférési engedélyek)
**SQL Server példányok és adatbázisok felderítése** | A sysadmin (rendszergazda) kiszolgálói szerepkörbe tartozó felhasználói fiók. | _Jelenleg nem támogatott_
**Ügynök nélküli függőségelemzés** | Tartományi vagy nem tartományi (helyi) fiók rendszergazdai engedélyekkel | Gyökér felhasználói fiók vagy <br/> a/bin/netstat-és/bin/ls-fájlokra vonatkozó engedélyekkel rendelkező fiók: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.<br/><br/> A következő parancsokkal állíthatja be ezeket a képességeket: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Ajánlott eljárások a hitelesítő adatok megadásához

- Azt javasoljuk, hogy hozzon létre egy dedikált tartományi felhasználói fiókot a [szükséges engedélyekkel](add-server-credentials.md#required-permissions), amelyek a szoftverek leltározására, az ügynök nélküli függőség elemzésére és SQL Server példányok és adatbázisok felderítésére vonatkoznak a kívánt kiszolgálókon.
- Ajánlott legalább egy sikeresen érvényesített tartományi hitelesítő adatot vagy legalább egy nem tartományi hitelesítő adatot megadnia a szoftver leltározásának elindításához.
- SQL Server példányok és adatbázisok felderítéséhez tartományi hitelesítő adatokat adhat meg, ha az SQL-kiszolgálókon beállította a Windows-hitelesítési módot.
-  SQL Server hitelesítő adatokat is megadhat, ha SQL Server hitelesítési módot konfigurált az SQL-kiszolgálókon, de ajánlott legalább egy sikeresen érvényesített tartományi hitelesítő adatot vagy legalább egy Windows-(tartományon kívüli) hitelesítő adatot megadni, hogy a készülék először elvégezze a szoftver leltározását.

## <a name="credentials-handling-on-appliance"></a>A készüléken a hitelesítő adatok kezelése

- A készülék Configuration Managerben megadott összes hitelesítő adat helyileg tárolódik a berendezés-kiszolgálón, és nem lesz elküldve az Azure-ba.
- A berendezés-kiszolgálón tárolt hitelesítő adatok titkosítva vannak a Protection API (DPAPI) használatával.
- A hitelesítő adatok hozzáadása után a készülék megkísérli automatikusan leképezni a hitelesítő adatokat a megfelelő kiszolgálók felderítésének végrehajtásához.
- A készülék a kiszolgálókon automatikusan leképezett hitelesítő adatokat használja az összes további felderítési ciklushoz, amíg a hitelesítő adatok beolvasni tudják a szükséges felderítési adatokat. Ha a hitelesítő adatok nem működnek, a készülék újból megkísérli a hozzárendelést a hozzáadott hitelesítő adatok listájáról, és folytatja a folyamatban lévő felderítést a kiszolgálón.
- A hozzáadott tartományi hitelesítő adatok automatikusan érvényesítve lesznek a tartomány Active Directory. Ennek célja, hogy megakadályozza a fiókok zárolását, amikor a készülék megkísérli leképezni a tartomány hitelesítő adatait a felderített kiszolgálókon. A készülék nem kísérli meg leképezni a sikertelen ellenőrzéssel rendelkező tartományi hitelesítő adatokat.
- Ha a készülék nem tud tartomány-vagy tartományon kívüli hitelesítő adatokat leképezni egy kiszolgálón, a "hitelesítő adatok nem érhetők el" állapot jelenik meg a projekt kiszolgálóján.

## <a name="next-steps"></a>Következő lépések

Tekintse át a [VMware-környezetben futó kiszolgálók felderítésére](tutorial-discover-vmware.md) vonatkozó oktatóanyagokat