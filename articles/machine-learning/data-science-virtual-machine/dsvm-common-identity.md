---
title: Közös identitás beállítása
titleSuffix: Azure Data Science Virtual Machine
description: Megtudhatja, hogyan hozhat létre olyan általános felhasználói fiókokat, amelyek több adatelemzési Virtual Machinesban is használhatók. Azure Active Directory vagy helyszíni Active Directory használatával hitelesítheti a felhasználókat a Data Science Virtual Machine.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, térinformatikai elemzés, csoportos adatelemzési folyamat
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519728"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Közös identitás beállítása Data Science Virtual Machine

Microsoft Azure virtuális gépen (VM), beleértve a Data Science Virtual Machinet (DSVM), helyi felhasználói fiókokat hozhat létre a virtuális gép kiépítés közben. A felhasználók ezután a hitelesítő adatok használatával hitelesítik magukat a virtuális gépen. Ha több virtuális gépre van szüksége a felhasználóknak, a hitelesítő adatok kezelése nagyon nehézkes lehet. Kiváló megoldás, ha általános felhasználói fiókokat telepít, és a szabványon alapuló identitás-szolgáltatón keresztül végzi a felügyeletet. Ezzel a módszerrel egyetlen hitelesítő adat használatával férhet hozzá több erőforráshoz az Azure-ban, többek között több Dsvm is.

A Active Directory egy népszerű identitás-szolgáltató, és az Azure-ban is támogatott, felhőalapú szolgáltatásként és helyszíni címtárként. A Azure Active Directory (Azure AD) vagy a helyszíni Active Directory használatával hitelesítheti a felhasználókat egy önálló DSVM vagy egy Azure virtuálisgép-méretezési csoportba tartozó Dsvm-fürtön. Ehhez a DSVM-példányokat egy Active Directory tartományhoz kell csatlakoztatnia.

Ha már rendelkezik Active Directory, használhatja azt a közös identitás-szolgáltatóként. Ha nem rendelkezik Active Directoryval, [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS) használatával futtathat egy felügyelt Active Directory-példányt az Azure-ban.

Az [Azure ad](../../active-directory/index.yml) dokumentációja részletes útmutatást nyújt, beleértve az Azure ad és a helyszíni címtár összekapcsolására [vonatkozó](../../active-directory/hybrid/whatis-hybrid-identity.md)útmutatást, ha van ilyen.

Ez a cikk azt ismerteti, hogyan állítható be egy teljes körűen felügyelt Active Directory tartományi szolgáltatás az Azure-ban az Azure AD DS használatával. Ezután csatlakozhat a Dsvm a felügyelt Active Directory tartományhoz. Ez a megközelítés lehetővé teszi a felhasználók számára, hogy a Dsvm (és más Azure-erőforrások) egy közös felhasználói fiókkal és hitelesítő adatokkal férhessenek hozzá.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Teljes körűen felügyelt Active Directory tartomány beállítása az Azure-ban

Az Azure AD DS segítségével egyszerűen kezelheti az identitásait egy teljes körűen felügyelt szolgáltatással az Azure-ban. Ezen a Active Directory tartományban kezelheti a felhasználókat és a csoportokat. Az Azure által üzemeltetett Active Directory tartományi és felhasználói fiókok a címtárban történő beállításához kövesse az alábbi lépéseket:

1. A Azure Portal adja hozzá a felhasználót a Active Directoryhoz: 

   1. Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
   1. Válassza az **Azure Active Directory**, majd a **Felhasználók és csoportok** elemet.
    
   1. A **felhasználók és csoportok** területen válassza a **minden felhasználó** lehetőséget, majd válassza az **új felhasználó** lehetőséget.
   
        Megnyílik a **felhasználó** ablaktábla:
      
        ![A "felhasználó" panel](./media/add-user.png)
    
   1. Adja meg a felhasználó adatait (például **Név** és **Felhasználónév**). A Felhasználónév tartománynév részének a kezdeti alapértelmezett tartománynévnek ([tartománynév]. onmicrosoft. com) vagy egy ellenőrzött, nem összevont [Egyéni tartománynévnek](../../active-directory/fundamentals/add-custom-domain.md) (például "contoso.com") kell lennie.
    
   1. Másolja a vágólapra vagy egyéb módon jegyezze fel a létrehozott jelszót, hogy megadhassa azt a felhasználónak a folyamat befejezése után.
    
   1. Az adatokat a felhasználóhoz tartozó **Profil**, **Csoportok**, vagy **Címtárbeli szerepkör** területeken is megadhatja. 
    
   1. A **felhasználó** területen válassza a **Létrehozás** elemet.
    
   1. Biztonságosan továbbítsa a generált jelszót az új felhasználónak, hogy be tudja jelentkezni.

