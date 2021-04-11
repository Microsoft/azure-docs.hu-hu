---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b3e097f1c41f3047dc4e9d6cae2a05a6b19dea9d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554757"
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) illesztőprogramok

Az NC, NCv2, NCv3, NCasT4_v3, ND és NDv2 sorozatú virtuális gépek NVIDIA Tesla-(CUDA-) illesztőprogramjai csak az alábbi táblázatban felsorolt operációs rendszereken támogatottak. Az illesztőprogram-letöltési hivatkozások a kiadvány aktuális időpontjában jelennek meg. A legújabb illesztőprogramokért látogasson el az [NVIDIA](https://www.nvidia.com/) webhelyére.

> [!TIP]
> A Windows Server rendszerű virtuális gépeken a CUDA-illesztőprogram manuális telepítésének alternatívájaként üzembe helyezhet egy Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) rendszerképet. A Windows Server 2016 DSVM kiadásai NVIDIA CUDA-illesztőprogramokat, a CUDA Deep neurális hálózati könyvtárat és más eszközöket telepítenek előre.


| Operációs rendszer | Illesztő |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-illesztőprogramok

A Microsoft a virtuális munkaállomásként vagy virtuális alkalmazásként használt NV-és NVv3-sorozatú virtuális gépek NVIDIA GRID-illesztőprogram-telepítőit terjeszti ki. Csak az alábbi táblázatban felsorolt operációs rendszereken telepítse ezeket a GRID-illesztőprogramokat az Azure NV-sorozatú virtuális gépeken. Ezek az illesztőprogramok közé tartoznak az Azure-beli GRID virtuális GPU-szoftverek licencelése. Nincs szükség az NVIDIA vGPU szoftverlicenc-kiszolgáló beállítására.

Az Azure által újraterjesztett GRID-illesztőprogramok nem működnek a nem NV sorozatú virtuális gépeken, például a NCv2, a NCv3, az ND és a NDv2 sorozatú virtuális gépeken. Az egyetlen kivétel a NCas_T4_V3 VM-sorozat, ahol a GRID-illesztőprogramok lehetővé teszik az NV-sorozathoz hasonló grafikus funkciókat.

Az NVIDIA K80 GPU-val való NC-Series nem támogatja a GRID/Graphics alkalmazásokat.  

Vegye figyelembe, hogy az NVIDIA-bővítmény mindig telepíti a legújabb illesztőprogramot. Az előző verzióra mutató hivatkozásokat biztosítunk azon ügyfelek számára, akik egy régebbi verziótól függenek.

Windows Server 2019, Windows Server 2016 1607, 1709 és Windows 10 rendszeren (akár Build 20H2):
- [Rács 12,1 (461,33)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Rács 12,0 (461,09)](https://download.microsoft.com/download/4/8/d/48d2d45b-bebc-44ad-9c58-e0b79a9d4ff2/461.09_grid_win10_server2016_server2019_64bit_azure_swl.exe) (. exe) 

Windows Server 2012 R2 esetén: 
- [Rács 12,1 (461,33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (. exe)
- [Rács 12,0 (461,09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (. exe) 


Az összes korábbi NVIDIA GRID-illesztőprogram-hivatkozás teljes listájáért látogasson el a [githubra](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
