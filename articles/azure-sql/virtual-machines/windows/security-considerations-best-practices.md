---
title: Biztonsági megfontolások | Microsoft Docs
description: Ez a témakör általános útmutatást nyújt az Azure-beli virtuális gépeken futó SQL Server biztonságossá tételéhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7ff77a407dfa87e408170573249876bbefee0abe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558575"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Az Azure-beli virtuális gépeken futó SQL Server biztonsági szempontjai
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a témakör olyan általános biztonsági irányelveket tartalmaz, amelyek segítenek az Azure-beli virtuális gépek (VM) SQL Server példányainak biztonságos elérésében.

Az Azure számos iparági szabályozást és szabványt is teljesít, amelyek lehetővé teszik egy megfelelő megoldás kiépítését a virtuális gépeken futó SQL Serverokkal. További információ az Azure-ban való megfelelőség szabályozásáról: [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Az SQL-alapú virtuális gép elérésének szabályozása

A SQL Server virtuális gép létrehozásakor gondolja át, hogyan szabályozhatja, hogy ki és milyen mértékben fér hozzá a géphez, és SQL Server. Általánosságban a következőket kell tennie:

- A SQL Serverhoz való hozzáférés korlátozása csak azokra az alkalmazásokra és ügyfelekre, amelyeknek szüksége van rá.
- Kövesse az ajánlott eljárásokat a felhasználói fiókok és jelszavak kezeléséhez.

A következő szakaszokban javaslatokat talál a fenti pontokon való gondolkodásra.

## <a name="secure-connections"></a>Biztonságos kapcsolatok

Ha katalógus-rendszerképpel rendelkező SQL Server virtuális gépet hoz létre, a **SQL Server kapcsolódási** lehetőséggel **helyi (virtuális gépen belüli)**, **magánjellegű (Virtual Networkon belüli**) vagy **nyilvános (Internet)** kapcsolaton keresztül választhat.

![SQL Server kapcsolat](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

A legjobb biztonság érdekében válassza a legszigorúbb beállítást a forgatókönyvhöz. Ha például olyan alkalmazást futtat, amely ugyanazon a virtuális gépen SQL Server fér hozzá, akkor a **helyi** a legbiztonságosabb választás. Ha olyan Azure-alkalmazást futtat, amelynek hozzáférésre van szüksége a SQL Serverhoz, akkor a **magánhálózati** kommunikáció csak a megadott Azure-beli [virtuális hálózaton](../../../virtual-network/virtual-networks-overview.md)belül SQL Server. Ha **nyilvános** (Internet) hozzáférést igényel a SQL Server VMhoz, akkor az ebben a témakörben ismertetett egyéb ajánlott eljárások követésével csökkentse a támadási felületet.

A portálon kiválasztott beállítások bejövő biztonsági szabályokat használnak a virtuális gép [hálózati biztonsági csoportján](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG), hogy engedélyezzék vagy megtagadják a virtuális géphez való hálózati forgalmat. Módosíthatja vagy létrehozhat új bejövő NSG szabályokat, amelyek engedélyezik a forgalmat a SQL Server portra (alapértelmezés szerint 1433). Megadhat konkrét IP-címeket is, amelyek számára engedélyezett a porton keresztüli kommunikáció.

![Hálózat biztonsági csoportok szabályai](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

A hálózati forgalom korlátozására szolgáló NSG kívül a Windows tűzfalat is használhatja a virtuális gépen.

Ha a klasszikus üzemi modellel rendelkező végpontokat használ, távolítsa el az összes végpontot a virtuális gépen, ha nem használja őket. Az ACL-ek végpontokkal való használatával kapcsolatos utasításokért lásd: [az ACL kezelése egy végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Ez a Azure Resource Manager használó virtuális gépek esetében nem szükséges.

Végezetül vegye fontolóra a titkosított kapcsolatok engedélyezését az Azure-beli virtuális gépen a SQL Server adatbázismotor példányához. Konfigurálja az SQL Server-példányt egy aláírt tanúsítvánnyal. További információ: [titkosított kapcsolatok engedélyezése az adatbázismotor és a](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) [kapcsolati karakterlánc szintaxisával](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Titkosítás

A felügyelt lemezek Server-Side titkosítást kínálnak, és Azure Disk Encryption. A [kiszolgálóoldali titkosítás](../../../virtual-machines/disk-encryption.md) titkosítást biztosít, és biztosítja az adatok védelmét a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítése érdekében. A [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) BitLocker-vagy DM-Crypt-technológiát használ, és integrálja a Azure Key Vault az operációs rendszer és az adatlemezek titkosítására. 

## <a name="use-a-non-default-port"></a>Nem alapértelmezett port használata

Alapértelmezés szerint az SQL Server a jól ismert 1433-as portot figyeli. A fokozott biztonság érdekében konfigurálja a SQL Servert egy nem alapértelmezett port figyelésére, például 1401. Ha kiépít egy SQL Server katalógus rendszerképet a Azure Portalban, ezt a portot a **SQL Server beállítások** panelen adhatja meg.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Ha ezt a kiépítés után szeretné konfigurálni, két lehetőség közül választhat:

- A Resource Manager-alapú [virtuális gépek esetében az SQL Virtual Machines erőforrásból](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)választhatja ki a **Security (biztonság** ) lehetőséget. Ez lehetőséget biztosít a port módosítására.

  ![TCP-port változása a portálon](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- A klasszikus virtuális gépek vagy a portálon nem kiépített SQL Server virtuális gépek esetében manuálisan is konfigurálhatja a portot úgy, hogy távolról csatlakozik a virtuális géphez. A konfigurációs lépésekért lásd: [kiszolgáló konfigurálása egy adott TCP-port figyelésére](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Ha ezt a manuális módszert használja, egy Windows tűzfal-szabályt is hozzá kell adnia, hogy engedélyezze a bejövő forgalmat az adott TCP-porton.

> [!IMPORTANT]
> A nem alapértelmezett port meghatározása jó ötlet, ha a SQL Server-port nyilvános internetes kapcsolatokhoz van nyitva.

Ha SQL Server egy nem alapértelmezett portot figyel, a csatlakozáskor meg kell adnia a portot. Vegyünk például egy olyan forgatókönyvet, ahol a kiszolgáló IP-címe 13.55.255.255, és SQL Server figyeli a 1401-es portot. A SQL Serverhoz való kapcsolódáshoz `13.55.255.255,1401` a kapcsolati sztringben kell megadnia.

## <a name="manage-accounts"></a>Fiókok kezelése

Nem szeretné, hogy a támadók egyszerűen kitalálják a fiókok nevét vagy jelszavát. A következő tippek segíthetnek a használatához:

- Hozzon létre egy olyan egyedi helyi rendszergazdai fiókot, amelynek neve nem **rendszergazda**.

- Komplex erős jelszavakat használhat az összes fiókhoz. Az erős jelszavak létrehozásával kapcsolatos további információkért tekintse meg az [erős jelszó létrehozásával](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386) foglalkozó cikket.

- Alapértelmezés szerint az Azure a SQL Server virtuális gép telepítése során kiválasztja a Windows-hitelesítést. Ezért az **sa** -bejelentkezés le van tiltva, és a telepítő jelszót rendel hozzá. Javasoljuk, hogy ne használjon vagy engedélyezze az **sa** bejelentkezést. Ha SQL-bejelentkezésre van szüksége, használja az alábbi stratégiák egyikét:

  - Hozzon létre egy olyan SQL-fiókot, amely a **sysadmin** tagsággal rendelkező egyedi névvel rendelkezik. Ezt a portálon végezheti el az **SQL-hitelesítés** engedélyezésével a kiépítés során.

    > [!TIP] 
    > Ha nem engedélyezi az SQL-hitelesítést a kiépítés során, manuálisan kell módosítania a hitelesítési módot **SQL Server és Windows hitelesítési módra**. További információ: a [kiszolgáló hitelesítési módjának módosítása](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Ha az **sa** bejelentkezést kell használnia, engedélyezze a bejelentkezést a kiépítés után, és rendeljen hozzá egy új erős jelszót.

## <a name="additional-best-practices"></a>További ajánlott eljárások

A jelen témakörben ismertetett eljárások mellett javasoljuk, hogy tekintse át és implementálja mind a hagyományos, mind a helyi biztonsági eljárások, mind a virtuális gépek biztonsági eljárásainak ajánlott biztonsági eljárásait. 

A helyszíni biztonsági gyakorlatokkal kapcsolatos további információkért tekintse meg a SQL Server telepítésének és a [Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database) [biztonsági szempontjait](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) . 

A virtuális gépek biztonságával kapcsolatos további információkért lásd a [Virtual Machines biztonsági áttekintése](../../../security/fundamentals/virtual-machines-overview.md)című témakört.


## <a name="next-steps"></a>Következő lépések

Ha a teljesítményre vonatkozó ajánlott eljárások is érdeklik, tekintse [meg az Azure Virtual Machines SQL Server teljesítményével kapcsolatos ajánlott eljárásokat](performance-guidelines-best-practices.md)ismertető témakört.

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md). Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](frequently-asked-questions-faq.md).