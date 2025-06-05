---
title: "Distributed Splunk Environment Lab: Part 2 - Create a Splunk Distributed Environment"
date: 2025-05-05 00:00:00 +0100
categories: [Splunk, Lab]
tags: [splunk, linux, lab, virtualbox]
image: 
  path: assets/images/VirtualBox-Splunk-Installation-Configuration-Part1/SplunkHeader.png
---

## Lab Overview

The Splunk environment is going to look like this at the end of this post:

![FLOWCHART](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkDistributedEnvironmentFlowchart.svg)

- 1 Search Head
- 3 Clustered Indexers
- 1 Cluster Manager
- 1 Heavy Forwarder
- 1 Universal Forwarder
- 1 Deployment Server also acting as a license manager and monitoring console.

## Pre Requisites

A license is needed to configure a distributed Splunk environment.

Request a Splunk developer [license here](https://dev.splunk.com/enterprise/dev_license/), if you haven't already.

## Deployment Server and deployment clients

A Deployment Server is going to allow us to distribute apps and configurations to multiple servers at the same time. This is particularly helpful in Splunk environments with a large number of clients. It also ensures all the clients have the same app version and default configurations.

### Configure the Deployment Clients

SSH into each server apart from the indexers and the deployment server and run the following command:
```bash
/opt/splunk/bin/splunk set deploy-poll "https://10.0.5.205:8089"
```
Enter the admin credentials when prompted.
![DeploymentClientConfiguration.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/DeploymentClientConfiguration.png)

To check if the clients are connected to the Deployment Server, go to the Deployment Server GUI:

1. Click `Settings` > `Forwarder Management`.

2. The 4 servers are know shown in this page.
![DeploymentClients-DSGUI.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/DeploymentClients-DSGUI.png)

Another way to check what servers are configured as deployment clients of the deployment server `ds` is to run the following splunk query:
```bash
index=_internal host=ds "*POST /services/broker/phonehome*"
| dedup clientip
| table clientip
```
![DeploymentClients-SplunkQuery.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/DeploymentClients-SplunkQuery.png)

The deployment server and clients are now configured.

## Cluster Configuration

To create the cluster of three indexers:

### Manager Node

1. SSH into the Cluster Manager.

2. Run the following command:
```bash
/opt/splunk/bin/splunk edit cluster-config -mode manager -replication_factor 3 -search_factor 2 -secret splunkLab -cluster_label indexerCluster
```
![IndexerCluster-ClusterManagerConfig.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-ClusterManagerConfig.png)

3. Restart Splunk.
```bash
/opt/splunk/bin/splunk restart
```

On the Cluster Manager GUI, click `Settings`  `Indexer Clustering`.<br>As the page shows, the server is configured as the manager node.
![IndexerCluster-ManagerNode](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-ManagerNode.png)

### Peer Nodes

1. SSH into each indexer and run the following command to configure them as peer nodes:
```bash
/opt/splunk/bin/splunk edit cluster-config -mode peer -manager_uri https://10.0.5.206:8089 -replication_port 9887 -secret splunkLab
```
![IndexerCluster-Peers.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-Peers.png)

2. Restart Splunk on each indexer.
On the Cluster Manager GUI, click `Settings` > `Indexer Clustering`. Select the `Indexes` tab.<br>As the page shows, the indexers have been configured as peer nodes.
![IndexerCluster-PeersGUI](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-PeersGUI.png)

### Search Head

1. SSH into the search head and run the following command to configure it as a search head of the indexer cluster.
```bash
/opt/splunk/bin/splunk edit cluster-config -mode searchhead -manager_uri https://10.0.5.206:8089 -secret splunkLab
```
![IndexerCluster-SearchHead.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-SearchHead.png)

2. Restart Splunk on the search head.

On the Cluster Manager GUI, click `Settings` > `Indexer Clustering`. Select the `Search Head` tab.<br>As the page shows, the search head has been configured.
![IndexerCluster-SearchHeadGUI](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-SearchHeadGUI.png)

To view the status of the cluster, go to the cluster manager GUI and click `Settings` > `Indexer Clustering`
![IndexerCluster-ConfiguredGUI](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-ConfiguredGUI.png)

The following command can also be run on the cluster manager to view the indexer cluster status.
```bash
/opt/splunk/bin/splunk show cluster-status
```
![IndexerCluster-ConfiguredCLI](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerCluster-ConfiguredCLI.png)

## License

To configure a distributed Splunk environment, a license is needed.

### Install the License

As per Splunk documentation, the deployment server can be used as a license manager.

To add the license to the deployment server:

1. Go to the Deployment Server GUI.

2. Click `Settings` > `Licensing`.

3. Click on `Add License` and click `Ok`.

4. Upload the Splunk License and select `Install`.

5. Restart Splunk after installing the license.
![SplunkLicense](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense.png)

6. Login again and go to `Settings` > `Licensing`. As you can see, the Splunk Developer license has been installed and is active.
![SplunkLicenseInstalled](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicenseInstalled.png)

### License App

All the other Splunk servers are going to use the deployment server as the license manager. To make the process easier, an app can be deployed to all the servers through the deployment server. This ensures any new change made to the file in the deployment server is pushed to the other Splunk servers. It also speeds up the process because we don't need to manually configure each server.

To easily connect the splunk servers to the license manager, a "SplunkLicense" app is going to be created.

1. On the Deployment Server CLI, go to the `$SPLUNK_HOME/etc/deployment-apps` directory.

> This is the directory the deployment server uses to store apps and files that are going to be deployed to the deployment clients.
{: .prompt-info }

2. Create `SplunkLicense/default`.

3. Create `SplunkLicense/default/server.conf` and add following to it:
```bash
[license]
manager_uri = https://10.0.5.205:8089
```
The app is now ready to be deployed to the clients but the deployment server still doesn't know where to send the app to.<br>
To map apps to clients, the deployment server uses server classes.<br> To create one, go to the Deployment Server GUI and:

1. Click `Settings` > `Forwarder Management`.

2. Select the `server class` tab and click on `create one`. Enter the name all_servers.

3. Click on `Add Clients` and enter `ind*` on the `Exclude` box. Click on `Preview` and select the `Matched` tab. Click `Save`.
![SplunkLicense-Clients.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-Clients.png)

4. Click on `Add Apps` and select the `SplunkLicense` app. Click `Save`.
![SplunkLicense-App](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-App.png)

5. Click on `Edit` under the `Actions` column and check the Restart Splunk box.
![SplunkLincese-Restart.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLincese-Restart.png)

The server class `all_servers` is now configured and the app can be pushed to the clients.

SSH into the Deployment Server and run the following command:
```bash
/opt/splunk/bin/splunk reload deploy-server -class all_servers
```
![SplunkLincese-ReloadServerClass.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLincese-ReloadServerClass.png)

The indexers are managed by the Cluster Manager.
To deploy the app to the indexers:

1. SSH into the cluster manager.

2. Copy the `SplunkLicense` app from the apps directory to `/opt/splunk/etc/manager_apps/`.
```bash
cp -R /opt/splunk/etc/apps/SplunkLicence /opt/splunk/etc/manager_apps/
```

3. Run the following command:
```bash
/opt/splunk/bin/splunk validate cluster-bundle --check-restart
```

4. Take note of the new bundle checksum.
![SplunkLicense-Validate](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-Validate.png)

4. Run the following command and confirm if the value of `last_bundle_checked_for_restart` matches the value from the previous step in all the servers.
```bash
/opt/splunk/bin/splunk show cluster-bundle-status
```
![SplunkLicense-Status](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-Status.png)


5. If it does, run the following command:
```bash
/opt/splunk/bin/splunk apply cluster-bundle
```

6. Run the following command and confirm if the value of `active_bundle` is the one from the previous steps. 
![SplunkLicense-Check](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-Check.png)

To confirm if the Splunk servers are using the deployment server as the license manager, run the following command on each server or go to the GUI of each and click `Settings` > `Forwarder Management`/.
```bash
/opt/splunk/bin/splunk list licenser-peers
```
![SplunkLicense-ConfiguredCLI](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-ConfiguredCLI.png)

Indexer GUI:<br>
![SplunkLicense-Configured](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/SplunkLicense-Configured.png)

## Outputs

Now, the indexer cluster is configured and connected to the Search Head and the Deployment Server and clients are configured.
To start forwarding data to the indexers, the outputs.conf file has to be configured.
To easily distribute and find indexers in the environment, indexer discovery is going to be configured.
When indexer discovery is enabled, each forwarder queries the manager node for a list of all peer nodes in the cluster. It then uses load balancing to forward data to the set of peer nodes.

The flow of dara of this lab is going to be as described below:

UniversalForwarder > HeavyForwarder > IndexerCluster > Search Head

### Indexer Discovery

Do the following on the Cluster Manager to enable indexer discovery:

1. Create `/opt/splunk/etc/system/local/inputs.conf`
```bash
[splunktcp://9997]
disabled = 0
```

2. Validate and apply the cluster bundle.
```bash
/opt/splunk/bin/splunk validate cluster-bundle --check-restart
/opt/splunk/bin/splunk apply cluster-bundle --answer-yes
```
3. Edit `/opt/splunk/etc/system/local/server.conf`.
```bash
[indexer_discovery]
pass4SymmKey = splunkLab
```
4. Restart splunk.

To configure outputs, the Deployment Server is going to be used again.

Two apps are going to be needed:
- HFOutputs (Indexer Discovery)
- UFOutputs
>Note: A Heavy Forwarder is not always needed and neither do all the logs have to go through the Heavy Forwarder even if one is used.

SSH into the deployment server and execute the following:

1. Change to the `/%SPLUNK_HOME/etc/deployment-apps`
```bash
cd /opt/splunk/etc/deployment-apps
```

2. Create the `HFOutputs` directory.
```bash
mkdir HFOutputs
```

3. Create `HFOutputs/default` and add the following to server.conf:

```bash
[indexer_discovery:indexerCluster]
master_uri = https://10.0.5.206:8089

[tcpout]
defaultGroup = indexerCluster

[tcpout:indexerCluster]
indexerDiscovery = indexerCluster
useACK = true
```

4. Create inputs.conf and add the following to it:
```bash
[splunktcp://9997]
disabled = 0
```
                                                                           
4. Go to the deployment server GUI and click `Settings` > `Forwarder Management`.

5. Select the server class tab and create a new server class called `HeavyForwarders`.

6. Click `Add Apps`, select the HFOutputs app and click `Save`.

7. Click `Add Clients` and type hf* in the Include box.
![IndexerDiscovery-HeavyForwardersClass.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerDiscovery-HeavyForwardersClass.png)

8. Run the following command:
```bash
/opt/splunk/bin/splunk reload deploy-server -class HeavyForwarders
```

9. Wait for the Heavy Forwarder to restart and SSH into it.

10. Go to `/opt/splunk/etc/apps/HFOutputs`.

11. Create outputs.conf and add the following to it:
```bash
[indexer_discovery:indexerCluster]
pass4SymmKey = splunkLab
```

12. Restart Splunk.

13. Delete the pass4SymmKey from `/opt/splunk/etc/apps/HFOutputs/default`.

14. Restart Splunk.
                                                      
Let's test if data flows through the different components.

As a reminder, the Heavy Forwarder is now querying the Cluster Manager for peer nodes to send the data to. The logs from the Heavy Forwarder are now sent to the indexer cluster and can be queried by the Search Head.

To test this configuration, open the Search Head GUI and enter the following query:
```bash
index=_internal sourcetype=splunkd host=hf
```
![IndexerDiscovery-HeavyForwarderLogs.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/IndexerDiscovery-HeavyForwarderLogs.png)

### Universal Forwarder

In most Splunk environments, both the Heavy and Universal Forwarders would use Indexer Discovery.<br>
In this environment, the Universal Forwarder is going to be forwarding logs to the Heavy Forwarder to be parsed and then it will be sent from the Heavy Forwarder to the Indexer Cluster.

To configure this, ssh into the deployment server and do the following:

1. Create UFOutputs in `/opt/splunk/etc/deployment-apps`.

2. Create the default directory and outputs.conf
```bash
mkdir UFOutputs/default
nano outputs.conf
```

3. Add the following to it:

```bash 
[tcpout]
defaultGroup = HeavyForwarders

[tcpout:HeavyForwarders]
server = 10.0.5.207:9997 
```

4. Go to the Deployment Server GUI and select `Settings` > `Forwarder Management`.

5. Create a server class called `UniversalForwarders`.

6. Click `Add Apps` and select the `UFOutputs` app to the server class.

7. Click `Add Clients` and enter `uf*` in the Include box.

8. Edit the app and enable `Restart Splunkd`.

9. Run the following on the Deployment Server GUI,

Logs now flow from the Universal Forwarder to the Heavy Forwarder that then forwards them to the Indexer Cluster to be queried by the Search Head.<br>
On the Search Head GUI, run the following query to confirm logs are being indexed:
```bash
index=_internal host=uf
```
![Outputs-UnivesalForwarderLogs.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/Outputs-UnivesalForwarderLogs.png)

## Monitoring Console

The Monitoring Console

### Enable Distributed Mode

1. Open the Deployment Server Splunk Web at .

2. Select `Settings` > `Monitoring Console` > `Settings` > `General Setup`.

3. Select `Distributed Mode`.

4. Click `Continue`.

5. Click `Edit Server Roles` under `Actions` and select:
>Deployment Server<br>Indexer<br>License Manager<br>Search Head

6. Select `Save`.

7. Select `Apply Changes`.

![MonitoringConsole-DistributedMode.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-DistributedMode.png)

### Search Peers

1. Click `Settings` > `Distributed Search`.

2. Select `Search Peers`.

3. Select `New Search Peer`.

4. Add the all the splunk servers, one by one:
>Peer uri:<server ip>:8089<br>remote username: admin<br>remote password: splunkLab

![MonitoringConsole-SearchPeers.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-SearchPeers.png)

5. Go back to `Monitoring Console` > `Settings` > `General Setup`

6. Edit the server roles as:
>On the sh, select only the KV Store and Search Head roles.<br>On cm, select only Cluster Manager.<br>On ind1 – ind3, select only Indexer.<br> On hf1, select Indexer.

7. Click `Apply Changes`.

### Universal Forwarders

1. Click `Settings` > `Forwarder Monitoring Setup`.

2. Select `Enable`.

3. Select `Save` and `Continue`.

The Monitoring Console is now configured.
You can check the following dashboards to confirm it's working for all the servers:

Forwarders > Forwarders:Deployment
![MonitoringConsole-Forwarders.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-Forwarders.png)

Indexing > Performance > Indexing Performance: Deployment
![MonitoringConsole-DeploymentPerformance](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-DeploymentPerformance.png)

Search > Activity > Search Activity: Deployment
![MonitoringConsole-DeploymentSearchActivity](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-DeploymentSearchActivity.png)

Resource Usage > Resource Usage: Deployment
![MonitoringConsole-DeploymentResourceUsage.png](/assets/images/VirtualBox-Splunk-Installation-Configuration-Part2/MonitoringConsole-DeploymentResourceUsage.png)

## Best Practice

### Enable HTTPS

To enable HTTPS:

1. SSH into the Deployment Server.

2. Create SplunkHTTPS and SplunkHTTPS/default.

3. Create web.conf and add the following to it:
```bash
[settings]
enableSplunkWebSSL = true
```

4. Add the app to the all_servers server class.

5. Reload the server class.

### Disable Indexing

Only the indexers need the indexing capability. All the other ones can have it turned off.

1. SSH into the Deployment Server.

2. Go to DSConfigs/default.

3. Create outputs.conf and add the following to it:
```bash
[indexAndForward]
index = false
```

4. Reload the server class.

To disable indexing on the deployment server:

1. Go to /opt/splunk/etc/system/local.

2. Add the following to outputs.conf:
```bash
[indexAndForward]
index = false
```

3. Restart Splunk.

### Indexers 

#### Disable Webpage of Indexers

1. SSH into the Cluster Manager.

2. Go to /opt/splunk/etc/_cluster/local.

3. Create server.conf and add the following to it:
```bash
[general]
startwebserver = 0
```

4. Validate and apply the bundle.

#### Parallel Ingestion pipeline

1. SSH into the Cluster Manager.

2. Add the following to server.conf.
```bash
[general]
parallelIngestionPipelines = 2
```

3. Validate and apply the bundle.

## Btool

btool is a command-line diagnostic tool in Splunk that shows how configuration files are parsed and merged. It helps you:

- View effective configuration settings
- Identify file precedence and overrides
- Troubleshoot misconfigurations

It can be used to see final merged config for a file:
```bash
/opt/splunk/bin/splunk btool [inputs|server|props|transforms|outputs] list --debug
```

## Config Files Recap

[deploymentclient.conf](https://docs.splunk.com/Documentation/Splunk/9.3.1/Admin/Deploymentclientconf) : Configures a Splunk instance to act as a deployment client that connects to a deployment server for app and configuration updates.<br><br>
[inputs.conf](https://docs.splunk.com/Documentation/Splunk/9.3.1/Admin/inputsconf) : Specifies data input settings, including file monitoring, network inputs, and script execution to ingest data into Splunk.<br><br>
[server.conf](https://docs.splunk.com/Documentation/Splunk/9.3.1/Admin/serverconf) : Defines core Splunk server settings such as server roles, clustering, and general instance configuration.<br><br>
[outputs.conf](https://docs.splunk.com/Documentation/Splunk/9.3.1/Admin/outputsconf) : Configures how and where a Splunk instance forwards data, typically to indexers or other Splunk instances, in a distributed or forwarding architecture.