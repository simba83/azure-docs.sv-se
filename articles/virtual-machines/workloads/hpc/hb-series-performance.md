---
title: Prestanda för virtuell dator i HB-serien
description: Lär dig mer om prestanda testnings resultat för VM-storlekar i HB i Azure.
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2267dc23e2f886d87342fc22c3b12a03e8df6a86
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994847"
---
# <a name="hb-series-virtual-machine-sizes"></a>HB-seriens storlekar på virtuella datorer

Flera prestandatester har körts på HB-seriens storlekar. Följande är några av resultaten av den här prestanda testningen.

| Arbetsbelastning                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM-Triad                                    | 260 GB/s (32-33 GB/s per CCX)  |
| High-Performance Linpack (HPL)                  | 1 000 GigaFLOPS (Rpeak), 860 GigaFLOPS (Rmax) |
| RDMA-latens & bandbredd                        | 1,27 mikrosekunder, 99,1 GB/s   |
| FIO på lokala NVMe SSD                           | 1,7 GB/s läsningar, 1,0 GB/s skrivningar      |  
| IOR på 4 * Azure Premium SSD (P30 Managed Disks, RAID0) * *  | 725 MB/s läsningar, 780 MB/skrivningar   |


## <a name="mpi-latency"></a>MPI-svars tid

MPI latens-test från OSU mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="MPI svars tid på Azure HB.":::

## <a name="mpi-bandwidth"></a>MPI-bandbredd

MPI för bandbredds test från OSU-mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="MPI svars tid på Azure HB.":::


## <a name="mellanox-perftest"></a>Mellanox perftest

[Mellanox perftest-paketet](https://community.mellanox.com/s/article/perftest-package) har många InfiniBand-test, till exempel latens (ib_send_lat) och bandbredd (ib_send_bw). Ett exempel kommando är nedan.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Nästa steg

- Läs om de senaste meddelandena och vissa HPC-exempel (data behandling med höga prestanda) och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En mer övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
