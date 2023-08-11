# Basics of GCP
## The Dashboard
`Dashboard` is like this
![Dashboard view](./gcp-basics-dashboard-images/dashboard-markedup.jpg)
The red mark point to current project. The blue marks describes the current project info. The yellow mark shows basic billing info.
Open the current project dropdown.
![Project view](./gcp-basics-dashboard-images/all-projects.png)
This opens a list view of all project. Open specific projects. Currently there is one. By selecting and opening you can change projects.
Opening the `naviagation menu` of the top left corner. This menu contains all pinned services and is quick access menu.
`Api & Services` is notable as has all api and services that gcp and what you are using currently.
![Navigation Menu](./gcp-basics-dashboard-images/navigation-menu.png)
The navigation menu is available from any of the google cloud console pages.
Scrolling and selecting `More Products` reveal all products available in gcp.
![More products](./gcp-basics-dashboard-images/more-products.png)
After opening the `More Products` dropdown.
![All products](./gcp-basics-dashboard-images/all-products-list.png)
Here is a chart of equivalent services in aws, azure, gcp.
![equivalent](./gcp-basics-dashboard-images/eq.png)
## Create virtual machines using GCP Compute Engine
### Manual Instance Creation
First of all creating a virtual machine manually follow the steps bellow,
Open virtual machine submenu in the `GCP Compute Engine` service.
![Submenu](./gcp-compute-engine-images/compute-engine.png)
Create a new instance like below. As needed.
![new instance](./gcp-compute-engine-images/create-vm.png)
The creation process,
![creation steps](./gcp-compute-engine-images/vm-creation.png)
Now in the list view selecting `SSH` will ssh to the created instance. Now through the opened browser terminal we can access the created instance and make changes.
![ssh](./gcp-compute-engine-images/ssh.png)
### Instance Creation with Cloud Shell
For using cloud shell, 
Select a project if needed using
```
gcloud config set project adept-acronym-395317
```
![setting project](./gcp-compute-engine-images/set-project.PNG)
Here is the confimation,
![setting project](./gcp-compute-engine-images/list-project.PNG)
Create instance using the following command,
```
gcloud compute instances create abir-instance-1 --machine-type e2-micro --zone=us-west4-b
```
Here abir-instance-1 is the instance name, its type is a e2-micro instance and the zone is set to us-west4-b. Other parameters can also be added.
![setting up instance](./gcp-compute-engine-images/create-ce-instance.PNG)
To connect and makes changes to the instance run the following command,
![connect to ssh with shell](./gcp-compute-engine-images/connect-ssh.PNG)
After work with resource is done run the following to delete the resource. 
```
gcloud compute instances delete abir-instance-1 --zone=us-west4-b
```
![Deletion process](./gcp-compute-engine-images/delete.PNG)
### Instance Creation with Terraform
Setup a vm instance. Ensure [gcloud cli installed](https://cloud.google.com/sdk/docs/install).
Now [install terraform](https://developer.hashicorp.com/terraform/tutorials/gcp-get-started/install-cli). By running the following codes in [terraform will be installed](https://developer.hashicorp.com/terraform/downloads).
```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install terraform
```
To build the the vm on compute engine follow the steps bellow,
1. Make a service account for terraform. Follow the illustrated below.
    ![step1](./gcp-compute-engine-images/service-account.png)

    ![step2](./gcp-compute-engine-images/create-sa.png)

    ![step3](./gcp-compute-engine-images/csa-name.png)

    ![step4](./gcp-compute-engine-images/csa-role.png)

    ![step5](./gcp-compute-engine-images/csa-done.png)

    ![step6](./gcp-compute-engine-images/completion.png)

    ![step7](./gcp-compute-engine-images/create-key.png)

    ![step8](./gcp-compute-engine-images/json-key.png)

    ![step9](./gcp-compute-engine-images/download-key.png)
    Keep the key file in the same directory as the configuration file for this configuration to work.
2. Write configuration file with `*.tf` as extension. This is what it will look like.
    ```
    terraform {
        required_providers {
            google = {
            source = "hashicorp/google"
            version = "4.77.0"
            }
        }
    }

    provider "google" {
        credentials = file("<NAME>.json")

        project = "<PROJECT_ID>"
        region  = "us-west4"
        zone    = "us-west4-b"
    }

    resource "google_compute_instance" "vm_instance" {
        name         = "terraform-instance"
        machine_type = "e2-micro"

        boot_disk {
            initialize_params {
            image = "debian-cloud/debian-11"
            }
        }
          network_interface {
            network = "default"
            access_config {
        }
    }
    ```
    Here `<NAME>` needs to changed to the name of json key file that was downloaded. `<PROJECT_ID>` needs to be changed to the id of the project where you want create your infrastructure.

3. Now run the following to initialize the directory.
    ```
    terraform init
    ```
    ![init](./gcp-compute-engine-images/init.PNG)
4. Run `terraform fmt` to formt the terraform configuration to be consistent and to ensure configuration is syntactically valid and internally consistent by using the `terraform validate` command. (Optional)
5. To build the infrastructure use the following command.
    ```
    terraform apply
    ```
    ![apply](./gcp-compute-engine-images/apply1.PNG)
    
    ![apply](./gcp-compute-engine-images/apply2.PNG)
    Now view the gcloud console to view created infrastructure.
    ![created infrastructure](./gcp-compute-engine-images/terraform-instance.png)
6. The infrastructure can be taken down with the following command.
    ```
    terraform destroy
    ```
    ![destroy](./gcp-compute-engine-images/destroy1.PNG)

    ![destroy](./gcp-compute-engine-images/destroy2.PNG)