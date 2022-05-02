Lab 1 - Connect to Terraform Cloud
##################################

Terraform Cloud will be our CI. TF Cloud will check any change/commit done in GitHub and will execute the Terraform plan.

Prepare your Teraform Cloud account
***********************************

* Connect to https://app.terraform.io/ and create a free account
* When connected

  * Create a Organisation (what you want)
  * Create a Workspace
    
    * Type ``Version Control Workflow``
    * Connect to VCS - select ``GitHub``, authenticate and authorize Github to Read your Github repos (select allow all or just the devsecops-nap repo)
    * Choose your ``devsecops-nap`` repo and click next

      .. image:: ../pictures/lab1/repo.png
         :scale: 50
         :align: center

    * In the next screen, open ``advanced options``
    * Set ``Terraform Working Directory`` to ``/terraform``. This is where the plan.rf is located
    * Set ``Automatic Run Trigerring`` to ``Always``
    * Set the VCS branch to ``tf_cloud``. We will use a dedicated branch for this module in order to keep 2 ways to do your demo/test (manual with Dev and CI with tf_cloud)

      .. image:: ../pictures/lab1/adv-option.png
         :scale: 50
         :align: center

    * Click ``create workspace``

|

Configure your workspace
************************

* Click on ``Settings > General``
* In ``Apply Method``, change to ``Auto-Apply`` so TF Cloud auto apply the Terraform plan
* Click ``Save``

Set Terraform variables
=======================

TF does not have any clue on your AKS credentials. In the previous module, Terraform (on your laptop),  uses your kubeconfig file. We can't use the kubeconfig file in TF Cloud, but we can use its content.

A kubeconfig file contains 3 certs/keys

* cluster CA certificate
* client certificate
* client key

In Module 1, when we created the AKS with Terraform, we did a Terraform Export. We exported several values

* Retrieve your ``client cert``, ``client key`` and ``cluster CA cert``. If you haven't stored them, here are the commands.

  .. code-bash:: bash

     terraform output client_certificate
     terraform output client_key
     terraform output cluster_ca_certificate

* Move to the ``Variable`` menu
* Create these 3 variables of type ``Terraform variable``

  * key ``cluster_ca_certificate`` and paste your cluster CA certificate base64 encoded value in the ``value`` field. Check the box ``Sensitive``
  * key ``client_certificate`` and paste your client certificate base64 encoded value in the ``value`` field. Check the box ``Sensitive``
  * key ``client_key`` and paste your client key base64 encoded value in the ``value`` field. Check the box ``Sensitive``

    .. image:: ../pictures/lab1/variables.png
       :scale: 50
       :align: center

.. note:: The terraform plan, in tf_cloud branch, has been modified to use these 3 variables instead of your kubeconfig file.

.. code-block:: JSON

    variable "client_certificate" {
    type = string
    }
    variable "client_key" {
    type = string
    }
    variable "cluster_ca_certificate" {
    type = string
    }

    provider "kubernetes" {
    host = "https://aks-matt-eu-dns-8dc14823.hcp.northeurope.azmk8s.io:443"

    client_certificate     = base64decode(var.client_certificate)
    client_key             = base64decode(var.client_key)
    cluster_ca_certificate = base64decode(var.cluster_ca_certificate)
    }

* Modify this plan 
  
  * line 12 - with your AKS server URL. You can find this URL in your kubeconfig file.
  * line 49 - with your ACR registry. You can retrieve this fqdn from the module 1. 

* Commit and push the change to your GitHub

Check your first pipeline execution
===================================

At this moment, a first ``Run`` should start, as you committed your branch.

* Go to ``Runs`` menu and look at the result.
* Result must be ``Applied``

