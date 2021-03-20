---
title: Tartományi független munkacsoport rendelkezésre állási csoportjának konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy Active Directory tartományi független munkacsoportot always on rendelkezésre állási csoportot egy SQL Server virtuális gépen az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359455"
---
# <a name="configure-a-workgroup-availability-group"></a>Munkacsoport rendelkezésre állási csoportjának konfigurálása 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk ismerteti azokat a lépéseket, amelyek szükségesek egy Active Directory tartományhoz független fürt létrehozásához egy always on rendelkezésre állási csoporttal; ezt munkacsoport-fürtnek is nevezzük. Ez a cikk a munkacsoportok és a rendelkezésre állási csoport előkészítéséhez és konfigurálásához szükséges lépéseket, valamint a más cikkekben tárgyalt lépéseket, például a fürt létrehozását, illetve a rendelkezésre állási csoport központi telepítését ismerteti. 


## <a name="prerequisites"></a>Előfeltételek

Munkacsoporton rendelkezésre állási csoport konfigurálásához a következőkre lesz szüksége:
- Legalább két Windows Server 2016 (vagy újabb) rendszerű virtuális gép, amely SQL Server 2016 (vagy újabb) rendszert futtat, ugyanarra a rendelkezésre állási csoportba, vagy különböző rendelkezésre állási zónákba helyezi üzembe statikus IP-címek használatával. 
- Egy helyi hálózat, amely legalább 4 ingyenes IP-címmel rendelkezik az alhálózaton. 
- Egy fiók a rendszergazda csoport minden olyan gépén, amely rendszergazdai jogosultságokkal is rendelkezik SQL Serveron belül. 
- Nyitott portok: TCP 1433, TCP 5022, TCP 59999. 

A következő paraméterek használhatók a cikkben, de a szükséges módon módosítható: 

| **Név** | **Paraméter** |
| :------ | :---------------------------------- |
| **Csomópont1**   | AGNode1 (10.0.0.4) |
| **Csomópont2**   | AGNode2 (10.0.0.5) |
| **Fürt neve** | AGWGAG (10.0.0.6) |
| **Figyelő** | AGListener (10.0.0.7) | 
| **DNS-utótag** | ag.wgcluster.example.com | 
| **Munkacsoport neve** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>DNS-utótag beállítása 

Ebben a lépésben konfigurálja mindkét kiszolgáló DNS-utótagját. Például: `ag.wgcluster.example.com`. Ez lehetővé teszi annak az objektumnak a nevét, amelyhez a hálózaton belül teljesen minősített címként szeretne csatlakozni, például: `AGNode1.ag.wgcluster.example.com` . 

A DNS-utótag konfigurálásához kövesse az alábbi lépéseket:

