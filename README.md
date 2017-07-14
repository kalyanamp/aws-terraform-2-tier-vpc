# AWS / Terraform Secure 2 Tier Infrastructure and Services

## Set-Up

```commandline
ssh-add -K your-key.pem
```

## Terraform

### First Time

There is an issue with the REST services not starting correctly if the Aurora cluster has not completed creation in time.
Terraform should have proper module dependencies in place shortly, follow this thread [1178](https://github.com/hashicorp/terraform/issues/1178) for details.
In the mean time the workaroud has been implemented although it does not always work.

The Aurora cluster takes quite a bit of time to create so I suggest that you target build that module first

```commandline
terraform apply -target=module.aws_aurora_cluster
```

Once the Aurota cluster is up and running in the Green Zone of the VPC you are ready to apply the remaining template

```commandline
terraform apply
```

If you decide to build the whole estate upfront and the micro service did not start up correctly you have 2 option. 
Firstly you can SSH to the Amber Zone instances re-start the docker containers or secondly you can taint the instances and reapply the template.
The following section explains how you do that.

### Subsequent Times

You will find that you may want to play around with the REST services which means you will redeploy them regularly.
If you do get this point you will undoubtedly changed the terraform scripts to source your own REST docker servcie from your own registry.

The following command is how to teardown the Amber Zone instances and redeploy them without destroying the entire stack.

```commandline
terraform taint -module=aws_docker_amb_az_a aws_instance.docker
terraform taint -module=aws_docker_amb_az_b aws_instance.docker
terraform apply
```

## Docker Build

```commandline
mvn clean package docker:build
docker push sverze/aws-terraform-2-tier-service:latest
docker run -p 8080:8080 aws-terraform-2-tier-service
```