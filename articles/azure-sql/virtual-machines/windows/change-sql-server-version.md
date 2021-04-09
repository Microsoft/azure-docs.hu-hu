---
title: SQL Server verzió helyben történő módosítása
description: Megtudhatja, hogyan módosíthatja a SQL Server virtuális gép verzióját az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358843"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Az Azure-beli virtuális gépen SQL Server verzió helyben történő módosítása

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan módosíthatja a Microsoft SQL Server verzióját egy Windows rendszerű virtuális gépen (VM) a Microsoft Azure.

## <a name="prerequisites"></a>Előfeltételek

SQL Server helyben történő frissítéséhez a következő feltételek érvényesek:

- A SQL Server kívánt verziójának telepítési adathordozójának megadása kötelező. A [Frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) rendelkező ügyfelek a [Mennyiségilicenc-szolgáltatási központból](https://www.microsoft.com/Licensing/servicecenter/default.aspx) szerezhetik be a telepítési adathordozót. Azok az ügyfelek, akik nem rendelkeznek frissítési garanciával, az Azure Marketplace SQL Server VM-rendszerkép telepítési adathordozóját használhatják, amely a SQL Server újabb verziójával rendelkezik (ez általában a C:\SQLServerFull-ben található).
- A kiadási [frissítéseknek a támogatási frissítések elérési útját](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)kell követniük.

## <a name="planning-for-version-change"></a>A verzió módosításának tervezése

Javasoljuk, hogy a verzió módosítása előtt tekintse át a következő elemeket:

1. Győződjön meg arról, hogy a verzió mely újdonságait tervezi frissíteni:

   - Az [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15) újdonságai
   - Az [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017) újdonságai
   - Az [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016) újdonságai


1. Javasoljuk, hogy tekintse meg azon verzió [kompatibilitási tanúsítványát](/sql/database-engine/install-windows/compatibility-certification) , amelyet módosítani szeretne, hogy az adatbázis-kompatibilitási módok használatával csökkentse a frissítés hatását.
1. A sikeres eredmény biztosításához tekintse át a következő cikkeket:

   - [Videó: a SQL Server modernizálása | PAM lorincz & Pedro Lopes | 20 éves bérlet](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Database Experimentation Assistant AB-teszteléshez](/sql/dea/database-experimentation-assistant-overview)
   - [Adatbázisok frissítése a lekérdezés-hangolási asszisztens használatával](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Az adatbázis-kompatibilitási szint módosítása és a lekérdezési tároló használata](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>SQL-verzió frissítése

> [!WARNING]
> A SQL Server verziójának frissítése a társított szolgáltatások, például a Analysis Services és az R szolgáltatások mellett SQL Server újraindítását végzi.

A SQL Server verziójának frissítéséhez szerezze be a SQL Server telepítési adathordozóját a későbbi verzióhoz, amely [támogatja a SQL Server frissítési útvonalát](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) , és hajtsa végre a következő lépéseket:

1. A folyamat megkezdése előtt biztonsági mentést készíthet az adatbázisokról, beleértve a rendszer (kivéve a tempdb) és a felhasználói adatbázisokat. Azure Backup Services használatával is létrehozhat egy alkalmazás-konzisztens virtuálisgép-biztonsági mentést.
1. Indítsa el a Setup.exe a SQL Server telepítési adathordozóról.
1. A telepítővarázsló elindítja a SQL Server telepítési központot. SQL Server meglévő példányának frissítéséhez válassza a navigációs ablaktábla **telepítés** elemét, majd válassza a frissítés lehetőséget a **SQL Server egy korábbi verziójából**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="SQL Server verziójának frissítésére szolgáló kijelölés":::

1. A **termékkulcs** lapon válassza ki azt a lehetőséget, amely azt jelzi, hogy a SQL Server ingyenes kiadására szeretne-e frissíteni, vagy a termék éles verziójának PID-kulcsa van. További információ: [SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15) és a [támogatott verzió és kiadás frissítései (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades)támogatott szolgáltatásai.
1. Kattintson a **tovább** gombra, amíg el nem éri a **frissítésre kész** lapot, majd válassza a **frissítés** lehetőséget. A módosítás érvénybe léptetéséhez a telepítési ablak több percig is leállhat. A **teljes** oldal megerősíti, hogy a frissítés befejeződött. A frissítéshez szükséges lépésenkénti eljárásért tekintse meg [a teljes eljárást](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Teljes oldal":::

Ha módosította a SQL Server kiadást a verzió módosítása mellett, frissítse a kiadást is, és tekintse meg a **verzió és kiadás ellenőrzése a portálon** című SZAKASZT az SQL VM-példány módosításához.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Verzió metaadatainak módosítása":::

## <a name="downgrade-the-version-of-sql-server"></a>SQL Server verziójának visszaminősítése

SQL Server verziójának visszalépéséhez teljesen el kell távolítania SQL Server, majd újra kell telepítenie a kívánt verzió használatával. Ez hasonló a SQL Server friss telepítéséhez, mert nem fogja tudni visszaállítani a korábbi adatbázist újabb verzióról az újonnan telepített korábbi verzióra. Az adatbázisokat újra létre kell hozni a semmiből. Ha a frissítés során is módosította SQL Server kiadását, módosítsa a Azure Portal SQL Server VM **kiadás** tulajdonságát az új kiadás értékre. Ez frissíti a virtuális géphez társított metaadatokat és számlázást.

> [!WARNING]
> SQL Server helyi visszalépése nem támogatott.

A SQL Server verzióját a következő lépésekkel állíthatja vissza:

1. Győződjön meg arról, hogy nem használ olyan funkciót, amely [csak a későbbi verzióban érhető el](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Az összes adatbázis biztonsági mentése, beleértve a rendszer (kivéve a tempdb) és a felhasználói adatbázisok biztonsági mentését.
1. Exportálja a szükséges kiszolgálóoldali objektumokat (például a kiszolgálói eseményindítókat, a szerepköröket, a bejelentkezéseket, a csatolt kiszolgálókat, a feladatokat, a hitelesítő adatokat és a tanúsítványokat).
1. Ha nem rendelkezik olyan parancsfájlokkal, amelyekkel újra létre szeretné hozni a felhasználói adatbázisokat a korábbi verziókban, az összes objektumot le kell írnia, és az összes adatexportálást BCP.exe, SSIS vagy DACPAC használatával kell exportálnia.

   Ügyeljen arra, hogy a megfelelő beállításokat válassza, ha az ilyen elemeket célként megadott verzióként, függő objektumként vagy speciális beállításokként parancsfájlként adja meg.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Parancsfájl-beállítások":::

1. Távolítsa el teljesen SQL Server és az összes társított szolgáltatást.
1. Indítsa újra a virtuális gépet.
1. Telepítse a SQL Servert az adathordozóról a program kívánt verziójára.
1. Telepítse a legújabb szervizcsomagokat és összesítő frissítéseket.
1. Importálja az összes szükséges kiszolgálói szintű objektumot (amelyet a 3. lépésben exportáltak).
1. Hozza létre újra az összes szükséges felhasználói adatbázist (a létrehozott parancsfájlok használatával vagy a 4. lépésből származó fájlokkal).

## <a name="verify-the-version-and-edition-in-the-portal"></a>A verzió és a kiadás ellenőrzése a portálon

A SQL Server verziójának módosítása után ismét regisztrálja SQL Server VM az [SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md) , hogy a Azure Portal használatával megtekinthesse a SQL Server verzióját. A felsorolt verziószámnak mostantól a SQL Server telepítésének újonnan frissített verziójának és kiadásának kell szerepelnie.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Verzió ellenőrzése":::

> [!NOTE]
> Ha már regisztrált az SQL IaaS-ügynök bővítménnyel, törölje [a regisztrációt az RP-ből](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) , majd [regisztrálja újra az SQL VM-erőforrást](sql-agent-extension-manually-register-single-vm.md#register-with-extension) , hogy az a virtuális gépre telepített SQL Server megfelelő verzióját és kiadását észlelje. Ez frissíti a virtuális géphez társított metaadatokat és számlázási adatokat.

## <a name="remarks"></a>Megjegyzések

- Javasoljuk, hogy a frissítés befejezése után indítsa el a biztonsági mentéseket/a statisztikák frissítését/az indexek újraépítését/a konzisztencia-ellenőrzést. Az egyes adatbázis-kompatibilitási szinteket is megtekintheti, hogy azok megfeleljenek a kívánt szintnek.
- Ha SQL Server frissült a virtuális gépen, győződjön meg arról, hogy a Azure Portal SQL Server **kiadás** tulajdonsága megegyezik-e a számlázáshoz telepített kiadási számmal.
- A [kiadás módosításának](change-sql-server-edition.md#change-edition-in-portal) lehetősége az SQL IaaS-ügynök bővítmény egyik funkciója. Egy Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal automatikusan regisztrálja a SQL Server VMt a kiterjesztéssel. A SQL Server öntelepítését végző ügyfeleknek azonban manuálisan kell [regisztrálniuk SQL Server VM](sql-agent-extension-manually-register-single-vm.md).
- Ha eldobja a SQL Server VM-erőforrást, a rendszer visszaállítja a rendszerkép rögzített kiadási beállítását.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
- [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
- [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](doc-changes-updates-release-notes.md)