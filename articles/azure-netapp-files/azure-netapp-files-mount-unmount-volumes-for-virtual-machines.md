---
title: Montera Azure NetApp Files volymer för virtuella datorer
description: Lär dig hur du monterar eller demonterar en volym för virtuella Windows-datorer eller virtuella Linux-datorer i Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: eebb0a8dc23a6a442a170dcc6e19495f96ce2921
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930602"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montera eller demontera en volym för virtuella Windows- eller Linux-datorer 

Du kan montera eller demontera en volym för virtuella Windows-eller Linux-datorer om det behövs.  Monterings anvisningarna för virtuella Linux-datorer finns på Azure NetApp Files.  

## <a name="requirements"></a>Krav 

* Du måste ha minst en export princip för att kunna komma åt en NFS-volym.
* För att montera en NFS-volym ser du till att följande NFS-portar är öppna mellan klienten och NFS-volymerna:
    * 111 = `RPCBIND/Portmapper`
    * 635 = `mountd`
    * 2049 = `nfs`
    * 4045 = `nlockmgr` (endast NFSv3)
    * 4046 = `status` (endast NFSv3)

## <a name="steps"></a>Steg

1. Klicka på bladet **volymer** och välj sedan den volym som du vill montera. 
2. Klicka på **monterings instruktioner** från den valda volymen och följ sedan anvisningarna för att montera volymen. 

    ![Monterings instruktioner NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Monterings instruktioner SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Om du monterar en NFS-volym måste du kontrol lera att du använder `vers` alternativet i `mount` kommandot för att ange den NFS-protokollversion som motsvarar den volym som du vill montera. 
    * Om du använder NFSv 4.1 använder du följande kommando för att montera fil systemet:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Om du använder NFSv 4.1 ser du till att alla virtuella datorer som monterar exporten använder unika värdnamn.

3. Om du vill att en NFS-volym ska monteras automatiskt när en virtuell Azure-dator startas eller startas om, lägger du till en post i `/etc/fstab` filen på värden. 

    Exempelvis:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` är IP-adressen för den Azure NetApp Files volym som finns på bladet volym egenskaper.
    * `$FILEPATH` är export Sök vägen för Azure NetApp Files volym.
    * `$MOUNTPOINT` är katalogen som skapas på den Linux-värd som används för att montera NFS-exporten.

4. Om du vill montera volymen till Windows med NFS:

    a. Montera volymen på en virtuell UNIX-eller Linux-dator först.  
    b. Kör ett `chmod 777` eller- `chmod 775` kommando mot volymen.  
    c. Montera volymen via NFS-klienten i Windows.
    
5. Om du vill montera en NFS Kerberos-volym läser du [Konfigurera nfsv 4.1 Kerberos-kryptering](configure-kerberos-encryption.md) för ytterligare information. 

## <a name="next-steps"></a>Nästa steg

* [Konfigurera NFSv4.1-standarddomän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Vanliga frågor och svar om NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Översikt över Network File System](/windows-server/storage/nfs/nfs-overview)
* [Montera en NFS Kerberos-volym](configure-kerberos-encryption.md#kerberos_mount)