1. RDP-be az első csomópontra, és nyissa meg a Kiszolgálókezelő alkalmazást. 
1. Válassza a **helyi kiszolgáló** lehetőséget, majd válassza ki a virtuális gép nevét a **számítógép neve** alatt. 
1. A **számítógép átnevezéséhez** kattintson a **módosítás..** . elemre... 
1. Módosítsa a munkacsoport nevének nevét úgy, hogy az legyen értelmes, például `AGWORKGROUP` : 

   ![Munkacsoport nevének módosítása](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Válassza a **továbbiak...** lehetőséget a **DNS-utótag és a NetBIOS-számítógépnév** párbeszédpanel megnyitásához. 
1. Írja be a DNS-utótag nevét a **számítógép elsődleges DNS-utótagja** mezőbe, például `ag.wgcluster.example.com` :, majd kattintson **az OK gombra**: 

   ![Képernyőfelvétel: a D N S utótag és a NetBIOS-számítógépnév párbeszédpanel, ahol megadhatja az értéket.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Győződjön meg arról, hogy a **teljes számítógépnév** mostantól a DNS-utótagot jeleníti meg, majd kattintson **az OK** gombra a módosítások mentéséhez: 

   ![A képernyőképen a teljes számítógépnév látható.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Ha a rendszer kéri, indítsa újra a kiszolgálót. 
1. Ismételje meg ezeket a lépéseket a rendelkezésre állási csoporthoz használni kívánt többi csomóponton. 

## <a name="edit-a-host-file"></a>Gazdagép fájljának szerkesztése

Mivel nincs Active Directory, a Windows-kapcsolatok hitelesítésére nincs mód. Ennek megfelelően rendeljen megbízhatóságot úgy, hogy a gazdagép fájlját szövegszerkesztővel szerkeszti. 

A gazda fájl szerkesztéséhez kövesse az alábbi lépéseket:

1. RDP-be a virtuális gépre. 
1. A **fájlkezelővel** nyissa meg a következőt: `c:\windows\system32\drivers\etc` . 
1. Kattintson a jobb gombbal a **hosts** fájlra, és nyissa meg a fájlt a **Jegyzettömbben** (vagy bármilyen más szövegszerkesztőben).
1. A fájl végén adjon hozzá egy bejegyzést az egyes csomópontokhoz, a rendelkezésre állási csoporthoz és a figyelőhöz `IP Address, DNS Suffix #comment` hasonló módon: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adja hozzá az IP-címhez, a fürthöz és a figyelőhöz tartozó bejegyzéseket a gazdagép fájljához](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Engedélyek beállítása

Mivel nincs Active Directory az engedélyek kezeléséhez, manuálisan engedélyeznie kell egy nem beépített helyi rendszergazdai fiókot a fürt létrehozásához. 

Ehhez futtassa a következő PowerShell-parancsmagot egy felügyeleti PowerShell-munkamenetben minden csomóponton: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ebben a lépésben a feladatátvevő fürtöt fogja létrehozni. Ha nem ismeri ezeket a lépéseket, követheti őket a [feladatátvevő fürt oktatóanyagában](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Jelentős eltérések az oktatóanyag és a munkacsoporthoz tartozó fürt esetében elvégzendő teendők között:
- Törölje a **tárolót**, és **közvetlen tárolóhelyek** a fürt érvényesítésének futtatásakor. 
- Ha a csomópontokat hozzáadja a fürthöz, adja hozzá a teljes nevet, például:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Törölje **az összes megfelelő tároló hozzáadása a fürthöz** jelölőnégyzet jelölését. 

A fürt létrehozása után rendeljen hozzá egy statikus fürt IP-címét. Ehhez kövesse az alábbi lépéseket:

1. Az egyik csomóponton nyissa meg a **Feladatátvevőfürt-kezelőt**, válassza ki a fürtöt, kattintson a jobb gombbal a **névre: \<ClusterNam>** a **fürt alapvető erőforrásai** területen, majd válassza a **Tulajdonságok** lehetőséget. 

   ![A fürt nevének megnyitása](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Válassza ki az IP-címet az **IP-címek** területen, és válassza a **Szerkesztés** lehetőséget. 
1. Válassza a **statikus használata** lehetőséget, adja meg a fürt IP-címét, majd kattintson **az OK gombra**: 

   ![Statikus IP-cím megadása a fürt számára](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Győződjön meg arról, hogy a beállítások helyesek, majd kattintson **az OK** gombra a mentéshez:

   ![Fürt tulajdonságainak ellenőrzése](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása 

Ebben a lépésben konfigurálja a tanúsító Felhőbeli megosztást. Ha nem ismeri a lépéseket, tekintse meg a [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürthöz](/windows-server/failover-clustering/deploy-cloud-witness)című témakört. 

## <a name="enable-the-availability-group-feature"></a>A rendelkezésre állási csoport funkciójának engedélyezése 

Ebben a lépésben engedélyezze a rendelkezésre állási csoport szolgáltatást. Ha még nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Kulcsok és tanúsítványok létrehozása

Ebben a lépésben olyan tanúsítványokat hoz létre, amelyeket az SQL-bejelentkezés a titkosított végponton használ. Hozzon létre egy mappát az egyes csomópontokon a tanúsítványok biztonsági másolatának tárolásához, például: `c:\certs` . 

Az első csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Nyissa meg **SQL Server Management Studio** és kapcsolódjon az első csomóponthoz, például: `AGNode1` . 
1. Nyisson meg egy **új lekérdezési** ablakot, és futtassa a következő Transact-SQL (T-SQL) utasítást egy összetett és biztonságos jelszóra való frissítés után:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Ezután hozza létre az HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. A **fájlkezelővel** nyissa meg a fájl helyét, ahol a tanúsítvány, például: `c:\certs` . 
1. A tanúsítvány másolatát manuálisan végezze el, például `AGNode1Cert.crt` az első csomópontról, majd vigye át a második csomópont ugyanazon helyére. 

A második csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Kapcsolódjon a második csomóponthoz **SQL Server Management Studio**, például: `AGNode2` . 
1. Egy **új lekérdezési** ablakban futtassa a következő Transact-SQL (T-SQL) utasítást egy összetett és biztonságos jelszóra való frissítés után: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Ezután hozza létre az HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. A **fájlkezelővel** nyissa meg a fájl helyét, ahol a tanúsítvány, például: `c:\certs` . 
1. A tanúsítvány másolatát manuálisan végezze el, például `AGNode2Cert.crt` a második csomópontról, majd vigye át az első csomóponton található helyre. 

Ha a fürt bármely más csomóponttal rendelkezik, ismételje meg ezeket a lépéseket is, ha módosítja a megfelelő tanúsítvány nevét. 

## <a name="create-logins"></a>Bejelentkezések létrehozása

A tanúsítvány-hitelesítés a csomópontok közötti adatszinkronizálásra szolgál. Ennek engedélyezéséhez hozzon létre egy bejelentkezési azonosítót a másik csomóponthoz, hozzon létre egy felhasználót a bejelentkezéshez, hozzon létre egy tanúsítványt a bejelentkezéshez a biztonsági másolat készítéséhez, majd engedélyezze a kapcsolódást a tükrözési végponton. 

Ehhez először futtassa a következő Transact-SQL (T-SQL) lekérdezést az első csomóponton, például `AGNode1` : 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést a második csomóponton, például `AGNode2` : 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Ha a fürt bármely más csomóponttal rendelkezik, ismételje meg ezeket a lépéseket is, és módosítsa a megfelelő tanúsítvány-és felhasználóneveket. 

## <a name="configure-an-availability-group"></a>Rendelkezésre állási csoport konfigurálása

Ebben a lépésben konfigurálja a rendelkezésre állási csoportot, és adja hozzá az adatbázisokat. Most ne hozzon létre figyelőt. Ha még nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát](availability-group-manually-configure-tutorial.md#create-the-availability-group). Győződjön meg arról, hogy a feladatátvételt és a feladat-visszavételt kezdeményezi annak ellenőrzéséhez, hogy minden megfelelően működik-e. 

   > [!NOTE]
   > Ha a szinkronizálási folyamat során hiba lép fel, lehetséges, hogy rendszergazdai jogosultságokat kell biztosítania a `NT AUTHORITY\SYSTEM` fürterőforrás létrehozásához az első csomóponton, például `AGNode1` ideiglenesen. 

## <a name="configure-a-load-balancer"></a>Terheléselosztó konfigurálása

Ebben az utolsó lépésben konfigurálja a terheléselosztó-t a [Azure Portal](availability-group-load-balancer-portal-configure.md) vagy a [PowerShell](availability-group-listener-powershell-configure.md)használatával.


## <a name="next-steps"></a>Következő lépések

A rendelkezésre állási csoport konfigurálásához az [az SQL VM CLI](./availability-group-az-commandline-configure.md) -t is használhatja.