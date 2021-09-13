# Alerts

```shell script
    - name: Ceph Cluster
      rules:
      - alert: CephState
        expr: ceph_health_status{rook_cluster="rook-ceph"} != 0
        for: 30m
        labels:
          severity: critical
        annotations:
          summary: "Ceph State (instance {{ $labels.instance }})"
          description: "Ceph instance unhealthy\n  VALUE = {{ $value }}\n  LABELS: {{ $labels.app }}"
      - alert: CephOsdDown
        expr: ceph_osd_up == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Ceph OSD Down (app {{ $labels.app }})"
          description: "Ceph Object Storage Daemon Down\n  VALUE = {{ $value }}\n  LABELS: {{ $labels.ceph_daemon }}"
      - alert: MonitorAvailableStorage
        expr: ceph_monitor_avail_percent < 15
        for: 60s
        labels:
          severity: critical
        annotations:
          description: Monitor storage for {{ $labels.monitor }} less than 15% - please check why its too high
          summary: Nonitor storage for  {{ $labels.monitor }} less than 15%
      - alert: CephOSDUtilizatoin
        expr: ceph_osd_utilization > 90
        for: 60s
        labels:
          severity: critical
        annotations:
          description: Osd free space for  {{ $labels.osd }} is higher tan 90%. Please validate why its so big, reweight or add storage
          summary: OSD {{ $labels.osd }} is going out of space
      - alert: CephPgDown
        expr: ceph_pg_down > 0
        for: 3m
        labels:
          severity: critical
        annotations:
          description: Some groups are down (unavailable) for too long on {{ $labels.cluster }}. Please ensure that all the data are available
          summary: PG DOWN [{{ $value }}] on {{ $labels.cluster }}
```

Reference: https://github.com/jsuchenia/ceph-prometheus-rules/blob/master/ceph.yml

