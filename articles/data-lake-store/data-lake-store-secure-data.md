---
title: A Azure Data Lake Storage Gen1 tárolt adatvédelem védelme | Microsoft Docs
description: Megtudhatja, hogyan védheti meg Azure Data Lake Storage Gen1 a csoportok és hozzáférés-vezérlési listák használatával az adatvédelmet
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370695"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme
A Azure Data Lake Storage Gen1ban lévő adatvédelme egy három lépésből álló megközelítés.  Az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a hozzáférés-vezérlési listákat (ACL-eket) úgy kell beállítani, hogy teljes mértékben engedélyezze a felhasználók és biztonsági csoportok számára az adathozzáférést.

1. Első lépésként hozzon létre biztonsági csoportokat a Azure Active Directoryban (Azure AD). Ezek a biztonsági csoportok az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) megvalósítására szolgálnak a Azure Portal. További információ: [Azure RBAC](../role-based-access-control/role-assignments-portal.md).
2. Rendelje hozzá az Azure AD biztonsági csoportokat a Data Lake Storage Gen1-fiókhoz. Ez szabályozza a Data Lake Storage Gen1 fiók hozzáférését a portálról vagy API-kból a portálról és a felügyeleti műveletekről.
3. Rendelje hozzá az Azure AD biztonsági csoportokat hozzáférés-vezérlési listákként (ACL) a Data Lake Storage Gen1 fájlrendszerben.
4. Emellett beállíthat egy IP-címtartományt is olyan ügyfelek számára, akik hozzáférhetnek a Data Lake Storage Gen1 található adatelérési pontokhoz.

