# Working with JSONPath<a name="about-json-path"></a>

JSONPath is a standardized way to query elements of a JSON object\. JSONPath uses path expressions to navigate elements, nested elements, and arrays in a JSON document\. For more information about JSON, see [Introducing JSON](http://www.json.org/)\.

## Accessing JSON Elements with JSONPath<a name="about-json-path-elements"></a>

Following, you can find how to use JSONPath expressions to access various elements in JSON\-formatted data\. For the examples in this section, assume that the source stream contains a JSON record in the following format\.

```
{
  "customerName":"John Doe",
  "address":
  {
    "streetAddress":
    [
      "number":"123",
      "street":"AnyStreet"
    ],
    "city":"Anytown"
  }
  "orders":
  [
    { "orderId":"23284", "itemName":"Widget", "itemPrice":"33.99" },
    { "orderId":"63122", "itemName":"Gadget", "itemPrice":"22.50" },
    { "orderId":"77284", "itemName":"Sprocket", "itemPrice":"12.00" }
  ]
}
```

### Accessing JSON Elements<a name="about-json-path-firstlevel"></a>

To query an element in JSON data using JSONPath, use the following syntax\. Here, `$` represents the root of the data hierarchy and `elementName` is the name of the element node to query\.

```
$.elementName
```

The following expression queries the `customerName` element in the preceding JSON example\.

```
$.customerName
```

The preceding expression returns the following from the preceding JSON record\.

```
John Doe
```

**Note**  
Path expressions are case\-sensitive\. The expression `$.Name` returns `null` from the preceding JSON example\.

**Note**  
If no element appears at the location where the path expression specifies, the expression returns `null`\. The following expression returns `null` from the preceding JSON example, because there is no matching element\.  

```
$.customerId
```

### Accessing Nested JSON Elements<a name="about-json-path-nested"></a>

To query a nested JSON element, use the following syntax\.

```
$.parentElement.element
```

The following expression queries the `city` element in the preceding JSON example\.

```
$.address.city
```

The preceding expression returns the following from the preceding JSON record\.

```
Anytown
```

You can query further levels of subelements using the following syntax\.

```
$.parentElement.element.subElement
```

The following expression queries the `street` element in the preceding JSON example\.

```
$.address.streetAddress.street
```

The preceding expression returns the following from the preceding JSON record\.

```
AnyStreet
```

### Accessing Arrays<a name="about-json-path-arrays"></a>

Arrays are queried using an array index expression inside square brackets \(`[]`\)\. Currently, the only index expression supported is `0:`, meaning that all the elements in the array are returned\.

The format of the data returned depends on whether the array index expression is the last expression in the path:

+ When the array index is the last expression in the path expression, all of the contents of the array are returned as a single field in a single data row\. 

+ When there is a nested expression after the array index expression, the array is "flattened\." In other words, each element in the array is returned as a separate data row\.

To query the entire contents of an array as a single row, use the following syntax\.

```
$.arrayObject[0:]
```

The following expression queries the entire contents of the `orders` element in the preceding JSON example\. It returns the array contents in a single column in a single row\.

```
$.orders[0:]
```

The preceding expression returns the following from the preceding JSON record\.

```
[{"orderId":"23284","itemName":"Widget","itemPrice":"33.99"},{"orderId":"61322","itemName":"Gadget","itemPrice":"22.50"},{"orderId":"77284","itemName":"Sprocket","itemPrice":"12.00"}]
```

To query the individual elements in an array as separate rows, use the following syntax\.

```
$.arrayObject[0:].element
```

The following expression queries the `orderId` elements in the preceding JSON example, and returns each array element as a separate row\.

```
$.orders[0:].orderId
```

The preceding expression returns the following from the preceding JSON record, with each data item returned as a separate row\.


****  

|  | 
| --- |
|  23284  | 
|  63122  | 
|  77284  | 

**Note**  
If expressions that query nonarray elements are included in a schema that queries individual array elements, the nonarray elements are repeated for each element in the array\. For example, suppose that a schema for the preceding JSON example includes the following expressions:  
$\.customerName
$\.orders\[0:\]\.orderId
In this case, the returned data rows from the sample input stream element resemble the following, with the `name` element repeated for every `orderId` element\.  


****  

|  |  | 
| --- |--- |
|  John Doe  |  23284  | 
|  John Doe  |  63122  | 
|  John Doe  |  77284  | 

**Note**  
The following limitations apply to array expressions in Amazon Kinesis Data Analytics:  
Only one level of dereferencing is supported in an array expression\. The following expression format is not supported\.  

  ```
  $.arrayObject[0:].element[0:].subElement
  ```
Only one array can be flattened in a schema\. Multiple arrays can be referenced—returned as one row containing all of the elements in the array\. However, only one array can have each of its elements returned as individual rows\.  
A schema containing elements in the following format is valid\. This format returns the contents of the second array as a single column, repeated for every element in the first array\.  

  ```
  $.arrayObjectOne[0:].element
  $.arrayObjectTwo[0:]
  ```
A schema containing elements in the following format is not valid\.  

  ```
  $.arrayObjectOne[0:].element
  $.arrayObjectTwo[0:].element
  ```

## Other Considerations<a name="about-json-path-other"></a>

Additional considerations for working with JSONPath are as follows:

+ If no arrays are accessed by individual element in the JSONPath expression, then a single row is created for each JSON record processed\. Every JSONPath expression corresponds to a single column\.

+ When an array is flattened, any missing elements result in a null value being created in the in\-application stream\. 

+ An array is always flattened to at least one row\. If no values would be returned \(that is, the array is empty or none of its elements are queried\), a single row with all null values is returned\.

  The following expression returns records with null values from the preceding JSON example, because there is no matching element at the specified path\.

  ```
  $.orders[0:].itemId
  ```

  The preceding expression returns the following from the preceding JSON example record\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/about-json-path.html)

## Related Topics<a name="about-json-path.Related"></a>

+ [Introducing JSON](http://www.json.org/)