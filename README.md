# lobjical
Object representation of logical operators, together with client SDKs

## Purpose
This object structure allows the definition of logical comparison operators within content that can be generated and stored by production systems. For example, a user can create a complex search based on many properties which is then stored for later processing (by one of the SDKs here).

## Structure
The following JSON defines a block of lobjical markup:

```
{
	"AND": [
		{
			"NOT": [
				{
					"eq": {
						"property": "enabled",
						"value": true
					}
				}
			]
		},
		{
			"contains": {
				"property": "details.name",
				"values": ["bloggs", "smith", "brown"]
			}
		},
		{
			"gt": {
				"property": "#orders",
				"value": 0
			}
		},
		{
			"OR": [
				{
					"lte" : {
						"property": "orders.*.quantity",
						"value": 500
					}
				},
				{
					"gte" : {
						"property": "orders.+.price",
						"value": 0.05
					}
				}
		   ]
        },
		{
			"eq": {
				"property": "details.contact.zip",
				"values": "$zips"
			}
		}
	]
}
```

When run through the parser (tbc) this will translate as:
- `my.first.property` must not equal "foo" AND
- `my.second.property` must contain "bar", "pub" or "restaurant" AND
- The `items` array must:
  - Have __all__ items with a `quantity` value less than one OR
  - Have __one or more__ items with a value >= 5 OR
- `third` must equal the variable `var_one`, `$var_two` or the string "string" (these variables must be accessible in the scope of the parser - see further on).

### Available comparisons
- `eq` Equals. Does not change type (e.g. 1 != "1")
- `lt` Less than. Converts strings to numbers, if possible.
- `gt` Greater than. Converts strings to numbers, if possible.
- `lte` Less than or equal
- `gte` Greater than or equal
- `contains` String appears at least once.
- `rx` Regular expression. Must be defined as a string in JSON, contained in forward slashes (e.g. `"/\\bword\\b/"` - note the double backslashes).
- `exists` Property exists.
- `type` Type of the property (one of `string`, `number`, `array`, `object`, `date`, `boolean`, `null`)
- `function` A function passed through to the parser in the "functions" argument. Will be called with `functions[function_name]($vals)` where `$v` may be any type of value. `null` returns from the function count as false.

### Property modifiers
- `+` One or more of an array or object.
- `*` All items of an array or object.
- `#` The count of an array.

### Compound condition sets
The above can be nested and sit next to compound conditions, allowing for:
- `AND` All of the child conditions must be met
- `OR` One of the child conditions must be met
- `XOR` Only one of the child conditions are met
- `NOT` None of the child conditions are met

## Code libraries
Libraries are provided in PHP and JavaScript. Coming soon
