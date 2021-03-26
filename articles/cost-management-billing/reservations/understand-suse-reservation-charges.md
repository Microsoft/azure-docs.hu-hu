---
title: Szoftvercsomag-kedvezmény – Azure | Microsoft Docs
description: Megtudhatja, hogyan történik a szoftvercsomag-kedvezmények alkalmazása a virtuális gépeken található szoftvereken.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: banders
ms.openlocfilehash: 8bf53715b7f19c44d9114150e617f903cd05a51e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566315"
---
# <a name="azure-software-plan-discount"></a>Azure-szoftvercsomag-kedvezmények

Az Azure-beli SUSE- és RedHat-szoftvercsomagok üzembe helyezett virtuális gépekre alkalmazható foglalások. A szoftvercsomag-kedvezmény a foglalással egyező, üzembe helyezett virtuális gépek szoftverhasználatára érvényes.

Egy virtuális gép leállításakor a rendszer automatikusan egy másik egyező virtuális gépre alkalmazza a kedvezményt, ha van ilyen. A szoftvercsomagok a szoftver virtuális gépen történő futtatásának költségét fedezik. Az egyéb – például számítási, tárolási és hálózati – díjakat külön számítjuk fel.

A megfelelő csomag megvásárlásához ismernie kell a saját virtuálisgép-használatát és azt, hogy a virtuális gépek hány virtuális processzorral rendelkeznek. Az alábbi szakaszok segítségével meghatározhatja a használati adatok alapján, hogy melyik csomagot érdemes megvásárolnia.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>A RedHat rendszerű virtuális gépek használatának áttekintése vásárlás előtt

Keresse meg a termék nevét a használati adatok alapján, és vásárolja meg az azonos típusú és méretű RedHat-csomagot.

Ha például a használati adatok alapján Önnek a **Red Hat Enterprise Linux – 1–4 virtuális processzorral rendelkező virtuális gép licence** termék szükséges, akkor a **Red Hat Enterprise Linuxot** vásárolja meg egy **1–4 virtuális processzorral rendelkező virtuális géphez**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>A SUSE rendszerű virtuális gépek használatának áttekintése vásárlás előtt

Keresse meg a termék nevét a használati adatok alapján, és vásárolja meg az azonos típusú és méretű SUSE-csomagot.

Ha például a használati adatok alapján Önnek a **SUSE Linux Enterprise Server – 2–4 virtuális processzorral rendelkező virtuális gép támogatása** termék szükséges, akkor a **SUSE Linux Enterprise Server Priorityt** vásárolja meg egy **2–4 virtuális processzorral rendelkező virtuális géphez**.

![Példa a megvásárolandó termék kiválasztására](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>A kedvezmény különböző virtuálisgép-méretekre érvényes a SUSE-csomagok esetében

A Reserved VM Instances-csomagokhoz hasonlóan a SUSE-csomagok is biztosítják a példányok méretrugalmasságát. Ez azt jelenti, hogy a kedvezmény akkor is érvényes, ha eltérő számú virtuális processzorral rendelkező virtuális gépet helyez üzembe. A kedvezmény különböző virtuálisgép-méretekre érvényes a szoftvercsomagban.

A kedvezmény mértéke az alábbi táblázatokban szereplő arányoktól függ. Az arány a csoportok fogyasztásmérőinek relatív lábnyomát hasonlítja össze. Az arány a virtuális gépek virtuális processzoraitól függ. Az arány használatával kiszámíthatja, hány virtuálisgép-példány kapja meg a SUSE Linux-csomaggal járó kedvezményt.

Ha például SUSE Linux Enterprise Server for HPC Priority-csomagot vásárol egy 3 vagy 4 virtuális processzorral rendelkező virtuális géphez, akkor az adott foglalás aránya 2. A kedvezmény az alábbiak esetében fedezi a SUSE szoftver költségét:

- 2 üzembe helyezett virtuális gép 1 vagy 2 virtuális processzorral;
- 1 üzembe helyezett virtuális gép 3 vagy 4 virtuális processzorral;
- vagy egy olyan virtuális gép 0,77 része vagy 77%-a, amely 5 vagy több virtuális processzorral rendelkezik.

5 vagy több virtuális processzor esetén az arány 2,6. Így egy 5 vagy több virtuális processzorral rendelkező virtuális gépet tartalmazó SUSE-foglalás a szoftverköltségnek csak egy részét, kb. 77%-át fedezi.

Az alábbi táblázatok azokat a szoftvercsomagokat ismertetik, amelyekhez foglalást vásárolhat, a csomagok kapcsolódó fogyasztásmérőit és arányait.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

|SUSE rendszerű virtuális gép | Fogyasztásmérő azonosítója| Arány| Példa virtuálisgép-méretre|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server for HPC Priority 1-2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Priority 3-4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server for HPC Priority 5 + vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

|SUSE rendszerű virtuális gép | Fogyasztásmérő azonosítója | Arány|Példa virtuálisgép-méretre|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server for HPC Standard 1-2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Standard 3-4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SUSE Linux Enterprise Server for HPC Standard 5 + vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SAP standard SUSE Linux Enterprise Server

Korábban az SAP standard SUSE Linux Enterprise Server neve SUSE Linux Enterprise Server for SAP Priority.

|SUSE rendszerű virtuális gép | Fogyasztásmérő azonosítója | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server az SAP standard 1-2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server az SAP standard 3-4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server az SAP standard 5 + vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

|SUSE rendszerű virtuális gép | Fogyasztásmérő azonosítója | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- |--- |
|SUSE Linux Enterprise Server Standard 1-2 mag vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SUSE Linux Enterprise Server Standard 3-4 mag vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SUSE Linux Enterprise Server Standard 5 + vCPU |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A foglalással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Előre fizetés Azure Reservationsbeli SUSE-szoftvercsomagokért](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)