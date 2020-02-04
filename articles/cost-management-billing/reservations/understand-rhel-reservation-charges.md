---
title: A Red Hat foglalási csomag kedvezményei – Azure
description: Tudnivalók a Red Hat-csomagkedvezmények alkalmazásáról a virtuális gépeken található Red Hat szoftveren.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: cwatson
ms.openlocfilehash: 280764d3b9185abd1d8112f641007a7dc1861b34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995324"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Tudnivalók a Red Hat Linux Enterprise szoftver foglalásicsomag-kedvezményeinek alkalmazásáról az Azure-ban

Red Hat Linux-csomag vásárlása után a kedvezmény automatikusan alkalmazva lesz a foglalással egyező, üzembe helyezett Red Hat rendszerű virtuális gépekre. A Red Hat Linux-csomagok a Red Hat szoftver Azure-beli virtuális gépen történő futtatásának költségét fedezik.

A megfelelő Red Hat Linux-csomag megvásárlásához tudnia kell, milyen Red Hat rendszerű virtuális gépeket futtat, és azok hány virtuális processzorral rendelkeznek. Az alábbi szakaszok segítségével meghatározhatja a használati CSV-fájlból, hogy melyik csomagot érdemes megvásárolnia.

## <a name="discount-applies-to-different-vm-sizes"></a>A kedvezmény különböző virtuálisgép-méretekre érvényes

A Reserved VM Instanceshez hasonlóan a Red Hat-csomagok is biztosítják a példányok méretrugalmasságát. Ez azt jelenti, hogy a kedvezmény akkor is érvényes, ha eltérő számú virtuális processzorral rendelkező virtuális gépet helyez üzembe. A kedvezmény különböző virtuálisgép-méretekre érvényes a szoftvercsomagban.

A kedvezmény mértéke az alábbi táblázatokban szereplő arányoktól függ. Az arány a csoportok fogyasztásmérőinek relatív lábnyomát hasonlítja össze. Az arány a virtuális gépek virtuális processzoraitól függ. Az arány használatával kiszámíthatja, hány virtuálisgép-példány kapja meg a Red Hat Linux-csomaggal járó kedvezményt.

Ha például Red Hat Linux Enterprise Server-csomagot vásárol egy 3 vagy 4 virtuális processzorral rendelkező virtuális gép számára, akkor az adott foglalás aránya 2. A kedvezmény az alábbiak esetében fedezi a Red Hat szoftver költségét:

- 2 üzembe helyezett virtuális gép 1 vagy 2 virtuális processzorral;
- 1 üzembe helyezett virtuális gép 3 vagy 4 virtuális processzorral;
- vagy egy olyan virtuális gép 0,77 része vagy 77%-a, amely 5 vagy több virtuális processzorral rendelkezik.

5 vagy több virtuális processzor esetén az arány 2,6. Így egy 5 vagy több virtuális processzorral rendelkező virtuális gépet tartalmazó Red Hat-foglalás a szoftverköltségnek csak egy részét, kb. 77%-át fedezi.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>A Red Hat rendszerű virtuális gépek használatának megismerése vásárlás előtt

Az alábbi táblázatok azokat a szoftvercsomagokat ismertetik, amelyekhez foglalást vásárolhat, a csomagok kapcsolódó fogyasztásmérőit és arányait.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure Portal Marketplace-en szereplő nevek:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (legújabb LVM)

|Red Hat rendszerű virtuális gép | MeterId| Arány| Példa virtuálisgép-méretre|
| -------| ------------------------| --- |--- |
|1–4 virtuális processzorral rendelkező virtuális gép licence|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1–4 virtuális processzorral rendelkező virtuális gép licence|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1–4 virtuális processzorral rendelkező virtuális gép licence|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|44 virtuális processzorral rendelkező virtuális gép licence|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|60 virtuális processzorral rendelkező virtuális gép licence|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux magas rendelkezésre állású SAP-hez

Azure Portal Marketplace-en szereplő név:

|Red Hat rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- | --- | ------------------------| --- | --- |
|1–4 virtuális processzorral rendelkező virtuális gép licence |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux magas rendelkezésre állással

Azure Portal Marketplace-en szereplő nevek:

|Red Hat rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- | --- |
|1–4 virtuális processzorral rendelkező virtuális gép licence|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 vagy több virtuális processzorral rendelkező virtuális gép licence|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure Portal Marketplace-en szereplő nevek:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- |--- |
|1 virtuális processzorral rendelkező virtuális gép licence|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 virtuális processzorral rendelkező virtuális gép licence|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 virtuális processzorral rendelkező virtuális gép licence|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 virtuális processzorral rendelkező virtuális gép licence|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|8 virtuális processzorral rendelkező virtuális gép licence|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|12 virtuális processzorral rendelkező virtuális gép licence|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|16 virtuális processzorral rendelkező virtuális gép licence|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|20 virtuális processzorral rendelkező virtuális gép licence|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|24 virtuális processzorral rendelkező virtuális gép licence|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|32 virtuális processzorral rendelkező virtuális gép licence|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|40 virtuális processzorral rendelkező virtuális gép licence|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|44 virtuális processzorral rendelkező virtuális gép licence|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|60 virtuális processzorral rendelkező virtuális gép licence|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|64 virtuális processzorral rendelkező virtuális gép licence|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|72 virtuális processzorral rendelkező virtuális gép licence|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|96 virtuális processzorral rendelkező virtuális gép licence|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|128 virtuális processzorral rendelkező virtuális gép licence|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure Portal Marketplace-en szereplő nevek:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- |--- |
|1 virtuális processzorral rendelkező virtuális gép licence|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 virtuális processzorral rendelkező virtuális gép licence|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 virtuális processzorral rendelkező virtuális gép licence|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 virtuális processzorral rendelkező virtuális gép licence|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|8 virtuális processzorral rendelkező virtuális gép licence|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|12 virtuális processzorral rendelkező virtuális gép licence|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|16 virtuális processzorral rendelkező virtuális gép licence|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|20 virtuális processzorral rendelkező virtuális gép licence|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|24 virtuális processzorral rendelkező virtuális gép licence|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|32 virtuális processzorral rendelkező virtuális gép licence|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|40 virtuális processzorral rendelkező virtuális gép licence|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|44 virtuális processzorral rendelkező virtuális gép licence|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|60 virtuális processzorral rendelkező virtuális gép licence|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|64 virtuális processzorral rendelkező virtuális gép licence|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|72 virtuális processzorral rendelkező virtuális gép licence|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|96 virtuális processzorral rendelkező virtuális gép licence|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|128 virtuális processzorral rendelkező virtuális gép licence|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>További lépések

A foglalással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok az Azure-beli foglalások?](save-compute-costs-reservations.md)
- [Előre fizetés Azure Reservationsbeli Red Hat-szoftvercsomagokért](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Foglalások kezelése az Azure-ban](manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
