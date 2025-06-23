

```vb
OLT(config)# display ont autofind all		# 查看未添加的光猫
OLT(config)# interface gpon 0/1			 # 配置模式进入GPON板卡 (如 0/1板卡)
```

添加光猫

```vb

OLT(config)# service-port vlan 100 gpon 0/1/1 ont 17 gemport 1 multi-service user-vlan 100 tag-transform translate inbound traffic-table index 50 outbound traffic-table index 50
```



修改上下行带宽

```vb
OLT(config)# undo service-port vlan 100 gpon 0/1/1 ont 17 gemport 1 
```

