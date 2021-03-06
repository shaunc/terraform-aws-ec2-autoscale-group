<!-- This file was automatically generated by the `build-harness`. Make all changes to `README.yaml` and run `make readme` to rebuild this file. -->
[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

# terraform-aws-ec2-autoscale-group [![Codefresh Build Status](https://g.codefresh.io/api/badges/pipeline/cloudposse/terraform-modules%2Fterraform-aws-ec2-autoscale-group?type=cf-1)](https://g.codefresh.io/public/accounts/cloudposse/pipelines/5d8b7d3d4cba337a81a18256) [![Latest Release](https://img.shields.io/github/release/cloudposse/terraform-aws-ec2-autoscale-group.svg)](https://github.com/cloudposse/terraform-aws-ec2-autoscale-group/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)


Terraform module to provision [Auto Scaling Group](https://www.terraform.io/docs/providers/aws/r/autoscaling_group.html) and [Launch Template](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html) on AWS.

The module also creates AutoScaling Policies and CloudWatch Metric Alarms to monitor CPU utilization on the EC2 instances and scale the number of instance in the AutoScaling Group up or down.
If you don't want to use the provided functionality, or want to provide your own policies, disable it by setting the variable `autoscaling_policies_enabled` to `"false"`.


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps. 
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]


[![Terraform Open Source Modules](https://docs.cloudposse.com/images/terraform-open-source-modules.svg)][terraform_modules]



It's 100% Open Source and licensed under the [APACHE2](LICENSE).







We literally have [*hundreds of terraform modules*][terraform_modules] that are Open Source and well-maintained. Check them out! 







## Usage


**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/cloudposse/terraform-aws-ec2-autoscale-group/releases).



```hcl
locals {
  userdata = <<-USERDATA
    #!/bin/bash
    cat <<"__EOF__" > /home/ec2-user/.ssh/config
    Host *
      StrictHostKeyChecking no
    __EOF__
    chmod 600 /home/ec2-user/.ssh/config
    chown ec2-user:ec2-user /home/ec2-user/.ssh/config
  USERDATA
}

module "autoscale_group" {
  source = "git::https://github.com/cloudposse/terraform-aws-ec2-autoscale-group.git?ref=master"

  namespace = "eg"
  stage     = "dev"
  name      = "test"

  image_id                    = "ami-08cab282f9979fc7a"
  instance_type               = "t2.small"
  security_group_ids          = ["sg-xxxxxxxx"]
  subnet_ids                  = ["subnet-xxxxxxxx", "subnet-yyyyyyyy", "subnet-zzzzzzzz"]
  health_check_type           = "EC2"
  min_size                    = 2
  max_size                    = 3
  wait_for_capacity_timeout   = "5m"
  associate_public_ip_address = true
  user_data_base64            = "${base64encode(local.userdata)}"

  tags = {
    Tier              = "1"
    KubernetesCluster = "us-west-2.testing.cloudposse.co"
  }

  # Auto-scaling policies and CloudWatch metric alarms
  autoscaling_policies_enabled           = "true"
  cpu_utilization_high_threshold_percent = "70"
  cpu_utilization_low_threshold_percent  = "20"
}
```






## Makefile Targets
```
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen
  lint                                Lint terraform code

```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| associate_public_ip_address | Associate a public IP address with an instance in a VPC | bool | `false` | no |
| attributes | Additional attributes (e.g. `1`) | list(string) | `<list>` | no |
| autoscaling_policies_enabled | Whether to create `aws_autoscaling_policy` and `aws_cloudwatch_metric_alarm` resources to control Auto Scaling | bool | `true` | no |
| block_device_mappings | Specify volumes to attach to the instance besides the volumes specified by the AMI | object | `<list>` | no |
| cpu_utilization_high_evaluation_periods | The number of periods over which data is compared to the specified threshold | number | `2` | no |
| cpu_utilization_high_period_seconds | The period in seconds over which the specified statistic is applied | number | `300` | no |
| cpu_utilization_high_statistic | The statistic to apply to the alarm's associated metric. Either of the following is supported: `SampleCount`, `Average`, `Sum`, `Minimum`, `Maximum` | string | `Average` | no |
| cpu_utilization_high_threshold_percent | The value against which the specified statistic is compared | number | `90` | no |
| cpu_utilization_low_evaluation_periods | The number of periods over which data is compared to the specified threshold | number | `2` | no |
| cpu_utilization_low_period_seconds | The period in seconds over which the specified statistic is applied | number | `300` | no |
| cpu_utilization_low_statistic | The statistic to apply to the alarm's associated metric. Either of the following is supported: `SampleCount`, `Average`, `Sum`, `Minimum`, `Maximum` | string | `Average` | no |
| cpu_utilization_low_threshold_percent | The value against which the specified statistic is compared | number | `10` | no |
| credit_specification | Customize the credit specification of the instances | object | `null` | no |
| default_cooldown | The amount of time, in seconds, after a scaling activity completes before another scaling activity can start | number | `300` | no |
| delimiter | Delimiter to be used between `name`, `namespace`, `stage`, etc. | string | `-` | no |
| disable_api_termination | If `true`, enables EC2 Instance Termination Protection | bool | `false` | no |
| ebs_optimized | If true, the launched EC2 instance will be EBS-optimized | bool | `false` | no |
| elastic_gpu_specifications | Specifications of Elastic GPU to attach to the instances | object | `null` | no |
| enable_monitoring | Enable/disable detailed monitoring | bool | `true` | no |
| enabled | Whether to create the resources. Set to `false` to prevent the module from creating any resources | string | `true` | no |
| enabled_metrics | A list of metrics to collect. The allowed values are `GroupMinSize`, `GroupMaxSize`, `GroupDesiredCapacity`, `GroupInServiceInstances`, `GroupPendingInstances`, `GroupStandbyInstances`, `GroupTerminatingInstances`, `GroupTotalInstances` | list(string) | `<list>` | no |
| force_delete | Allows deleting the autoscaling group without waiting for all instances in the pool to terminate. You can force an autoscaling group to delete even if it's in the process of scaling a resource. Normally, Terraform drains all the instances before deleting the group. This bypasses that behavior and potentially leaves resources dangling | bool | `false` | no |
| health_check_grace_period | Time (in seconds) after instance comes into service before checking health | number | `300` | no |
| health_check_type | Controls how health checking is done. Valid values are `EC2` or `ELB` | string | `EC2` | no |
| iam_instance_profile_name | The IAM instance profile name to associate with launched instances | string | `` | no |
| image_id | The EC2 image ID to launch | string | `` | no |
| instance_initiated_shutdown_behavior | Shutdown behavior for the instances. Can be `stop` or `terminate` | string | `terminate` | no |
| instance_market_options | The market (purchasing) option for the instances | object | `null` | no |
| instance_type | Instance type to launch | string | - | yes |
| key_name | The SSH key name that should be used for the instance | string | `` | no |
| launch_template_version | Launch template version. Can be version number, `$Latest` or `$Default` | string | `$Latest` | no |
| load_balancers | A list of elastic load balancer names to add to the autoscaling group names. Only valid for classic load balancers. For ALBs, use `target_group_arns` instead | list(string) | `<list>` | no |
| max_size | The maximum size of the autoscale group | number | - | yes |
| metrics_granularity | The granularity to associate with the metrics to collect. The only valid value is 1Minute | string | `1Minute` | no |
| min_elb_capacity | Setting this causes Terraform to wait for this number of instances to show up healthy in the ELB only on creation. Updates will not wait on ELB instance number changes | number | `0` | no |
| min_size | The minimum size of the autoscale group | number | - | yes |
| name | Solution name, e.g. 'app' or 'cluster' | string | `app` | no |
| namespace | Namespace, which could be your organization name, e.g. 'eg' or 'cp' | string | `` | no |
| placement | The placement specifications of the instances | object | `null` | no |
| placement_group | The name of the placement group into which you'll launch your instances, if any | string | `` | no |
| protect_from_scale_in | Allows setting instance protection. The autoscaling group will not select instances with this setting for terminination during scale in events | bool | `false` | no |
| scale_down_adjustment_type | Specifies whether the adjustment is an absolute number or a percentage of the current capacity. Valid values are `ChangeInCapacity`, `ExactCapacity` and `PercentChangeInCapacity` | string | `ChangeInCapacity` | no |
| scale_down_cooldown_seconds | The amount of time, in seconds, after a scaling activity completes and before the next scaling activity can start | number | `300` | no |
| scale_down_policy_type | The scalling policy type, either `SimpleScaling`, `StepScaling` or `TargetTrackingScaling` | string | `SimpleScaling` | no |
| scale_down_scaling_adjustment | The number of instances by which to scale. `scale_down_scaling_adjustment` determines the interpretation of this number (e.g. as an absolute number or as a percentage of the existing Auto Scaling group size). A positive increment adds to the current capacity and a negative value removes from the current capacity | number | `-1` | no |
| scale_up_adjustment_type | Specifies whether the adjustment is an absolute number or a percentage of the current capacity. Valid values are `ChangeInCapacity`, `ExactCapacity` and `PercentChangeInCapacity` | string | `ChangeInCapacity` | no |
| scale_up_cooldown_seconds | The amount of time, in seconds, after a scaling activity completes and before the next scaling activity can start | number | `300` | no |
| scale_up_policy_type | The scalling policy type, either `SimpleScaling`, `StepScaling` or `TargetTrackingScaling` | string | `SimpleScaling` | no |
| scale_up_scaling_adjustment | The number of instances by which to scale. `scale_up_adjustment_type` determines the interpretation of this number (e.g. as an absolute number or as a percentage of the existing Auto Scaling group size). A positive increment adds to the current capacity and a negative value removes from the current capacity | number | `1` | no |
| security_group_ids | A list of associated security group IDs | list(string) | `<list>` | no |
| service_linked_role_arn | The ARN of the service-linked role that the ASG will use to call other AWS services | string | `` | no |
| stage | Stage, e.g. 'prod', 'staging', 'dev', or 'test' | string | `` | no |
| subnet_ids | A list of subnet IDs to launch resources in | list(string) | - | yes |
| suspended_processes | A list of processes to suspend for the AutoScaling Group. The allowed values are `Launch`, `Terminate`, `HealthCheck`, `ReplaceUnhealthy`, `AZRebalance`, `AlarmNotification`, `ScheduledActions`, `AddToLoadBalancer`. Note that if you suspend either the `Launch` or `Terminate` process types, it can prevent your autoscaling group from functioning properly. | list(string) | `<list>` | no |
| tags | Additional tags (e.g. `map('BusinessUnit`,`XYZ`) | map(string) | `<map>` | no |
| target_group_arns | A list of aws_alb_target_group ARNs, for use with Application Load Balancing | list(string) | `<list>` | no |
| termination_policies | A list of policies to decide how the instances in the auto scale group should be terminated. The allowed values are `OldestInstance`, `NewestInstance`, `OldestLaunchConfiguration`, `ClosestToNextInstanceHour`, `Default` | list(string) | `<list>` | no |
| user_data_base64 | The Base64-encoded user data to provide when launching the instances | string | `` | no |
| wait_for_capacity_timeout | A maximum duration that Terraform should wait for ASG instances to be healthy before timing out. Setting this to '0' causes Terraform to skip all Capacity Waiting behavior | string | `10m` | no |
| wait_for_elb_capacity | Setting this will cause Terraform to wait for exactly this number of healthy instances in all attached load balancers on both create and update operations. Takes precedence over `min_elb_capacity` behavior | number | `0` | no |

## Outputs

| Name | Description |
|------|-------------|
| autoscaling_group_arn | ARN of the AutoScaling Group |
| autoscaling_group_default_cooldown | Time between a scaling activity and the succeeding scaling activity |
| autoscaling_group_desired_capacity | The number of Amazon EC2 instances that should be running in the group |
| autoscaling_group_health_check_grace_period | Time after instance comes into service before checking health |
| autoscaling_group_health_check_type | `EC2` or `ELB`. Controls how health checking is done |
| autoscaling_group_id | The AutoScaling Group id |
| autoscaling_group_max_size | The maximum size of the autoscale group |
| autoscaling_group_min_size | The minimum size of the autoscale group |
| autoscaling_group_name | The AutoScaling Group name |
| launch_template_arn | The ARN of the launch template |
| launch_template_id | The ID of the launch template |




## Share the Love 

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/terraform-aws-ec2-autoscale-group)! (it helps us **a lot**) 

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [terraform-aws-ec2-instance](https://github.com/cloudposse/terraform-aws-ec2-instance) - Terraform module for providing a general purpose EC2 instance
- [terraform-aws-ec2-bastion-server](https://github.com/cloudposse/terraform-aws-ec2-bastion-server) - Terraform module to define a generic bastion host with parameterized user data
- [terraform-aws-ec2-admin-server](https://github.com/cloudposse/terraform-aws-ec2-admin-server) - Terraform module for providing an EC2 instance capable of running admin tasks
- [terraform-aws-ec2-instance-group](https://github.com/cloudposse/terraform-aws-ec2-instance-group) - Terraform module for provisioning multiple general purpose EC2 hosts for stateful applications
- [terraform-aws-ec2-ami-snapshot](https://github.com/cloudposse/terraform-aws-ec2-ami-snapshot) - Terraform module to easily generate AMI snapshots to create replica instances



## Help

**Got a question?**

File a GitHub [issue](https://github.com/cloudposse/terraform-aws-ec2-autoscale-group/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## Commercial Support

Work directly with our team of DevOps experts via email, slack, and video conferencing. 

We provide [*commercial support*][commercial_support] for all of our [Open Source][github] projects. As a *Dedicated Support* customer, you have access to our team of subject matter experts at a fraction of the cost of a full-time engineer. 

[![E-Mail](https://img.shields.io/badge/email-hello@cloudposse.com-blue.svg)][email]

- **Questions.** We'll use a Shared Slack channel between your team and ours.
- **Troubleshooting.** We'll help you triage why things aren't working.
- **Code Reviews.** We'll review your Pull Requests and provide constructive feedback.
- **Bug Fixes.** We'll rapidly work to fix any bugs in our projects.
- **Build New Terraform Modules.** We'll [develop original modules][module_development] to provision infrastructure.
- **Cloud Architecture.** We'll assist with your cloud strategy and design.
- **Implementation.** We'll provide hands-on support to implement our reference architectures. 



## Terraform Module Development

Are you interested in custom Terraform module development? Submit your inquiry using [our form][module_development] today and we'll get back to you ASAP.


## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Newsletter

Signup for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover. 

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/terraform-aws-ec2-autoscale-group/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2019 [Cloud Posse, LLC](https://cpco.io/copyright)



## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.  

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] | [![Igor Rodionov][goruha_avatar]][goruha_homepage]<br/>[Igor Rodionov][goruha_homepage] |
|---|---|---|

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png
  [goruha_homepage]: https://github.com/goruha
  [goruha_avatar]: https://img.cloudposse.com/150x150/https://github.com/goruha.png



[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs
  [website]: https://cpco.io/homepage
  [github]: https://cpco.io/github
  [jobs]: https://cpco.io/jobs
  [hire]: https://cpco.io/hire
  [slack]: https://cpco.io/slack
  [linkedin]: https://cpco.io/linkedin
  [twitter]: https://cpco.io/twitter
  [testimonial]: https://cpco.io/leave-testimonial
  [newsletter]: https://cpco.io/newsletter
  [email]: https://cpco.io/email
  [commercial_support]: https://cpco.io/commercial-support
  [we_love_open_source]: https://cpco.io/we-love-open-source
  [module_development]: https://cpco.io/module-development
  [terraform_modules]: https://cpco.io/terraform-modules
  [readme_header_img]: https://cloudposse.com/readme/header/img?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [readme_header_link]: https://cloudposse.com/readme/header/link?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [readme_footer_img]: https://cloudposse.com/readme/footer/img?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?repo=cloudposse/terraform-aws-ec2-autoscale-group
  [share_twitter]: https://twitter.com/intent/tweet/?text=terraform-aws-ec2-autoscale-group&url=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=terraform-aws-ec2-autoscale-group&url=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [share_email]: mailto:?subject=terraform-aws-ec2-autoscale-group&body=https://github.com/cloudposse/terraform-aws-ec2-autoscale-group
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/terraform-aws-ec2-autoscale-group?pixel&cs=github&cm=readme&an=terraform-aws-ec2-autoscale-group
