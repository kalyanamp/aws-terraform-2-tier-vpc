## Terraform

terraform taint -module=aws_docker_amb_az_a aws_instance.docker
terraform taint -module=aws_docker_amb_az_b aws_instance.docker

## Docker Build

mvn clean package docker:build
docker push sverze/aws-terraform-2-tier-service:latest
docker run -p 8080:8080 aws-terraform-2-tier-service