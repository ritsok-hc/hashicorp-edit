# Getting Started with Terraform

Terraform is a language for defining and managing infrastructure as code (IaC). 

In this tutorial, you will install Terraform, and create and destroy a
containerized NGINX server on your machine.

## Prerequisites

For this exercise, you will need: 
- Terraform (installation steps below)
- [Docker](https://docs.docker.com/get-docker/)

## Install Terraform

To install Terraform, download the package for your system from
[Terraform.io](https://www.terraform.io/downloads.html) and follow the steps to
complete the setup.

## Define Terraform configuration

With Terraform installed, let's create some infrastructure. 

Create a new directory for your Terraform configuration, and change into it.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code. The file must have a
`.tf` extension for Terraform to detect it.

```shell
$ touch main.tf
```

Paste the following lines into the file and save it.

```hcl
provider "docker" {
    host = "unix:///var/run/docker.sock"
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

## Initialize Terraform

In your new directory, initialize Terraform with the `terraform init` command. Terraform
will install the Docker provider declared in your configuration.

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "docker" (terraform-providers/docker) 2.7.2...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.docker: version = "~> 2.7"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

If the command ran successfully, continue on to create your resources.

## Create resources 

Use the `terraform apply` command to provision your resources. Terraform will
download the declared Docker image and create an NGINX container.

```shell
$ terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + dns              = (known after apply)
      + dns_opts         = (known after apply)
      + entrypoint       = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + log_opts         = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + restart          = "no"
      + rm               = false
      + shm_size         = (known after apply)
      + start            = true
      + user             = (known after apply)
      + working_dir      = (known after apply)

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id     = (known after apply)
      + latest = (known after apply)
      + name   = "nginx:latest"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
```

If the configuration output looks correct, respond `yes` to the prompt and hit
`ENTER`. The command may take a few minutes to run and will output a message when
complete.

```shell
Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 10s [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=ba86ffdb9e63f1f313e901e7b0bef982f3aa83d8b65e2209a63067974adc813d]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

## Destroy resources

When you are ready, use the `terraform destroy` command to tear down your resources.

```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_container.nginx: Refreshing state... [id=ba86ffdb9e63f1f313e901e7b0bef982f3aa83d8b65e2209a63067974adc813d]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "ba86ffdb9e63" -> null
      - id                = "ba86ffdb9e63f1f313e901e7b0bef982f3aa83d8b65e2209a63067974adc813d" -> null
      - image             = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1" -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "shareable" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway          = "172.17.0.1"
              - ip_address       = "172.17.0.2"
              - ip_prefix_length = 16
              - network_name     = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id     = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest" -> null
      - latest = "sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1" -> null
      - name   = "nginx:latest" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: 
```

Respond `yes` to the prompt at the bottom of the output and hit `ENTER`.
Terraform will destroy the resources defined in the working directory.

```shell
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes
docker_container.nginx: Destroying... [id=ba86ffdb9e63f1f313e901e7b0bef982f3aa83d8b65e2209a63067974adc813d]
docker_container.nginx: Destruction complete after 1s
docker_image.nginx: Destroying... [id=sha256:c39a868aad02a383c7e490e0fc4a5b0217f667f2de764bc2755e315a5adf64a1nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

## Next steps

You have now provisioned and destroyed your first resource with Terraform!

Continue on with another [tutorial](https://learn.hashicorp.com/terraform) and
browse the list of [providers](https://www.terraform.io/docs/providers/index.html) to learn more
about what you can do with Terraform.

Visit our [Terraform page](https://www.terraform.io/docs/index.html) for
documentation and more information.
