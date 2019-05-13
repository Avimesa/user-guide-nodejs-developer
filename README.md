# User Guide for Node.js Developers (Alpha)
*last updated 2019-May-13*




## Introduction

This project contains the steps necessary to use the Avimesa Device Cloud system targeting Node.js developers by providing examples for both a Device client to produce data and an application to consume data.

You will be using the following resources in this Guide:
- to sign up for a subscription, the [Avimesa website](https://avimesa.com)
- to provision and configure devices, the [Avimesa Toolkit application](https://app.avimesa.com)
- to consume data, the [Group API Example applications](https://github.com/Avimesa/examples-nodejs-group-api-amqp) 
- to produce data, the Virtual Device Client:
    - [Node.js version, for ARMv7 or x86_64](https://github.com/Avimesa/virtual-device-client-nodejs) 
    - [CLI version for x86_64](https://github.com/Avimesa/virtual-device-client-x86-64)
    - [CLI version for ARMv7](https://github.com/Avimesa/virtual-device-client-ARMv7)

<a id="toc"></a>
## Table of Contents
- [1. Overview](#1.-overview)
- [2. Avimesa Subscription](#2.-subscription)
- [3. Subscriber Example Code](#3.-example-subscriber)
- [4. Add Virtual Device](#4.-add-device)
- [5. Configure Virtual Device](#5.-configure-device)
- [6. Use the Virtual Device Client](#6.-virtual-device)
- [7. Script Modification](#6.-scripts)






<a id="1.-overview"></a>
## 1. Overview

The Avimesa Device Cloud system provides the ability to enable device messaging through a scriptable and secure hosted runtime and deliver valuable data to end-user applications.

This User Guide will show you how to:
- Sign up for an Avimesa Subscription and gain access to the Avimesa Toolkit
- Configure a sample application with the subscription using the Group API Examples 
- Add and configure a 'virtual' device using the Avimesa Toolkit
- Use the virtual device to push messages and receive actuations through the system







<a id="2.-subscription"></a>
## 2. Avimesa Subscription

To use the Avimesa Device Cloud, you need to sign up for a subscription [here](https://avimesa.com/pricing/).

Once you've signed up using an email address, you'll be sent your **API Key** and **API Password** which are required to enable access through a sample application.

The **API Key** and **API Password** are also available when logged into the [Avimesa Toolkit](https://app.avimesa.com) under **Account > Settings**








<a id="3.-example-subscriber"></a>
## 3. Subscriber Example Code

If you have your **API Key** and **API Password**, the next step is to clone the Group API repository:

```
git clone https://github.com/Avimesa/examples-nodejs-group-api-amqp.git
cd examples-nodejs-group-api-amqp
npm install
``` 

There are several examples in this repo to explore. We will be using the `queue-subscriber` example, which by default will consume data from the `raw` queue.

First, we need to set the **API Key** and **API Password** by editing the `queue-subscriber/index.js` file, and updating the following lines:

```
let apiKey = '';
let apiPassword = '';
```

Now, we can run this example and leave it running while we continue through the guide.

```
node queue-subscriber/index.js
```

You should simply have a blocking process in the terminal that is waiting to print out messages as they show up.






<a id="4.-add-device"></a>
## 4. Add a Device Using the Toolkit

At this point we have a sample 'application' that is listening for messages. We will now make a Device that can produce messages.  

- Signed into your account at app.avimesa.com, click on **[Your Group Name] > Add Device**
- Press the **Generate ID** button to generate a random Device ID, which is ideal for a Virtual Device.
- Add the optional **Name** and **Location** for your own tracking means.
- Click on **Add Device** to provision a new Device.

You will be provided with an **Authentication Key** so please make note of this for later when we use it with the Virtual Device Client.







<a id="5.-config-device"></a>
## 5. Configure Device Using the Toolkit

At this point, we have a 'brainless' Device runtime hosted in the Device Cloud.  Now we'll upload a Script and Config for this device.

When you cloned the `examples-nodejs-group-api-amqp` repository, you got a Script and Config that can be used for this demo, located at `examples-nodejs-group-api-amqp/files`

- Signed into your account at app.avimesa.com, click on **[Group Name] >> View Devices**
- Find the Device in the list, and click on **Configure**
- Click on **Upload Script**, and browse to `examples-nodejs-group-api-amqp/files/script.js`
- Click on **Upload Config**, and browse to `examples-nodejs-group-api-amqp/files/config.js`

The Device now has a hosted brain!  The `script.js` will simply pass the Device data as is to the `raw` queue.  The `config.json` doesn't do much in this demo besides being sent to the Device in response to the transaction.

This is what the script looks like internally:

```
function avmsaMain(){

	// Send the 'dev_in' data to the raw queue
	avmsaSendToRawQueue();

	// Get the next actuation messsge (if any) and relay to device
	dev_out.actuation = avmsaGetNextActuationMsg();
}
```


<a id="6.-virtual-device"></a>
## 6. Use the Virtual Device Client

The Device Cloud is now ready to be used and an application is waiting for data.  We have two options, both require a Linux based host (e.g. Debian, CentOS, RHEL, Raspbian).

**Node.js Virtual Device Client**

- Follow the `General Use Case` directions [here](https://github.com/Avimesa/virtual-device-client-nodejs#41-general-use-case)
- Use the **Device ID** and **Authentication Key** generated in step 4 above to configure the Device

**Command Line Virtual Device Client**

- For RPi (ARMv7), follow the directions [here](https://github.com/Avimesa/virtual-device-client-ARMv7)
- For x86_64, follow the directions [here](https://github.com/Avimesa/virtual-device-client-x86-64)
- Use the **Device ID** and **Authentication Key** generated in step 4 above in the command line arguments

Using either of the clients above, when you execute the Device Client, the application running from step 3 above should show the data from the device!





<a id="7.-scripts"></a>
## 7. Script Modification

There are examples Device Driver Scripts in GitHub [here](https://github.com/Avimesa/device-driver-scripts)

Subscribe to the `sys_log` queue to check for runtime errors.  You can do this by modifying the example used in step 3 above with the following change:

```
var queue = rmqSettings.queues.syslog;
```

Detailed information about the RMQ interface is available [here](https://github.com/Avimesa/user-guide-group-api-amqp)


