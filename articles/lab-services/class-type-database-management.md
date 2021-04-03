---
title: Tesztkörnyezet létrehozása a kapcsolódó adatbázisok adatbázis-kezelésének megtanításához | Microsoft Docs
description: Ismerje meg, hogyan állíthat be egy labort a viszonyítási adatbázisok kezelésének tanításához.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462420"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Tesztkörnyezet létrehozása a kapcsolódó adatbázisok adatbázis-kezelésének megtanításához

Ez a cikk bemutatja, hogyan állíthat be egy labort egy alapszintű adatbázis-felügyeleti osztályhoz Azure Lab Servicesban. Az adatbázisok fogalmai az egyetemen bevezető tanfolyamok egyike. A Structured Query Language (SQL) egy nemzetközi szabvány. Az SQL a kapcsolatok adatbázis-kezelésének szabványos nyelve, beleértve az adatbázisok tartalmának hozzáadását, elérését és kezelését.  A legtöbb esetben a gyors feldolgozás, a bevált megbízhatóság, a könnyű használat és a rugalmasság jellemzi.

Ebben a cikkben bemutatjuk, hogyan állíthat be egy virtuálisgép-sablont egy olyan laborban, amely a MySQL adatbázis-kiszolgálóval és a SQL Server 2019-kiszolgálóval is rendelkezik.  A [MySQL](https://www.mysql.com/) egy szabadon elérhető, nyílt forráskódú, összehasonlítható adatbázis-kezelő rendszer (RDBMS).  A [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) a Microsoft RDBMS legújabb verziója.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).  Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért lásd: a [piactér-rendszerképek elérhetővé tétele a labor-készítők](./specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
|Marketplace-beli rendszerkép| Engedélyezze a "SQL Server 2019 standard on Windows Server 2019" rendszerképet a labor-fiókjában való használatra.|

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A tantermi labor beállításakor használja az alábbi táblázatban szereplő beállításokat.  A tantermi laborok létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Közepes. Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.|
|Virtuálisgép-rendszerkép| SQL Server 2019 standard Windows Server 2019 rendszeren|

## <a name="template-machine-configuration"></a>Sablon Számítógép-konfigurációja

Ha a MySQL-t Windows Server 2019-re szeretné telepíteni, kövesse a [MySQL közösségi kiszolgáló telepítése és futtatása virtuális gépen](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)című témakörben leírt lépéseket.

A SQL Server 2019 előre telepítve van az új tesztkörnyezet létrehozásakor választott virtuálisgép-rendszerképben.

## <a name="cost-estimate"></a>Költségbecslés

Az osztályra vonatkozó lehetséges költségbecslés.  25 tanulós osztályt fogunk használni.  20 órányi ütemezett idő van.  Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára.  A kiválasztott virtuálisgép-méret közepes, ami 42 labor egység.

Az alábbi példa egy lehetséges költségbecslés erre az osztályra vonatkozóan:

25 tanuló \* (20 ütemezett óra + 10 kvóta óra) \* 0,42 USD/óra = 315,00 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés

Ez a cikk végigvezeti a tesztkörnyezet létrehozásához szükséges lépéseken, valamint a MySQL-t és a SQL Servert egyaránt használó alapszintű adatbázis-kezelési fogalmakhoz. Hasonló beállításokat használhat más adatbázisok osztályaihoz is.

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)