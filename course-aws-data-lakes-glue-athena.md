
# Data Lakes

Architectural approach for a centralized enterprise data repository stored on Amazon S3:

- Stores all structured, semi-structured, unstructured, and binary data at unlimited scale
- Holds curated and raw data
- Uses AWS data analytics tools for analytics
- Increases pace of innovation by extracting insights from data
- Enables more organizational agility
- Reduces cost and delivers results with predictive analytics and ML

# Glue

A fully managed ETL service. Customers point glue to data stored in a data lake and other data stores where Glue discovers the data and stored metadata such as table definitions and schemas in the Glue catalog.


## Glue Data Catalog - central “map” of data

A unified metadata repo across relational databases, RDS, Red Shift lake house and S3.

- provides a single view into data no matter where it is stored
- automatically classifies data in a central searchable list
- tracks data evolution using schema versioning
- query the data using Athena or Redshift Spectrum

You can use SQL to query data in ANY data store including a data lake.

## Glue Crawlers - builds the catalog

Automatically build and sync a customers Data Catalog. It scans data sources such as RDS, Redshift, DynamoDB, and data lakes and constructs the data catalog using classifiers.

## Example with Athena


- streaming data ingested into first S3
- Glue crawler inspects the data in the first S3 and performans extraction, transforms, creates partitions and loads this data into the second S3
- Creates the Data Catlog which contains metadata about the data in the second bucket
- Athena runs queries using the Data Catalog

# Data Exchange

- helps engineers subscribe to third-party data to help reduce integration issues.

# Athena

An interactive query service to analyze data in S3 using ANSI SQL.


  

# Lake Formation

**Features**

- fully managed data lake
- powerful data cleansing
- automated data ingestion
- centralized access control
- customers can create workflows
- integrated with Glue, Athena, Redshift Spectrum, QuickSight (Data Visualization), EMR

  


- It can take months for a customer to set up a Data Lake on prem

  

AWS Lake Formation simplifies the process of building secure data lakes and automates steps
