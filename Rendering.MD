# Rendering Service

The rendering service is responsible for returning a webpage that can be displayed by the streaming service
The rendering service should have a template mechanism built in, allowing easy customization of the look of the page.
It should also serve the template on `/`.
In a typical setup, you would have 1 rendering service for every data gathering service to visualize the software.

## Templating

When you hit the `/` endpoint on the templating service your template file should be returned.
The client should load some sort of library that will resolve all the templating tags and start rendering the service.
However, there are some special tags like `<ls-env />` which should be replaced server side. 

The template should beable to resolve the following tags:
* ls
* ls-metadata
* ls-graph
* ls-value

Optionally the following should also be included:
* ls-env

All examples on this page, are refering to the example data provided on the [Data Gathering specification](https://github.com/LiveStats/specifications/blob/master/Data-Gathering.MD)

### `ls`

ls is a required tag, it contains the metadata endpoint that should be requested to grab the remainder of the information

```
Key: ls
Type: String (URL Endpoint of metadata)
```

Example: 
```html
<ls>https://livesta.ts/stats/lsvsft</ls>
```

### `ls-metadata`

ls-metadata will replace the inner text with the value selected from a JSONPath where the root element is the metadata, retreived from the url specified in the `ls` tag

```
Key: ls-metadata
Type: String (JSONPath)
```

Example:
```html
<ls-metadata>$.title</ls-metadata>
<!-- Expected Output: LiveStats vs Compeditors -->
```

### `ls-graph`

ls-graph will replace the contents of it, with a chart of the data. The body of the tag is the chart type to use, as specified in the templating library used.

```
Key: ls-graph
Type: String (Chart Type)
```

Example:
```html
<ls-graph>line</ls-graph>
<!-- Expected Output: (A line graph displaying data) -->
```

### `ls-value`

ls-value will output the value of the live data as plain text. The selector can be changed with the body of the tag, containing the data key to display

```
Key: ls-value
Type: String (Data Key)
```

Example:
```html
<ls-value>ls</ls-value>
<!-- Expected Output: 2 -->
```

### `ls-env`

ls-env is an optional tag which will replace its innerText with the value of the environement variable contained with in the body when it is first loaded.
ls-env could be implemented in 2 ways, either serving the environment variables from the server and updating the innerText in the client, or doing the replacing server side.
ls-env should only select environment vairables prefixed with `LST_` (Standing for LiveStats Template)

```
Key: ls-env
Type: String (Environment Variable Name)
```

Example:

**env**

```env
TEXT="This data should not be displayed"
LST_TEXT="Hello, World!"
```

**html**

```html
<ls-env>TEXT</ls-env>
<!-- Expected Output: Hello, World! -->
```
