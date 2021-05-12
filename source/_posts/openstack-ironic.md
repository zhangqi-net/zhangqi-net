---
title: openstack ironic
date: 2021-05-12 12:53:09
tags: openstack, ironic, train
---

## Ironic

Ironic项目一直是使用率不高的项目之一，能活到W版并且可预期在X版还有功能更新也实属不易。

厂商在自家公有云推广裸金属服务时也没有见到纯粹使用openstack ironic项目的，毕竟从成熟度、社区活跃度等方面来看还是很不够。

但是对于中小企业私有云平台来说，使用ironic可以带来一定的好处（多少需要自行评估）。

本文仅总结一下ironic项目的一些问题（作者最新只用到T版，因此在UVW中的新特性可能已经解决了本文中列举的问题。

## 安装

要点：
- 推荐使用kolla-ansible安装ironic。
- 需要自制ramdisk、kernel与sys image。
- 安装完成后需要额外配置相应的网络。

### kolla-ansible配置
T版中kolla-ansible默认依赖两个文件，这个在precheck时会检查出来，如果此时还没有做好这两个镜像文件没有关系，可以简单的在/etc/kolla中touch两个空文件。

相关代码：
- https://github.com/openstack/kolla-ansible/blob/stable/train/ansible/roles/ironic/tasks/precheck.yml

```
  with_items:
    - "ironic-agent.kernel"
    - "ironic-agent.initramfs"
```

### 自制ramdisk、kernel
TODO

### 自制sys image
TODO

### 网络相关配置
TODO

## ironic配置与使用

### inspector

W版在发布视频中有讲inspector的改进，暂时还没有尝试。

T版以及之前版本中，ironic-inspector一直独立于ironic项目运行，需要手工进行若干配置：
- dnsmasq: 注意，这个dnsmasq区别于neutron的dhcp，是专门为ironic-inspector运行的，所有配置均需要手工进行，包括指定网卡、IP地址段等，默认可以支持UEFI等。
- ironic_dnsmasq_interface 对应inspector程序运行监听的网卡（可以是vlan子接口）
- ironic_dnsmasq_dhcp_range  给他分配一个DHCP段

#### inspector.conf
```
...
[processing]
ramdisk_logs_dir = /var/log/kolla/ironic-inspector
always_store_ramdisk_logs = true
store_data = database
add_ports = all
keep_ports = all
overwrite_existing = true
processing_hooks = ramdisk_error,root_disk_selection,scheduler,validate_interfaces,capabilities,pci_devices,lldp_basic,local_link_connection,raid_device,extra_hardware
...
```

#### ironic-inspector
如需使用inspector进行自动发现并且将相应网卡信息加入node info，代码中有相应缺失，W版代码中没有发现修正痕迹
- ironic_inspector/plugins/local_link_connection.py
```
diff --git a/ironic_inspector/plugins/local_link_connection.py b/ironic_inspector/plugins/local_link_connection.py
index aea9faa..892d7c8 100644
--- a/ironic_inspector/plugins/local_link_connection.py
+++ b/ironic_inspector/plugins/local_link_connection.py
@@ -32,9 +32,11 @@ CONF = cfg.CONF

 PORT_ID_ITEM_NAME = "port_id"
 SWITCH_ID_ITEM_NAME = "switch_id"
+SWITCH_SYSTEM_NAME = "switch_info"

 LLDP_PROC_DATA_MAPPING =\
     {lldp_parsers.LLDP_CHASSIS_ID_NM: SWITCH_ID_ITEM_NAME,
+     lldp_parsers.LLDP_SYS_NAME_NM: SWITCH_SYSTEM_NAME,
      lldp_parsers.LLDP_PORT_ID_NM: PORT_ID_ITEM_NAME}
```