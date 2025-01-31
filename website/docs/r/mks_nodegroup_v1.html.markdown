---
layout: "selectel"
page_title: "Selectel: selectel_mks_nodegroup_v1"
sidebar_current: "docs-selectel-resource-mks-nodegroup-v1"
description: |-
  Creates and manages a node group in Selectel Managed Kubernetes using public API v1.
---

# selectel\_mks\_nodegroup\_v1

Creates and manages a Managed Kubernetes node group using public API v1. For more information about node groups, see the [official Selectel documentation](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/).

## Example usage

```hcl
resource "selectel_mks_nodegroup_v1" "nodegroup_1" {
  cluster_id        = selectel_mks_cluster_v1.cluster_1.id
  project_id        = selectel_mks_cluster_v1.cluster_1.project_id
  region            = selectel_mks_cluster_v1.cluster_1.region
  availability_zone = "ru-3a"
  nodes_count       = 3
  cpus              = 2
  ram_mb            = 2048
  volume_gb         = 20
  volume_type       = "fast.ru-3a"
  labels            = {
    "label-key0": "label-value0",
    "label-key1": "label-value1",
    "label-key2": "label-value2",
  }
  taints {
    key    = "test-key-0"
    value  = "test-value-0"
    effect = "NoSchedule"
  }
  taints {
    key    = "test-key-1"
    value  = "test-value-1"
    effect = "NoExecute"
  }
  taints {
    key    = "test-key-2"
    value  = "test-value-2"
    effect = "PreferNoSchedule"
  }
}
```

## Argument Reference

