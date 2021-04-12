---
title: Külső file Storage használata a labor Servicesben | Microsoft Docs
description: Ismerje meg, hogyan állíthat be olyan labort, amely külső file Storage-t használ a labor Servicesben.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111795"
---
# <a name="using-external-file-storage-in-lab-services"></a>Külső file Storage használata a labor Servicesben

Ez a cikk a külső fájlok tárolásának egyes lehetőségeit tárgyalja Azure Lab Services használatakor.  A [Azure Files](https://azure.microsoft.com/services/storage/files/) a felhőben elérhető teljes körűen felügyelt FÁJLMEGOSZTÁST az [SMB 2,1 és az SMB 3,0 használatával biztosítja.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Egy Azure Files-megosztás nyilvánosan vagy magántulajdonban is csatlakoztatható egy virtuális hálózaton belül.  Azt is beállíthatja, hogy a tanuló AD hitelesítő adatait használja a fájlmegosztáshoz való csatlakozáshoz.  A Azure NetApp Files és a Linux rendszerű gépek NFS-köteteinek használata egy másik lehetőség a külső fájlok tárolásához Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>A használandó megoldás meghatározása

Minden megoldás eltérő követelményekkel és képességekkel rendelkezik.  Az alábbi táblázat felsorolja az egyes megoldások esetében megfontolandó fontos pontokat.  

|     Megoldás    |    Fontos tudni    |
| -------------- | ------------------------ |
| [Azure Files megosztás nyilvános végponttal](#azure-files-share) | <ul><li>Mindenki rendelkezik írási/olvasási hozzáféréssel.</li><li>Nincs szükség vnet-társításra.</li><li>Az összes virtuális gép számára elérhető, nem csak a tesztkörnyezet virtuális gépei.</li><li>Linux használata esetén a tanulók hozzáférhetnek a Storage-fiók kulcsához.</li></ul> |
| [Megosztás Azure Files privát végponttal](#azure-files-share) | <ul><li>Mindenki rendelkezik írási/olvasási hozzáféréssel.</li><li>Vnet-társítás szükséges.</li><li>Csak olyan virtuális gépek számára érhető el, amelyek Storage-fiókként vannak eltárolva ugyanazon a hálózaton (vagy a társ hálózaton).</li><li>Linux használata esetén a tanulók hozzáférhetnek a Storage-fiók kulcsához.</li></ul> |
| [Azure Files identitás-alapú hitelesítéssel](#azure-files-with-identity-base-authorization) | <ul><li>Az olvasási vagy olvasási/írási hozzáférési engedélyek megadhatók a mappákhoz vagy fájlokhoz.</li><li>Vnet-társítás szükséges.</li><li>A Storage-fióknak csatlakoztatva kell lennie Active Directoryhoz.</li><li>A labor virtuális gépeknek tartományhoz kell csatlakozniuk.</li><li>A Storage-fiók kulcsa nem használatos a tanulók számára a fájlmegosztáshoz való kapcsolódáshoz.</li></ul> |
| [NetApp-fájlok NFS-kötetekkel](#netapp-files-with-nfs-volumes) | <ul><li>A kötetek olvasási és olvasási/írási hozzáférése is beállítható.</li><li>Az engedélyek a tanuló virtuális gép IP-címével vannak beállítva.</li><li>Vnet-társítás szükséges.</li><li>Előfordulhat, hogy regisztrálnia kell a NetApp Files szolgáltatás használatához.</li><li>Csak Linux.</li></ul>

A külső tárterület használatának díja nem része a Azure Lab Services használatának.  A díjszabással kapcsolatos további információkért tekintse meg a [Azure Files díjszabását](https://azure.microsoft.com/pricing/details/storage/files/) és a [Azure NetApp Files díjszabását](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Azure Files megosztás

Az Azure-fájlmegosztás nyilvános vagy privát végponton keresztül érhető el.  Az Azure-fájlmegosztás jelszóként a Storage-fiók kulcsa alapján van csatlakoztatva.  Ezzel a módszerrel mindenki rendelkezik írási és olvasási hozzáféréssel a fájlmegosztás számára.

Ha nyilvános végpontot használ a Azure Files megosztáshoz, fontos megjegyezni:

- A Storage-fiók virtuális hálózatát nem kell összeállítani a labor-fiókkal.  A fájlmegosztás bármikor létrehozható a sablon virtuális gép közzétételének megkezdése előtt.
- A fájlmegosztás bármely gépről elérhető, ha a felhasználó rendelkezik a Storage-fiók kulcsával.  
- A Linux-tanulók láthatják a Storage-fiók kulcsát.  Azure Files-megosztás csatlakoztatásához szükséges hitelesítő adatokat Linux rendszerű `{file-share-name}.cred` virtuális gépeken tárolják, és a sudo által olvashatók.  Mivel a tanulók alapértelmezés szerint az Azure Lab Service-beli virtuális gépeken keresztül kapják meg a sudo hozzáférését, elolvashatják a Storage-fiók kulcsát.  Ha a Storage-fiók végpontja nyilvános, a diákok hozzáférhetnek a saját diák virtuális gépén kívüli fájlmegosztás eléréséhez.  Érdemes elforgatni a Storage-fiók kulcsát az osztály befejezése után, és privát fájlmegosztás használatával.

Ha privát végpontot használ a Azure Files megosztáshoz, fontos megjegyezni:

- A hozzáférés a magánhálózaton kívüli forgalomra korlátozódik, és a nyilvános interneten keresztül nem érhető el.  Csak a magánhálózati virtuális hálózatban lévő virtuális gépek, a magánhálózati virtuális hálózattal vagy a magánhálózaton VPN-hez csatlakozó számítógépek a fájlmegosztás elérésére használhatók.  
- A Linux-tanulók láthatják a Storage-fiók kulcsát.  Azure Files-megosztás csatlakoztatásához szükséges hitelesítő adatokat Linux rendszerű `{file-share-name}.cred` virtuális gépeken tárolják, és a sudo által olvashatók.  Mivel a tanulók alapértelmezés szerint az Azure Lab Service-beli virtuális gépeken keresztül kapják meg a sudo hozzáférését, elolvashatják a Storage-fiók kulcsát.  A Storage-fiók kulcsát az osztály befejezése után érdemes elforgatni.
- Ehhez a megközelítéshez a fájlmegosztás virtuális hálózatának egyenrangúnak kell lennie a labor-fiókhoz.  Az Azure Storage-fiók virtuális hálózatát a tesztkörnyezet létrehozása **előtt** a labor-fiók virtuális hálózatának kell megadnia.

> [!NOTE]
> A 5TB-nél nagyobb fájlmegosztás csak a [[helyileg redundáns tárolás (LRS) fiókok](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions)esetében érhető el].

Az alábbi lépéseket követve létrehozhat egy Azure-fájlmegosztást csatlakoztató virtuális gépet.

1. Hozzon létre egy [Azure Storage-fiókot](/azure/storage/files/storage-how-to-create-file-share). A kapcsolati módszer lapon válassza a nyilvános végpont vagy a magánhálózati végpont lehetőséget.
2. A használata esetén hozzon létre egy [privát végpontot](/azure/private-link/create-private-endpoint-storage-portal) ahhoz, hogy a fájlmegosztás elérhető legyen a virtuális hálózatról.  Hozzon létre egy [magánhálózati DNS-zónát](/azure/dns/private-dns-privatednszone) , vagy használjon egy meglévőt. A magánhálózati Azure DNS zónák névfeloldást biztosítanak a virtuális hálózaton belül.
3. Hozzon létre egy [Azure-fájlmegosztást](/azure/storage/files/storage-how-to-create-file-share). A fájlmegosztást a Storage-fiók nyilvános állomásneve érheti el.
4. Csatlakoztassa az Azure-fájlmegosztást a sablon virtuális géphez:
    - [Windows](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Lásd: a [Azure Files és a Linux használata](#using-azure-files-with-linux) a tanulói virtuális gépek csatlakoztatási problémáinak elkerüléséhez.
5. [Tegye közzé](how-to-create-manage-template.md#publish-the-template-vm) a sablon virtuális gépet.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tűzfal nem blokkolja a kimenő SMB-kapcsolatokat a 445-es porton keresztül. Alapértelmezés szerint az SMB engedélyezve van az Azure-beli virtuális gépek számára.

### <a name="using-azure-files-with-linux"></a>Azure Files használata Linux rendszeren

Ha az _alapértelmezett utasításokat_ használja az Azure-fájlmegosztás csatlakoztatásához, úgy tűnik, hogy a fájlmegosztás a sablon közzétételekor eltűnik a tanuló virtuális gépeken.  Alább látható a probléma megoldására szolgáló módosított parancsfájl.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Ha a sablon virtuális gépe már közzé van téve, amely az Azure-fájlmegosztást a `/mnt` címtárhoz csatlakoztatja, a tanuló a következők egyikét teheti meg.

- Helyezze át az utasítást a `/mnt` fájl elejére a csatlakoztatáshoz `/etc/fstab` .  
- Módosítsa az utasítást úgy, hogy a következőhöz `/mnt/{file-share-name}` hasonló könyvtárba csatlakoztassa `/prm-mnt/{file-share-name}` .

A tanulóknak `mount -a` a címtárak újracsatlakoztatását kell futtatniuk.

További általános információk a fájlmegosztás Linux rendszeren való használatáról: [a Azure Files használata Linux rendszeren](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Azure Files identitás-alapú hitelesítéssel

Azure Files megosztások az AD-hitelesítés használatával is elérhetők, ha

1. A tanuló virtuális gép tartományhoz van csatlakoztatva.
2. Az AD-hitelesítés engedélyezve van a fájlmegosztást tároló [Azure Storage-fiókban](/azure/storage/files/storage-files-active-directory-overview) .  

A hálózati meghajtó a felhasználó identitásának használatával van csatlakoztatva a virtuális géphez, nem pedig a Storage-fiók kulcsa.  A Storage-fiók eléréséhez nyilvános vagy magánhálózati végpontok is használhatók.

Nézzük át a megközelítés néhány fontos pontját.

- Az engedélyek könyvtár vagy fájl szintjén állíthatók be.
- A rendszer az aktuális felhasználói hitelesítő adatokat használja a fájlmegosztás hitelesítésére.

Ha nyilvános végpontot használ a Azure Files megosztáshoz, fontos megjegyezni:

- A Storage-fiók virtuális hálózatának nem kell egyenrangúnak lennie a labor-fiókhoz.  A fájlmegosztás bármikor létrehozható a sablon virtuális gép közzétételének megkezdése előtt.

Ha privát végpontot használ a Azure Files megosztáshoz, fontos megjegyezni:

- A hozzáférés a magánhálózaton kívüli forgalomra korlátozódik, és a nyilvános interneten keresztül nem érhető el.  Csak a magánhálózati virtuális hálózatban lévő virtuális gépek, a magánhálózati virtuális hálózattal vagy a magánhálózaton VPN-hez csatlakozó számítógépek a fájlmegosztás elérésére használhatók.  
- Ehhez a megközelítéshez a fájlmegosztás virtuális hálózatának egyenrangúnak kell lennie a labor-fiókhoz.  Az Azure Storage-fiók virtuális hálózatát a tesztkörnyezet létrehozása **előtt** a labor-fiók virtuális hálózatának kell megadnia.

Az alábbi lépéseket követve hozzon létre egy Active Directory-hitelesítést, Azure Files a megosztást, és csatlakozzon a labor virtuális gépekhez.

1. Hozzon létre egy [Azure Storage-fiókot](/azure/storage/files/storage-how-to-create-file-share).
2. A használata esetén hozzon létre egy [privát végpontot](/azure/private-link/create-private-endpoint-storage-portal) ahhoz, hogy a fájlmegosztás elérhető legyen a virtuális hálózatról.  Hozzon létre egy [magánhálózati DNS-zónát](/azure/dns/private-dns-privatednszone) , vagy használjon egy meglévőt. A magánhálózati Azure DNS zónák névfeloldást biztosítanak a virtuális hálózaton belül.
3. Hozzon létre egy [Azure-fájlmegosztást](/azure/storage/files/storage-how-to-create-file-share).
4. Az identitás-alapú hitelesítés engedélyezéséhez kövesse az alábbi lépéseket.  Ha az Azure AD-vel szinkronizált helyszíni AD-t használ, kövesse a helyszíni [Active Directory Domain Services hitelesítés SMB-en keresztül az Azure-fájlmegosztás esetében](/azure/storage/files/storage-files-identity-auth-active-directory-enable)című témakör lépéseit.  Ha csak az Azure AD-t használja, hajtsa végre a következő lépéseket a [Azure Files Azure Active Directory Domain Services hitelesítésének engedélyezéséhez](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Beszéljen az AD-t kezelő csapattal, és ellenőrizze, hogy teljesülnek-e az utasításokban felsorolt előfeltételek.
5. SMB-megosztás engedélyezési szerepköreinek kiosztása az Azure-ban.  Az egyes szerepkörökhöz megadott engedélyekkel kapcsolatos részletekért lásd: [megosztási szintű engedélyek](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. A "Storage file-adat SMB-megosztás emelt szintű közreműködői" szerepkört hozzá kell rendelni ahhoz a személyhez vagy csoporthoz, amely a fájlmegosztás tartalmára vonatkozó engedélyeket állít be.
    2. A "Storage file-adat SMB-megosztási közreműködői" szerepkört olyan diákokhoz kell rendelni, amelyeknek a fájlmegosztás fájljait kell felvenniük vagy szerkeszteniük.
    3. A "Storage file-adatok SMB-megosztásának olvasója" szerepkört olyan diákokhoz kell rendelni, amelyeknek csak a fájlmegosztás fájljait kell beolvasniuk.
6. Adja meg a fájlmegosztás könyvtár-vagy fájl szintű engedélyeit.  Az engedélyeket olyan tartományhoz csatlakoztatott gépről kell beállítani, amely hálózati hozzáféréssel rendelkezik a fájlmegosztás számára.  **A könyvtár/fájl szintű engedélyek módosításához csatlakoztassa a fájlmegosztást a tárolási kulccsal, ne a HRE hitelesítő adataival.**  [ACL beállítása](/powershell/module/microsoft.powershell.security/set-acl) A PowerShell-parancs vagy a [icacls](/windows-server/administration/windows-commands/icacls) az engedélyek kiosztásakor használandó ajánlott eszközök.
7. A Storage-fiókhoz tartozó [virtuális hálózat](how-to-connect-peer-virtual-network.md) társítása a labor-fiókhoz.
8. [Hozza létre az osztályterem labort](how-to-manage-classroom-labs.md).
9. Mentse egy parancsfájlt a sablon virtuális gépre, amelyet a tanulók futtathatnak a hálózati meghajtóhoz való kapcsolódáshoz.  Példa a szkript beszerzésére:
    1. Nyissa meg a tárfiókot az Azure Portalon.
    1. A menüben válassza **a fájlmegosztás lehetőséget** a **file Service** alatt.
    1. Keresse meg azt a megosztást, amelyhez csatlakozni szeretne, válassza a jobb szélen található ellipszisek gombot, és válassza a **Kapcsolódás** lehetőséget.
    1. A **csatlakozási** menet megnyílik a Windows, Linux és MacOS rendszerekre vonatkozó utasításokkal.  Ha Windows rendszert használ, állítsa a **hitelesítési módszert** **Active Directoryra**.
    1. Másolja a példában szereplő kódot, és mentse a sablonban található gépre a Windows vagy a Linux rendszerhez készült fájlokban `.ps1` `.sh` .
10. A sablon gépen töltse le és futtassa a szkriptet a [tanulói gépek tartományhoz való csatlakoztatásához](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  A `Join-AzLabADTemplate` parancsfájl automatikusan [közzéteszi a sablon virtuális gépet](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > A sablon számítógépe nem lesz tartományhoz csatlakoztatva. Az oktatóknak hozzá kell rendelniük a közzétett tanulói virtuális gépet a megosztásban található fájlok megtekintéséhez.
11. A Windowst használó tanulók a fájlmegosztás elérési útjának megadása után kapcsolódhatnak a Azure Files megosztáshoz a [fájlkezelőben](/azure/storage/files/storage-how-to-use-files-windows) a hitelesítő adataik használatával.  Másik lehetőségként a tanulók a fent létrehozott szkriptet futtathatják a hálózati meghajtóhoz való kapcsolódáshoz.  A Linux rendszert használó diákok számára futtassa a fent létrehozott szkriptet.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp-fájlok NFS-kötetekkel

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) egy nagyvállalati szintű, nagy teljesítményű, mért file Storage szolgáltatás.

- A hozzáférési szabályzatok mennyiségi szinten is megadhatók.
- Az engedélyezési házirendek IP-alapúak minden kötethez.
- Ha a tanulóknak saját kötetre van szükségük, amelyhez a többi tanulónak nincs hozzáférése, a tesztkörnyezet közzétételét követően hozzá kell rendelni a jogosultsági házirendeket.
- Azure Lab Services kontextusában csak a Linux rendszerű gépek támogatottak.
- A Azure NetApp Files kapacitás készletének virtuális hálózatát a tesztkörnyezet létrehozása **előtt** a labor fiók virtuális hálózatának kell megadnia.

Az alábbi lépések végrehajtásával Azure NetApp Files megosztást használhat a Azure Lab Servicesban.

1. Szükség esetén [Azure NetApp Files](https://aka.ms/azurenetappfiles)beléptetése.
2. A NetApp-fájlok kapacitása és az NFS-kötet (ek) létrehozásával kapcsolatban lásd: [Azure NetApp Files és NFS-kötet beállítása](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  További információ a szolgáltatási szintekről: [Azure NetApp Files szolgáltatási szintjei](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. A NetApp Files Capacity-készlet [virtuális hálózatának](how-to-connect-peer-virtual-network.md) társítása a labor-fiókhoz.
4. [Hozza létre az osztályterem labort](how-to-manage-classroom-labs.md).
5. A sablon virtuális gépen telepítse az NFS-fájlmegosztás használatához szükséges összetevőket.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. A sablon virtuális gépen mentse az alábbi szkriptet `mount_fileshare.sh` a [NetApp-fájlok megosztásának csatlakoztatásához](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Rendeljen hozzá egy `capacity_pool_ipaddress` változót a kapacitási készlet csatlakoztatási cél IP-címéhez.  A megfelelő érték megkereséséhez szerezze be a kötet csatlakoztatási utasításait.  A parancsfájl a NetApp-fájlok kötetének elérési útját/nevét várja.  Ne felejtse el futtatni `chmod u+x mount_fileshare.sh` a parancsfájlt a felhasználók által végrehajtható parancsfájlok biztosításához.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Ha minden diák megosztja a hozzáférést ugyanahhoz a NetApp-fájlhoz, a `mount_fileshare.sh` szkript a közzététel előtt futtatható a sablon gépen.  Ha a tanulók mindegyike saját kötetet kap, mentse a szkriptet később a tanulók számára.
8. [Tegye közzé](how-to-create-manage-template.md#publish-the-template-vm) a sablon virtuális gépet.
9. A fájlmegosztás [házirendjének konfigurálása](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) .  Az exportálási házirend lehetővé teszi, hogy egy adott virtuális gép vagy több virtuális gép hozzáférhessen egy kötethez.  Csak olvasási vagy írási/olvasási hozzáférés adható meg.
10. A tanulóknak el kell indítaniuk a virtuális gépet, és futtatniuk kell a parancsfájlt a fájlmegosztás csatlakoztatásához.  Csak egyszer kell futtatniuk a parancsfájlt.  A parancs a következőhöz hasonlóan fog kinézni: `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)