# INICIAL

R6 y R3, se configura el protocolo vrrp desde que se le asigna la ip, R6 |f0/0|-> master, R3 |f0/0|-> backup

## R6
```
    enable
    configure terminal
    interface f0/0
    ip address 10.19.64.5 255.255.192.0
    vrrp 1 ip 10.19.64.64
    vrrp 1 priority 150
    no shutdown
    exit
    do write
    interface f0/0
    ip address 10.19.0.2 255.255.192.0
    no shutdown
    exit
    do write
    end
```

## R3
```
    enable
    configure terminal
    interface f0/0
    ip address 10.19.64.2 255.255.192.0
    vrrp 1 ip 10.19.64.64
    no shutdown
    exit
    do write
    interface f0/1
    ip address 10.19.128.2 255.255.192.0
    no shutdown
    exit
    do write
    interface f2/0
    no switchport
    ip address 10.19.0.3 255.255.192.0
    no shutdown
    exit
    do write
    end
```

## R4
```
    enable
    configure terminal
    interface f0/1
    ip address 10.19.64.3 255.255.192.0
    do write
    no shutdown
    exit
    interface f0/0
    ip address 10.19.128.3 255.255.192.0
    do write
    no shutdown
    exit
    end
```

## R5
```
    enable
    configure terminal
    interface f0/1
    ip address 10.19.64.4 255.255.192.0
    no shutdown
    exit
    do write
    interface f0/0
    ip address 10.19.128.4 255.255.192.0
    do write
    no shutdown
    exit
    do write
    interface f1/0
    no swichtport
    ip address 10.19.192.3 255.255.192.0
    no shutdown
    exit
    do write
    end
```

## R7
```
    enable
    configure terminal
    interface f0/0
    ip address 10.19.128.5 255.255.192.0
    no shutdown
    exit
    do write
    interface f0/1
    ip address 10.19.192.2 255.255.192.0
    no shutdown
    exit
    do write
    end
```


# ROUTE RIP

## R - TODAS
```
    enable
    configure terminal
    no router rip
    router rip
    version 2
    network 10.19.0.0
    exit
    do write
    end
```

# HSRP