* `cluster_id` - (Required) Unique identifier of the associated Managed Kubernetes cluster. Changing this creates a new node group. You can create up to eight group nodes in a cluster. Retrieved from the [selectel_mks_cluster_v1](https://registry.terraform.io/providers/selectel/selectel/latest/docs/resources/mks_cluster_v1) resource.

* `project_id` - (Required) Unique identifier of the associated Cloud Platform project. Changing this creates a new node group. Retrieved from the [selectel_vpc_project_v2](https://registry.terraform.io/providers/selectel/selectel/latest/docs/resources/vpc_project_v2) resource. Learn more about [Cloud Platform projects](https://docs.selectel.ru/cloud/managed-kubernetes/about/projects/).

* `region` - (Required) Pool where the cluster is located, for example, `ru-3`. Changing this creates a new node group. Learn more about available pools in the [Availability matrix](https://docs.selectel.ru/control-panel-actions/availability-matrix/#managed-kubernetes).

* `availability_zone` (Required) Pool segment where all nodes of the node group are located. Changing this creates a new node group.  Learn more about available pool segments in the  [Availability matrix](https://docs.selectel.ru/control-panel-actions/availability-matrix/#managed-kubernetes).  

* `nodes_count` (Required) Number of worker nodes in the node group. The maximum number of nodes in a node group is 15. Changing this resizes the node group if `enable_autoscale` is false.

* `cpus` (Optional) Number of CPU cores for each node. Can be skipped only when `flavor_id` is set. Changing this creates a new node group. Learn more about [Configurations](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/configurations/).

* `ram_mb` (Optional) Amount of RAM in MB for each node. Can be skipped only when `flavor_id` is set. Changing this creates a new node group. Learn more about [Configurations](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/configurations/).

* `volume_gb` (Optional) Volume size in GB for each node. Can be skipped only when flavor_id is set and local_volume is `true`. Changing this creates a new node group.  Learn more about [Configurations](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/configurations/).

* `volume_type` (Optional) Type of an OpenStack blockstorage volume for each node. Can be skipped only when `flavor_id` is set and `local_volume` is `true`. Changing this creates a new node group.  Available volume types are `fast`, `basic`, and `universal`. The format is `<volume_type>`.`<availability_zone>`. Learn more about [Network volumes](https://docs.selectel.ru/cloud/servers/volumes/about-network-volumes/).

* `local_volume` (Optional) Specifies if nodes use a local volume.  Changing this creates a new node group. Boolean flag, the default value is false.

* `flavor_id` (Optional) Unique identifier of an OpenStack flavor for all nodes in the node group. Changing this creates a new node group. Learn more about [Flavors](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/configurations/#создать-группу-нод-с-фиксированной-конфигурацией-облачного-сервера).

* `labels` (Optional) List of Kubernetes labels applied to each node in the node group.

* `taints` (Optional) List of Kubernetes taints applied to each node in the node group.  Contains a key-value pair and an effect applied for the taint. Available effects are `NoSchedule`, `PreferNoSchedule`, and `NoExecute`. Learn more about [Taints](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/add-taints/).

* `keypair_name` (Optional) Name of the SSH key added to all nodes. Changing this creates a new node group.

* `affinity_policy` (Optional) Specifies affinity policy of the nodes. Changing this creates a new node group. Available values are `soft-anti-affinity` and `soft-affinity`. The default value is `soft-anti-affinity`. For more information about affinity and anti-affinity, see the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity).

* `enable_autoscale` (Optional) Enables or disables autoscaling of the node group. Boolean flag, the default value is false. `autoscale_min_nodes` and `autoscale_max_nodes` must be specified. Learn more about [Autoscaling](https://docs.selectel.ru/cloud/managed-kubernetes/node-groups/cluster-autoscaler/).

  * `autoscale_min_nodes` (Optional) Minimum number of worker nodes in the node group.
  * `autoscale_max_nodes` (Optional) Maximum number of worker nodes in the node group.

## Attributes Reference

* `nodes` - List of nodes in the node group.

* `nodegroup_type` - Type of the node group. Available values are `STANDARD` and `GPU`.

## Import

You can import a node group:

```shell
export OS_DOMAIN_NAME=<account_id>
export OS_USERNAME=<username>
export OS_PASSWORD=<password>
export SEL_PROJECT_ID=<selectel_project_id>
export SEL_REGION=<selectel_pool>
terraform import selectel_mks_nodegroup_v1.nodegroup_1 <cluster_id>/<nodegroup_id>
```

where:

* `<account_id>` — Selectel account ID. The account ID is in the top right corner of the [Control panel](https://my.selectel.ru/). Learn more about [Registration](https://docs.selectel.ru/control-panel-actions/account/registration/).

* `<username>` — Name of the service user. To get the name, in the top right corner of the [Control panel](https://my.selectel.ru/profile/users_management/users?type=service), go to the account menu ⟶ **Profile and Settings** ⟶ **User management** ⟶ the **Service users** tab ⟶ copy the name of the required user. Learn more about [Service users](https://docs.selectel.ru/control-panel-actions/users-and-roles/user-types-and-roles/).

* `<password>` — Password of the service user. 

* `<selectel_project_id>` — Unique identifier of the associated Cloud Platform project. To get the project ID, in the [Control panel](https://my.selectel.ru/vpc/), go to **Cloud Platform** ⟶ project name ⟶ copy the ID of the required project. Learn more about [Cloud Platform projects](https://docs.selectel.ru/cloud/managed-kubernetes/about/projects/).

* `<selectel_pool>` — Pool where the cluster is located, for example, `ru-3`. To get information about the pool, in the [Control panel](https://my.selectel.ru/vpc/mks/), go to **Cloud Platform** ⟶ **Kubernetes**. The pool is in the **Pool** column.

* `<cluster_id>` — Unique identifier of the cluster, for example, `b311ce58-2658-46b5-b733-7a0f418703f2`. To get the cluster ID, in the [Control panel](https://my.selectel.ru/vpc/mks/), go to **Cloud Platform** ⟶ **Kubernetes** ⟶ the cluster page ⟶ copy the ID at the top of the page under the cluster name, near the region and pool.

* `<nodegroup_id>` — Unique identifier of the node, for example, `63ed5342-b22c-4c7a-9d41-c1fe4a142c13`. To get the cluster ID, in the [Control panel](https://my.selectel.ru/vpc/mks/), go to **Cloud Platform** ⟶ **Kubernetes**. Click the required cluster ⟶ the required node group. The node group ID is at the top of the page under the node group name, near the region and pool.
