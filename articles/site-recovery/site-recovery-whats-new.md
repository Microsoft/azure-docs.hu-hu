---
title: A Azure Site Recovery újdonságai
description: A Azure Site Recovery szolgáltatás új szolgáltatásainak és legújabb frissítéseinek összefoglalása.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 423fbc563878d0cc2a63a5366d2226281a204e87
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454596"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás folyamatosan frissül és fejleszthető. Ez a cikk a legfrissebb kiadásokról, új funkciókról és új tartalmakról nyújt tájékoztatást. Ez az oldal rendszeresen frissül.

Az [Azure](https://azure.microsoft.com/updates/?product=site-recovery) Updates csatornán követheti és előfizethet site Recovery frissítési értesítéseire.

## <a name="supported-updates"></a>Támogatott frissítések

Site Recovery-összetevők esetében az N-4 verziót támogatjuk, ahol N a legújabb kiadású verzió. Ezeket az alábbi táblázat foglalja össze:

**Frissítés** |  **Egyesített beállítás** | **Konfigurációs kiszolgáló petesejtjei** | **Mobilitási szolgáltatás ügynöke** | **Site Recovery szolgáltató** | **Recovery Services ügynök**
--- | --- | --- | --- | --- | ---
[54-es összesítés](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 9.41.5888.1 | 5.1.6620.0 | 9.41.5888.1 | 5.1.6620.0  | 2.0.9202.0
[53-es összesítés](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[52-es összesítés](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[51-es összesítés](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[50-es összesítés](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[49-es összesítés](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


[További](service-updates-how-to.md) információ a frissítések telepítéséről és támogatásáról.

## <a name="updates-february-2021"></a>Frissítések (február 2021)

### <a name="update-rollup-54"></a>54-es kumulatív frissítés

A 54-es [kumulatív frissítés](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben. 
**Azure-beli virtuális gép vész-helyreállítási** | A Zone to Zone vész-helyreállítás a Azure Site Recovery használatával most már 4 régióban található: Észak-Európa, az USA keleti régiója, az USA középső régiója és az USA 2. nyugati régiója.<br/>
**VMware virtuális gép/fizikai vész-helyreállítás az Azure-ba** | A frissítés tartalmazza a portál támogatását, amellyel a replikáció engedélyezése után kiválaszthatja a VMware/fizikai gépek közelségi elhelyezési csoportjait.<br/><br/> A VMware rendszerű, adatlemezes, 32 TB-os ISS-mérettel rendelkező gépek védelme mostantól támogatott.
**Hyper-V vész-helyreállítás az Azure-ba** | A frissítés tartalmazza a portál támogatását, amely a replikáció engedélyezése után kijelöli a közelségi elhelyezési csoportokat a Hyper-V gépekhez.


## <a name="updates-january-2021"></a>Frissítések (január 2021)

### <a name="update-rollup-53"></a>53-es kumulatív frissítés

A 53-es [kumulatív frissítés](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben. 
**Azure-beli virtuális gép vész-helyreállítási** | A címkék replikációja mostantól támogatott. Az Azure-beli virtuális gépekhez, lemezekhez és hálózati adapterekhez hozzáadott címkéket a rendszer a célként megadott régióban lévő számítógépekre replikálja.<br/><br/> A Debian 10 rendszert futtató Azure-beli virtuális gépek már támogatottak a replikáláshoz.
**VMware virtuális gép/fizikai vész-helyreállítás az Azure-ba** | A frissítés naplózási fejlesztéseket tartalmaz a VMware virtuális gépek Azure-ba történő replikálásához, valamint a továbbfejlesztett hibaüzeneteket.<br/><br/> A VMware virtuális gépek és a Debian 10 rendszert futtató fizikai gépek mostantól a replikáláshoz is támogatottak.


## <a name="updates-november-2020"></a>Frissítések (november 2020)

### <a name="update-rollup-52"></a>52-es kumulatív frissítés

A 52-es [kumulatív frissítés](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos, a kumulatív frissítéssel kapcsolatos javítás és javítás, beleértve a mobilitási szolgáltatás új Linux-támogatását is.
**Azure-beli virtuális gép vész-helyreállítási** | Mostantól támogatott a RHEL 8,3 és Oracle Linux 7,9 rendszerű virtuális gépek esetében
**VMware virtuális gép/fizikai vész-helyreállítás az Azure-ba** | Mostantól támogatott a RHEL 8,3, Oracle Linux 7,9/8.3 rendszert futtató virtuális gépek esetében.

## <a name="updates-october-2020"></a>Frissítések (október 2020)

### <a name="update-rollup-51"></a>51-es kumulatív frissítés

A 51-es [kumulatív frissítés](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos, a kumulatív frissítéssel kapcsolatos javítás és javítás, beleértve a mobilitási szolgáltatás új Linux-támogatását is.

## <a name="updates-september-2020"></a>Frissítések (2020. szeptember)

### <a name="update-rollup-50"></a>50-es kumulatív frissítés

A 50-es [kumulatív frissítés](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

## <a name="updates-august-2020"></a>Frissítések (2020. augusztus)

### <a name="update-rollup-49"></a>49-es kumulatív frissítés

A 49-es [kumulatív frissítés](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos, a kumulatív frissítéssel kapcsolatos javítás és javítás, beleértve a mobilitási szolgáltatás új Linux-támogatását is.

## <a name="updates-july-2020"></a>Frissítések (2020. július)

### <a name="update-rollup-48"></a>48-es kumulatív frissítés

A 48-es [kumulatív frissítés](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

> [!NOTE]
> A 48-es kumulatív frissítés ismert hibával rendelkezik, amely az ADE használatával titkosított Linux rendszerű gépek replikálását teszi lehetővé. [További információk](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>47-es kumulatív frissítés

A 47-es [kumulatív frissítés](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

## <a name="updates-june-2020"></a>Frissítések (2020. június)

### <a name="update-rollup-46"></a>46-es kumulatív frissítés

A 46-es [kumulatív frissítés](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

## <a name="updates-march-2020"></a>Frissítések (2020. március)

### <a name="update-rollup-45"></a>45-es kumulatív frissítés

A 45-es [kumulatív frissítés](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) a következő frissítéseket tartalmazza:

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítései a kumulatív módon részletezve.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

## <a name="updates-january-2020"></a>Frissítések (január 2020)

### <a name="update-rollup-44"></a>44-es kumulatív frissítés

A 44-es [kumulatív frissítés](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Nem voltak frissítések a Site Recovery szolgáltatók és ügynökök számára.
**Hibajavítások/javítások** | Számos javítás és javítás a kumulatív összesítésben.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware vész-helyreállítás

Az Azure Virtual Machines mostantól támogatja a virtuális gépek számára a titkosítást az ügyfél által felügyelt kulcsokkal. [További információk](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>43-es kumulatív frissítés

A 43-es [kumulatív frissítés](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


## <a name="updates-november-2019"></a>Frissítések (november 2019)

### <a name="update-rollup-42"></a>42-es kumulatív frissítés

A 42-es [kumulatív frissítés](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**UEFI** | A Site Recovery mostantól támogatja az UEFI-alapú rendszerindítási architektúrával rendelkező Azure-beli virtuális gépek vész-helyreállítását.
**Linux** | A Site Recovery mostantól támogatja a Linux rendszerű Azure-beli virtuális gépeket Azure Disk Encryption (ADE).
**2. generációs** | Az összes 2. generációs Azure-beli virtuális gép mostantól a vész-helyreállítást is támogatja.
**Régiók** | Mostantól engedélyezheti az Azure-beli virtuális gépek vész-helyreállítását a norvégiai geo-ben.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A VMware – Azure vész-helyreállítás új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**UEFI** | Site Recovery mostantól támogatja az UEFI-alapú rendszerindítási architektúrával rendelkező VMware virtuális gépek vész-helyreállítását.<br/><br/> A támogatott operációs rendszerek közé tartozik a Windows Server 2019, a Windows Server 2016, a Windows Server 2012 R2, a Windows Server 2012, a SLES 12 SP4, a RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Frissítés a karbantartási verem frissítéséhez/SHA-2

Ha az Azure-beli virtuális gépeket másodlagos régióba vagy helyszíni VMware virtuális gépekre vagy fizikai kiszolgálókra szeretné helyreállítani az Azure-ba, vegye figyelembe a következőket:

- A mobilitási szolgáltatás bővítményének (Azure-beli virtuális gépek esetén) és a mobilitási szolgáltatás ügynökének (VMware/fizikai gépek esetén) a verziójának 9.30.5407.1 néhány gépi operációs rendszernek a karbantartási verem frissítését és az SHA-2-et kell futtatnia. A részletek az alábbi táblázatban láthatók.
- Telepítse a frissítést és az SHA-2-et a csatolt KB-nak megfelelően. Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni.
- További információ az [SHA-2 frissítésről és a követelményekről](https://aka.ms/SHA-2KB).

**Operációs rendszer** | **Azure VM** | **VMware VM/fizikai gép**
--- | --- | ---
**Windows 2008 R2 SP1** | [Karbantartási verem frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Karbantartási verem frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Karbantartási verem frissítése](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Karbantartási verem frissítése](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Karbantartási verem frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Karbantartási verem frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Frissítések (október 2019)

### <a name="update-rollup-41"></a>41-es kumulatív frissítés

A 41-es [kumulatív frissítés](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)



### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**Feladatátvételi beállítások tesztelése** | A feladatátvételi teszt beállításakor mostantól konfigurálhatja a feladatátvételi teszt virtuális gépe és hálózata beállításait, beleértve az IP-címet, a NSG, a belső terheléselosztást, valamint az egyes gépek hálózati ADAPTERéhez tartozó nyilvános IP-címet is. Ezek a beállítások nem kötelezőek, és nem változtatják meg az aktuális viselkedést. Ha nem konfigurálja ezeket a beállításokat, kiválaszthat egy Azure-VNet a feladatátvételi teszt időpontjában. [További információk](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Helyreállítási tervek** | A helyreállítási tervek mostantól 100 virtuális gépre korlátozódnak, így biztosítva a feladatátvétel megbízhatóságát.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A VMware – Azure vész-helyreállítás új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**Helyreállítási tervek** | A helyreállítási tervek mostantól 100 virtuális gépre korlátozódnak, így biztosítva a feladatátvétel megbízhatóságát.


## <a name="updates-september-2019"></a>Frissítések (2019. szeptember)

### <a name="update-rollup-40"></a>40-es kumulatív frissítés

A 40-es [kumulatív frissítés](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)




### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**Törlés a feladat-visszavétel után** | A másodlagos Azure-ba történő feladatátvétel után, majd az elsődleges régióba való visszalépés után Site Recovery automatikusan törli a gépeket a másodlagos régióban. Nem szükséges manuálisan törölni a virtuális gépeket és a hálózati adaptereket.
**A feladatátvételi teszt megtartja az IP-címet** | Mostantól megtarthatja a forrás virtuális gép IP-címét a vész-helyreállítási részletezés során, és egy statikus IP-címet is kiválaszthat a feladatátvételi teszthez.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
Új Process Server-riasztások | Új Process Server-riasztások lettek hozzáadva. [További információk](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Hyper-V vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
A(z) | A Site Recovery mostantól támogatja a Hyper-V-ről az Azure-ba irányuló, a tűzfalon engedélyezve lévő Storage-fiókok használatát.  A tűzfalat támogató Storage-fiókokat célként fiókként vagy gyorsítótárbeli tárolóként is kiválaszthatja. Ha tűzfalat használó fiókot használ, engedélyezze a megbízható Microsoft-szolgáltatások engedélyezését.<br/><br/> Ezt a Hyper-V virtuális gépek támogatják a System Center VMM vagy anélkül.


## <a name="updates-august-2019"></a>Frissítések (2019. augusztus)

### <a name="update-rollup-39"></a>39-es kumulatív frissítés

A 39-es [kumulatív frissítés](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Szolgáltatás** | **Részletek**
--- | ---
**Titkosítás az Azure AD nélkül** | Az Azure AD-alkalmazás nélküli titkosítás mostantól támogatott az Azure-beli virtuális gépek replikálásához Windows rendszerű felügyelt lemezeken.
**Feladatátvételhez szükséges hálózati erőforrások** | Ha feladatátvételt végez egy másik régióba, most hálózati erőforrás-beállításokat (NSG, terheléselosztást, nyilvános IP-címet) csatolhat egy virtuális géphez.

## <a name="updates-july-2019"></a>Frissítések (2019. július)

### <a name="update-rollup-38"></a>38-es kumulatív frissítés

A 38-es [kumulatív frissítés](https://support.microsoft.com/help/4513507/) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="general"></a>Általános kérdések

A Site Recovery mostantól támogatja az általános célú v2 Storage-fiókok használatát a gyorsítótár-tároláshoz vagy a célhelyek tárolásához. Korábban csak a v1 támogatott.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A lemezeket mostantól akár 8 TB-ra is replikálhatja, ha felügyelt lemezekkel rendelkező Azure-beli virtuális gépre végez replikálást.


## <a name="updates-june-2019"></a>Frissítések (2019. június)

### <a name="update-rollup-37"></a>37-es kumulatív frissítés

A 37-es [kumulatív frissítés](https://support.microsoft.com/help/4508614/) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**GPT-partíciók** | Az 37-as kumulatív frissítéstől kezdve (a mobilitási szolgáltatás verziójának 9.25.5241.1) legfeljebb öt GPT-partíció támogatott az UEFI-ben. A frissítés előtt négy támogatott.



## <a name="updates-may-2019"></a>Frissítések (május 2019)

### <a name="update-rollup-36"></a>36-es kumulatív frissítés

A 36-es [kumulatív frissítés](https://support.microsoft.com/help/4503156) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Hozzáadott lemezek replikálása** | A vész-helyreállításra már engedélyezett Azure-beli virtuális gépekhez hozzáadott adatlemezek replikálásának engedélyezése. [További információk](azure-to-azure-enable-replication-added-disk.md).
**Automatikus frissítések** | Ha olyan Azure-beli virtuális gépeken futó mobilitási szolgáltatás bővítményének automatikus frissítését konfigurálja, amelyeken engedélyezve van a vész-helyreállítás, mostantól kiválaszthatja a használni kívánt Automation-fiókot a Site Recovery által létrehozott alapértelmezett fiók használata helyett. [További információk](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**A Process Server figyelése** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók vész-helyreállításához, a kiszolgáló állapotának javításához és a riasztások kezeléséhez a folyamat-kiszolgálókkal kapcsolatos problémák figyelése és hibaelhárítása. [További információk](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Frissítések (2019. március)

### <a name="update-rollup-35"></a>35-es kumulatív frissítés

A 35-es [kumulatív frissítés](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Felügyelt lemezek** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása mostantól közvetlenül az Azure-ban felügyelt lemezekre történik. A helyszíni információk egy gyorsítótárbeli Storage-fiókba lesznek továbbítva az Azure-ban, és a helyreállítási pontok a célhelyen felügyelt lemezeken jönnek létre. Ez biztosítja, hogy ne kelljen több cél Storage-fiókot kezelnie.
**Konfigurációs kiszolgáló** | Site Recovery mostantól támogatja a több hálózati adapterrel rendelkező konfigurációs kiszolgálókat. A konfigurációs kiszolgáló a tárolóban való regisztrálása előtt adjon hozzá további adaptereket a konfigurációs kiszolgáló virtuális géphez. Ha ezt követően hozzáadja, újra kell regisztrálnia a kiszolgálót a tárolóban.


## <a name="updates-february-2019"></a>Frissítések (február 2019)

### <a name="update-rollup-34"></a>34-es kumulatív frissítés

A 34-es [kumulatív frissítés](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


### <a name="update-rollup-33"></a>33-es kumulatív frissítés

A 33-es [kumulatív frissítés](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Hálózatleképezés** | Az Azure-beli virtuális gépek vész-helyreállítási folyamata esetén a replikáció engedélyezésekor bármilyen elérhető célszámítógépet is használhat.
**Standard SSD** | Most már beállíthatja az Azure-beli virtuális gépek vész-helyreállítását [standard SSD lemezek](../virtual-machines/disks-types.md#standard-ssd)használatával.
**Közvetlen tárolóhelyek** | Az Azure VM-alkalmazásokon futó alkalmazások vész-helyreállítását a magas rendelkezésre állás érdekében [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) használatával állíthatja be.  A Közvetlen tárolóhelyek (S2D) és a Site Recovery együttes használata átfogó védelmet nyújt az Azure-beli virtuális gépek számítási feladataihoz. A S2D lehetővé teszi a vendég fürtök üzemeltetését az Azure-ban. Ez különösen akkor hasznos, ha egy virtuális gép kritikus alkalmazást (például SAP ASCS Layer, SQL Server vagy kibővített fájlkiszolgáló) üzemeltet.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux rendszerű BRTFS fájlrendszer** | A Site Recovery mostantól támogatja a VMware virtuális gépek replikálását a BRTFS fájlrendszerrel. A replikáció nem támogatott, ha:<br/><br/>– A replikálás engedélyezése után a rendszer a BTRFS fájlrendszer alkötetét módosítja.<br/><br/>– A fájlrendszer több lemezre oszlik.<br/><br/>– A BTRFS fájlrendszer támogatja a RAID-t.
**Windows Server 2019** | A Windows Server 2019 rendszerű gépekhez hozzáadott támogatás.


## <a name="updates-january-2019"></a>Frissítések (január 2019)


### <a name="accelerated-networking-azure-vms"></a>Gyorsított hálózatkezelés (Azure-beli virtuális gépek)

A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, és javítja a hálózat teljesítményét. Ha engedélyezi egy Azure-beli virtuális gép replikálását, Site Recovery észleli, hogy engedélyezve van-e a gyorsított hálózat. Ha igen, a feladatátvételt követően Site Recovery automatikusan a gyorsított hálózatkezelést konfigurálja a célként megadott replika Azure virtuális gépen, [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) és [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)rendszereken egyaránt.

[További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>32-es kumulatív frissítés

A 32-es [kumulatív frissítés](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatást kapott a RedHat munkaállomás 6/7-es verziójához, valamint az Ubuntu, a Debian és a SUSE új kernel-verzióihoz.
**Közvetlen tárolóhelyek** | A Site Recovery Közvetlen tárolóhelyek (S2D) használatával támogatja az Azure-beli virtuális gépeket.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>A VMware virtuális gépek/fizikai kiszolgálók katasztrófa utáni helyreállítása

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás lett hozzáadva a RedHat Enterprise Linux 7,6, a RedHat munkaállomás 6/7, Oracle Linux 6,10 és Oracle Linux 7,6, valamint az Ubuntu, a Debian és a SUSE új kernel-verzióihoz.


### <a name="update-rollup-31"></a>31. kumulatív frissítés

A [31. kumulatív frissítés](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuális gépek/fizikai kiszolgálók replikációja

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** |  Támogatás lett hozzáadva a Oracle Linux 6,8, Oracle Linux 6,9 és Oracle Linux 7,0 a Red hat kompatibilis kernelhez, valamint a nem törhető Enterprise kernel (UEK) 5. kiadásához.
**LVM** | Az LVM és a LVM2 kötetek támogatása.<br/><br/> A lemezpartíció és az LVM kötetek/boot könyvtára mostantól támogatott.
**Könyvtárak** | Ezeket a címtárakat külön partícióként, illetve nem ugyanazon rendszerlemezen található fájlrendszereknél adták hozzá a rendszer:<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | A dinamikus lemezekhez hozzáadott támogatás.
**Feladatátvétel** | Továbbfejlesztett feladatátvételi idő a VMware virtuális gépeknél, ahol a storvsc és a vsbus nem rendszerindítási illesztőprogramok.
**UEFI-támogatás** | Az Azure virtuális gépek nem támogatják az UEFI rendszerindítási típust. Most már áttelepítheti a helyszíni fizikai kiszolgálókat az UEFI-ből az Azure-ba Site Recovery használatával. A Site Recovery áttelepíti a kiszolgálót úgy, hogy az áttelepítés előtt átalakítja a rendszerindítási típust a BIOS-ba. Site Recovery korábban csak virtuális gépek esetében támogatta ezt a konverziót. A támogatás a Windows Server 2012-es vagy újabb verzióját futtató fizikai kiszolgálókon érhető el.

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás lett hozzáadva a Oracle Linux 6,8, Oracle Linux 6,9 és Oracle Linux 7,0 a Red hat kompatibilis kernelhez, valamint a nem törhető Enterprise kernel (UEK) 5. kiadásához.
**Linux rendszerű BRTFS fájlrendszer** | Azure-beli virtuális gépek esetén támogatott.
**Azure-beli virtuális gépek a rendelkezésre állási zónákban** | Engedélyezheti a replikálást egy másik régióba a rendelkezésre állási zónákban üzembe helyezett Azure-beli virtuális gépek esetében. Mostantól engedélyezheti az Azure-beli virtuális gépek replikálását, és beállíthatja a feladatátvétel célját egyetlen virtuálisgép-példányra, egy rendelkezésre állási csoportba tartozó virtuális gépre vagy egy rendelkezésre állási zónában lévő virtuális gépre. A beállítás nem érinti a replikálást. [Olvassa el](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) a bejelentést.
**Tűzfallal kompatibilis tároló (portál/PowerShell)** | Támogatott a [tűzfalat használó Storage-fiókok](../storage/common/storage-network-security.md)támogatása.<br/><br/> Az Azure-beli virtuális gépeket nem felügyelt lemezekkel replikálhatja a tűzfalat támogató Storage-fiókokba egy másik Azure-régióba a vész-helyreállítás érdekében.<br/><br/> A nem felügyelt lemezekhez a tűzfalat támogató Storage-fiókokat célként tároló fiókokként használhatja.<br/><br/> A Portálon és a PowerShell használatával támogatott.

## <a name="updates-december-2018"></a>Frissítések (2018. december)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>A mobilitási szolgáltatás (Azure-beli virtuális gépek) automatikus frissítései

Site Recovery hozzáadott egy lehetőséget a mobilitási szolgáltatás bővítményének automatikus frissítéseihez. A mobilitási szolgáltatás bővítmény a Site Recovery által replikált összes Azure-beli virtuális gépre telepítve van. A replikáció engedélyezésekor kiválaszthatja, hogy engedélyezi-e Site Recovery számára a bővítmény frissítéseinek kezelését.

A frissítések nem igénylik a virtuális gépek újraindítását, és nem érintik a replikálást. [További információk](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Díjszabás az Azure-beli virtuális gép vész-helyreállításához

Az Azure-beli virtuális gépek vész-helyreállítási költségei a virtuálisgép-licencelési költségek, valamint a hálózati és tárolási költségek. Az Azure egy [díjszabási számológépet](https://aka.ms/a2a-cost-estimator) biztosít, amely segít kideríteni ezeket a költségeket. A Site Recovery mostantól egy példaként értékeli a [becslést](https://aka.ms/a2a-cost-estimator) , amely egy három rétegből álló, 12 standard HDD lemezzel és 6 prémium SSD lemezzel rendelkező, háromrészes alkalmazáson alapuló minta-telepítés árát tartalmazza.

- A minta azt feltételezi, hogy a standard és 20 GB-os adatváltozások napi 10 GB-ban változnak.
- Az adott központi telepítés esetében módosíthatja a változókat a költségek becslése érdekében.
- Megadhatja a virtuális gépek számát, a felügyelt lemezek számát és típusát, valamint a virtuális gépeken várható teljes adatváltozási arányt.
- Emellett a sávszélességgel kapcsolatos költségek becsléséhez is alkalmazhat tömörítési tényezőt.

[Olvassa el](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) a bejelentést.


## <a name="updates-october-2018"></a>Frissítések (október 2018)

### <a name="update-rollup-30"></a>30. kumulatív frissítés

A [30. kumulatív frissítés](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Régiós támogatás** | Site Recovery támogatás hozzáadva az Australia Central 1 és az Australia Central 2 rendszerhez.
**A lemez titkosításának támogatása** | Az Azure AD-alkalmazással titkosított Azure-beli virtuális gépek vész-helyreállításának támogatása Azure Disk Encryption (ADE) titkosítással. [További információk](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Lemez kizárása** | A nem inicializált lemezek mostantól automatikusan ki vannak zárva az Azure-beli virtuális gépek replikálásakor.
**Tűzfallal kompatibilis tároló (PowerShell)** | Támogatott a [tűzfalat használó Storage-fiókok](../storage/common/storage-network-security.md)támogatása.<br/><br/> Az Azure-beli virtuális gépeket nem felügyelt lemezekkel replikálhatja a tűzfalat támogató Storage-fiókokba egy másik Azure-régióba a vész-helyreállítás érdekében.<br/><br/> A nem felügyelt lemezekhez a tűzfalat támogató Storage-fiókokat célként tároló fiókokként használhatja.<br/><br/> Csak a PowerShell használatával támogatott.


### <a name="update-rollup-29"></a>29. kumulatív frissítés

A [29. kumulatív frissítés](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


## <a name="updates-august-2018"></a>Frissítések (2018. augusztus)

### <a name="update-rollup-28"></a>28. kumulatív frissítés

A [28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatott a RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Felhő-támogatás** | Támogatott vész-helyreállítás az Azure-beli virtuális gépek számára a németországi felhőben.
**Az előfizetések közötti vész-helyreállítás** | Az Azure-beli virtuális gépek egyik régióban való replikálásának támogatása egy másik előfizetésben, ugyanazon a Azure Active Directory bérlőn belül. [További információk](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware virtuális gép/fizikai kiszolgáló vész-helyreállítás
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva a RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> A Linux-alapú virtuális gépek, amelyek a GUID partíciós tábla (GPT) partíciós stílust használják a régi BIOS-kompatibilitási módban, mostantól támogatottak. További információért tekintse át az Azure-beli virtuális gépekkel kapcsolatos [gyakori kérdéseket](../virtual-machines/faq-for-disks.md) .
**Vész-helyreállítási virtuális gépek a Migrálás után** | Az Azure-ba migrált helyszíni VMware virtuális gépek másodlagos régióba való vész-helyreállításának támogatása anélkül, hogy a replikáció engedélyezése előtt el kellene távolítani a mobilitási szolgáltatást a virtuális gépen.
**Windows Server 2008** | A Windows Server 2008 R2/2008 64-bit és 32-bit rendszert futtató gépek áttelepítésének támogatása.<br/><br/> Csak áttelepítés (replikálás és feladatátvétel). A feladat-visszavétel nem támogatott.

## <a name="updates-july-2018"></a>Frissítések (2018. július)

### <a name="update-rollup-27-july-2018"></a>27. kumulatív frissítés (2018. július)

A [27. kumulatív frissítés](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | A támogatás a Red Hat Enterprise Linux 7,5-hez lett hozzáadva.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware virtuális gép/fizikai kiszolgáló vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | A támogatás a Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12 rendszerhez lett hozzáadva.



## <a name="next-steps"></a>Következő lépések

Naprakészen tarthatja frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) oldalon.