Provisioning Settings
=====================

`Administration -> Provisioning`

Settings
  Configure Global Provisioning, Cloud-init and PXE Boot settings.
Environments
  Create and manage Environment Tags
Licenses
  Add License to apply to Windows Instances during Provisioning.

Settings
--------

Allow Cloud Selection
  Displays or hides Cloud Selection dropdown in Provisioning wizard.
Allow Host Selection
  Displays or hides Host Selection dropdown in Provisioning wizard.
Show Pricing
  Displays or hides Pricing in Provisioning wizard and Instance and Host detail pages.
Deployment Archive Store
  Default Storage Provider for storing Deployment Archives.

.. NOTE:: Storage Providers can be configured and managed in the `Infrastructure -> Storage` section.

Cloud-Init Settings
-------------------

|morpheus| can add Global users for Linux and Windows at provision time. Cloud-init/Cloudbase-Init or VMware Tools installed on the provisioned Virtual Images is required.

Linux
  * *Username*: Enter User to be added to Linux Instances during provisioning.
  * *Password*: Enter password to be set for the above Linux user.
  * *KeyPair*: Select KeyPair to be added for the above Linux user.

.. NOTE:: Either a Password, KeyPair, or both can be populated for the Linux User. KeyPairs can be added in the `Infrastructure -> Key Pairs` section.

Windows
  * *Administrator Password*: Enter password to be set for the Windows Administrator User during provisioning.

PXE Boot Settings
-----------------

Default Root Password
  Enter the default password to be set for Root during PXE Boots.

.. include:: environments.rst
.. include:: licenses.rst
