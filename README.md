# Template Zabbix para Zerto Virtual Manager

Template para **Zabbix 7.4** que monitorea **Zerto Virtual Manager (ZVM)**: estado de replicación, RPO, checkpoints, VRAs y detección temprana de posible ransomware. Los datos llegan a Zabbix mediante un **colector en Python** que consulta la API de Zerto y envía los valores como items *trapper*, con control de duplicidad por sitio.

## Qué monitorea

| Componente | Descubrimiento automático | Métricas principales |
| --- | --- | --- |
| VPGs (grupos de protección) | Sí | Estado y sub-estado de replicación, RPO actual vs. configurado, checkpoints, journal, IOPS, throughput, storage, último test de failover, RTO de la última recuperación |
| VMs protegidas | Sí | Estado de replicación, RPO, journal usado, storage, IOPS, throughput, sitio origen y destino |
| VRAs | Sí | Estado, versión, host ESXi, datastore, CPU y memoria, VMs/VPGs protegidos, entradas huérfanas |
| Peer sites | Sí | Ancho de banda saliente y throughput entrante (Mbps) |
| Global | — | Alertas activas (error/warning), eventos, tareas en curso, conteo de VMs, volúmenes y VPGs con encriptación sospechosa |

## Alertas incluidas

- **RPO** por encima del umbral de warning o crítico.
- **Checkpoint** más reciente demasiado antiguo.
- VPG o VM **sin cumplir el SLA** de replicación.
- **Sub-estados críticos** de replicación (errores de storage, journal, desconexión del peer, pausas, etc.).
- **Posible ransomware o encriptación anómala** detectada por Zerto (prioridad *Disaster*).
- VPG con **VMs que ya no existen** en el inventario.
- VRA fuera del estado *Installed*, con alertas activas o como entrada huérfana.

Las alertas usan **dependencias** para evitar ruido: si hay sospecha de ransomware o un sub-estado crítico, no se dispara además la alerta genérica de SLA.

## Umbrales configurables (macros)

| Macro | Valor por defecto | Descripción |
| --- | --- | --- |
| `{$ZERTO.RPO.WARN}` | 900 s (15 min) | Umbral de warning de RPO |
| `{$ZERTO.RPO.CRIT}` | 3600 s (1 h) | Umbral crítico de RPO |
| `{$ZERTO.CHECKPOINT.CRIT}` | 7200 s (2 h) | Antigüedad máxima del checkpoint más reciente |

## Instalación

1. En Zabbix, ve a **Data collection → Templates → Import**.
2. Importa `Zerto_Template.json`.
3. Crea un host que represente el ZVM y vincúlale el template **Zerto Virtual Manager**.
4. Configura el colector en Python para que envíe los datos a ese host con `zabbix_sender`.
5. Ajusta las macros de RPO y checkpoint según el SLA de cada cliente.

> Algunas alertas vienen deshabilitadas por defecto para que las actives según tu entorno.

## Tecnologías

Zabbix 7.4 · Python · API REST de Zerto · Low-Level Discovery · Zabbix trapper

## Autor

**Kleiver Ruiz** — [LinkedIn](https://www.linkedin.com/in/kleiver-ruiz-medina) · [GitHub](https://github.com/kleiverru)

## Licencia

GPL-3.0
