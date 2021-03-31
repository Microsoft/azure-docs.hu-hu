---
title: Az Azure Data Box kábelezési lehetőségei | Microsoft Docs
description: Ismerkedjen meg az adatátvitelhez szükséges különböző módszerekkel a Azure Data Box a MGMT port vagy az adatport használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/24/2018
ms.author: alkohli
ms.openlocfilehash: a2c4cce492c48ebe6e09ad142f97bc6377a8a5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94334601"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Az Azure Data Box kábelezési lehetőségei

Ez a cikk az Azure Data Box adatátvitelre szolgáló különböző kábelezési módjait írja le.

## <a name="transfer-via-mgmt-port"></a>Átvitel MGMT-porton keresztül

Ez a lehetőség a Data Box abszolút minimális konfigurációja. Konfigurálhatja csak az MGMT-portot a felügyelethez és az adatokhoz is.

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy RJ45 Ethernet-kábel az MGMT számára.
- Egy [támogatott operációs rendszert](data-box-system-requirements.md#supported-operating-systems-for-clients) futtató adatforrás.

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. Egy RJ45-ös kábellel csatlakoztassa az MGMT-portot az adatokat tartalmazó kiszolgálóhoz.

    ![Egy portos telepítés](media/data-box-cable-options/cabling-mgmt-only.png)

2. A kiszolgálón állítsa be a következő értékeket:

    - **IP-cím**: 192.168.100.5
    - **Alhálózat**: 255.255.255.0

3. Nyissa meg az eszköz helyi webes felületét a 192.168.100.10 címen. Jelentkezzen be és oldja fel a Data Box zárolását az Azure Portalról beszerzett feloldási jelszóval.


## <a name="transfer-via-data-port-with-static-ips"></a>Átvitel DATA-porton keresztül statikus IP-címekkel

Beállíthat két portot is a Data Boxhoz: az MGMT-portot a forgalom felügyeletéhez és egy adatportot az adatokhoz. Az adatport a DATA 1, DATA 2 vagy DATA 3 lehet.

Határozottan javasoljuk, hogy ha csak egy portot konfigurál, akkor az egy 10-GbE-port legyen, például a DATA 1 vagy a DATA 2. Az 1-GbE-portok jelentősen növelik az adatok átviteléhez szükséges időt.

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy RJ45 Ethernet-kábel az MGMT számára.
- Egy 10-GbE SFP+ Twinax-rézkábel minden csatlakoztatni kívánt 10-GbE-adatporthoz.
- Egy vagy több, [támogatott operációs rendszert](data-box-system-requirements.md#supported-operating-systems-for-clients) futtató adatforrás.

### <a name="option-1---initial-setup-via-server"></a>1. lehetőség – Kezdeti beállítás kiszolgálón keresztül

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. A konfigurációhoz használjon egy RJ45 Ethernet-kábelt a kiszolgálótól közvetlenül az MGMT-portig.
2. Egy DATA 3-porthoz készült RJ45- vagy SFP+ kábellel csatlakoztassa a DATA 1- vagy a DATA 2-portot az adatforrásként működő kiszolgálóhoz. Azt javasoljuk, hogy a jó teljesítmény érdekében 10-GbE DATA 1- és DATA 2- portokat használjon.
3. A kiszolgálón állítsa be a következő értékeket:

   - **IP-cím**: 192.168.100.5
   - **Alhálózat**: 255.255.255.0

     ![Két portos telepítés](media/data-box-cable-options/cabling-2-port-setup.png)

3. Nyissa meg az eszköz helyi webes felületét a 192.168.100.10 címen. Jelentkezzen be és oldja fel a Data Box zárolását az Azure Portalról beszerzett feloldási jelszóval.
4. Rendeljen statikus IP-címeket a konfigurált adatportokhoz.

### <a name="option-2---initial-setup-via-separate-computer"></a>2. lehetőség – Kezdeti beállítás különálló számítógépen keresztül

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. A különálló számítógépről származó RJ45 Ethernet-kábelt közvetlenül a MGMT-portra használhatja a konfiguráláshoz.
2. Egy DATA 3-porthoz készült RJ45- vagy SFP+ kábellel csatlakoztassa a DATA 1 vagy a DATA 2 portot a kiszolgálóhoz. Azt javasoljuk, hogy a jó teljesítmény érdekében 10-GbE DATA 1- és DATA 2- portokat használjon. Az adatportok egy 10-GbE-kapcsolón keresztül csatlakoznak az adatokat tartalmazó kiszolgálóhoz.
3. Konfigurálja az Ethernet-adaptert azon a laptopon, amellyel az eszközhöz csatlakozik:

   - **IP-cím**: 192.168.100.5
   - **Alhálózat**: 255.255.255.0.
  
   ![Két portos beállítás kapcsolóval](media/data-box-cable-options/cabling-with-static-ip.png)

3. Nyissa meg az eszköz helyi webes felületét a 192.168.100.10 címen. Jelentkezzen be és oldja fel a Data Box zárolását az Azure Portalról beszerzett feloldási jelszóval.
4. Azonosítsa a DHCP-kiszolgáló által hozzárendelt IP-címeket.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Átvitel DATA-porton keresztül statikus IP-címekkel és kapcsolóval 

Ezt a konfigurációt használja az 1 GbE- és a 10 gbE-hálózatokban több adatforráshoz.

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy RJ45 Ethernet-kábel az MGMT számára.
- Egy 10-GbE SFP+ Twinax-rézkábel minden csatlakoztatni kívánt 10-GbE-adatporthoz.
- Egy vagy több, [támogatott operációs rendszert](data-box-system-requirements.md#supported-operating-systems-for-clients) futtató adatforrás. Ezek az adatforrások különböző hálózatokon lehetnek, például 1-GbE- vagy 10-GbE-hálózatokon.

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. A konfigurációhoz használjon egy RJ45 Ethernet-kábelt a kiszolgálótól közvetlenül az MGMT-portig.
2. Egy DATA 3-porthoz készült RJ45- vagy SFP+ kábellel csatlakoztassa a DATA 1 vagy a DATA 2 portot a kiszolgálóhoz. Azt javasoljuk, hogy a jó teljesítmény érdekében 10-GbE DATA 1- és DATA 2- portokat használjon.
3. Konfigurálja az Ethernet-adaptert azon a laptopon, amellyel az eszközhöz csatlakozik:

   - **IP-cím**: 192.168.100.5
   - **Alhálózat**: 255.255.255.0.

     ![Két portos beállítás 2 kapcsolóval](media/data-box-cable-options/cabling-with-switch-static-ip.png)

3. Nyissa meg az eszköz helyi webes felületét a 192.168.100.10 címen. Jelentkezzen be és oldja fel a Data Box zárolását az Azure Portalról beszerzett feloldási jelszóval.
4. Rendeljen statikus IP-címeket a konfigurált adatportokhoz.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>Átvitel DATA-porton keresztül DHCP-környezetben

Használja ezt a konfigurációt, ha az eszköz DHCP-környezetben lesz.

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy RJ45-kábel, ha a DATA 1-portot szeretné csatlakoztatni.
- Egy 10-GbE SFP+ Twinax-rézkábel minden csatlakoztatni kívánt 10-GbE-adatporthoz.
- Egy vagy több, [támogatott operációs rendszert](data-box-system-requirements.md#supported-operating-systems-for-clients) futtató adatforrás. Ezek az adatforrások különböző hálózatokon lehetnek, például 1-GbE- vagy 10-GbE-hálózatokon.

Végezze el az alábbi lépéseket az eszköz bekábelezéséhez.

1. Egy RJ45- vagy SFP+ kábellel egy kapcsolón keresztül (ahol a DHCP-kiszolgáló elérhető) csatlakozzon a kiszolgálóhoz.

    ![Két port beállítása 3 kapcsolóval](media/data-box-cable-options/cabling-dhcp-data-only.png)

2. A DHCP-kiszolgálóval vagy a DNS-kiszolgálóval azonosítsa az IP-címet.
3. Egy ugyanazon a hálózaton lévő egyik kiszolgálóról érje el az eszköz helyi webes felületét a DHCP-kiszolgáló által hozzárendelt IP-címmel. Jelentkezzen be és oldja fel a Data Box zárolását az Azure Portalról beszerzett feloldási jelszóval.

## <a name="next-steps"></a>Következő lépések

- Az eszköz kábelezése után lépjen az [adatok Azure Data Boxba való másolását](data-box-deploy-copy-data.md) leíró szakaszra.
