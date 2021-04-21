---
title: Erőforráskorlátok a Azure NetApp Files | Microsoft Docs
description: Ismerteti a Azure NetApp Files korlátait és az erőforráskorlát növelésének kérését.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750193"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

A kötetek erőforráskorlátának Azure NetApp Files segít a kötetek kezelésében.

## <a name="resource-limits"></a>Erőforráskorlátok

Az alábbi táblázat a fürtök erőforráskorlátát Azure NetApp Files:

|  Erőforrás  |  Alapértelmezett korlát  |  Támogatási kérésen keresztül módosítható  |
|----------------|---------------------|--------------------------------------|
|  NetApp-fiókok száma Előfizetésenként Azure-régiónként  |  10    |  Yes   |
|  Kapacitáskészletek száma NetApp-fiókonként   |    25     |   Yes   |
|  Kötetek száma előfizetésenként   |    500     |   Yes   |
|  Kötetek száma kapacitáskészletenként     |    500   |    Yes     |
|  Pillanatképek száma kötetenként       |    255     |    No        |
|  Azure-beli virtuális Azure NetApp Files (Microsoft.NetApp/volumes) számára delegált alhálózatok Virtual Network    |   1   |    No    |
|  A virtuális hálózatokban használt IP-k száma (beleértve az azonnal társviszonyban Azure NetApp Files   |    1000   |    No   |
|  Egyetlen kapacitáskészlet minimális mérete   |  4 TiB     |    No  |
|  Egyetlen kapacitáskészlet maximális mérete    |  500 TiB   |   No   |
|  Egyetlen kötet minimális mérete    |    100 GiB    |    No    |
|  Egyetlen kötet maximális mérete     |    100 TiB    |    No    |
|  Egyetlen fájl maximális mérete     |    16 TiB    |    No    |    
|  Könyvtármetaadatok maximális mérete egyetlen könyvtárban      |    320 MB    |    No    |    
|  Fájlok maximális száma ([maxfiles](#maxfiles)) kötetenként     |    100 millió    |    Yes    |    
|  Az exportálási szabályzat szabályainak maximális száma kötetenként     |    5  |    No    | 
|  Minimális hozzárendelt átviteli sebesség manuális QoS-kötethez     |    1 MiB/s   |    No    |    
|  Manuális QoS-kötethez hozzárendelt maximális átviteli sebesség     |    4500 MiB/s    |    No    |    
|  Régiók közötti replikációs adatvédelmi kötetek száma (célkötetek)     |    5    |    Yes    |     

Annak megállapításához, hogy egy könyvtár megközelíti-e a címtár-metaadatok maximális méretkorlátját (320 MB), tekintse meg Hogyan, hogy egy könyvtár megközelíti-e a korlát [méretét.](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)   

További információ: [Kapacitáskezelés – gyakori kérdések.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Maximális fájlkorlátok <a name="maxfiles"></a> 

Azure NetApp Files kötetek korlátja *maxfiles.* A maxfiles korlátja a köteten található fájlok maximális száma. A Linux-fájlrendszerek a korlátot *inode-nak (inodes) stb.* A kötetek maximális Azure NetApp Files a kötet mérete (kvótája) alapján indexeli a rendszer. A kötetek maximális fájlkorlátja a kiépített kötet méretének TiB-nkénti 20 millió fájlméretével nő vagy csökken. 

A szolgáltatás dinamikusan módosítja egy kötet maxfiles korlátját a kiépített mérete alapján. Egy kezdetben 1 TiB-os mérettel konfigurált kötet maximális fájlkorlátja például 20 millió. A kötet méretének későbbi módosításai a maxfiles korlát automatikus, a következő szabályok alapján való módosításakor lépnek fel: 

|    Kötet mérete (kvóta)     |  A maxfiles korlát automatikus újrakorlátja    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 millió     |
|    > 1 TiB, de <= 2 TiB    |    40 millió     |
|    > 2 TiB, de <= 3 TiB    |    60 millió     |
|    > 3 TiB, de <= 4 TiB    |    80 millió     |
|    > 4 TiB                 |    100 millió    |

Ha már lefoglalt legalább 4 TiB kvótát egy kötethez, kezdeményezhet egy támogatási kérést, hogy növelje [a](#limit_increase) maximális fájlok (inode-k) korlátját 100 milliónál nagyobbra. Minden 100 millió megnövelt fájlhoz (vagy ezek töredékéhez) meg kell növelnie a megfelelő kötetkvótát 4 TiB-ral.  Ha például 100 millió fájlról 200 millió fájlra (vagy közöttük bármely számra) növeli a maximális fájlok korlátját, akkor a kötetkvótát 4 TiB-ről 8 TiB-ra kell növelnie.

Ha a kötetkvóta legalább 20 TiB, a maximális fájlszám korlátját 500 millióra növelheti. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Kérelemkorlát növelése <a name="limit_increase"></a> 

Létrehozhat egy új Azure-támogatás a fenti táblázatból a módosítható korlátok növeléséhez. 

A Azure Portal síkról: 

1. Kattintson **a Súgó és támogatás elemre.**
2. Kattintson **az + Új támogatási kérelem elemre.**
3. Az Alapvető beállítások lapon adja meg a következő információkat: 
    1. Probléma típusa: Válassza **a Szolgáltatási és előfizetési korlátok (kvóták) lehetőséget.**
    2. Előfizetések: Válassza ki annak az erőforrásnak az előfizetését, amely esetében növelni kell a kvótát.
    3. Kvóta típusa: Válassza a **Storage: Azure NetApp Files lehetőséget.**
    4. Kattintson **a Tovább: Megoldások elemre.**
4. A Részletek lapon:
    1. A Leírás mezőben adja meg a következő információkat a megfelelő erőforrástípushoz:

        |  Erőforrás  |    Szülőerőforrások      |    Kért új korlátok     |    Kvótanövelés oka       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Fiók |  *Előfizetés azonosítója*   |  *Új maximális fiókszám **kérése***    |  *Milyen forgatókönyv vagy felhasználási eset kérte a kérést?*  |
        |  Készlet    |  *Előfizetés azonosítója, NetApp-fiók URI-azonosítója*  |  *Új maximális készletszám **kérése***   |  *Milyen forgatókönyv vagy felhasználási eset kérte a kérést?*  |
        |  Kötet  |  *Előfizetés azonosítója, NetApp-fiók URI- és kapacitáskészlet-URI*   |  *Kért új maximális **kötetszám***     |  *Milyen forgatókönyv vagy felhasználási eset kérte a kérést?*  |
        |  Maxfiles (Maximális fájlok)  |  *Előfizetés azonosítója, NetApp-fiók URI-azonosítója, kapacitáskészlet URI-azonosítója, kötet URI-azonosítója*   |  *Új maximális maximális **fájlszám kérése***     |  *Milyen forgatókönyv vagy felhasználási eset kérte a kérést?*  |    
        |  Régiók közötti replikáció adatvédelmi kötetei  |  *Előfizetés azonosítója, cél NetApp-fiók URI-azonosítója, célkapacitás-készlet URI-azonosítója, forrás NetApp-fiók URI-azonosítója, forráskapacitás-készlet URI, forráskötet URI*   |  *A régiók közötti replikációs adatvédelmi kötetek **(célkötetek)** új maximális számának kérése _     |  _What forgatókönyv vagy a felhasználási eset a kérést kérte?*  |    

    2. Adja meg a megfelelő támogatási módszert, és adja meg a szerződés adatait.

    3. Kattintson **a Tovább: Áttekintés + létrehozás elemre** a kérés létrehozásához. 


## <a name="next-steps"></a>Következő lépések  

- [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
- [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md)
