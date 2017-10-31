# Deploy a Blockchain cluster on IBM Cloud Container Service
Follow these instructions to launch a basic IBM Blockchain network on the IBM Container Service's free plan.

_This guide is mostly the same as [this one](https://ibm-blockchain.github.io/), with minor changes._

**What do I get?**

The Simple Install will bring up the following components:
- A pre-configured Fabric (blockchain runtime):
  - 3 Fabric CAs (one apiece for the orderer org and two peer orgs)
  - Orderer node (running "solo")
  - 2 Fabric peer nodes (one apiece for each peer org - org1 & org2)
  - Some example installed and instantiated chaincode
- Composer Playground (UI for creating and deploying Business Networks to Fabric)
- The basic-sample-network deployed

## Step 1. Prepare & Setup

_Before deploying the IBM Blockchain Developer Environment, you must first prepare a basic cluster on the IBM Container Service._

### Prepare required CLIs and plugins
First, we will download and add the CLIs and plugins that we need to interact with the IBM Container Service.
#### 1. Download and install [kubectl CLI](https://kubernetes.io/docs/tasks/kubectl/install/)

#### 2. Download and install the [Bluemix CLI](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html#download_install)

#### 3. Add the bluemix plugins repo
```
$ bx plugin repo-add bluemix https://plugins.ng.bluemix.net
```

#### 4. Add the container service plugin
```
$ bx plugin install container-service -r bluemix
```

### Setup a cluster

Now, we will use those CLIs and plugins to create a cluster on the IBM Container Service. Use these steps to setup a cluster named blockchain on IBM Container Service. For more information about how to use the [bluemix cli](https://console.bluemix.net/docs/cli/reference/bluemix_cli/bx_cli.html#bluemix_cli).

#### 5. Point Bluemix CLI to production API
```
bx api api.au-syd.bluemix.net
```

#### 6. Login to Bluemix
```
$ bx login
```

#### 7. Target organization and space
```
$ bx target -o YOUR_ORG -s YOUR_SPACE
```
_Note: you can create them via Bluemix dashboard or CLI. List available orgs and spaces using `bx account`_

#### 8. Create a cluster on IBM Container Service
This will create a **free cluster** named blockchain on the IBM Container Service.
```
$ bx cs cluster-create --name blockchain
```

#### 9. Wait for the cluster to be ready
Issue the following command to ascertain the status of your cluster:
```
$ bx cs clusters
```
The process goes through the following lifecycle - `requesting` –> `pending` –> `deploying` –> `normal`. Initially you will see something similar to the following:
```
Name         ID                                 State       Created                    Workers
blockchain   7fb45431d9a54d2293bae421988b0080   deploying   2017-05-09T14:55:09+0000   0
```
Wait for the State to change to normal. Note that this can take upwards of 15-30 minutes. If it takes more than 30 minutes, there is an inner issue on the IBM Container Service.

You should see the following output when the cluster is ready:
```
$ bx cs clusters
Listing clusters...
OK
Name         ID                                 State    Created                    Workers
blockchain   0783c15e421749a59e2f5b7efdd351d1   normal   2017-05-09T16:13:11+0000   1
```
Use the following syntax to inspect on the status of the workers: Command:
```
$ bx cs workers <cluster-name>
```
For example:
```
$ bx cs workers blockchain
```
The expected response is as follows:
```
$ bx cs workers blockchain
Listing cluster workers...
OK
ID                                                 Public IP       Private IP       Machine Type   State    Status
kube-dal10-pa0783c15e421749a59e2f5b7efdd351d1-w1   169.48.140.48   10.176.190.176   free           normal   Ready
```

#### 10. Configure kubectl to use the cluster
Issue the following command to download the configuration for your cluster:
```
$ bx cs cluster-config blockchain
```
Expected output:
```
Downloading cluster config for blockchain
OK
The configuration for blockchain was downloaded successfully. Export environment variables to start using Kubernetes.

export KUBECONFIG=/home/*****/.bluemix/plugins/container-service/clusters/blockchain/kube-config-prod-dal10-blockchain.yml
```
Use the export command printed as output above to point your kubectl cli to the cluster. For example:

(Replace this example with the output from the step above!)
```
$ export KUBECONFIG=/home/*****/.bluemix/plugins/container-service/clusters/blockchain/kube-config-prod-dal10-blockchain.yml
```

#### 11. Access Kubernetes WebUI
Issue following command:
```
$ kubectl proxy
```
It will start a service listening on `localhost:8001`. Open the URL in your favorite browser.

## Step 2. Deploy Blockchain
### Install with a single script
The Simple Install method is to use the all in one script - create_all.sh - which will call a series of scripts to ultimately bootstrap a blockchain network, join peers to a channel and launch the Composer playground. You can then use Composer Playground to create and deploy Business Networks to your blockchain network.
#### 1. Clone ibm-container-service repository
You’ll be using the config files and scripts from this repository, so start by cloning it to a directory of your choice on your local machine.
```
git clone https://github.com/IBM-Blockchain/ibm-container-service
```

#### 2. Run the script
Navigate to the `scripts` sub-directory:
```
cd cs-offerings/scripts
```
Now run the script:
  - Option 1: To use **leveldb** as worldstate db for peers:
```
./create_all.sh
```
  - Option 2: To use couchdb as worldstate db for peers:
```
./create_all.sh --with-couchdb
```

## Step 3. Interacting with your Blockchain
See [this](https://ibm-blockchain.github.io/interacting/) guide
