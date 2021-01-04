---
layout: post
title: Scripting In Large Environments
---
When I first started working in Azure running scripts was easy. I could easily do:
```powershell
$subscriptions = Get-AZSubscription
foreach($subscription in $subscriptions)
{
    Select-AZSubscription $subscription
    ### Do Stuff

}
```
As I've gotten the opportunity to work with larger organizations these types of scripts become less efficient. With some clients having hundreds of subscriptions, this method can take a very long time when its not needed.

In comes Azure Resource Graph.

The Azure Resource Graph can be used to search for (almost) anything, however the documentation is sometimes hard to grasp. Here are the top few searches I have found the most useful.

#### Find the Network Interface by IP
This comes in handy when the network team finds a loud talking VM and they don't have a good reverse DNS or method to look up by IP

```kusto
Resources | where properties.ipConfigurations[0].properties.privateIPAddress == '10.100.100.50'
```
Now this won't work if you have a Network Interface with secondary IPs, but I find that not super common.

#### VMs by OS type
I find this one to be helpful when working with clients to true-up their Hybrid Use Benefits.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

#### Joins
To make queries human readable I generally do a inner join to show the subscription name instead of ID

```kusto
Resources
| where type == 'microsoft.storage/storageaccounts'
| join kind = inner (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubscriptionName=name, subscriptionId) on subscriptionId
| project resourceGroup, name, SubscriptionName
```
[Further Reading on join](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer#inner-join)


To run these you can either search for ***Azure Resource Explorer***, using Cloud Shell, or installing the Az.ResourceGraph and using the Search-AzGraph cmdlet.

I think this tool is very powerful and worth learning. I would start with the [Sample Queries](https://docs.microsoft.com/en-us/azure/governance/resource-graph/samples/starter?tabs=azure-cli) provided by Microsoft to start learning KQL. If you work in a large environment and get asked to generate Azure reports often, the Azure Resource Graph is going to be your new best friend.