---
title: Azure VMware-megoldás elérése helyszíni CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure VMware-megoldás elérése a helyszíni hálózatról a tűzfalon keresztül történő CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3cc9ad0c694be8f95ddcffbc3d952d59b83f89f3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077210"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Hozzáférés a CloudSimple saját felhőalapú környezetéhez és a helyszíni alkalmazásokhoz

Egy kapcsolat beállítható a helyszíni hálózatról az Azure ExpressRoute vagy a helyek közötti VPN-t használó CloudSimple.  A kapcsolat használatával elérheti a CloudSimple saját Felhőbeli vCenter és a privát felhőben futtatott számítási feladatokat.  Megadhatja, hogy milyen portok legyenek megnyitva a kapcsolatban a helyszíni hálózat tűzfala használatával.  Ez a cikk a tipikus alkalmazások portokra vonatkozó követelményeit tárgyalja.  Bármely más alkalmazás esetében tekintse meg az alkalmazás dokumentációjában a portokra vonatkozó követelményeket.

## <a name="ports-required-for-accessing-vcenter"></a>A vCenter eléréséhez szükséges portok

A saját felhőalapú vCenter és a NSX-T kezelőhöz való hozzáféréshez az alábbi táblázatban definiált portokat meg kell nyitni a helyszíni tűzfalon.  

| Port       | Forrás                           | Cél                      | Cél                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Helyszíni DNS-kiszolgálók          | Privát Felhőbeli DNS-kiszolgálók        | Szükséges a *az.CLOUDSIMPLE.IO* DNS-keresésének továbbításához a helyszíni hálózatról a saját felhőalapú DNS-kiszolgálókra.       |
| 53 (UDP)   | Privát Felhőbeli DNS-kiszolgálók        | Helyszíni DNS-kiszolgálók          | A DNS továbbításához a helyszíni tartománynevek a helyszíni DNS-kiszolgálókra való átadásához szükségesek. |
| 80 (TCP)   | Helyszíni hálózat              | Saját felhőalapú felügyeleti hálózat | A vCenter URL-címének *http* -ről *https*-re való átirányításához szükséges.                                                           |
| 443 (TCP)  | Helyszíni hálózat              | Saját felhőalapú felügyeleti hálózat | A vCenter és a NSX-T kezelő helyszíni hálózatról való eléréséhez szükséges.                                             |
| 8000 (TCP) | Helyszíni hálózat              | Saját felhőalapú felügyeleti hálózat | A helyszíni környezetből a saját felhőbe irányuló virtuális gépek vMotion szükséges.                                            |
| 8000 (TCP) | Saját felhőalapú felügyeleti hálózat | Helyszíni hálózat              | A virtuális gépeknek a helyszíni környezetbe való vMotion szükséges.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>A helyszíni Active Directory identitás forrásaként való használatához szükséges portok

Ha a helyszíni Active Directoryt identitás forrásaként szeretné konfigurálni a saját felhőalapú vCenter, meg kell nyitni a táblázatban definiált portokat.  Lásd: az [Azure ad használata identitás-szolgáltatóként a CloudSimple vCenter a](./azure-ad.md) konfigurációs lépésekhez.

| Port         | Forrás                           | Cél                                         | Cél                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Privát Felhőbeli DNS-kiszolgálók        | Helyszíni DNS-kiszolgálók                             | A DNS továbbításához a helyszíni Active Directory-tartománynevek a saját Felhőbeli vCenter a helyszíni DNS-kiszolgálókra való megkeresése szükséges.          |
| 389 (TCP/UDP) | Saját felhőalapú felügyeleti hálózat | Helyszíni Active Directory-tartományvezérlők     | A Private Cloud vCenter-kiszolgálóról az Active Directory-tartományvezérlők felhasználói hitelesítéshez való LDAP-kommunikációhoz szükséges.                |
| 636 (TCP)     | Saját felhőalapú felügyeleti hálózat | Helyszíni Active Directory-tartományvezérlők     | A biztonságos LDAP (LDAPs) kommunikációhoz szükséges a Private Cloud vCenter-kiszolgálóról az Active Directory-tartományvezérlőkre a felhasználói hitelesítéshez. |
| 3268 (TCP)    | Saját felhőalapú felügyeleti hálózat | Helyszíni Active Directory globáliskatalógus-kiszolgálók | Több tartományvezérlős üzemelő példányokban történő LDAP-kommunikációhoz szükséges.                                                                        |
| 3269 (TCP)    | Saját felhőalapú felügyeleti hálózat | Helyszíni Active Directory globáliskatalógus-kiszolgálók | Több tartományvezérlős üzemelő példányokban történő LDAP-kommunikációhoz szükséges.                                                                       |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>A számítási feladatok virtuális gépei eléréséhez szükséges általános portok

A privát felhőben futó számítási feladatok virtuális gépei számára szükséges, hogy a helyszíni tűzfalon a portok meg legyenek nyitva.  Az alábbi táblázat a szükséges általános portok némelyikét és azok célját mutatja be.  Az alkalmazás-specifikus portokra vonatkozó követelményekért tekintse meg az alkalmazás dokumentációját.

| Port         | Forrás                         | Cél                          | Cél                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Helyszíni hálózat            | Saját Felhőbeli számítási feladatok hálózata       | Biztonságos rendszerhéj-hozzáférés a privát felhőben futó linuxos virtuális gépekhez.              |
| 3389 (TCP)    | Helyszíni hálózat            | Saját Felhőbeli számítási feladatok hálózata       | Távoli asztalról a privát felhőben futó Windows rendszerű virtuális gépekre.                 |
| 80 (TCP)      | Helyszíni hálózat            | Saját Felhőbeli számítási feladatok hálózata       | A privát felhőben futó virtuális gépeken üzembe helyezett webkiszolgálók elérése.        |
| 443 (TCP)     | Helyszíni hálózat            | Saját Felhőbeli számítási feladatok hálózata       | Hozzáférés a privát felhőben futó virtuális gépeken üzembe helyezett biztonságos webkiszolgálókhoz. |
| 389 (TCP/UDP) | Saját Felhőbeli számítási feladatok hálózata | Helyszíni Active Directory-hálózat | Csatlakoztassa a Windows munkaterhelés virtuális gépeket a helyszíni Active Directory-tartományhoz.       |
| 53 (UDP)      | Saját Felhőbeli számítási feladatok hálózata | Helyszíni hálózat                  | DNS-szolgáltatás elérése a számítási feladatok virtuális gépei számára a helyszíni DNS-kiszolgálókra.         |

## <a name="next-steps"></a>Következő lépések

* [VLAN-ok és alhálózatok létrehozása és kezelése](./create-vlan-subnet.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](./on-premises-connection.md)
* [Helyek közötti VPN beállítása a helyszíni környezetből](./vpn-gateway.md)
