---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078798"
---
A virtuális gép mérete határozza meg a virtuális gép számára elérhető számítási erőforrások (például a processzor, a GPU és a memória) mennyiségét. A virtuális gépeket a számítási feladatok számára megfelelő virtuálisgép-mérettel kell létrehoznia. Annak ellenére, hogy az összes gép ugyanazon a hardveren fut, a gépi méretek eltérő korlátokkal rendelkeznek a lemez-hozzáféréshez. Ez segítséget nyújt a virtuális gépeken a teljes lemezes hozzáférés kezelésében. Ha a munkaterhelés növekszik, akkor átméretezheti a meglévő virtuális gépeket is.

A következő virtuális gépek támogatottak a Azure Stack peremhálózati eszközön történő létrehozáshoz.

### <a name="dv2-series"></a>Dv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)   | Adatlemezek max. száma | Hálózati adapterek maximális száma |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3.5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>DSv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | Adatlemezek max. száma| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3.5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


További információ: [Dv2 és DSv2 sorozat](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 sorozat (előzetes verzió)

Ezek a méretek támogatottak az eszközön található GPU-alapú virtuális gépek esetében, és nagy számítási igényű GPU-gyorsított alkalmazásokhoz vannak optimalizálva. Ez a sorozat az NVIDIA Tesla T4 GPU-val jellemezhető számítási feladatokra összpontosít. 

|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | GPU | GPU memória (GiB) | Hálózati adapterek maximális száma |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

További információ: [NCasT4_v3 sorozat](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F-sorozat

Ez a sorozat számítási feladatokhoz van optimalizálva, és Intel Xeon processzorokon fut. 

| Méret | vCPU | Memória: GiB | Ideiglenes tároló (GiB) |  Adatlemezek max. száma | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

További információ: [Fsv2 sorozat](../articles/virtual-machines/fsv2-series.md).

