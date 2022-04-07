Lab 4 - Disable a WAF signature
###############################

Let's do another Security Operation very common. Regularly, SecOps have to disable WAF signatures due to False Positive. When you send this attack ?a=<script>, this attack matches 2 signatures.

In this lab, we will disable these 2 signatures so that the attack is not blocked anymore by the NAP.

* Modify the JSON file managing the signature exceptions ``nginx-nap/etc/nginx/nap-files/policies/custom-references/signatures/modifications.json``
* Disable the 2 signature ID (200001475 and 200000098)

  * Change the value for "enabled" from ``true`` to ``false``
  * Do it for the 2 signature IDs

* As in the previous lab, commit your change, and push your change to GitHub
* Run your Terraform plan again

  .. code-block:: bash

      terraform apply -auto-approve

* Look at the rolling upgrade and re-send the attack.
* Attack should pass and not be blocked

.. note:: Congrats, you did your first DevSecOps operations. Now, let's do it for real, with real tools.