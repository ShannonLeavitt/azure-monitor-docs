---
title: Example log table queries for AZMSHybridConnectionsEvents
description:  Example queries for AZMSHybridConnectionsEvents log table
ms.topic: generated-reference
ms.service: azure-monitor
ms.author: edbaynash
author: EdB-MSFT
ms.date: 04/14/2025

# This file is automatically generated. Changes will be overwritten. Do not change this file directly. 

---

# Queries for the AZMSHybridConnectionsEvents table

For information on using these queries in the Azure portal, see [Log Analytics tutorial](/azure/azure-monitor/logs/log-analytics-tutorial). For the REST API, see [Query](/rest/api/loganalytics/query).


### Publish HTTP send data for hybrid connection  


Publish details for send events on a hybrid connection.  

```query
//Endpoint needs to be replaced with client specific endpoint.
AZMSHybridConnectionsEvents
| extend NamespaceName = tostring(split(_ResourceId, "/")[8])
| where OperationName == "Microsoft.Relay/HybridConnections/SenderSentHttpRequest"
| where Endpoint contains "shamavijay-relay-hybconn"
| project NamespaceName, TaskName, Message, OperationName
| summarize by NamespaceName, TaskName
```

