---
title: A Reservations szoftverköltségei az Azure-ban
description: Megtudhatja, hogy mely szoftverek használatát nem tartalmazzák az Azure-beli fenntartott VM-példányok költségei.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: banders
ms.openlocfilehash: 464e129314a369c14dcc6bc6c16eadf5f8bcf01f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052989"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Az Azure-beli fenntartott VM-példányok által nem tartalmazott szoftverköltségek

A virtuális gép fenntartott példánya és az SQL számára fenntartott kapacitásra vonatkozó kedvezmények csak az infrastrukturális költségekre vonatkoznak, a szoftverköltségekre nem. Ha Windows rendszerű virtuális gépet használ, és nem rendelkezik Azure Hybrid Benefittel a fenntartott virtuálisgép-példányokon, akkor a következő szakaszban felsorolt szoftver használata díjköteles. Az SQL-alapú PaaS-környezetek esetén az IP-díjat a rendszer továbbra is külön mérés alapján terheli, ha az Azure Hybrid Benefit nincs kiválasztva.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>A foglalási költség által nem tartalmazott Windows szoftverhasználat-mérők

| Fogyasztásmérő azonosítója | MeterName a használati fájlban | Felhasználó virtuális gép |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Burst (1 Core) | B sorozat |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Burst (2 Core) | B sorozat |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Burst (4 Core) | B sorozat |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Burst (8 Core) | B sorozat |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr (1 Core) | A B sorozat kivételével az összes |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr (2 Core) | A B sorozat kivételével az összes |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr (4 Core) | A B sorozat kivételével az összes |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr (6 Core) | A B sorozat kivételével az összes |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr (8 Core) | A B sorozat kivételével az összes |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr (12 Core) | A B sorozat kivételével az összes |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr (16 Core) | A B sorozat kivételével az összes |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 Core) | A B sorozat kivételével az összes |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr (24 Core) | A B sorozat kivételével az összes |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr (32 Core) | A B sorozat kivételével az összes |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr (40 Core) | A B sorozat kivételével az összes |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr (64 Core) | A B sorozat kivételével az összes |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr (72 Core) | A B sorozat kivételével az összes |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr (128 Core) | A B sorozat kivételével az összes |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Core) | A B sorozat kivételével az összes |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr (96 Core) | A B sorozat kivételével az összes |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>A foglalási költség által nem tartalmazott felhőhasználati szoftverhasználat-mérők

| Fogyasztásmérő azonosítója | MeterName a használati fájlban |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU License|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU License|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU License|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU License|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU License|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU License|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU License|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU License|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU License|

## <a name="get-rates-for-azure-meters"></a>Az Azure-beli mérők díjainak lekérése

Az egyes mérőszámok díjait az Azure kiskereskedelmi díjszabás API-val érheti el. Az Azure Meter díjszabásával kapcsolatos információkért lásd: az [Azure kiskereskedelmi árainak áttekintése](/rest/api/cost-management/retail-prices/azure-retail-prices).

## <a name="next-steps"></a>Következő lépések
Az Azure-beli foglalásokkal kapcsolatos további információkért olvassa el a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Foglalások kezelése az Azure-ban](manage-reserved-vm-instance.md)
- [A foglalási kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).