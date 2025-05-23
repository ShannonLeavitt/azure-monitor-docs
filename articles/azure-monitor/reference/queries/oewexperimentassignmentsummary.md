---
title: Example log table queries for OEWExperimentAssignmentSummary
description:  Example queries for OEWExperimentAssignmentSummary log table
ms.topic: generated-reference
ms.service: azure-monitor
ms.author: edbaynash
author: EdB-MSFT
ms.date: 04/14/2025

# This file is automatically generated. Changes will be overwritten. Do not change this file directly. 

---

# Queries for the OEWExperimentAssignmentSummary table

For information on using these queries in the Azure portal, see [Log Analytics tutorial](/azure/azure-monitor/logs/log-analytics-tutorial). For the REST API, see [Query](/rest/api/loganalytics/query).


### Variant assignment counts by features  


List the total number of assignments for each variant in feature allocations.  

```query
// Variant assignment counts by features
OEWExperimentAssignmentSummary
| summarize
    IsControlVariant = take_any(IsControlVariant),
    AllocationPercentage = take_any(AllocationPercentage),
    AssignmentEventCount = sum(AssignmentEventCount),
    FirstAssignmentTimestamp = min(FirstAssignmentTimestamp),
    LastAssignmentTimestamp = max(LastAssignmentTimestamp)
    by FeatureName, AllocationId, Variant
| order by FeatureName asc, LatestAssignment desc, Variant asc
```



### Latest scorecard metadata for a given feature  


Query the latest experimentscorecard metadata for a given feature.  

```query
// Latest scorecard metadata for a given feature
// set the feature flag name to query
let QueryFeature = "MyFeatureFlag";
OEWExperimentAssignmentSummary
| where FeatureName == QueryFeature
| summarize LastAssignmentTimestamp=max(LastAssignmentTimestamp), Variants=make_set(Variant, 1000) by AllocationId
| summarize arg_max(LastAssignmentTimestamp, *)
| join kind=inner OEWExperimentScorecards on AllocationId
| summarize arg_max(TimeGenerated, ScorecardId)
| project
    FeatureName, AllocationId, Variants,
    ScorecardId, AnalysisStartTime, AnalysisEndTime, Insights
```



### Latest scorecard results for a given feature  


Query the latest experiment scorecard result for a given feature.  

```query
// Latest scorecard results for a given feature
// set the feature flag name to query
let QueryFeature = "MyFeatureFlag";
OEWExperimentAssignmentSummary
| where FeatureName == QueryFeature
| summarize arg_max(LastAssignmentTimestamp, AllocationId)
| join kind=inner OEWExperimentScorecards on AllocationId
| summarize arg_max(TimeGenerated, ScorecardId)
| join kind=inner OEWExperimentScorecardMetricPairs on ScorecardId
| project
    ScorecardId, MetricId, MetricDisplayName, MetricKind, MetricTags,
    TreatmentVariant, TreatmentCount, TreatmentMetricValue,
    ControlVariant, ControlCount, ControlMetricValue,
    TreatmentEffect, RelativeDifference, PValue, Insights
```

