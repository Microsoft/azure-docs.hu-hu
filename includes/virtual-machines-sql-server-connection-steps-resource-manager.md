---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025608"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>DNS-címke konfigurálása a nyilvános IP-címhez

Ha az internetről szeretne csatlakozni az SQL Server-adatbázismotorhoz, érdemes létrehoznia egy DNS-címkét a nyilvános IP-címhez. Csatlakozhat IP-cím alapján, de a DNS-címke létrehoz egy könnyebben azonosítható A rekordot, és kivonatolja az alapul szolgáló nyilvános IP-címet.

> [!NOTE]
> Nincs szükség DNS-címkére, ha csak az adott virtuális hálózaton belül vagy csak helyben szeretne csatlakozni az SQL Server-példányhoz.

DNS-címke létrehozásához először válassza a **Virtuális gépek** elemet a portálon. Jelölje ki az SQL Server rendszerű virtuális gépet a tulajdonságai megjelenítéséhez.

1. A virtuális gép áttekintésében válassza ki a **nyilvános IP-címét**.

    ![nyilvános IP-cím](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. A Nyilvános IP-cím tulajdonságai között bontsa ki a **Konfiguráció** elemet.

1. Adjon meg egy DNS-címkenevet. Ez a név egy A rekord, amelynek használatával név szerint csatlakozhat az SQL Server rendszerű virtuális géphez az IP-cím megadásával való közvetlen csatlakozás helyett.

1. Kattintson a **Mentés** gombra.

    ![dns-címke](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Csatlakozás az adatbázismotorhoz egy másik számítógépről

1. Nyissa meg az SQL Server Management Studio (SSMS) alkalmazást egy internethez csatlakozó számítógépen. Ha még nem rendelkezik az SQL Server Management Studio alkalmazással, [innen](/sql/ssms/download-sql-server-management-studio-ssms) letöltheti.

1. A **Kapcsolódás a kiszolgálóhoz** vagy a **Kapcsolódás az adatbázismotorhoz** párbeszédpanelen szerkessze a **Kiszolgáló neve** értéket. Adja meg a virtuális gép (az előző feladat során meghatározott) IP-címét vagy teljes DNS-nevét. Egy vessző hozzáadásával az SQL Server TCP-portját is megadhatja. Például: `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.

1. A **Bejelentkezés** szövegmezőbe írjon be egy érvényes SQL-bejelentkezési nevet.

1. A **Jelszó** szövegmezőbe írja be a bejelentkezési jelszót.

1. Kattintson a **Csatlakozás** gombra.

    ![ssms connect](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)