Ez a cikk azt ismerteti, hogyan használható a Azure Portal a fenti feladatok végrehajtásához. Részletes információk arról, hogy a Data Lake Storage Gen1 hogyan valósítja meg a biztonságot a fiók és az adatok szintjén, lásd: [Biztonság a Azure Data Lake Storage Gen1ban](data-lake-store-security-overview.md). Az ACL-ek Data Lake Storage Gen1-ben való megvalósításával kapcsolatos részletes információkért lásd: [a Data Lake Storage Gen1 Access Control áttekintése](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg az Ismerkedés [a Azure Data Lake Storage Gen1rel](data-lake-store-get-started-portal.md) című témakört.

## <a name="create-security-groups-in-azure-active-directory"></a>Biztonsági csoportok létrehozása a Azure Active Directoryban
Az Azure AD biztonsági csoportok létrehozásával és a felhasználók csoportba való felvételével kapcsolatos útmutatásért lásd: [biztonsági csoportok kezelése Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Az Azure AD-ben a Azure Portal használatával is hozzáadhat felhasználókat és más csoportokat egy csoporthoz. Ahhoz azonban, hogy egy egyszerű szolgáltatásnevet hozzá lehessen adni egy csoporthoz, használja az [Azure ad PowerShell-modulját](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Felhasználók vagy biztonsági csoportok társítása Data Lake Storage Gen1 fiókokhoz
Amikor felhasználókat vagy biztonsági csoportokat rendel hozzá Data Lake Storage Gen1 fiókokhoz, a Azure Portal és a Azure Resource Manager API-k használatával vezérelheti a fiók felügyeleti műveleteinek elérését. 

1. Nyisson meg egy Data Lake Storage Gen1 fiókot. A bal oldali panelen kattintson a **minden erőforrás** lehetőségre, majd a minden erőforrás panelen kattintson arra a fiók nevére, amelyhez felhasználót vagy biztonsági csoportot szeretne rendelni.

2. A Data Lake Storage Gen1-fiók panelen kattintson a **Access Control (iam)** elemre. A panel alapértelmezés szerint a tulajdonosként listázza az előfizetés tulajdonosait.
   
    ![Biztonsági csoport kiosztása Azure Data Lake Storage Gen1-fiókhoz](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Biztonsági csoport kiosztása Azure Data Lake Storage Gen1-fiókhoz")

3. A **Access Control (iam)** panelen kattintson a **Hozzáadás** gombra az **engedélyek hozzáadása** panel megnyitásához. Az **engedélyek hozzáadása** panelen válasszon ki egy **szerepkört** a felhasználó/csoport számára. Keresse meg a korábban létrehozott biztonsági csoportot Azure Active Directory és jelölje ki. Ha sok felhasználóval és csoporttal rendelkezik a kereséshez, a csoport nevére való szűréshez használja a **Select** szövegmezőt. 
   
    ![Szerepkör hozzáadása a felhasználóhoz](./media/data-lake-store-secure-data/adl.add.user.1.png "Szerepkör hozzáadása a felhasználóhoz")
   
    A **tulajdonos** és a **közreműködő** szerepkör számos felügyeleti funkcióhoz biztosít hozzáférést a Microsoft Access Lake-fiókban. Azok a felhasználók, akik a Information Lake-ben lévő adatokkal működnek, de még meg kell tekinteniük a fiókkezelés adatait, hozzáadhatják őket az **olvasó** szerepkörhöz. Ezeknek a szerepköröknek a hatóköre az Data Lake Storage Gen1-fiókhoz kapcsolódó felügyeleti műveletekre korlátozódik.
   
    Az adatműveletek esetében egyéni fájlrendszerbeli engedélyek határozzák meg, hogy a felhasználók mit tehetnek. Ezért az olvasó szerepkörrel rendelkező felhasználók csak a fiókhoz társított felügyeleti beállításokat tekinthetik meg, de a hozzájuk rendelt fájlrendszer-engedélyek alapján is olvashatók és írhatók. Data Lake Storage Gen1 fájlrendszerbeli engedélyeket [a biztonsági csoport társítása ACL-ként a Azure Data Lake Storage Gen1 fájlrendszerhez](#filepermissions)című témakörben talál.

    > [!IMPORTANT]
    > Csak a **tulajdonosi** szerepkör engedélyezi automatikusan a fájlrendszer-hozzáférést. A **közreműködő**, az **olvasó** és az összes többi szerepkör hozzáférés-vezérlési listákat igényel a mappákhoz és fájlokhoz való hozzáférés bármely szintjének engedélyezéséhez.  A **tulajdonosi** szerepkör olyan felügyelői fájl-és mappaengedélyek-engedélyeket biztosít, amelyeket nem lehet felülbírálni az ACL-eken keresztül. Az Azure RBAC-szabályzatok az adatokhoz való hozzáférésével kapcsolatos további információkért lásd: [Azure RBAC for Account Management](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. Ha olyan csoportot/felhasználót szeretne hozzáadni, amely nem szerepel az **engedélyek hozzáadása** panelen, meghívja őket úgy, hogy beírja az e-mail-címét a **kijelölés** szövegmezőbe, majd kiválasztja őket a listából.
   
    ![Biztonsági csoport hozzáadása](./media/data-lake-store-secure-data/adl.add.user.2.png "Biztonsági csoport hozzáadása")
   
5. Kattintson a **Mentés** gombra. A hozzáadott biztonsági csoport az alábbi módon jelenik meg.
   
    ![Biztonsági csoport hozzáadva](./media/data-lake-store-secure-data/adl.add.user.3.png "Biztonsági csoport hozzáadva")

6. A felhasználó/biztonsági csoport most már rendelkezik hozzáféréssel a Data Lake Storage Gen1 fiókhoz. Ha bizonyos felhasználók számára szeretne hozzáférést biztosítani, a biztonsági csoportba is felveheti őket. Hasonlóképpen, ha egy felhasználó hozzáférését vissza kívánja vonni, eltávolíthatja őket a biztonsági csoportból. Egy fiókhoz több biztonsági csoportot is hozzárendelhet. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Felhasználók vagy biztonsági csoportok társítása ACL-ként a Data Lake Storage Gen1 fájlrendszerhez
Ha felhasználói/biztonsági csoportokat rendel hozzá a Data Lake Storage Gen1 fájlrendszerhez, a Data Lake Storage Gen1 tárolt adathozzáférés-vezérlést kell megadnia.

1. A Data Lake Storage Gen1-fiók paneljén kattintson az **adatkezelő** elemre.
   
    ![Az adatmegjelenítés Adatkezelő használatával](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Az adatmegjelenítés Adatkezelő használatával")
2. A **adatkezelő** panelen kattintson arra a mappára, amelyhez konfigurálni szeretné az ACL-t, majd kattintson a **hozzáférés** elemre. Ha ACL-eket szeretne hozzárendelni egy fájlhoz, először a fájlra kell kattintania az előnézethez, majd a **fájl előnézete panelen** kattintson a **hozzáférés** elemre.
   
    ![ACL-ek beállítása Data Lake Storage Gen1 fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "ACL-ek beállítása Data Lake Storage Gen1 fájlrendszerben")
3. A **hozzáférés** panel felsorolja azokat a tulajdonosokat és hozzárendelt engedélyeket, amelyek már hozzá vannak rendelve a gyökérkönyvtárhoz. További hozzáférési ACL-ek hozzáadásához kattintson a **Hozzáadás** ikonra.
    > [!IMPORTANT]
    > Egyetlen fájl hozzáférési engedélyeinek beállítása nem feltétlenül biztosít felhasználói vagy csoportos hozzáférést ehhez a fájlhoz. A fájl elérési útjának elérhetőnek kell lennie a hozzárendelt felhasználó/csoport számára. További információkért és példákért tekintse [meg az engedélyekkel kapcsolatos gyakori forgatókönyveket](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Szabványos és egyéni hozzáférés listázása](./media/data-lake-store-secure-data/adl.acl.2.png "Szabványos és egyéni hozzáférés listázása")
   
   * A **tulajdonosok** és **mindenki más** Unix-stílusú hozzáférést biztosít, ahol megadhatja az olvasási, írási, végrehajtási (rwx) három különböző felhasználói osztályt: tulajdonos, csoport és egyebek.
   * A **hozzárendelt engedélyek** a POSIX ACL-ek, amelyek lehetővé teszik a fájl tulajdonosán vagy csoportján kívüli meghatározott felhasználók vagy csoportok engedélyeinek beállítását. 
     
     További információ: [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)-EK. Az ACL-ek Data Lake Storage Gen1-ben való megvalósításával kapcsolatos további információkért lásd: [Access Control Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kattintson a **Hozzáadás** ikonra az **engedélyek kiosztása** panel megnyitásához. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása** elemre, majd a **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott biztonsági csoportot Azure Active Directory. Ha sok csoporttal rendelkezik a kereséshez, a felül található szövegmezővel szűrheti a csoport nevét. Kattintson a hozzáadni kívánt csoportra, majd a **kiválasztás** elemre.
   
    ![Csoport hozzáadása](./media/data-lake-store-secure-data/adl.acl.3.png "Csoport hozzáadása")
5. Kattintson az **engedélyek kiválasztása** elemre, jelölje ki az engedélyeket, hogy az engedélyeket rekurzív módon kell-e alkalmazni, és hogy szeretné-e hozzárendelni az engedélyeket hozzáférési ACL-ként, alapértelmezett ACL-ként vagy mindkettőként. Kattintson az **OK** gombra.
   
    ![Képernyőkép az engedélyek kiosztása panelről, a kinevezett engedélyek kiválasztása lehetőséggel és az engedélyek kiválasztása panelen, az OK lehetőség kiválasztásával.](./media/data-lake-store-secure-data/adl.acl.4.png "Engedélyek kiosztása a csoportnak")
   
    A Data Lake Storage Gen1 engedélyeivel és az alapértelmezett/hozzáférési ACL-ekkel kapcsolatos további információkért lásd: [Access Control Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Miután rákattintott az **OK gombra** az **engedélyek kiválasztása** panelen, az újonnan hozzáadott csoport és a társított engedélyek mostantól megjelennek a **hozzáférési** panelen.
   
    ![Képernyőkép a hozzáférés panelről, az adatmérnöki lehetőséggel.](./media/data-lake-store-secure-data/adl.acl.5.png "Engedélyek kiosztása a csoportnak")
   
   > [!IMPORTANT]
   > A jelenlegi kiadásban a **hozzárendelt engedélyek** alatt legfeljebb 28 bejegyzés adható meg. Ha több mint 28 felhasználót szeretne hozzáadni, hozzon létre biztonsági csoportokat, adja hozzá a felhasználókat a biztonsági csoportokhoz, adja hozzá a hozzáférést a Data Lake Storage Gen1 fiókhoz a biztonsági csoportokhoz.
   > 
   > 
7. Ha szükséges, a hozzáférési engedélyeket a csoport hozzáadása után is módosíthatja. Törölje vagy jelölje be a jelölőnégyzetet az egyes engedélyek típusaihoz (olvasás, írás, végrehajtás) attól függően, hogy el kívánja-e távolítani vagy hozzárendelni az engedélyt a biztonsági csoporthoz. Kattintson a **Save (Mentés** ) gombra a módosítások mentéséhez, vagy a módosítások visszavonásához. 

## <a name="set-ip-address-range-for-data-access"></a>IP-címtartomány beállítása adateléréshez
Data Lake Storage Gen1 lehetővé teszi, hogy hálózati szinten tovább zárolja az adattárhoz való hozzáférést. Engedélyezheti a tűzfalat, megadhat egy IP-címet, vagy megadhat egy IP-címtartományt a megbízható ügyfelek számára. Ha engedélyezve van, csak a megadott tartományon belüli IP-címmel rendelkező ügyfelek csatlakozhatnak az áruházhoz.

![Tűzfalbeállítások és IP-hozzáférés](./media/data-lake-store-secure-data/firewall-ip-access.png "Tűzfalbeállítások és IP-cím")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Biztonsági csoportok eltávolítása egy Data Lake Storage Gen1 fiókhoz
Ha Data Lake Storage Gen1 fiókokból távolítja el a biztonsági csoportokat, akkor csak a Azure Portal és Azure Resource Manager API-k használatával módosítja a fiók felügyeleti műveleteihez való hozzáférést.  

Az adathozzáférés nem változik, és továbbra is a hozzáférési ACL-ek kezelik.  Ez alól kivételt képeznek a tulajdonosi szerepkörben lévő felhasználók/csoportok.  A tulajdonosi szerepkörből eltávolított felhasználók és csoportok már nem felügyelők, és a hozzáférésük a hozzáférés-VEZÉRLÉSi beállításokhoz is vissza fog térni. 

1. A Data Lake Storage Gen1-fiók panelen kattintson a **Access Control (iam)** elemre. 
   
    ![Biztonsági csoport kiosztása Data Lake Storage Gen1-fiókhoz](./media/data-lake-store-secure-data/adl.select.user.icon.png "Biztonsági csoport kiosztása Data Lake Storage Gen1-fiókhoz")
2. A **Access Control (iam)** panelen kattintson az eltávolítani kívánt biztonsági csoport (ok) ra. Kattintson az **Eltávolítás** elemre.
   
    ![Biztonsági csoport eltávolítva](./media/data-lake-store-secure-data/adl.remove.group.png "Biztonsági csoport eltávolítva")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Biztonsági csoport ACL-listáinak eltávolítása Data Lake Storage Gen1 fájlrendszerből
Ha a biztonsági csoport ACL-jeit egy Data Lake Storage Gen1 fájlrendszerből távolítja el, akkor a Data Lake Storage Gen1-fiókban lévő adatokhoz való hozzáférést kell megváltoztatnia.

1. A Data Lake Storage Gen1-fiók paneljén kattintson az **adatkezelő** elemre.
   
    ![Könyvtárak létrehozása Data Lake Storage Gen1 fiókban](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Könyvtárak létrehozása Data Lake Storage Gen1 fiókban")
2. A **adatkezelő** panelen kattintson arra a mappára, amelyről el szeretné távolítani az ACL-t, majd kattintson a **hozzáférés** elemre. A fájlokhoz tartozó ACL-ek eltávolításához először a fájlra kell kattintania, majd a **fájl előnézete panelen** kattintson a **hozzáférés** elemre. 
   
    ![ACL-ek beállítása Data Lake Storage Gen1 fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "ACL-ek beállítása Data Lake Storage Gen1 fájlrendszerben")
3. A **hozzáférés** panelen kattintson az eltávolítani kívánt biztonsági csoportra. A **hozzáférési részletek** panelen kattintson az **Eltávolítás** elemre.
   
    ![Képernyőkép a hozzáférés panelről, az adatmérnöki lehetőséggel és a hozzáférés részletei panelen, az Eltávolítás lehetőséggel.](./media/data-lake-store-secure-data/adl.remove.acl.png "Engedélyek kiosztása a csoportnak")

## <a name="see-also"></a>Lásd még
* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az 1. generációs Data Lake Storage használatának első lépései a PowerShell-lel](data-lake-store-get-started-powershell.md)
* [A Data Lake Storage Gen1 használatának első lépései a .NET SDK-val](data-lake-store-get-started-net-sdk.md)
* [Diagnosztikai naplók elérése Data Lake Storage Gen1hoz](data-lake-store-diagnostic-logs.md)
