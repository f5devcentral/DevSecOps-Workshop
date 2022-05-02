Lab 1 - Build and push the Nginx App Protect docker image
#########################################################

In order to expose the Sentence Application, we will deploy a NAP as a POD (not Ingress) in order to expose and protect the Sentence Application.

The docker image will stay static. It means, we don't want to re-build a new image everytime a config update is done. It means configuration files will be imported from a source of truth (Github). A configuration update is:

* A new application exposed (nginx.conf)
* A NAP policy update (signature, violation exception ...)

|

Create the Azure Container Registry secret in AKS
*************************************************

The NAP image is a private image. You are not allowed to push this image in a public repo. It means, your AKS needs to know how to connect to this private registry.

In Azure Container Registry, it is stray forward:

* In the first lab, we created the AKS (K8S) and the ACR (Container Registry). At the end of the lab, we did a Terraform Export in order to get the username, password and registry name.
* Retrieve the ``username``, ``password`` and ``registry name`` from the Terraform Export

* Create the k8s secret

  .. code-block:: bash

    kubectl create secret docker-registry secret-azure-acr --namespace sentence --docker-server=<your_registry>.azurecr.io --docker-username=<username-generated> --docker-password=<password-generated>

* Check the secret is created

  .. code-block:: bash

   ❯ kubectl get secret -n sentence
   NAME                  TYPE                                  DATA   AGE
   default-token-xhmzs   kubernetes.io/service-account-token   3      6d5h
   secret-azure-acr      kubernetes.io/dockerconfigjson        1      6d5h

.. note:: Your AKS is now able to download docker image from your Azure Container Registry

|

Build and push the Nginx App Protect docker image
*************************************************

* In ``/nginx-nap`` directory, copy your ``nginx-repo.crt`` and ``nginx-repo.key``
* Modifty the ``entrypoint.sh`` script so it points to your GitHub repo

  .. code-block:: bash

    git clone --branch dev https://github.com/<YOUR_REPO>/devsecops-nap.git /tmp/devsecops/

    ......

  .. note:: This script is run at every boot. If you look at deeper in the script, you can see the script clones your GitHub repo (dev branch) in the nginx directory. It means the Nginx will run with the config files coming from the GitHub repo -> GitHub is our ``source of truth``

* Build your docker image

  .. code-block:: bash

    DOCKER_BUILDKIT=1 docker build --no-cache --secret id=nginx-crt,src=nginx-repo.crt --secret id=nginx-key,src=nginx-repo.key -t <your_registry>.azurecr.io/nginx/nap:v1.0 .

* Push your NAP image into your private registry

  .. code-block:: bash

    docker push <your_registry>.azurecr.io/nginx/nap:v1.0

