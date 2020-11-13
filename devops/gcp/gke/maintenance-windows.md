# Maintenance windows

> References:
> https://cloud.google.com/kubernetes-engine/docs/concepts/maintenance-windows-and-exclusions
> https://cloud.google.com/kubernetes-engine/docs/how-to/maintenance-windows-and-exclusions


A [**maintenance window**](https://cloud.google.com/kubernetes-engine/docs/concepts/maintenance-windows-and-exclusions#maintenance_windows) is an arbitrary, repeating window of time during which automatic maintenance are permitted.

A [**maintenance exclusion**](https://cloud.google.com/kubernetes-engine/docs/concepts/maintenance-windows-and-exclusions#exclusions) is an arbitrary non-repeating window of time during which automatic maintenance is forbidden. A cluster can have up to three maintenance exclusions at a time.


### Examples of automatic maintenance

- [Auto-upgrades to cluster control planes (masters)](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-upgrades#cluster_upgrades) in accordance with GKE's [version policy](https://cloud.google.com/kubernetes-engine/versioning-and-upgrades)
- [Node auto-upgrades](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-upgrades#upgrading_automatically), if enabled
- User-initiated configuration changes that cause nodes to be re-created, such as [GKE Sandbox](https://cloud.google.com/kubernetes-engine/docs/how-to/sandbox-pods).
- User-initiated configuration changes that fundamentally change the cluster's internal network topology, such as [optimizing IP address allocation](https://cloud.google.com/kubernetes-engine/docs/how-to/flexible-pod-cidr)

---

GKE reserves the right to roll out unplanned, emergency upgrades outside of maintenance windows. Additionally, mandatory upgrades to upgrade from deprecated or outdated software might automatically occur outside of maintenance windows.

---

GKE clusters and workloads can also be impacted by automatic maintenance on other, dependent services, such as Compute Engine. Maintenance windows and exclusions do not affect automatic maintenance on other services.

---

GKE performs automated repairs on [control planes](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-architecture#control_plane). This includes processes like upscaling the control plane to an appropriate size or restarting the control plane to resolve issues. Most repairs ignore maintenance windows and exclusions because failing to perform the repairs can result in non-functional clusters. Repairing control planes cannot be disabled.

Nodes also have [auto-repair functionality](https://cloud.google.com/kubernetes-engine/docs/how-to/node-auto-repair), but can be disabled.

---

Some examples of features that cause nodes to be recreated are:
- [Shielded nodes](https://cloud.google.com/kubernetes-engine/docs/how-to/shielded-gke-nodes)
- [Network policies](https://cloud.google.com/kubernetes-engine/docs/how-to/network-policy)
- [Intranode visibility](https://cloud.google.com/kubernetes-engine/docs/how-to/intranode-visibility)
- [Rotating the control plane's IP address](https://cloud.google.com/kubernetes-engine/docs/how-to/ip-rotation)

If you use maintenance windows and you enable or modify a feature or option that requires nodes to be recreated, the new configuration is applied to the nodes **only during a maintenance window**. You can manually "upgrade" the node pool to the same version it is already using, by setting the `--cluster-version` flag to the same GKE version the nodes are already running.

---

You can only configure a single maintenance window per cluster. 

---

When configuring maintenance windows using the older `--maintenance-window` flag, you cannot specify a timezone. 

UTC is used when using the `gcloud` command or the API.

Google Cloud Console displays times using the local timezone.

---

#### Commands

```bash
# Format: 
# https://tools.ietf.org/html/rfc5545#section-3.8.2.4
# https://tools.ietf.org/html/rfc5545#section-3.8.5.3
gcloud container clusters update xxxxxxxxxxx \
  --maintenance-window-start 2020-10-25T00:00:00-00:00 \
  --maintenance-window-end 2020-10-26T00:00:00-00:00 \
  --maintenance-window-recurrence FREQ=WEEKLY;BYDAY=SU

# Removing a maintenance window
gcloud container clusters update xxxxxxxxxxx \
  --clear-maintenance-window

# Weekly on Tuesdays and Wednesdays, starting August 27, 2019, for the entire day
--maintenance-window-start 2019-08-27T00:00:00Z \
--maintenance-window-end 2019-08-28T00:00:00Z \
--maintenance-window-recurrence 'FREQ=WEEKLY;BYDAY=TU,WE'

# Daily on weekdays from 9:00-17:00 UTC-4
--maintenance-window-start 2019-09-02T09:00:00-04:00 \
--maintenance-window-end 2019-09-02T17:00:00-04:00 \
--maintenance-window-recurrence 'FREQ=WEEKLY;BYDAY=MO,TU,WE,TH,FR'

# Weekly at 4PM for 8 hours, UTC-7
--maintenance-window-start 2019-08-13T16:00:00-7:00 \
--maintenance-window-end 2019-08-14T00:00:00-7:00 \
--maintenance-window-recurrence FREQ=WEEKLY

# Removing a maintenance exclusion
gcloud container clusters update xxxxxxxxxxx \
  --remove-maintenance-exclusion exclusion-name

# This example shows how to prevent a maintenance window from occurring from Black Friday 2019 (November 29, 2019) 
# to Cyber Monday 2019 (December 2, 2019), from midnight on the east coast (UTC-5) 
# to 23:59:59 on the west coast (UTC-7).
gcloud container clusters update xxxxxxxxxxx \
 --add-maintenance-exclusion-name black-friday \
 --add-maintenance-exclusion-start 2019-11-29T00:00:00-05:00 \
 --add-maintenance-exclusion-end 2019-12-02T23:59:59-07:00

# Viewing a cluster's maintenance policy
gcloud container clusters describe xxxxxxxxxxx

maintenancePolicy:
  resourceVersion: 377d5d70
  window:
    recurringWindow:
      recurrence: FREQ=WEEKLY;BYDAY=SA,SU
      window:
        endTime: '2025-08-12T05:00:00Z'
        startTime: '2020-08-12T05:00:00Z'
```
