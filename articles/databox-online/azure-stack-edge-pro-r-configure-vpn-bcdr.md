---
title: Az üzletmenet-folytonosság és a vész-helyreállítási (BCDR) konfigurálása Azure Stack Edge virtuális magánhálózat (VPN) számára
description: Útmutató a BCDR konfigurálásához Azure Stack Edge VPN-en.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367690"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Az üzleti folytonosság és a vész-helyreállítás konfigurálása Azure Stack Edge VPN-hez

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az üzletmenet folytonosságát és a vész-helyreállítási (BCDR) szolgáltatást egy Azure Stack Edge-eszközön konfigurált virtuális magánhálózati (VPN) hálózaton.

Ez a cikk a Azure Stack Edge Pro R és a Azure Stack Edge mini R eszközre egyaránt vonatkozik.

## <a name="configure-failover-to-a-paired-region"></a>Feladatátvétel konfigurálása párosított régióra

Az Azure Stack Edge-eszköz más Azure-szolgáltatásokat használ, például az Azure Storage-t. A BCDR az Azure Stack Edge-eszköz által használt bármely adott Azure-szolgáltatáshoz konfigurálhatja. Ha Azure Stack Edge által használt Azure-szolgáltatás feladatátvételt hajt végre a párosított régiójában, akkor az Azure Stack Edge-eszköz most csatlakozik az új IP-címekhez, és a kommunikáció nem lesz kétszer titkosítva. 

Az Azure Stack Edge-eszköz megosztott bújtatást használ, és a hazai régióban konfigurált összes adat és szolgáltatás (az Azure Stack Edge-eszközhöz társított régió) a VPN-alagúton halad át. Ha az Azure-szolgáltatások feladatátvételt hajtanak végre a hazai régión kívüli párosított régión, akkor az adat többé nem lesz a VPN-en keresztül, ezért a rendszer nem titkosítja. 

Ebben a forgatókönyvben általában csak néhány Azure-szolgáltatást érint. A probléma megoldásához a következő módosításokat kell elvégezni a Azure Stack Edge VPN-konfigurációban:

1. Adja hozzá a feladatátvételi Azure szolgáltatás IP-tartományát a VPN-hez Azure Stack peremhálózati útvonalon. A szolgáltatások ezután elkezdik a VPN-en keresztüli útválasztást.

    A befogadó útvonalak hozzáadásához le kell töltenie a szolgáltatás-specifikus útvonalakkal rendelkező JSON-fájlt. Ügyeljen arra, hogy a fájlt az új útvonalakkal frissítse.
2. Adja hozzá a megfelelő Azure-szolgáltatások IP-tartományát (ka) t az Azure Route táblában.
3. Adja hozzá az útvonalakat a tűzfalhoz.

> [!NOTE]
>
> 1. Az Azure VPN Gateway és az Azure Virtual Network (VNET) feladatátvételét egy olyan Azure-régióból történő helyreállítás című szakasz tárgyalja, [amely vészhelyzet miatt sikertelen volt](#recover-from-a-failed-azure-region).
> 2. Az Azure Route táblában hozzáadott IP-címtartományok a 400-as korláttal léphetnek fel. Ha ez történik, a szakasz útmutatását követve [lépjen az egyik Azure-régióból egy másik Azure-régióba](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Helyreállítás sikertelen Azure-régióból

Abban az esetben, ha a teljes Azure-régió feladatátvételi esemény (például földrengés) miatt meghiúsul, az adott régióban található összes Azure-szolgáltatás, beleértve az Azure Stack Edge szolgáltatást is, feladatátvételt hajt végre. Mivel több szolgáltatás is rendelkezésre áll, a befogadó útvonalak könnyedén elterjedhetnek néhány százra. Az Azure 400 útvonalakat tartalmaz. 

Ha a régió feladatátvételt végez, a virtuális hálózat (vnet) átadja az új régiónak, és így a virtuális hálózati átjárót (VPN-átjárót) is. A módosítás kezeléséhez végezze el a következő módosításokat a Azure Stack Edge VPN-konfigurációban:

1. Helyezze át a vnet a célként megadott régióba. További információkért lásd: [Azure-beli virtuális hálózat áthelyezése egy másik régióba a Azure Portal használatával](../virtual-network/move-across-regions-vnet-portal.md).
2. Helyezzen üzembe egy új Azure VPN Gateway-átjárót abban a régióban, ahol áthelyezte a vnet. További információ: [create a Virtual Network Gateway (virtuális hálózati átjáró létrehozása](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)).
3. Frissítse Azure Stack Edge VPN-konfigurációját a VPN-kapcsolat fenti VPN-átjárójának használatára, majd válassza ki a célként kijelölt régiót a VPN-átjárót használó útvonalak hozzáadásához.
4. Frissítse a bejövő Azure Route-táblázatot, ha az ügyfél-címkészlet is megváltozik. 

## <a name="move-from-an-azure-region-to-another"></a>Áthelyezés Azure-régióból egy másikba

Az Azure Stack Edge-eszközt az egyik helyről egy másik helyre helyezheti át. Ahhoz, hogy az eszköz üzembe helyezéséhez legközelebb eső régiót használhassa, konfigurálnia kell az eszközt egy új otthoni régióra. Hajtsa végre a következő módosításokat:

1. Frissítheti Azure Stack Edge VPN-konfigurációját egy új régió VPN-átjárójának használatára, és kiválaszthatja az új régiót a VPN-átjárót használó útvonalak hozzáadásához.

## <a name="next-steps"></a>Következő lépések

[Biztonsági másolat készítése az Azure stack Edge-eszközről](azure-stack-edge-gpu-prepare-device-failure.md).