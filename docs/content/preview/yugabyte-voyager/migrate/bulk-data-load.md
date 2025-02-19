---
title: Steps for a bulk data load
headerTitle: Bulk data load from files
linkTitle: Bulk data load
headcontent: Import data from flat files using YugabyteDB Voyager
description: Run the steps to ensure a successful offline migration using YugabyteDB Voyager.
menu:
  preview_yugabyte-voyager:
    identifier: bulk-data-load
    parent: migration-types
    weight: 104
type: docs
---

This page describes the steps to import data in CSV or TEXT format from flat files on your local disk or in cloud storage, including AWS S3, GCS buckets, and Azure Blob.

## Prerequisite

* Before you perform a bulk load, in your target YugabyteDB database, create the schema of the tables into which the data in the flat files will be imported.

## Import data files from the local disk

If your data files are in CSV or TEXT format and are present on the local disk, and you have already created a schema in your target YugabyteDB database, you can use the following `yb-voyager import data file` command to load the data into the target table directly from the flat file(s).

```sh
# Replace the argument values with those applicable to your migration.
yb-voyager import data file --export-dir <EXPORT_DIR> \
       --target-db-host <TARGET_DB_HOST> \
       --target-db-user <TARGET_DB_USER> \
       --target-db-password <TARGET_DB_PASSWORD> \ # Enclose the password in single quotes if it contains special characters.
       --target-db-name <TARGET_DB_NAME> \
       --target-db-schema <TARGET_DB_SCHEMA> \
       --data-dir </path/to/files/dir/> \
       --file-table-map <filename1:table1,filename2:table2> \
       # Optional arguments as per data format
       --delimiter <DELIMITER> \
       --escape-char <ESCAPE_CHAR> \
       --quote-char <QUOTE_CHAR> \
       --has-header \
       --null-string "<NULL_STRING>"
```

Refer to [import data file](../../reference/yb-voyager-cli/#import-data-file) for details about the arguments.

### Incremental data loading

The `import data file` command also supports importing multiple files to the same table by providing the [--file-table-map](../../reference/yb-voyager-cli/#file-table-map) flag `<fileName>:<tableName>` entry for each file, or by passing a glob expression in place of the file name.

For example, `fileName1:tableName,fileName2:tableName` OR `fileName*:tableName`.

## Import data files from cloud storage

Using the `import data file` command, you can import data from files in cloud storage, including AWS S3, GCS buckets, and Azure blob. Importing from cloud storage reduces local disk requirements for storing the data while it is imported to your YugabyteDB database.

{{< tabpane text=true >}}

  {{% tab header="AWS S3" %}}

To import data from AWS S3, provide the S3 bucket URI in the `data-dir` flag as follows:

```sh
yb-voyager import data file .... \
        --data-dir s3://voyager-data
```

The authentication mechanism for accessing an S3 bucket using yb-voyager is the same as that used by the AWS CLI. Refer to [Configure the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) for additional details on setting up your S3 bucket.

  {{% /tab %}}

  {{% tab header="GCS buckets" %}}
To import data from GCS buckets, provide the GCS bucket URI in the `data-dir` flag as follows:

```sh
yb-voyager import data file .... \
        --data-dir gs://voyager-data
```

The authentication mechanism for accessing a GCS bucket using yb-voyager is the Application Default Credentials (ADC) strategy for GCS. Refer to [Set up Application Default Credentials](https://cloud.google.com/docs/authentication/provide-credentials-adc) for additional details on setting up your GCS buckets.
  {{% /tab %}}

  {{% tab header="Azure blob" %}}
To import data from Azure blob storage containers, provide the Azure container URI in the `data-dir` flag as follows:

```sh
yb-voyager import data file .... \
        --data-dir https://<account_name>.blob.core.windows.net/<container_name>...
```

The authentication mechanism for accessing blobs using yb-voyager is the same as that used by the Azure CLI. The Azure storage account used for the import should at least have the [Storage Blob Data Reader](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) role assigned to it.
Refer to [Sign in with Azure CLI](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli) for additional details on setting up your Azure blobs.
  {{% /tab %}}

{{< /tabpane >}}