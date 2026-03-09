# Databricks_Professional_exam_preparation-
Databricks_Professional_exam_preparation 


## Data model 
<img width="636" height="358" alt="image" src="https://github.com/user-attachments/assets/d4fe2f5e-d665-4997-99f5-497f593c0ef0" />


## Config Auto Loader

When using Auto Loader, you can configure several options for your stream to ensure reliable data ingestion:



Setting Maximum Bytes per Trigger

If you're ingesting large files that cause long micro-batch processing times or memory issues, you can use the cloudFiles.maxBytesPerTrigger option to control the maximum amount of data processed in each micro-batch. This improves stability and keeps batch durations more predictable. For example, to limit each micro-batch to 1 GB of data, you can configure your stream as follows:

spark.readStream
     .format("cloudFiles")
     .option("cloudFiles.format", <source_format>)
     .option("cloudFiles.maxBytesPerTrigger", "1g")
     .load("/path/to/files")


Handling Bad Records

When working with JSON or CSV files, you can use the badRecordsPath option to capture and isolate invalid records in a separate location for further review. Records with malformed syntax (e.g., missing brackets, extra commas) or schema mismatches (e.g., data type errors, missing fields) are redirected to the specified path.

spark.readStream
     .format("cloudFiles")
     .option("cloudFiles.format", "json")
     .option("badRecordsPath", "/path/to/quarantine")
     .schema("id int, value double")
     .load("/path/to/files")


Files Filters:

To filter input files based on a specific pattern, such as *.png, you can use the pathGlobFilter option. For example:

spark.readStream
     .format("cloudFiles")
     .option("cloudFiles.format", "binaryFile")
     .option("pathGlobfilter", "*.png")
     .load("/path/to/files")


Schema Evolution:

Auto Loader detects the addition of new columns in input files during processing. To control how this schema change is handled, you can set cloudFiles.schemaEvolutionMode option:

spark.readStream
     .format("cloudFiles")
     .option("cloudFiles.format", <source_format>)
     .option("cloudFiles.schemaEvolutionMode", <mode>)
     .load("/path/to/files")


The supported schema evolution modes include:


The default mode is addNewColumns, so when Auto Loader detects a new column, the stream stops with an UnknownFieldException. Before your stream throws this error, Auto Loader updates the schema location with the latest schema by merging new columns to the end of the schema. The next run of the stream executes successfully with the updated schema.

Note that the addNewColumns mode is the default when a schema is not provided, but none is the default when you provide a schema. addNewColumns is not allowed when the schema of the stream is provided.
