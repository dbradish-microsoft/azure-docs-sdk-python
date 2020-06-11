---
title: Azure Storage File Share client library for Python
keywords: Azure, Python, SDK, API, storage, azure-storage-file-share
author: maggiepint
ms.author: magpint
ms.date: 04/16/2020
ms.topic: article
ms.prod: azure
ms.technology: azure
ms.devlang: Python
ms.service: storage
---
 # Azure Storage File Share client library for Python - Version 12.1.1 

Azure File Share storage offers fully managed file shares in the cloud that are accessible via the industry standard [Server Message Block (SMB) protocol](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview). Azure file shares can be mounted concurrently by cloud or on-premises deployments of Windows, Linux, and macOS. Additionally, Azure file shares can be cached on Windows Servers with Azure File Sync for fast access near where the data is being used.

Azure file shares can be used to:

* Replace or supplement on-premises file servers
* "Lift and shift" applications
* Simplify cloud development with shared application settings, diagnostic share, and Dev/Test/Debug tools

[Source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/azure/storage/fileshare) | [Package (PyPI)](https://pypi.org/project/azure-storage-file-share/) | [API reference documentation](https://aka.ms/azsdk-python-storage-fileshare-ref) | [Product documentation](https://docs.microsoft.com/azure/storage/) | [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples)

## Getting started

## 1: Set up your local development environment
 
If you haven't already, follow all the instructions on [Configure your local Python dev environment for Azure](https://docs.microsoft.com/azure/developer/python/configure-local-development-environment?tabs=bash).
 
Be sure to create a service principal for local development, and create and activate a virtual environment for this project.

## 2. Create a storage account

[Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)

## 3. Install the package

```bash
pip install azure-storage-queue
```

## 4. Create the client
The Azure Storage File Share client library for Python allows you to interact with four types of resources: the storage
account itself, file shares, directories, and files. Interaction with these resources starts with an instance of a
[client](#clients). To create a client object, you will need the storage account's file service URL and a
credential that allows you to access the storage account:

```python
from azure.storage.fileshare import ShareServiceClient

service = ShareServiceClient(account_url="https://<my-storage-account-name>.file.core.windows.net/", credential=new DefaultAzureCredential())
```

*Notes:* If you have created a service principal following the [configure your local environment documentation](https://docs.microsoft.com/azure/developer/python/configure-local-development-environment?tabs=bash), `Default Azure Credential` works without additional parameters. For additional configuration options see [Authorizing access to data in Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-auth).

You can find the storage account's blob service URL using the [Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints).


## Examples
The following sections provide several code snippets covering some of the most common Storage File Share tasks, including:

* [Creating a file share](#creating-a-file-share "Creating a file share")
* [Uploading a file](#uploading-a-file "Uploading a file")
* [Downloading a file](#downloading-a-file "Downloading a file")
* [Listing contents of a directory](#listing-contents-of-a-directory "Listing contents of a directory")

### Creating a file share
Create a file share to store your files

```python
from azure.storage.fileshare import ShareClient

share = ShareClient.from_connection_string(conn_str="<connection_string>", share_name="my_share")
share.create_share()
```

Use the async client to create a file share

```python
from azure.storage.fileshare.aio import ShareClient

share = ShareClient.from_connection_string(conn_str="<connection_string>", share_name="my_share")
await share.create_share()
```

### Uploading a file
Upload a file to the share

```python
from azure.storage.fileshare import ShareFileClient

file_client = ShareFileClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", file_path="my_file")

with open("./SampleSource.txt", "rb") as source_file:
    file_client.upload_file(source_file)
```

Upload a file asynchronously

```python
from azure.storage.fileshare.aio import ShareFileClient

file_client = ShareFileClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", file_path="my_file")

with open("./SampleSource.txt", "rb") as source_file:
    await file_client.upload_file(source_file)
```

### Downloading a file
Download a file from the share

```python
from azure.storage.fileshare import ShareFileClient

file_client = ShareFileClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", file_path="my_file")

with open("DEST_FILE", "wb") as file_handle:
    data = file_client.download_file()
    data.readinto(file_handle)
```

Download a file asynchronously

```python
from azure.storage.fileshare.aio import ShareFileClient

file_client = ShareFileClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", file_path="my_file")

with open("DEST_FILE", "wb") as file_handle:
    data = await file_client.download_file()
    await data.readinto(file_handle)
```

### Listing contents of a directory
List all directories and files under a parent directory

```python
from azure.storage.fileshare import ShareDirectoryClient

parent_dir = ShareDirectoryClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", directory_path="parent_dir")

my_list = list(parent_dir.list_directories_and_files())
print(my_list)
```

List contents of a directory asynchronously

```python
from azure.storage.fileshare.aio import ShareDirectoryClient

parent_dir = ShareDirectoryClient.from_connection_string(conn_str="<connection_string>", share_name="my_share", directory_path="parent_dir")

my_files = []
async for item in parent_dir.list_directories_and_files():
    my_files.append(item)
print(my_files)
```

## Understanding The Examples
The following components make up the Azure File Share Service:
* The storage account itself
* A file share within the storage account
* An optional hierarchy of directories within the file share
* A file within the file share, which may be up to 1 TiB in size

The Azure Storage File Share client library for Python - Version 12.1.1 
 allows you to interact with each of these components through the
use of a dedicated client object.

### Clients
Four different clients are provided to to interact with the various components of the File Share Service:
1. [ShareServiceClient](https://aka.ms/azsdk-python-storage-fileshare-shareserviceclient) -
    this client represents interaction with the Azure storage account itself, and allows you to acquire preconfigured
    client instances to access the file shares within. It provides operations to retrieve and configure the service
    properties as well as list, create, and delete shares within the account. To perform operations on a specific share,
    retrieve a client using the `get_share_client` method.
2. [ShareClient](https://aka.ms/azsdk-python-storage-fileshare-shareclient) -
    this client represents interaction with a specific file share (which need not exist yet), and allows you to acquire
    preconfigured client instances to access the directories and files within. It provides operations to create, delete,
    configure, or create snapshots of a share and includes operations to create and enumerate the contents of
    directories within it. To perform operations on a specific directory or file, retrieve a client using the
    `get_directory_client` or `get_file_client` methods.
3. [ShareDirectoryClient](https://aka.ms/azsdk-python-storage-fileshare-sharedirectoryclient) -
    this client represents interaction with a specific directory (which need not exist yet). It provides operations to
    create, delete, or enumerate the contents of an immediate or nested subdirectory, and includes operations to create
    and delete files within it. For operations relating to a specific subdirectory or file, a client for that entity can
    also be retrieved using the `get_subdirectory_client` and `get_file_client` functions.
4. [ShareFileClient](http://aka.ms/azsdk-python-storage-fileshare-sharefileclient) -
    this client represents interaction with a specific file (which need not exist yet). It provides operations to
    upload, download, create, delete, and copy a file.

For details on path naming restrictions, see [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata).


## Optional Configuration

Optional keyword arguments that can be passed in at the client and per-operation level. 

### Retry Policy configuration

Use the following keyword arguments when instantiating a client to configure the retry policy:

* __retry_total__ (int): Total number of retries to allow. Takes precedence over other counts.
Pass in `retry_total=0` if you do not want to retry on requests. Defaults to 10.
* __retry_connect__ (int): How many connection-related errors to retry on. Defaults to 3.
* __retry_read__ (int): How many times to retry on read errors. Defaults to 3.
* __retry_status__ (int): How many times to retry on bad status codes. Defaults to 3.
* __retry_to_secondary__ (bool): Whether the request should be retried to secondary, if able.
This should only be enabled of RA-GRS accounts are used and potentially stale data can be handled.
Defaults to `False`.

### Other client / per-operation configuration

Other optional configuration keyword arguments that can be specified on the client or per-operation.

**Client keyword arguments:**

* __connection_timeout__ (int): Optionally sets the connect and read timeout value, in seconds.
* __transport__ (Any): User-provided transport to send the HTTP request.

**Per-operation keyword arguments:**

* __raw_response_hook__ (callable): The given callback uses the response returned from the service.
* __raw_request_hook__ (callable): The given callback uses the request before being sent to service.
* __client_request_id__ (str): Optional user specified identification of the request.
* __user_agent__ (str): Appends the custom value to the user-agent header to be sent with the request.
* __logging_enable__ (bool): Enables logging at the DEBUG level. Defaults to False. Can also be passed in at
the client level to enable it for all requests.
* __headers__ (dict): Pass in custom headers as key, value pairs. E.g. `headers={'CustomValue': value}`


## Troubleshooting
### General
Storage File clients raise exceptions defined in [Azure Core](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/core/azure-core/README.md).
All File service operations will throw a `StorageErrorException` on failure with helpful [error codes](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes).

### Logging
This library uses the standard
[logging](https://docs.python.org/3/library/logging.html) library for logging.
Basic information about HTTP sessions (URLs, headers, etc.) is logged at INFO
level.

Detailed DEBUG level logging, including request/response bodies and unredacted
headers, can be enabled on a client with the `logging_enable` argument:
```python
import sys
import logging
from azure.storage.fileshare import ShareServiceClient

# Create a logger for the 'azure.storage.fileshare' SDK
logger = logging.getLogger('azure.storage.fileshare')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

# This client will log detailed information about its HTTP sessions, at DEBUG level
service_client = ShareServiceClient.from_connection_string("your_connection_string", logging_enable=True)
```

Similarly, `logging_enable` can enable detailed logging for a single operation,
even when it isn't enabled for the client:
```py
service_client.get_service_properties(logging_enable=True)
```

## Next steps

### More sample code

Get started with our [File Share samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples).

Several Storage File Share Python SDK samples are available to you in the SDK's GitHub repository. These samples provide example code for additional scenarios commonly encountered while working with Storage File Share:

* [file_samples_hello_world.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world_async.py)) - Examples found in this article:
    * Client creation
    * Create a file share
    * Upload a file

* [file_samples_authentication.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication_async.py)) - Examples for authenticating and creating the client:
    * From a connection string
    * From a shared access key
    * From a shared access signature token

* [file_samples_service.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_service_async.py)) - Examples for interacting with the file service:
    * Get and set service properties
    * Create, list, and delete shares
    * Get a share client

* [file_samples_share.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_share_async.py)) - Examples for interacting with file shares:
    * Create a share snapshot
    * Set share quota and metadata
    * List directories and files
    * Get the directory or file client to interact with a specific entity

* [file_samples_directory.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory_async.py)) - Examples for interacting with directories:
    * Create a directory and add files
    * Create and delete subdirectories
    * Get the subdirectory client

* [file_samples_client.py](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py) ([async version](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples/file_samples_client_async.py)) - Examples for interacting with files:
    * Create, upload, download, and delete files
    * Copy a file from a URL

### Additional documentation
For more extensive documentation on Azure File Share storage, see the [Azure File Share storage documentation](https://docs.microsoft.com/azure/storage/files/) on docs.microsoft.com.

## Contributing
This project welcomes contributions and suggestions.  Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.