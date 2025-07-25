---
title: Configure access control lists with Azure NetApp Files
description: Learn how to configure access control lists (ACLs) on NFSv4.1 with Azure NetApp Files.
author: b-ahibbard
ms.service: azure-netapp-files
ms.topic: how-to
ms.date: 07/14/2025
ms.author: anfdocs
# Customer intent: "As a system administrator, I want to configure access control lists on NFSv4.1 volumes in Azure NetApp Files, so that I can manage fine-grained file permissions for users and groups to enhance security and control over shared resources."
---
# Configure access control lists on NFSv4.1 volumes for Azure NetApp Files

Azure NetApp Files supports access control lists (ACLs) on NFSv4.1 volumes. ACLs provide granular file security via NFSv4.1.

ACLs contain access control entities (ACEs), which specify the permissions (read, write, etc.) of individual users or groups. When assigning user roles, provide the user email address if you're using a Linux VM joined to an Active Directory Domain. Otherwise, provide user IDs to set permissions. 

To learn more about ACLs in Azure NetApp Files, see [Understand NFSv4.x ACLs](nfs-access-control-lists.md).

## Requirements

- ACLs can only be configured on NFS4.1 volumes. You can [convert a volume from NFSv3 to NFSv4.1](convert-nfsv3-nfsv41.md).

- You must have two packages installed:
    1.  `nfs-utils` to mount NFS volumes 
    1. `nfs-acl-tools` to view and modify NFSv4 ACLs. 
    If you do not have either, install them:
        - On a Red Hat Enterprise Linux or SUSE Linux instance:
        ```bash
        sudo yum install -y nfs-utils
        sudo yum install -y nfs4-acl-tools
        ```
        - On Ubuntu or Debian instance:
        ```bash
        sudo apt-get install nfs-common
        sudo apt-get install nfs4-acl-tools
        ```

## Configure ACLs

1. If you want to configure ACLs for a Linux VM joined to Active Directory, complete the steps in [Join a Linux VM to a Microsoft Entra Domain](join-active-directory-domain.md).

1. [Mount the volume](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

1. Use the command `nfs4_getfacl <path>` to view the existing ACL on a directory or file.
    
    The default NFSv4.1 ACL is a close representation of the POSIX permissions of 770.
    - `A::OWNER@:rwaDxtTnNcCy` - owner has full (RWX) access
    - `A:g:GROUP@:rwaDxtTnNcy` - group has full (RWX) access
    - `A::EVERYONE@:tcy` - everyone else has no access

1. To modify an ACE for a user, use the `nfs4_setfacl` command: `nfs4_setfacl -a|x A|D|U::<user|group>:<permissions_alias> <file>`

    - Use `-a` to add permission. Use `-x` to remove permission.
    - `A` creates access; `D` denies access. `U:` is used for audit ACEs to log access attempts. 
    - In an Active Directory-joined set up, enter an email address for the user. Otherwise, enter the numerical user ID.
    - Permission aliases include read, write, append, execute, and others. For a full list of permissions, see: [NFSv4.x permissions](nfs-access-control-lists.md#nfsv4x-permissions). 
    In the following Active Directory-joined example, user regan@contoso.com is given read, write, and execute access to `/nfsldap/engineering`:
    ```bash
    nfs4_setfacl -a A::regan@contoso.com:RWX /nfsldap/engineering
    ```
    - If you're configuring an ACE for [file access logs](manage-file-access-logs.md), you must use the `U:` prefix to denote the ACE is an audit ACE. The following example configures an audit log for everyone for successful and failed access attempts:
    `nfs4_setfacl -a U:fdiSF:EVERYONE@:rwaDdxtTnNcCoy /<mount_point>`.
    - To apply ACLs recursively on a directory and its contents, use the `-R` option with the `nfs4_setfacl` command. This option ensures the ACL changes are applied to all files and subdirectories within the specified directory.

## Next steps

* [Configure NFS clients](configure-nfs-clients.md)
* [Understand NFSv4.x ACLs](nfs-access-control-lists.md)
