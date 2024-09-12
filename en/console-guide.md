## Network > Flow Log > Console User Guide

## Manage flow logs
### Create Flow Log
It can be created through the Create flow log in the NHN Cloud console. For flow logs to work correctly, the flow log system must have access to storage. For more information, see **Granting storage access for flow logs**below.


After you click the **Create flow log** button, you can set basic information about the flow log. The settings require the following items.


* Name: Enter a name for the flow log.
* Description: Describe the flow log.
* Collect target: Allows you to select the flow log collection unit. Currently, you can only create `network interface` units. Here you can see a list of network interfaces that exist in your project, and select the ones you want to collect.
* Collect traffic type: You can select **Allow**, **Block**, or both. If you select Allow, only packets allowed to **Security Groups** are collected; if you click Block, only blocked packets are collected. If you select both, both allowed and blocked packets are collected and aggregated.

* Collect only connection attempt packets: If checked, packets after the connection is established are not collected. For TCP, packets with the TCP state established are not collected, and for UDP/ICMP, response packets are not collected.
* Collection interval: The time interval to clean and create statistics through collected packets and create a file in storage. It can be set between 1 minute and 15 minutes. 
* File storage path: Currently, only Object Storage is supported. For **Object Storage**, enter the OBS endpoint, AUTH_tenant, container, and path at once.
    * {OBS https endpoint}/{AUTH_OBS_TENANT}/{Container}/{Path}
    * For example, if the OBS https endpoint is `https://api-storage.cloud.toast.com/v1,` the AUTH_OBS_TENANT is `AUTH_e670167936434f85a03694184000ffe6`, the Container is named `flowlog_container`, and the desired save path is `example/my/folder,` the file save path would look like this.

    * Example file storage path: https://api-storage.cloud.toast.com/v1/AUTH_e670167936434f85a03694184000ffe6/flowlog_container/example/my/folder


> [Caution] If the container specified does not exist in Object Storage, or if permissions are not set correctly, the flow log enters an error state. You can see the error by hovering over the status on the View flow log screen.

 
> [Note] If the path specified does not exist within Object Storage, it will be created.


* File type: The format of the file to save to the repository. **CSV** and **PARQUET** file formats are supported. `PARQUET`allows you to compress large files into smaller sizes.

* Save split files: The folder frequency at which the flow log categorizes the files it generates. If you specify **Every hour**, the flow log writes to one folder every hour under the file storage path set. If you specify **Every 1 day**, it creates a folder every 1 day.

    * {storage_url}/{YEAR}/{MONTH}/{DAY}/{HOUR}
    * Example: When set to every hour, save flow log files from 22:00 to 22:59 under the example/my/folder/2024/09/01/22 folder
    * Example: When set to Every 1 day, save the flow log files for September 1 under the folder example/my/folder/2024/09/01


### Change Flow Log
You can modify the name and description of the flow log by clicking the **Change flow log** button.

### Delete Flow Log
You can delete a flow log by clicking the **Delete flow log** button.

> [Caution] If the network interface on which the flow logs are being collected is deleted, the flow logs are not deleted. Instead, the **Deleted resource** message is displayed and you must delete the flow logs yourself.
If the resource being collected is deleted, you will not be charged because the data being collected does not exist.

### Flow Log System Account Information
Click **Flow Log system account information** to display the **Flow Log tenant ID** and **Flow Log API user ID** used by the Flow Log system. To use flow logs, you must grant write access to your storage to the corresponding Flow Log tenant ID and Flow Log API user ID. For more information, see **Granting storage access for flow logs** below.




## Grant storage access permissions for flow logs
### Object Storage
If you created a flow log with the storage type specified as object storage (OBS), the flow log system account must have write access to that OBS. If you don't grant write permissions correctly, the flow log system account can't write data to the OBS.


### How to set it up

* Access the NHN Cloud > Object Storage console.

* Select a container to store flow log data in.
* At the bottom, click **Basic info > Change access policy settings**.

* Under **Role-based access policy,**click **Enable**.
* Enter the tenant ID and API user ID from the **flow log system account information** identified above, and grant **Write** permission.

> [Note] When a flow log is created, it sends a 0 Bytes file (dummy file) to the first-time user's OBS storage. This verifies that access permissions are set correctly. This dummy file is deleted after a short period of time.

