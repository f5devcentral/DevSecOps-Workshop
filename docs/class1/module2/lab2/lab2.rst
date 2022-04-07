Lab 2 - Deploy the NAP in AKS
#############################

It is time to expose and protect Sentence Application. To do so, the NAP will be deployed in your AKS and will route traffic to the right services.

As a reminder (kubectl get services), the FrontEnd service is called ``sentence-frontend-nginx`` and listening on port ``80``

In your Github repo, check the files

* nginx-nap/etc/nginx/upstream.d/http-sentence.conf -> this is the upstream (the sentence-frontend-nginx service)
* nginx-nap/etc/nginx/vhosts.d/http-sentence.conf -> this is the vhosts (choose any fqdn for your lab)

We will have a look later on the NAP policy tree

|

Deploy the NAP in your AKS via Terraform
****************************************

In order to be **immutable**, we will use Terraform for Config as Code, and Infra as Code.

**Immutable** : An application or service is effectively redeployed each time any change occurs

In this module, we will not use any CI tool. You will be the CI tool and run the command manually. So that you understand all the steps equired to do DevSecOps.

First, adapt the Terraform with your environment information

* Modify the plan accordingly so that the image is pulled from your private repository (line 36)
* and also point to your kubeconfig context (line 3)

Go to the ``terraform`` folder and execute the terraform plan

.. code-block:: bash

  terraform init
  terraform plan -auto-approve
  terraform apply -auto-approve

|

Check and test your NAP
***********************

* First of all, check the pod and the services

  .. code-block:: bash

    ❯ kubectl get pods -n sentence
    NAME                                       READY   STATUS    RESTARTS   AGE
    nginx-nap-56f9594df4-67q74                 1/1     Running   0          4h45m
    sentence-adjectives-5558f7d7d9-dkj58       1/1     Running   0          6d5h
    sentence-animals-6496766bc8-x5f4n          1/1     Running   0          6d5h
    sentence-backgrounds-5f784ffd-vd6d6        1/1     Running   0          6d5h
    sentence-colors-5c4c4f8b89-785tb           1/1     Running   0          6d5h
    sentence-frontend-nginx-6fc654698c-ktgfc   1/1     Running   0          6d5h
    sentence-generator-54b5687b54-nrf7h        1/1     Running   0          6d5h
    sentence-locations-bd85f5b7-9bt4n          1/1     Running   0          6d5h

    ❯ kubectl get services -n sentence
    NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
    nginx-nap                 NodePort       10.0.50.62     <none>          80:31146/TCP   6h1m
    nginx-nap-external        LoadBalancer   10.0.248.90    20.67.132.103   80:30086/TCP   6h1m
    sentence-adjectives       ClusterIP      10.0.175.176   <none>          80/TCP         6d5h
    sentence-animals          ClusterIP      10.0.163.130   <none>          80/TCP         6d5h
    sentence-backgrounds      ClusterIP      10.0.71.153    <none>          80/TCP         6d5h
    sentence-colors           ClusterIP      10.0.160.97    <none>          80/TCP         6d5h
    sentence-frontend-nginx   ClusterIP      10.0.36.129    <none>          80/TCP         6d5h
    sentence-generator        ClusterIP      10.0.218.182   <none>          80/TCP         6d5h
    sentence-locations        ClusterIP      10.0.98.74     <none>          80/TCP         6d5h

* As you can notice, an Azure LB with a public address has been created by the Terraform plan
* Modify your ``host`` file in order to result the value set in the file ``nginx-nap/etc/nginx/vhosts.d/http-sentence.conf`` with this IP address

  .. code-block:: bash

    example in nginx-nap/etc/nginx/vhosts.d/http-sentence.conf
    server {
        server_name my-sentence-matt.lab.nordics;
        listen 80;

    Change your host file to resolv my-sentence-matt.lab.nordics with 20.67.132.103
    

* Test your deployment http://<your_fqdn>

  .. image:: ../pictures/lab2/app.png
     :align: center

* Send an attack ``http://<your_fqdn>?a=<script>``
* Attack is blocked

