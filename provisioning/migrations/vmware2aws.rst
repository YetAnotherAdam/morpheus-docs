VMware to AWS Migration
-----------------------

Requirements
^^^^^^^^^^^^

When performing a Hypervisor to Hypervisor migration from VMware to AWS, there are some requirements that must be met:

#. Add S3 Storage Provider to |morpheus|
#. Set Image Transfer Store in you AWS cloud(s) settings in |morpheus|
#. Create VM Import Service roles in your AWS account (not in |morpheus| )
#. Storage Provider selected for migration destination must be set as a Local Storage Provider (not AWS)

Add S3 Storage Provider
^^^^^^^^^^^^^^^^^^^^^^^

In the ```Infrastructure-> Storage`` section, select :guilabel:`+ADD` and enter the same AWS Access key and Secret Key used to create your AWS cloud in |morpheus|. Then add the S3 bucket name that the migration image(s) will be added to.

Set Image Transfer Store
^^^^^^^^^^^^^^^^^^^^^^^^

Under ``Infrastructure -> Clouds``, select your AWS cloud and click :guilabel:`EDIT`. Expand the Advanced Options section and for `IMAGE TRANSFER STORE` select the AWS S3 Storage Provider you created previously and then Save.

Add VM Import Service
^^^^^^^^^^^^^^^^^^^^^

.. TIP:: Refer to the AWS document below to add the required VM Import Service role in AWS: http://docs.aws.amazon.com/vm-import/latest/userguide/import-vm-image.html

VM Import requires a role to perform certain operations in your account, such as downloading disk images from an Amazon S3 bucket. You must create a role named vmimport with a trust relationship policy document that allows VM Import to assume the role, and you must attach an IAM policy to the role.

To create the service role
``````````````````````````

Create a file named ``trust-policy.json`` with the following policy:

.. code-block:: bash

  {
     "Version": "2012-10-17",
     "Statement": [
        {
           "Effect": "Allow",
           "Principal": { "Service": "vmie.amazonaws.com" },
           "Action": "sts:AssumeRole",
           "Condition": {
              "StringEquals":{
                 "sts:Externalid": "vmimport"
              }
           }
        }
     ]
  }

You can save the file anywhere on your computer. Take note of the location of the file, because you'll specify the file in the next step.

Use the create-role command to create a role named vmimport and give VM Import/Export access to it. Ensure that you specify the full path to the location of the ``trust-policy.json`` file.

.. code-block:: bash

  aws iam create-role --role-name vmimport --assume-role-policy-document file://trust-policy.json


Create a file named `role-policy.json` with the following policy, where disk-image-file-bucket is the bucket where the disk images are stored:

.. code-block:: bash

  {
     "Version": "2012-10-17",
     "Statement": [
        {
           "Effect": "Allow",
           "Action": [
              "s3:ListBucket",
              "s3:GetBucketLocation"
           ],
           "Resource": [
              "arn:aws:s3:::disk-image-file-bucket"
           ]
        },
        {
           "Effect": "Allow",
           "Action": [
              "s3:GetObject"
           ],
           "Resource": [
              "arn:aws:s3:::disk-image-file-bucket/*"
           ]
        },
        {
           "Effect": "Allow",
           "Action":[
              "ec2:ModifySnapshotAttribute",
              "ec2:CopySnapshot",
              "ec2:RegisterImage",
              "ec2:Describe*"
           ],
           "Resource": "*"
        }
     ]
  }

Use the following put-role-policy command to attach the policy to the role created above. Ensure that you specify the full path to the location of the ``role-policy.json`` file.

.. code-block:: bash

  aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json

For more information about IAM roles, see IAM Roles in the IAM User Guide.

Storage Providers
^^^^^^^^^^^^^^^^^

Set the "Storage Provider" in the migration wizard destination as a Local Storage type, or leave as Select to use the |morpheus| Appliance.

A local image must be created by |morpheus| prior to S3 upload. A Local Storage provider can be used if one had been added in the ``Infrastructure-> Storage`` section. Simply leaving the Storage Provider setting as "select" will create an image on the |morpheus| appliance, provided sufficient storage existing on the |morpheus| appliance drive.

.. IMPORTANT:: Setting AWS as the Destination Storage Provider will result in a migration failure.

These settings will allow a successful migration from VMware to AWS using the |morpheus| migration wizard.
