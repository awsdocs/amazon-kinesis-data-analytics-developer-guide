# Step 3: Examine the Results<a name="examine-results-with-exp"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

When you run the SQL code for this [example](app-anomaly-detection-with-explanation.md), you first see rows with an anomaly score equal to zero\. This happens during the initial learning phase\. Then you get results similar to the following:

```
ROWTIME SYSTOLIC DIASTOLIC BLOODPRESSURELEVEL ANOMALY_SCORE ANOMALY_EXPLANATION
27:49.0	101      66        NORMAL             0.711460417   {"Systolic":{"DIRECTION":"LOW","STRENGTH":"0.0922","ATTRIBUTION_SCORE":"0.3792"},"Diastolic":{"DIRECTION":"HIGH","STRENGTH":"0.0210","ATTRIBUTION_SCORE":"0.3323"}}
27:50.0	144      123       HIGH               3.855851061   {"Systolic":{"DIRECTION":"HIGH","STRENGTH":"0.8567","ATTRIBUTION_SCORE":"1.7447"},"Diastolic":{"DIRECTION":"HIGH","STRENGTH":"7.0982","ATTRIBUTION_SCORE":"2.1111"}}
27:50.0	113      69        NORMAL             0.740069409   {"Systolic":{"DIRECTION":"LOW","STRENGTH":"0.0549","ATTRIBUTION_SCORE":"0.3750"},"Diastolic":{"DIRECTION":"LOW","STRENGTH":"0.0394","ATTRIBUTION_SCORE":"0.3650"}}
27:50.0	105      64        NORMAL             0.739644157   {"Systolic":{"DIRECTION":"HIGH","STRENGTH":"0.0245","ATTRIBUTION_SCORE":"0.3667"},"Diastolic":{"DIRECTION":"LOW","STRENGTH":"0.0524","ATTRIBUTION_SCORE":"0.3729"}}
27:50.0	100      65        NORMAL             0.736993425   {"Systolic":{"DIRECTION":"HIGH","STRENGTH":"0.0203","ATTRIBUTION_SCORE":"0.3516"},"Diastolic":{"DIRECTION":"LOW","STRENGTH":"0.0454","ATTRIBUTION_SCORE":"0.3854"}}
27:50.0	108      69        NORMAL             0.733767202   {"Systolic":{"DIRECTION":"LOW","STRENGTH":"0.0974","ATTRIBUTION_SCORE":"0.3961"},"Diastolic":{"DIRECTION":"LOW","STRENGTH":"0.0189","ATTRIBUTION_SCORE":"0.3377"}}
```
+ The algorithm in the `RANDOM_CUT_FOREST_WITH_EXPLANATION` function sees that the `Systolic` and `Diastolic` columns are numeric, and uses them as input\.
+ The `BloodPressureLevel` column has text data, and is therefore not taken into account by the algorithm\. This column is simply a visual aide to help you quickly spot the normal, high, and low blood pressure levels in this example\.
+ In the `ANOMALY_SCORE` column, records with higher scores are more anomalous\. The second record in this sample set of results is the most anomalous, with an anomaly score of 3\.855851061\.
+ To understand the extent to which each of the numeric columns taken into account by the algorithm contributes to the anomaly score, consult the JSON field named `ATTRIBUTION_SCORE` in the `ANOMALY_SCORE` column\. In the case of the second row in this set of sample results, the `Systolic` and `Diastolic` columns contribute to the anomaly in the ratio of 1\.7447:2\.1111\. In other words, 45 percent of the explanation for the anomaly score is attributable to the systolic value, and the remaining attribution is due to the diastolic value\.
+ To determine the direction in which the point represented by the second row in this sample is anomalous, consult the JSON field named `DIRECTION`\. Both the diastolic and systolic values are marked as `HIGH` in this case\. To determine the confidence with which these directions are correct, consult the JSON field named `STRENGTH`\. In this example, the algorithm is more confident that the diastolic value is high\. Indeed, the normal value for the diastolic reading is usually 60–80, and 123 is much higher than expected\. 