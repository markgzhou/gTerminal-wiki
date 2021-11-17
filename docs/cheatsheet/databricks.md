# Databricks CLI

## Setup

### Requirements

- Python 3.6 and above

### Install the CLI

     pip install databricks-cli
     pip3 install databricks-cli  # If you use pip3

### Update the CLI

    pip install databricks-cli
    pip3 install databricks-cli  # If you use pip3

### Config Authentication

Here you need to have your
Databricks [personal access token](https://docs.databricks.com/dev-tools/api/latest/authentication.html) ready.

Run command  `databricks configure --token` <br />
It will ask you to fill in the Host, you need to fill in your Databricks portal host URL. If you are using Azure China,
it looks like `https://adb-****.databricks.azure.cn`

## Cheat Sheet

### DBFS command

    databricks fs -h
    Usage: databricks fs [OPTIONS] COMMAND [ARGS]...

    cat        Show the contents of a file.
    configure  Configures host and authentication info for the CLI.
    cp         Copy files to and from DBFS.
    ls         List files in DBFS.
    mkdirs     Make directories in DBFS.
    mv         Moves a file between two DBFS paths.
    rm         Remove files from dbfs.

```shell
databricks fs cat dbfs:/tmp/my-file.txt                # Show file content
databricks fs cp dbfs:/tmp/file_a.txt dbfs:/dir_a/file_a.txt --overwrite
databricks fs ls dbfs:/tmp --absolute -l               # List information
databricks fs mkdirs dbfs:/tmp/new-dir                 # Make new directory
databricks fs mv dbfs:/a/a.txt dbfs:/b/b.txt           # Move file
databricks fs rm dbfs:/tmp/new-dir/my-file.txt         # Delete file
```

### Cluster command

#### Create a cluster

```shell
databricks clusters create --json-file create-cluster.json
```

create-cluster.json:

```json
{
  "cluster_name": "my-cluster",
  "spark_version": "7.3.x-scala2.12",
  "node_type_id": "Standard_D3_v2",
  "spark_conf": {
    "spark.speculation": true
  },
  "num_workers": 3
}
```

<br />

#### Get the cluster information

```shell
databricks clusters list  # list all clusters
databricks clusters list --output JSON | jq .  # list all clusters in Json format
databricks clusters get --cluster-id 1234-567890-batch1234
databricks clusters get --cluster-name my-cluster
```

#### Start and Restart a cluster

```shell
databricks clusters start --cluster-id 1234-567890-batch1234
databricks clusters restart --cluster-id 1234-567890-batch1234
```

#### Delete a cluster

```shell
databricks clusters delete --cluster-id 1234-567890-batch1234
```

### Job Command

#### Create a job

```shell
databricks jobs create --json-file create-job.json
```

create-job.json

```json
{
  "name": "my-job",
  "existing_cluster_id": "1234-567890-reef123",
  "notebook_task": {
    "notebook_path": "/Users/someone@example.com/My Notebook"
  },
  "email_notifications": {
    "on_success": [
      "someone@example.com"
    ],
    "on_failure": [
      "someone@example.com"
    ]
  }
}
```

Terminal will return `{ "job_id": 246 }`

```shell
# You can also create a duplicated job

SETTINGS_JSON=$(databricks jobs get --job-id 246 | jq .settings)
databricks jobs create --json "$SETTINGS_JSON"
```

#### Run a job

```shell
databricks jobs run-now --job-id 246
```

#### Delete a job

```shell
databricks job delete --job-id 246
```

#### Get job information

```shell
databricks jobs list                     # List all jobs
databricks jobs get --job-id 246         # Get job information
```

### Scope and Secret

#### Scopes

```shell
databricks secrets list-scopes          # List all secret scopes
databricks secrets create-scope --scope <scope-name>   # Create a scope
databricks secrets delete-scope --scope <scope-name>   # Delete a scope
```

#### Secrets

```shell
databricks secrets put --scope <scope-name> --key <key-name>  # Create a secret
databricks secrets list --scope <scope-name>     # List all secrets in the scope
databricks secrets delete --scope <scope-name> --key <key-name>   # Delete a secret in the scope
```

## Databricks Connect

```shell
pip uninstall pyspark

pip install -U "databricks-connect==9.1.*"  # or X.Y.* to match your cluster version.
databricks-connect configure
databricks-connect test
```

### with Jupyter notebook

To enable spark session

```shell
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
```

To enable `%sql` shorthand for running SQL queries

```shell
from IPython.core.magic import line_magic, line_cell_magic, Magics, magics_class

@magics_class
class DatabricksConnectMagics(Magics):

   @line_cell_magic
   def sql(self, line, cell=None):
       if cell and line:
           raise ValueError("Line must be empty for cell magic", line)
       try:
           from autovizwidget.widget.utils import display_dataframe
       except ImportError:
           print("Please run `pip install autovizwidget` to enable the visualization widget.")
           display_dataframe = lambda x: x
       return display_dataframe(self.get_spark().sql(cell or line).toPandas())

   def get_spark(self):
       user_ns = get_ipython().user_ns
       if "spark" in user_ns:
           return user_ns["spark"]
       else:
           from pyspark.sql import SparkSession
           user_ns["spark"] = SparkSession.builder.getOrCreate()
           return user_ns["spark"]

ip = get_ipython()
ip.register_magics(DatabricksConnectMagics)
```


