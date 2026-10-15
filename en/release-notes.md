<!-- machine_translated: true -->

## Network > Flow Log > Release Notes

### August 25, 2026

#### Feature Updates

* You can add the timezone template variable (`#{timezone}`) as an option to Flow Log file name customization.
    * If you include the `#{timezone}` variable when customizing a file name, the timezone is displayed in the file name; if you exclude it, the timezone is not displayed.
    * The basic file name includes the timezone as before. The timezone value is determined by region. (KST for the Korea region)
    * For more information, see the [Flow Log Console Guide](/Network/Flow%20Log/en/console-guide/).

### May 27, 2026

#### Feature Updates

* Added the `traffic_path` field.
    * The network path that a packet traversed (VPC Local, Internet Gateway, VPN Gateway, VPC Peering, Region Peering, Project Peering, Service Gateway) can be viewed as an integer value.
    * For more information, see [Flow Log Overview](/Network/Flow%20Log/en/overview/).

### May 27, 2025

#### Added Features

* Added support for creating Flow Logs targeting the network interfaces of Region peering gateways, Project peering gateways, Colocation gateways, and load balancers.

#### Feature Updates

* Improved so that you can freely edit folder and file names when storing Flow Log files in Object Storage.

### March 4, 2025

#### Feature Updates

* Added support for creating Flow Logs targeting Transit Hub attachments.
* Added VPC and Subnet to Flow Log Collection Target.

### November 26, 2024

#### Feature Updates

* Added gzip compression.
* Improved so that you can select only the Statistics Information Items you want to record from among the items supported by Flow Log. For information about the supported statistics items, see [Flow Log Overview](/Network/Flow%20Log/en/overview/).

### August 27, 2024

#### Added Features

* Added the Flow Log service. Flow Log allows you to collect and store information about IP traffic sent to and received from a network interface.
    * Flow Log is only available in the Korea (Pyeongchon) region and Korea (Pangyo) region.