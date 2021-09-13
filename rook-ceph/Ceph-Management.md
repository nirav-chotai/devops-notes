# Common Ceph Commands

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph health detail
HEALTH_OK
```

```shell script
(base) ➜  ~ kubectl -n rook-ceph exec -it $(kubectl -n rook-ceph get pod -l "app=rook-ceph-tools" -o jsonpath='{.items[0].metadata.name}') -- bash
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph -s
  cluster:
    id:     c40d82d5-3193-457d-a628-a3db67839a37
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum b,d,e (age 4d)
    mgr: a(active, since 2d)
    osd: 8 osds: 8 up (since 7d), 8 in (since 9d)
 
  data:
    pools:   4 pools, 97 pgs
    objects: 2.22M objects, 8.4 TiB
    usage:   25 TiB used, 77 TiB / 102 TiB avail
    pgs:     96 active+clean
             1  active+clean+scrubbing+deep
 
  io:
    client:   341 B/s rd, 9.3 MiB/s wr, 0 op/s rd, 155 op/s wr
 
[root@rook-ceph-tools-b5765bff4-fprsr /]# 
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd status
ID  HOST          USED  AVAIL  WR OPS  WR DATA  RD OPS  RD DATA  STATE      
 4  k8s-prod-22  4033G  9005G      3      772k      0        0   exists,up  
 9  k8s-prod-24  3229G  9809G     11     2673k      0        0   exists,up  
10  k8s-prod-21  3766G  9272G     33     12.8M     21      140k  exists,up  
11  k8s-prod-23  3226G  9812G     34     3269k      1        0   exists,up  
12  k8s-prod-21  2420G  10.3T     14     3095k      1        0   exists,up  
13  k8s-prod-23  4034G  9004G     20      310k      0        0   exists,up  
14  k8s-prod-22  2424G  10.3T      1     10.3k      0        0   exists,up  
15  k8s-prod-24  2692G  10.1T     10     8907k      2        0   exists,up  
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd tree
ID   CLASS  WEIGHT     TYPE NAME                      STATUS  REWEIGHT  PRI-AFF
 -1         101.86719  root default   
-19          25.46680      host k8s-prod-21                                                                          
 10    hdd   12.73340          osd.10                     up   1.00000  1.00000
 12    hdd   12.73340          osd.12                     up   1.00000  1.00000
-13          25.46680      host k8s-prod-22                            
  4    hdd   12.73340          osd.4                      up   1.00000  1.00000
 14    hdd   12.73340          osd.14                     up   1.00000  1.00000
-21          25.46680      host k8s-prod-23                            
 11    hdd   12.73340          osd.11                     up   1.00000  1.00000
 13    hdd   12.73340          osd.13                     up   1.00000  1.00000
-17          25.46680      host k8s-prod-24                            
  9    hdd   12.73340          osd.9                      up   1.00000  1.00000
 15    hdd   12.73340          osd.15                     up   1.00000  1.00000
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd utilization
avg 36.375
stddev 5.91476 (expected baseline 5.64164)
min osd.15 with 25 pgs (0.687285 * mean)
max osd.4 with 42 pgs (1.15464 * mean)
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd crush tree
ID   CLASS  WEIGHT     TYPE NAME                    
 -1         101.86719  root default                 
-19          25.46680      host k8s-prod-21 
 10    hdd   12.73340          osd.10               
 12    hdd   12.73340          osd.12               
-13          25.46680      host k8s-prod-22 
  4    hdd   12.73340          osd.4                
 14    hdd   12.73340          osd.14               
-21          25.46680      host k8s-prod-23 
 11    hdd   12.73340          osd.11               
 13    hdd   12.73340          osd.13               
