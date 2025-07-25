# terraform-aws-ecgroups
This is a Terraform module and cannot stand on its own. It is meant to be included into a project as a module or to be uploaded to the Terraform Public Repository.

This module allows you to deploy an EC Group composed of from 4 to 16 EC2 instances. Each node will communicate with each other and form a single cluster with a single mountpoint.

All of the guard-rails for error free deployments are in the main Terraform project that would import this module. Except for one... Each module must verify that the requested EC2 instance is available in their availability zone. If this is not done, then Terraform could hang waiting for that resource to be available. 

## Table of Contents
- [Configuration](#configuration)
  - [Global Variables](#global-variables)
- [Component Variables](#component-variables)
  - [ECGroup Variables](#ecgroup-variables)
- [Outputs](#outputs)

## Configuration

Configuration must be done in the main project by managing `terraform.tfvars`. Additionally, in the root of the main project, you must take the variables from this module and include them into root `variables.tf`. We recommend that you preface those variables with the module name, such that a variable in a module that looks like `ami =` is created as `ecgroups-ami =` in the root.

Then, in the root main.tf, you reference this module in the source. This is a sample for your root main.tf.

```module "ecgroups" {
  source = "git::https://github.com/your-username/terraform-aws-ecgroups.git?ref=v1.0.0"

  # ... provide the required variables for the module
  common_config = local.common_config
  instance_count = 2
  # ... etc.
}
```

## Module Variables

### ECGroup Variables

These variables configure the ECGroup storage cluster and are prefixed with `ecgroup_` in your `terraform.tfvars` file.

* `instance_type`: EC2 instance type for the cluster nodes (Default: "m6i.16xlarge").
* `node_count`: Number of EC2 nodes to create (must be between 4 and 16) (Default: 4).
* `boot_volume_size`: Root volume size (GB) for each node (Default: 100).
* `boot_volume_type`: Root volume type for each node (Default: "gp2").
* `metadata_volume_size`: Size of the metadata EBS volume for each node in GiB (Default: 4096).
* `metadata_volume_type`: Type of EBS metadata volume for each node (Default: "io2").
* `metadata_volume_throughput`: Throughput for metadata EBS volumes.
* `metadata_volume_iops`: IOPS for the metadata EBS volumes.
* `storage_volume_count`: Number of storage volumes to attach to each node (Default: 4).
* `storage_volume_size`: Size of each EBS storage volume (GB) (Default: 4096).
* `storage_volume_type`: Type of EBS storage volume (Default: "gp3").
* `storage_volume_throughput`: Throughput for each EBS storage volume.
* `storage_volume_iops`: IOPS for each EBS storage volume.
* `user_data`: Path to user data script for the nodes.

## Outputs

After a successful `apply`, this module will provide the following outputs. Sensitive values will be redacted and can be viewed with `terraform output <output_name>`.

* `ecgroup_nodes`: Details about the deployed ECGroup nodes.

The output will look something like this:

```
ecgroup_nodes = [
  [
    {
      "id" = "i-06d475c3e626e513a"
      "name" = "KadeTest-ecgroup-1"
      "private_ip" = "172.26.6.231"
    },
    {
      "id" = "i-0aec6b471c2261cc3"
      "name" = "KadeTest-ecgroup-2"
      "private_ip" = "172.26.6.75"
    },
    {
      "id" = "i-023ebb770c828b94e"
      "name" = "KadeTest-ecgroup-3"
      "private_ip" = "172.26.6.253"
    },
    {
      "id" = "i-0e1c8de4a0a06c1f5"
      "name" = "KadeTest-ecgroup-4"
      "private_ip" = "172.26.6.155"
    },
    {
      "id" = "i-09144f631094e8bce"
      "name" = "KadeTest-ecgroup-5"
      "private_ip" = "172.26.6.53"
    },
    {
      "id" = "i-0e2d302da59a2ab58"
      "name" = "KadeTest-ecgroup-6"
      "private_ip" = "172.26.6.95"
    },
  ],
]
```