1. Hozzon létre egy Azure AD DS-példányt. Kövesse a  [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md) (az "példány létrehozása és alapszintű beállítások konfigurálása" című szakaszt). Fontos, hogy frissítse a meglévő felhasználói jelszavakat Active Directoryban, hogy az Azure-AD DS jelszava szinkronizálva legyen. Fontos továbbá, hogy a DNS-t az Azure AD DS-hoz adja hozzá, ahogy az a következő szakaszban is látható: "a mezők befejezése a Azure Portal alapjai ablakban az Azure AD DS-példány létrehozásakor" című részében leírtak szerint.

1. Hozzon létre egy különálló DSVM alhálózatot az előző lépés "virtuális hálózat létrehozása és konfigurálása" szakaszában létrehozott virtuális hálózaton.
1. Hozzon létre egy vagy több DSVM-példányt a DSVM-alhálózatban.
1. Az [utasításokat](../../active-directory-domain-services/join-ubuntu-linux-vm.md) követve adja hozzá a DSVM a Active Directoryhoz. 
1. Csatlakoztassa egy Azure Files-megosztást a saját otthoni vagy jegyzetfüzet-címtárának üzemeltetéséhez, hogy a munkaterület bármely gépen csatlakoztatható legyen. (Ha szűk fájl szintű engedélyekre van szüksége, egy vagy több virtuális gépen futó hálózati fájlrendszerre [NFS-re] van szükség.)

   1. [Hozzon létre egy Azure Files megosztást](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Csatlakoztassa ezt a megosztást a Linux-DSVM. Ha a Azure Portal a Storage-fiókban a **Csatlakozás** a Azure Files megosztáshoz lehetőséget választja, megjelenik a Linux DSVM bash rendszerhéjában futtatandó parancs. A parancs a következőképpen néz ki:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Tegyük fel például, hogy csatlakoztatta a Azure Files-megosztást a/Data/Workspace.-ben Most hozzon létre címtárakat az egyes felhasználók számára a megosztásban:/Data/Workspace/user1,/Data/Workspace/USER2 stb. Hozzon létre egy `notebooks` könyvtárat az egyes felhasználók munkaterületén. 
1. Szimbolikus hivatkozások létrehozása a következőhöz `notebooks` : `$HOME/userx/notebooks/remote` .   

Most már rendelkezik az Azure-ban üzemeltetett Active Directory-példány felhasználóinak. Active Directory hitelesítő adatok használatával a felhasználók bejelentkezhetnek az Azure AD DShoz csatlakoztatott bármely DSVM (SSH vagy JupyterHub). Mivel a felhasználói munkaterület egy Azure Files megosztáson van, a felhasználók a JupyterHub használatakor hozzáférhetnek a notebookokhoz és egyéb munkához bármely DSVM.

Az automatikus skálázáshoz a virtuálisgép-méretezési csoport használatával létrehozhat olyan virtuális gépek készletét, amelyek mind a tartományhoz, mind a megosztott lemezhez csatlakoznak. A felhasználók bejelentkezhetnek bármely elérhető gépre a virtuálisgép-méretezési csoportba, és hozzáférhetnek a megosztott lemezhez, amelyen a jegyzetfüzetek el vannak mentve. 

## <a name="next-steps"></a>Következő lépések

* [Hitelesítő adatok biztonságos tárolása a felhőalapú erőforrások eléréséhez](dsvm-secure-access-keys.md)