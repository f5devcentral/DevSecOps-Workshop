DevSecOps Workshop in Azure
===========================

Welcome
-------

.. warning:: Workshop under construction

Welcome to the |classbold| - |year|

|repoinfo|

This workshop is focused on DevSecOps methodolgy with Nginx solutions.
It covers:

* The deployment of the workshop environment in Azure via Terraform ``(Class 1)``
  
  * Azure Container Registry to store your Nginx App Protect docker images
  * Azure Kubenetes cluster

* Understanding of DevSecOps methodology with Nginx solutions with you will be the CI pipeline (manuel apply) ``(Class 2)``

  * Github as source of truth
  * Terraform as infra and config as code
  * Yourself as a CI

* DevSecOps methodology with Nginx solutions with Terraform Cloud ``(Class 3)``

  * Github as source of truth
  * Terraform as infra and config as code
  * Terraform Cloud as a CI


.. toctree::
   :maxdepth: 3
   :caption: Contents:
   :glob:

   class*/class*
