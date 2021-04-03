---
title: Hálózati biztonság az 1. generációs Azure Data Lake Storage-ben | Microsoft Docs
description: Ismerje meg, hogyan működik a Virtual Network Integration Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108711"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Virtual Network Integration for Azure Data Lake Storage Gen1

Ez a cikk a Azure Data Lake Storage Gen1 virtuális hálózati integrációját mutatja be. A virtuális hálózat integrációjával mostantól úgy állíthatja be fiókjait, hogy azok csak az adott virtuális hálózatokról és alhálózatokról érkező forgalmat fogadják. 

Ezzel a funkcióval megvédheti Data Lake Storage-fiókját a külső fenyegetésektől.

Az 1. generációs Data Lake Storage-fiókok virtuális hálózati integrációja a virtuális hálózat és az Azure Active Directory (AD) közötti virtuális hálózati szolgáltatásvégpont biztonsági funkcióit használja, hogy létrehozza a hozzáférési jogkivonat által tartalmazott további biztonsági jogcímeket. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést.

> [!NOTE]
> A funkció felár nélkül használható. Az 1. generációs Data Lake Storage esetében fiókjánál a standard árat számlázzuk ki. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable) tartalmaz. Az összes többi Ön által használt Azure-szolgáltatásról a [díjszabásban](https://azure.microsoft.com/pricing/#product-picker) talál további információt.

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Forgatókönyvek a virtuális hálózat integrációjához az 1. generációs Data Lake Storage-ban

Az 1. generációs Data Lake Storage virtuális hálózati integrációjánál korlátozhatja adott virtuális hálózatok és alhálózatok hozzáférését az 1. generációs Data Lake Storage-fiókhoz. Miután a fiókot egy megadott virtuális hálózathoz/alhálózathoz kötötte, más Azure-beli virtuális hálózatok vagy virtuális gépek nem férhetnek hozzá. Funkcióit tekintve az 1. generációs Data Lake Storage-fiókok virtuális hálózati integrációja ugyanazt a fogatókönyvet valósítja meg, mint a [virtuális hálózati szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md). Néhány fontos különbség van, amelyeket az alábbi szakaszok ismertetnek. 

![Forgatókönyv-diagram a virtuális hálózat integrációjához az 1. generációs Data Lake Storage-ban](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> A helyszíni hálózatokról való hozzáférések engedélyezéséhez a meglévő IP-tűzfalszabályok is használhatók a virtuális hálózati szabályok mellett. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optimális útválasztás a virtuális hálózat integrációjához az 1. generációs Data Lake Storage-ban

A virtuális hálózati szolgáltatásvégpontok egyik legfőbb előnye a virtuális hálózat felőli [optimális útválasztás](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits). Ugyanezt az optimális útválasztást az 1. generációs Data Lake Storage-fiókoknál is végrehajthatja. Használja az alábbi, [felhasználó által meghatározott útvonalakat](../virtual-network/virtual-networks-udr-overview.md#user-defined) az 1. generációs Data Lake Storage-fiók virtuális hálózatából.

**Data Lake Storage nyilvános IP-címe** – Használja a nyilvános IP-címet az 1. generációs Data Lake Storage-célfiókokhoz. Az 1. generációs Data Lake Storage-fiókok IP-címei a fiókok [DNS-nevének](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) feloldásával azonosíthatók. Hozzon létre külön bejegyzést mindegyik címhez.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Adatok kiszűrése az ügyféloldali virtuális hálózatokról

A Data Lake Storage-fiókok virtuális hálózatok felőli hozzáférésének biztosítása mellett érdemes gondoskodni arról is, hogy ne lehessen adatokat kiszűrni egy jogosulatlan fiókkal.

A virtuális hálózaton használjon egy tűzfalmegoldást, amely a célfiók URL-címe alapján megszűri a kimenő forgalmat. Csak a jóváhagyott 1. generációs Data Lake Storage-fiók számára engedélyezze a hozzáférést.

Néhány elérhető lehetőség:
- [Azure Firewall](../firewall/overview.md): A virtuális hálózathoz [helyezzen üzembe és konfiguráljon egy Azure Firewallt](../firewall/tutorial-firewall-deploy-portal.md). Lássa el védelemmel a kimenő Data Lake Storage-forgalmat, és korlátozza az ismert és jogosult fiók-URL-címekre.
- [Hálózati virtuális berendezés](https://azure.microsoft.com/solutions/network-appliances/) tűzfala: Előfordulhat, hogy a rendszergazda csak bizonyos kereskedelmi tűzfaltermékek használatát engedélyezi. Használjon az Azure Marketplace-en elérhető hálózati virtuális berendezési tűzfalmegoldást, amely elláthatja ugyanezt a funkciót.

> [!NOTE]
> Ha az adatok útvonalán tűzfalat használ, az egy további ugrást jelent az adatok útvonalán. Ez hatással lehet a végpontok közötti hálózati adatátvitel teljesítményére. Az elérhető adatátviteli sebességet és a kapcsolatok késését is befolyásolhatja. 

## <a name="limitations"></a>Korlátozások

- Az új szolgáltatás támogatásához újra létre kell hozni azokat a HDInsight-fürtöket, amelyeket a virtuális hálózat integrációs támogatásának Data Lake Storage Gen1a előtt hoztak létre.
 
- Amikor új HDInsight-fürt létrehozásakor egy 1. generációs Data Lake Storage-fiókot választ ki, amelyen engedélyezve van a virtuális hálózati integráció, a folyamat sikertelen lesz. Elsőként tiltsa le a virtuális hálózati szabályokat. Vagy a Data Lake Storage-fiók **Tűzfal és virtuális hálózatok** panelén válassza a **Hozzáférés engedélyezése minden hálózatról és szolgáltatásról** lehetőséget. Ezután hozza létre a HDInsight-fürtöt, mielőtt ismét engedélyezi a virtuális hálózati szabályt, vagy **letiltja a hozzáférést az összes hálózatról és szolgáltatásról**. További információt a [Kivételek](#exceptions) szakaszban talál.

- Data Lake Storage Gen1 virtuális hálózati integráció nem működik az [Azure-erőforrások felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md).
  
- A virtuális hálózattal integrált 1. generációs Data Lake Storage-fiókokban található fájl- és mappaadatok nem érhetők el a portálról. Ez a korlátozás magában foglalja a virtuális hálózathoz tartozó virtuális gépekről való hozzáférést és az olyan tevékenységeket is, mint az Adatkezelő használata. A fiókfelügyeleti tevékenységek továbbra is működni fognak. A virtuális hálózattal integrált Data Lake Storage-fiókokban található fájl- és mappaadatok minden nem portálalapú erőforrás által elérhetők. Ebbe beletartozik az SDK-hozzáférés, a PowerShell-szkriptek és az egyéb (nem a portálról indított) Azure-szolgáltatások. 

## <a name="configuration"></a>Konfiguráció

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>1. lépés: Konfigurálja a virtuális hálózatot az Azure AD-szolgáltatásvégpont használatára

1.  Nyissa meg az Azure Portalt, és jelentkezzen be fiókjába.
 
2.  [Hozzon létre egy új virtuális hálózatot](../virtual-network/quick-create-portal.md)az előfizetésében. Vagy használhat egy meglévő virtuális hálózatot is. A virtuális hálózatoknak az 1. generációs Data Lake Storage-fiókkal megegyező régióban kell lenniük.
 
3.  A **Virtuális hálózat** panelen válassza a **Szolgáltatásvégpontok** lehetőséget.
 
4.  Válassza a **Hozzáadás** elemet egy új szolgáltatásvégpont hozzáadásához.

    ![Egy virtuális hálózati szolgáltatásvégpont hozzáadása](media/data-lake-store-network-security/config-vnet-1.png)

5.  Válassza ki a **Microsoft.AzureActiveDirectory** szolgáltatást a végponthoz.

     ![A Microsoft.AzureActiveDirectory szolgáltatásvégpont kiválasztása](media/data-lake-store-network-security/config-vnet-2.png)

6.  Válassza ki, mely alhálózatok számára kívánja engedélyezni a csatlakozást. Válassza a **Hozzáadás** lehetőséget.

    ![Alhálózat kiválasztása](media/data-lake-store-network-security/config-vnet-3.png)

7.  A szolgáltatásvégpont hozzáadása akár 15 percet is igénybe vehet. A hozzáadást követően a szolgáltatásvégpont megjelenik a listában. Ellenőrizze, hogy megjelenik-e, és hogy a beállítások megfelelően vannak-e konfigurálva.
 
    ![A szolgáltatásvégpont sikeres hozzáadása](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>2. lépés: Az 1. generációs Data Lake Storage-fiókhoz engedélyezett virtuális hálózat vagy alhálózat beállítása

1.  A virtuális hálózat konfigurálását követően [hozzon létre egy új 1. generációs Azure Data Lake Storage-fiókot](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) az előfizetésében. Vagy használhat egy meglévő 1. generációs Data Lake Storage-fiókot is. Az 1. generációs Data Lake Storage-fióknak a virtuális hálózattal megegyező régióban kell lennie.
 
2.  Válassza a **Tűzfalak és virtuális hálózatok** lehetőséget.

    > [!NOTE]
    > Ha a **Tűzfalak és virtuális hálózatok** elem nem látható a beállítások között, jelentkezzen ki a portálról. Zárja be a böngészőt, és ürítse ki a böngésző gyorsítótárát. Indítsa újra a számítógépet, és próbálkozzon újra.

       ![Virtuális hálózati szabály hozzáadása a Data Lake Storage-fiókhoz](media/data-lake-store-network-security/config-adls-1.png)

3.  Válassza a **Kijelölt hálózatok** lehetőséget.
 
4.  Válassza a **Meglévő virtuális hálózat hozzáadása** elemet.

    ![Meglévő virtuális hálózat hozzáadása](media/data-lake-store-network-security/config-adls-2.png)

5.  Válassza ki azokat a virtuális hálózatokat és alhálózatokat, amelyek számára engedélyezi a csatlakozást. Válassza a **Hozzáadás** lehetőséget.

    ![A virtuális hálózat és alhálózatok kiválasztása](media/data-lake-store-network-security/config-adls-3.png)

6.  Győződjön meg arról, hogy a virtuális hálózatok és alhálózatok megfelelően jelennek meg a listában. Kattintson a **Mentés** gombra.

    ![Az új szabály mentése](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > A beállítások módosításának életbe léptetése akár 5 percig is eltarthat.

7.  [Opcionális] A **Tűzfal és virtuális hálózatok** lap **Tűzfal** szakaszában engedélyezheti a csatlakozást adott IP-címekről. 

## <a name="exceptions"></a>Kivételek
Engedélyezheti a csatlakozást az Azure-szolgáltatásokból vagy a kiválasztott virtuális hálózaton kívüli virtuális gépekről. A **Tűzfal és virtuális hálózatok** panel **Kivételek** területén válasszon a két lehetőség közül:
 
- **Hozzáférés engedélyezése ehhez az 1. generációs Data Lake Storage-fiókhoz az összes Azure-szolgáltatás számára**. Ez a beállítás minden Azure-szolgáltatás (pl.: Azure Data Factory, Azure Event Hubs) és minden Azure-beli virtuális gép számára engedélyezi a kommunikációt a Data Lake Storage-fiókkal.

- **Hozzáférés engedélyezése ehhez az 1. generációs Data Lake Storage-fiókhoz az Azure Data Lake Analytics számára**. Ez a beállítás a Data Lake Analytics számára engedélyezi, hogy csatlakozzon a Data Lake Storage-fiókhoz. 

  ![A tűzfal és a virtuális hálózat kivételei](media/data-lake-store-network-security/firewall-exceptions.png)

Javasoljuk, hogy ezeket a kivételeket tartsa kikapcsolva. Ezeket a kivételeket csak akkor kapcsolja be, ha e szolgáltatások számára a virtuális hálózaton kívülről is biztosítania kell a csatlakozási lehetőséget.