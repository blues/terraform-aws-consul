# This repository was forked from https://github.com/hashicorp/terraform-aws-consul. It's no longer supported by hashicorp yet we currently rely on its scripts and tooling for provisioning Consul.

# DISCLAIMER
**This repository is no longer supported, please consider using [this repository](https://registry.terraform.io/modules/hashicorp/consul-starter/aws/latest) for the latest and most supported version for Consul.**

Moving forward in the future this repository will be no longer supported and eventually lead to
deprecation. Please use our latest versions of our products moving forward or alternatively you
may fork the repository to continue use and development for your personal/business use.

---
# Consul AWS Module

This repo contains a set of modules in the [modules folder](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules) for deploying a [Consul](https://www.consul.io/) cluster on 
[AWS](https://aws.amazon.com/) using [Terraform](https://www.terraform.io/). Consul is a distributed, highly-available 
tool that you can use for service discovery and key/value storage. A Consul cluster typically includes a small number
of server nodes, which are responsible for being part of the [consensus 
quorum](https://www.consul.io/docs/internals/consensus.html), and a larger number of client nodes, which you typically 
run alongside your apps:

![Consul architecture](https://github.com/hashicorp/terraform-aws-consul/blob/master/_docs/architecture.png?raw=true)



## How to use this Module

This repo has the following folder structure:

* [modules](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules): This folder contains several standalone, reusable, production-grade modules that you can use to deploy Consul.
* [examples](https://github.com/hashicorp/terraform-aws-consul/tree/master/examples): This folder shows examples of different ways to combine the modules in the `modules` folder to deploy Consul.
* [test](https://github.com/hashicorp/terraform-aws-consul/tree/master/test): Automated tests for the modules and examples.
* [root folder](https://github.com/hashicorp/terraform-aws-consul/tree/master): The root folder is *an example* of how to use the [consul-cluster module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-cluster) 
  module to deploy a [Consul](https://www.consul.io/) cluster in [AWS](https://aws.amazon.com/). The Terraform Registry requires the root of every repo to contain Terraform code, so we've put one of the examples there. This example is great for learning and experimenting, but for production use, please use the underlying modules in the [modules folder](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules) directly.

To deploy Consul servers for production using this repo:

1. Create a Consul AMI using a Packer template that references the [install-consul module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-consul).
   Here is an [example Packer template](https://github.com/hashicorp/terraform-aws-consul/tree/master/examples/consul-ami#quick-start). 
   
   If you are just experimenting with this Module, you may find it more convenient to use one of our official public AMIs.
   Check out the `aws_ami` data source usage in `main.tf` for how to auto-discover this AMI.
  
    **WARNING! Do NOT use these AMIs in your production setup. In production, you should build your own AMIs in your own 
    AWS account.**
   
1. Deploy that AMI across an Auto Scaling Group using the Terraform [consul-cluster module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-cluster) 
   and execute the [run-consul script](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/run-consul) with the `--server` flag during boot on each 
   Instance in the Auto Scaling Group to form the Consul cluster. Here is [an example Terraform 
   configuration](https://github.com/hashicorp/terraform-aws-consul/tree/master/examples/root-example#quick-start) to provision a Consul cluster.

To deploy Consul clients for production using this repo:
 
1. Use the [install-consul module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-consul) to install Consul alongside your application code.
1. Before booting your app, execute the [run-consul script](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/run-consul) with `--client` flag.
1. Your app can now use the local Consul agent for service discovery and key/value storage.
1. Optionally, you can use the [install-dnsmasq module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-dnsmasq) for Ubuntu 16.04 and Amazon Linux 2 or [setup-systemd-resolved](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/setup-systemd-resolved) for Ubuntu 18.04 and Ubuntu 20.04 to configure Consul as the DNS for a
   specific domain (e.g. `.consul`) so that URLs such as `foo.service.consul` resolve automatically to the IP 
   address(es) for a service `foo` registered in Consul (all other domain names will be continue to resolve using the
   default resolver on the OS).
   
 


## What's a Module?

A Module is a canonical, reusable, best-practices definition for how to run a single piece of infrastructure, such 
as a database or server cluster. Each Module is created using [Terraform](https://www.terraform.io/), and
includes automated tests, examples, and documentation. It is maintained both by the open source community and 
companies that provide commercial support. 

Instead of figuring out the details of how to run a piece of infrastructure from scratch, you can reuse 
existing code that has been proven in production. And instead of maintaining all that infrastructure code yourself, 
you can leverage the work of the Module community to pick up infrastructure improvements through
a version number bump.
 
 

## Who created this Module?

These modules were created by [Gruntwork](http://www.gruntwork.io/?ref=repo_aws_consul), in partnership with HashiCorp, in 2017 and maintained through 2021. They were deprecated in 2022 in favor of newer alternatives (see the top of the README for details).


## Code included in this Module:

* [install-consul](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-consul): This module installs Consul using a
  [Packer](https://www.packer.io/) template to create a Consul 
  [Amazon Machine Image (AMI)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html).

* [consul-cluster](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-cluster): The module includes Terraform code to deploy a Consul AMI across an [Auto 
  Scaling Group](https://aws.amazon.com/autoscaling/). 
  
* [run-consul](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/run-consul): This module includes the scripts to configure and run Consul. It is used
  by the above Packer module at build-time to set configurations, and by the Terraform module at runtime 
  with [User Data](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-shell-scripts)
  to create the cluster.

* [install-dnsmasq module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-dnsmasq): Install [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html)
  for Ubuntu 16.04 and Amazon Linux 2 and configure it to forward requests for a specific domain to Consul. This allows you to use Consul as a DNS server
  for URLs such as `foo.service.consul`.

* [setup-systemd-resolved module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/setup-systemd-resolved): Setup [systemd-resolved](https://www.freedesktop.org/software/systemd/man/resolved.conf.html)
  for Ubuntu 18.04 and Ubuntu 20.04 and configure it to forward requests for a specific domain to Consul. This allows you to use Consul as a DNS server
  for URLs such as `foo.service.consul`.

* [consul-iam-policies](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-iam-policies): Defines the IAM policies necessary for a Consul cluster. 

* [consul-security-group-rules](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-security-group-rules): Defines the security group rules used by a 
  Consul cluster to control the traffic that is allowed to go in and out of the cluster.

* [consul-client-security-group-rules](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-client-security-group-rules): Defines the security group rules
  used by a Consul agent to control the traffic that is allowed to go in and out.



## How is this Module versioned?

This Module follows the principles of [Semantic Versioning](http://semver.org/). You can find each new release, 
along with the changelog, in the [Releases Page](../../releases). 

During initial development, the major version will be 0 (e.g., `0.x.y`), which indicates the code does not yet have a 
stable API. Once we hit `1.0.0`, we will make every effort to maintain a backwards compatible API and use the MAJOR, 
MINOR, and PATCH versions on each release to indicate any incompatibilities. 



## License

This code is released under the Apache 2.0 License. Please see [LICENSE](https://github.com/hashicorp/terraform-aws-consul/tree/master/LICENSE) and [NOTICE](https://github.com/hashicorp/terraform-aws-consul/tree/master/NOTICE) for more 
details.

Copyright &copy; 2017 [Gruntwork](http://www.gruntwork.io/?ref=repo_aws_consul), Inc.
