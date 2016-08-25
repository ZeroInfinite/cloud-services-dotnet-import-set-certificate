Introduction
----

This example shows how to import a certificate and grant access to the
private key for a specific account in Cloud Service Startup tasks.


Prerequisites
----

***1. Create a Cloud Service***

You may refer to the following guide on how to create an Azure Cloud
Service.

<https://azure.microsoft.com/en-us/documentation/articles/cloud-services-how-to-create-deploy-portal/>

***2. Obtain a certificate that contains a private key***

Please export the private key to a Personal Information Exchange (.pfx)
file. You may refer to the “To export the .pfx and .cer files” section
in the following link for detailed view of steps.

<https://technet.microsoft.com/en-us/library/dd261744.aspx>

Scenario 1
----

In the first scenario, the certificate is packed in Cloud Service
package, so redeployment is required in case the certificate needs to be
replaced.

***1. Create files needed.***

Create a “startup.cmd” file and a “startup.ps1” file in the directory of
the Role that will use the certificate. Place the .pfx file in the same
directory.

***2. Configure Service Definition File.***

Open “ServiceDefinition.csdef”, add &lt;Startup&gt; element under
&lt;WebRole&gt; configuration element.

``` xml
<Startup>
```

***3. Add code to “startup.cmd”.***

Replace the three arguments.

• **Argument1**: the account that will be granted permission to access the
private key, e.g. User1

• **Argument2**: the .pfx filename, e.g. cer.pfx

• **Argument3**: the password for the .pfx, e.g. Password01.

``` command
REM   Run an unsigned PowerShell script and log the output
```

***4. Add code to “startup.ps1”.***

``` powershell
param
```

***5. Modify file properties.***

Set “Copy to Output Directory” of “startup.cmd”, “xxx.pfx” and
“startup.ps1” to “Copy always”.

***6. Save all and deploy the Cloud Service.***

Logs of the startup task can be found in
“C:\\Resources\\temp\\*\[guid\].\[rolename\]*\\RoleTemp” of the Role
instance.

Scenario 2
----

In the second scenario, the certificate is uploaded manually via Azure
Portal and can be replaced without re-deploying the Cloud Service.

***1. Upload the certificate (.pfx file) in Azure Portal &gt; Cloud
Service &gt; Certificates.***

You may refer to the following link for detailed view of steps.

<https://azure.microsoft.com/en-us/documentation/articles/cloud-services-configure-ssl-certificate/\#step-3-upload-a-certificate>

***2. Configure the Role to use the certificate.***

Double click the Role, click **Certificates**, click **Add Certificate** and
enter appropriate value for **Name** and **Thumbprint**.

***3. Create files needed.***

Create a “startup.cmd” file and a “startup.ps1” file in the directory of
the Role that will use the certificate.

***4. Configure Service Definition File.***

``` xml
<Startup>
```

***5. Add code to “startup.cmd”.***

Replace the two arguments.

• **Argument1**: the account that will be granted permission to access the
private key, e.g. User1

• **Argument2**: the subject of the certificate, e.g. corp.contoso.com

``` commandLine
REM   Run an unsigned PowerShell script and log the output
```

***6. Add code to “startup.ps1”.***

Note that the script locates the certificate by matching the subject
with “\**\[Argument2 in step5\]*\*”.

``` PowerShell
param
```

***7. Modify file properties.***

Set “Copy to Output Directory” of “startup.cmd” and “startup.ps1” to
“Copy always”.

***8. Save all and deploy the Cloud Service.***

Logs of the startup task can be found in
“C:\\Resources\\temp\\*\[guid\].\[rolename\]*\\RoleTemp” of the Role
instance.

Replacing certificate
----

For above Scenario 1, please replace the “xxx.pfx” file in the Cloud
Service package, modify “startup.cmd” accordingly and re-deploy the
Cloud Service.

For above Scenario 2, please refer to following steps:

1\. Upload the new certificate (.pfx file) in **Azure Portal &gt; Cloud
Service &gt; Certificates**.

2\. Go to **Configuration** and update **THUMBPRINT** to the one of the new
certificate.

3\. Reboot the instance (in order to execute startup task).