-17          25.46680      host k8s-prod-24 
  9    hdd   12.73340          osd.9                
 15    hdd   12.73340          osd.15                         
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd df
ID  CLASS  WEIGHT    REWEIGHT  SIZE     RAW USE  DATA     OMAP     META     AVAIL    %USE   VAR   PGS  STATUS
10    hdd  12.73340   1.00000   13 TiB  3.7 TiB  3.7 TiB  5.1 MiB  7.2 GiB  9.1 TiB  28.89  1.17   39      up
12    hdd  12.73340   1.00000   13 TiB  2.4 TiB  2.4 TiB  2.3 MiB  4.6 GiB   10 TiB  18.56  0.75   33      up
 4    hdd  12.73340   1.00000   13 TiB  3.9 TiB  3.9 TiB  816 KiB  7.8 GiB  8.8 TiB  30.94  1.25   42      up
14    hdd  12.73340   1.00000   13 TiB  2.4 TiB  2.4 TiB  1.4 MiB  5.1 GiB   10 TiB  18.60  0.75   30      up
11    hdd  12.73340   1.00000   13 TiB  3.2 TiB  3.1 TiB  1.3 MiB  6.6 GiB  9.6 TiB  24.74  1.00   39      up
13    hdd  12.73340   1.00000   13 TiB  3.9 TiB  3.9 TiB  4.4 MiB  7.7 GiB  8.8 TiB  30.94  1.25   41      up
 9    hdd  12.73340   1.00000   13 TiB  3.2 TiB  3.1 TiB  3.8 MiB  6.6 GiB  9.6 TiB  24.77  1.00   42      up
15    hdd  12.73340   1.00000   13 TiB  2.6 TiB  2.6 TiB  219 KiB  6.0 GiB   10 TiB  20.65  0.83   25      up
                        TOTAL  102 TiB   25 TiB   25 TiB   19 MiB   52 GiB   77 TiB  24.76                   
MIN/MAX VAR: 0.75/1.25  STDDEV: 4.83
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph osd pool stats
pool replicapool id 1
  client io 49 KiB/s rd, 7.3 MiB/s wr, 2 op/s rd, 58 op/s wr

pool device_health_metrics id 2
  nothing is going on

pool myfs-metadata id 3
  nothing is going on

pool myfs-data0 id 4
  nothing is going on
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph pg stat
97 pgs: 1 active+clean+scrubbing+deep, 96 active+clean; 8.4 TiB data, 25 TiB used, 77 TiB / 102 TiB avail; 18 KiB/s rd, 22 MiB/s wr, 178 op/s
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph crash stat
2 crashes recorded
2 older than 1 days old:
2020-12-07T03:52:09.884281Z_531ab7d2-f864-4b77-8008-2aa1d9d5eede
2021-02-06T06:26:09.844214Z_1600ce34-cba6-4fe4-af50-500d858e50fe
2 older than 3 days old:
2020-12-07T03:52:09.884281Z_531ab7d2-f864-4b77-8008-2aa1d9d5eede
2021-02-06T06:26:09.844214Z_1600ce34-cba6-4fe4-af50-500d858e50fe
2 older than 7 days old:
2020-12-07T03:52:09.884281Z_531ab7d2-f864-4b77-8008-2aa1d9d5eede
2021-02-06T06:26:09.844214Z_1600ce34-cba6-4fe4-af50-500d858e50fe
```

```shell script
[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph crash ls
ID                                                                ENTITY  NEW  
2020-12-07T03:52:09.884281Z_531ab7d2-f864-4b77-8008-2aa1d9d5eede  mon.a        
2021-02-06T06:26:09.844214Z_1600ce34-cba6-4fe4-af50-500d858e50fe  mon.a  
```

```shell script
# Troubleshooting for Ceph Dashboard
ceph config dump
ceph mgr module enable dashboard
ceph mgr module ls
ceph mgr services
ceph config set mgr mgr/dashboard/server_addr 0.0.0.0
ceph config rm mgr mgr/dashboard/server_addr 0.0.0.0
```

```shell script
# OSD Removal
ceph osd out osd.<ID>
ceph osd purge <ID> --yes-i-really-mean-it
# crush map
ceph osd crush rm <node-crush-name>

```