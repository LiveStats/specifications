# Data Gathering

The data gathering service is responsible for sourcing data from the internet and aggregating all the data together.
Examples of services include an InfluxDB gatherer or other timeseries data source.

This service will expose 2 or 3 endpoints over http,
1. JSON endpoint on `/` containing metadata
2. Web Socket on `/live` with a live stream of data
3. JSON endpoint on `/history` allowing you to search stored historic data, if it is available

**NOTE:** `/history` is not required if historic data cannot be sourced

This services environment variables must be prefixed with `LSDG_`

This service does not require any environment variables by default

## Endpoints

### `/` - Metadata

Metadata for this service will be made available on the `/` endpoint and will respond in JSON form.
Metadata will require the following attributes:
* Title 
* Labels
* Description
* Data Location
* Browsable History?
* Data Source
* Start Timestamp

Example:
```json
{
  "title": "LiveStats vs Compeditors",
  "labels": {
    "ls": "LiveStats",
    "ft": "FlareTV"
  },
  "description": "LiveStats has alot of compeditors (we think) so we are comparing them",
  "data_location": "http://livestats.hexf.me/stats/competition",
  "history": false,
  "data_source": "YouTube",
  "start": 1579760188
}
```

#### Title

Title defined the dataset with a title, that is displayed on the graph

```
Key: title
Type: String
```

#### Labels

Labels is an object representing the relation between the key used in data packets (could be numerical or a string value) and the name of the label

```
Key: labels
Type: Object
```

#### Description

A description defining the data, and what should eventually make its way to the render service

```
Key: description
Type: String
```

#### Data Location

A URL pointing to a location where the data can be accessed (Live and Historic (only if specified))

```
Key: data_location
Type: String
```

#### History

A boolean specifiying if the History endpoint is available and contains data

```
Key: history
Type: boolean
```

#### Data Source

A description of where the data has been grabbed from.

```
Key: data_source
Type: String
```

#### Start Timestamp

A timestamp from UNIX Epoch, representing when the first datapoint was published, or how far back the history goes

```
Key: start
Type: number (Timestamp from UNIX Epoch)
```

### `/live` - Live Data

Live data for this service will be made available on the `/live` endpoint and will send JSON messages.
Live data packets will require the following attributes:
* Data
* Timestamp

Example Packet:
```json
{
  "data": {
    "ls": 2,
    "ft": 2700000
  },
  "timestamp": 1579760188
}
```

#### Data

Data is the data that has been collected. Data is an object using the keys defined under `metadata.labels` with values of the data values.

```
Key: data
Type: Object
```

#### Timestamp

Timestamp is a timestamp, with a format of UNIX Epoch Seconds

```
Key: timestamp
Type: number (Timestamp from UNIX Epoch)
```


### `/history` - History

History for this service will be made available on the `/history` endpoint.
The data here will be pageinated limited to 50 datapoints per page.

Altering the request body can specify a range of data that will be returned.
The request body for this endpoint will be JSON, and has only one parameter, `time`.
The service should return the first 50 data points in an array, in the format used by Live Data, starting at either the time specified or if none is specified, the current time.

Example:
*Request*
```json
{
  "time": 1579760190
}
```
*Response*
```json
[
  {
    "data": {
      "ls": 2,
      "ft": 2700000
    },
    "timestamp": 1579760190
  },
  {
    "data": {
      "ls": 2,
      "ft": 2700000
    },
    "timestamp": 1579760189
  },
  {
    "data": {
      "ls": 2,
      "ft": 2700000
    },
    "timestamp": 1579760188
  }
]
```
