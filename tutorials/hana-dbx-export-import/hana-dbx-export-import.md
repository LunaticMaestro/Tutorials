---
title: Export and Import Data and Schema with SAP HANA Database Explorer
description: Use wizards or SQL statements to export and import data and schema using CSV, Apache Parquet, or binary formats.
auto_validation: true
time: 10
tags: [ tutorial>beginner, software-product-function>sap-hana-cloud\,-sap-hana-database, software-product>sap-hana, software-product>sap-hana\,-express-edition]
primary_tag: software-product>sap-hana-cloud
---

## Prerequisites
- An SAP HANA database such as SAP HANA Cloud trial or the SAP HANA, express edition that includes the SAP HANA database explorer
- Google Cloud or Microsoft Azure accounts will be needed for optional steps in this tutorial.
- You have completed the first 3 tutorials in this group.

## Details
### You will learn
- How to export and import data using the export and import data wizards, SQL statements export into and import from, and the download option in the SQL console results tab
- How to export and import schema objects using export and import catalog wizards and the SQL statements export and import
- How to use cloud storage providers as a target when exporting or importing

The following steps will demonstrate a few ways to export and import data such as the contents of tables or views as well how to export and import database schema or catalog objects.  

>A few differences between exporting and importing data and importing and exporting catalog objects are:  
>
>    - Data export and import works with one table or view
>    - Catalog export or import works with multiple objects at one time
>    - Catalog export or import includes the SQL to recreate the object
>    - Catalog export or import can include additional objects such as functions and procedures


---

[ACCORDION-BEGIN [Step 1: ](Export and import data)]

The following tables list the different options available in the SAP HANA database explorer to export and import data from a single table or view.

Methods to export tables or views

| Method  | Version       | Target                 | Format(s)      |
| ------- | -------------|------------------------| ----------------|
| Export from SQL Console | All | local computer         | CSV      |
| [Export data wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/97e8ec0306eb4a12a4fd72de8bdd6a62.html)   | SAP HANA Cloud, HANA database  | data lake Files, S3, Azure, GCS,  Alibaba OSS | CSV, Parquet    |
| [Export into statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/6a6f59bbfbb64ade84d83d7f87789753.html)  | SAP HANA Cloud, HANA database  | data lake Files, S3, Azure, GCS, Alibaba OSS | CSV, Parquet, JSON    |  
| [Export into statement](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/latest/en-US/6a6f59bbfbb64ade84d83d7f87789753.html)  | SAP HANA on-premise  | SAP HANA file system    | CSV |

Methods to import into tables

| Method  | Version       | Source          | Format(s)       | Notes |
| ------- | -------------|------------------------| ----------------| ------------|
| [Import data wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/ee0e1389fde345fa8ccf937f19c99c30.html)   | All    | local computer         | CSV             | 1 GB max, 2 MB per row in SAP HANA Cloud, HANA database; 200 MB max SAP HANA on-premise |
| [Import data wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/ee0e1389fde345fa8ccf937f19c99c30.html)   | SAP HANA Cloud, HANA database    | data lake Files, S3, Azure, GCS, Alibaba OSS | CSV, Parquet, JSON    | |
| [Import data wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/ee0e1389fde345fa8ccf937f19c99c30.html)    | SAP HANA Cloud, HANA database   | data lake Files, local computer, S3, Azure, GCS, Alibaba OSS   | `ESRI shapefiles` | An example of importing an `ESRI shapefile` can be found in [Try Out Multi-Model Functionality with the SAP HANA Database Explorer](hana-dbx-multi-model) tutorial. |
| [Import data wizard](https://help.sap.com/viewer/e8d0ddfb84094942a9f90288cd6c05d3/latest/en-US/ee0e1389fde345fa8ccf937f19c99c30.html)   | SAP HANA on-premise    | SAP HANA file system         | CSV             | Target table can be created |
| [Import from statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20f712e175191014907393741fadcb97.html) | SAP HANA Cloud, HANA database | data lake Files, S3, Azure, GCS, Alibaba OSS | CSV, Parquet, JSON    | |
| [Import from statement](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/latest/en-US/20f712e175191014907393741fadcb97.html) | SAP HANA on-premise  | SAP HANA file system    | CSV |  |
| [Insert into table name select from statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20f7f70975191014a76da70c9181720e.html) | All  | local or remote tables  | select statement |  |

The following steps will attempt to demonstrate an export and import of data from the maintenance table using the download option from the SQL Console and the import data wizard.

1. Enter the SQL statement below.

    ```SQL
    SELECT * FROM HOTEL.MAINTENANCE;
    ```

    Left-click on the download toolbar item.

    ![Download](download.png)

    Choose **Download**.

    !![Download options](downloadOptions1.png)

    >Note, there is a setting that controls the number of results displayed which may need to be adjusted for tables with larger results.

    >![Maximum rows to display setting](maxRows.png)

2. Enter the SQL statement below to delete the rows in the table.  They will be added back in the next sub-step.

    ```SQL
    DELETE FROM HOTEL.MAINTENANCE;
    ```

3. Right-click on the maintenance table and choose **Import Data**.  

    ![Open Import Data Wizard](importDataWizard.png)

    Browse to the previously downloaded CSV file and complete the wizard.

    ![Choose file to import](importDataWizard2.png)

    Note that header row in the `data.csv` file is used to set the initial values of the source column to database column mappings.

    ![Choose file to import](importDataWizard4.png)

    After completing the wizard, the contents of the maintenance table should now be the same as it was before the previously executed delete statement.

4. With SAP HANA, express edition, the following statements can be executed to export and import from a directory on the SAP HANA file system assuming that the directory exists and the user `hxeadm` has permission to access it.

    ```SQL
    EXPORT INTO '/tmp/export/maintenance.csv' FROM HOTEL.MAINTENANCE WITH COLUMN LIST IN FIRST ROW;
    DELETE FROM HOTEL.MAINTENANCE;
    ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'system') set ('import_export', 'csv_import_path_filter') = '/tmp/export' WITH RECONFIGURE;
    IMPORT FROM CSV FILE '/tmp/export/maintenance.csv' INTO HOTEL.MAINTENANCE WITH COLUMN LIST IN FIRST ROW ERROR LOG 'error_log.txt' FAIL ON INVALID DATA;
    ```

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 2: ](Use cloud storage services for export and import (optional))]

The following steps are for illustrative purposes only and are not meant to be followed. Complete steps for working with cloud storage services are provided in steps 3, 4, 6, and 7.

1. With SAP HANA Cloud, an export data wizard is available.  

    ![Export Data Wizard](exportDataWizard.png)

    It can be used to export data to cloud storage providers such as SAP HANA Cloud, data lake Files, Amazon S3, Microsoft Azure, Google Cloud Storage, and Alibaba Cloud OSS.  

    >Additionally, the SAP BTP offers an [object store](https://help.sap.com/viewer/product/ObjectStore/Cloud/en-US).  

    ![available cloud storage providers](available-cloud-storage-providers.png)

    The screenshot below shows the data lake Files target being used and in the bottom left, you can see the result of the export, the maintenance.csv file in the HANA DL File Container.

    ![Export Data Wizard](exportDataWizard2.png)

    The wizard makes use of the export into statement.  An example is shown below:

    ```SQL
    EXPORT INTO CSV FILE
        'hdlfs://1234-567-890-1234-56789.files.hdl.prod-us10.hanacloud.ondemand.com/maintenance.csv'
    FROM HOTEL.MAINTENANCE
    WITH
        CREDENTIAL 'DBADMINCREDENTIAL'
        COLUMN LIST IN FIRST ROW;
    ```

2. The import data wizard provides a corresponding option to import from cloud storage providers.

    ![Export Data Wizard](importDataWizard3.png)

    The wizard makes use of the import from statement.  An example is shown below:

    ```SQL
    --DELETE FROM HOTEL.MAINTENANCE;
    IMPORT FROM PARQUET FILE 'azure://danstestsa:sp=racwdl&st=2021-01-09T13:00:46Z&se=2021-01-10T13:00:46Z&sv=2019-12-12&sr=c&sig=TP%2BVYhcvSPDc4DZxcls6vN%2BCLHDNagedbei2IuEZsWU%3D@myblobcontainer/maintenance.parquet' INTO HOTEL.MAINTENANCE WITH FAIL ON INVALID DATA;
    ```

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 3: ](Use data lake Files for export and import (optional))]

The following steps walk through the process of exporting to and importing data using data lake Files with a SAP HANA Cloud, SAP HANA database. Note that a licensed SAP HANA data lake instance (non trial / non free-tier) is needed for this step.

1. Complete steps 3 and 4 in the [Getting Started with Data Lake Files HDLFSCLI](data-lake-file-containers-hdlfscli) tutorial to configure the trust setup of the data lake Files container.

2. Add the data lake Files container to the SAP HANA database explorer.

    ![Add Connection](addConnection.png)

    The REST API endpoint can be copied from the instances actions menu in SAP HANA Cloud Central.

    ![Add data lake Files](add-data-lake-files.png)

    After the data lake Files container has been added, files can be uploaded, viewed, or deleted.

    ![File container added](file-container-added.png)

3. Create a database credential for the data lake Files container.  Further details are described at [Importing and Exporting with SAP HANA Cloud Data Lake Files Storage](https://help.sap.com/docs/HANA_CLOUD_DATABASE/f9c5015e72e04fffa14d7d4f7267d897/462c861413b043bd93b9e8e838249b6e.html).

    ```SQL
    SELECT * FROM PSES;
    CREATE PSE HTTPS;
    SELECT * FROM PSES;

    SELECT * FROM CERTIFICATES;
    --cert from https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem and is the CA for HANA Cloud
    CREATE CERTIFICATE FROM '-----BEGIN CERTIFICATE-----MIIDrzCCApegAwIBAgIQCDvgVpBCRrGhdWrJWZHHSjANBgkqhkiG9w0BAQUFADBh
    MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
    d3cuZGlnaWNlcnQuY29tMSAwHgYDVQQDExdEaWdpQ2VydCBHbG9iYWwgUm9vdCBD
    QTAeFw0wNjExMTAwMDAwMDBaFw0zMTExMTAwMDAwMDBaMGExCzAJBgNVBAYTAlVT
    MRUwEwYDVQQKEwxEaWdpQ2VydCBJbmMxGTAXBgNVBAsTEHd3dy5kaWdpY2VydC5j
    b20xIDAeBgNVBAMTF0RpZ2lDZXJ0IEdsb2JhbCBSb290IENBMIIBIjANBgkqhkiG
    9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4jvhEXLeqKTTo1eqUKKPC3eQyaKl7hLOllsB
    CSDMAZOnTjC3U/dDxGkAV53ijSLdhwZAAIEJzs4bg7/fzTtxRuLWZscFs3YnFo97
    nh6Vfe63SKMI2tavegw5BmV/Sl0fvBf4q77uKNd0f3p4mVmFaG5cIzJLv07A6Fpt
    43C/dxC//AH2hdmoRBBYMql1GNXRor5H4idq9Joz+EkIYIvUX7Q6hL+hqkpMfT7P
    T19sdl6gSzeRntwi5m3OFBqOasv+zbMUZBfHWymeMr/y7vrTC0LUq7dBMtoM1O/4
    gdW7jVg/tRvoSSiicNoxBN33shbyTApOB6jtSj1etX+jkMOvJwIDAQABo2MwYTAO
    BgNVHQ8BAf8EBAMCAYYwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQUA95QNVbR
    TLtm8KPiGxvDl7I90VUwHwYDVR0jBBgwFoAUA95QNVbRTLtm8KPiGxvDl7I90VUw
    DQYJKoZIhvcNAQEFBQADggEBAMucN6pIExIK+t1EnE9SsPTfrgT1eXkIoyQY/Esr
    hMAtudXH/vTBH1jLuG2cenTnmCmrEbXjcKChzUyImZOMkXDiqw8cvpOp/2PV5Adg
    06O/nVsJ8dWO41P0jmP6P6fbtGbfYmbW0W5BjfIttep3Sp+dWOIrWcBAI+0tKIJF
    PnlUkiaY4IBIqDfv8NZ5YBberOgOzW6sRBc4L0na4UU+Krk2U886UAb3LujEV0ls
    YSEY1QSteDwsOoBrp+uvFRTp2InBuThs4pFsiv9kuXclVzDAGySj4dzp30d8tbQk
    CAUw7C29C79Fv1C5qfPrmAESrciIxpg0X40KPMbp1ZWVbd4=-----END CERTIFICATE-----' COMMENT 'SAP_HC';
    SELECT * FROM CERTIFICATES WHERE COMMENT = 'SAP_HC';

    SELECT * FROM PSE_CERTIFICATES;
    ALTER PSE HTTPS ADD CERTIFICATE CERTIFICATE_ID_FROM_SELECT_ABOVE;
    SELECT * FROM PSE_CERTIFICATES;

    ALTER PSE HTTPS SET OWN CERTIFICATE
    'Contents from client.key
    Contents from client.crt
    Contents from ca.crt'

    --GRANT REFERENCES ON PSE HTTPS TO USER1;

    CREATE CREDENTIAL FOR USER DBADMIN COMPONENT 'SAPHANAIMPORTEXPORT' PURPOSE 'DBADMIN_DL_CREDENTIAL' TYPE 'X509' PSE HTTPS;
    ```

4. Export `HOTEL.MAINTENANCE` into the data lake Files container.

    ![Export to data lake Files](export-data-lake-files.png)

    The wizard makes use of the export from statement.  An example is shown below:

    ```SQL
    EXPORT INTO CSV FILE
        'hdlfs://1234-567-890-1234-56789.files.hdl.prod-us10.hanacloud.ondemand.com/maintenance.csv'
    FROM HOTEL.MAINTENANCE
    WITH
        CREDENTIAL 'DBADMINCREDENTIAL'
        COLUMN LIST IN FIRST ROW;
    ```

5. Enter the SQL statement below to delete the rows in the table.  They will be added back in the next sub-step when the import command is shown.

    ```SQL
    DELETE FROM HOTEL.MAINTENANCE;
    ```

6. Right-click on the maintenance table and choose **Import Data**.  

    ![Open Import Data Wizard](importDataWizard.png)

    From the Import data from drop down, select **Data Lake Files**.

    ![Import form data lake Files](importDataWizardDataLakeFiles.png)

    The wizard makes use of the import from statement.  An example is shown below:

    ```SQL
    IMPORT FROM CSV FILE 'hdlfs://example-file-container.files.hdl.demo-hc-3-hdl-hc-dev.dev-aws.hanacloud.ondemand.com/directory/data.csv' INTO HOTEL.MAINTENANCE WITH CREDENTIAL 'myCredential'
    ```

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 4: ](Use Google Cloud Storage (GCS) for data exports and imports (optional))]

The following steps walk through the process of exporting to and importing data from Google Cloud Storage service with a SAP HANA Cloud, SAP HANA database.

1. Sign in to the [Google Cloud Console](https://console.cloud.google.com/).

2. Create a project.

    ![Create a Google Cloud Platform project](create-GCP-project.png)

3. Navigate to Cloud Storage.

    ![Cloud Storage](cloud-storage.png)

4. Create a bucket.

    ![Create a storage bucket](create-bucket.png)

5. Navigate to IAM & Admin.

    ![Identity Access Management](IAM-console.png)

6. Create a service account.  

    ![Create a service account](service-account.png)

    Add the Owner role so that the service account can access the resources in the project.

    ![create a service account](create-service-account.png)

7. In the generated service account, add a key.

    ![add a key to the service account](service-account-add-key.png)

    Once complete, a JSON file will be downloaded that contains the `client_email` and `private_key` which will be used when accessing the bucket.

8. Remove any line breaks (i.e. \n) from the private key.  This can be done by pasting the private key into a new SQL Console and opening the search and replace menu (Ctrl-F).

    ![Remove line breaks](remove-line-breaks.png)

9. Execute the following SQL to store the private key and service account as a credential in the database.  

    ```SQL
    CREATE CREDENTIAL FOR USER DBADMIN COMPONENT 'SAPHANAIMPORTEXPORT' PURPOSE 'gcsImport' TYPE 'PASSWORD' USING 'user=<client_email>;password=<private_key>';
    SELECT * FROM CREDENTIALS;
    --DROP CREDENTIAL FOR USER DBADMIN COMPONENT 'SAPHANAIMPORTEXPORT' PURPOSE 'gcsImport' TYPE 'PASSWORD';
    ```

    ![Create Credential](createCredential.png)

    Additional details can be found at [CREATE CREDENTIAL Statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20d3f464751910148968e73782586ed0.html) and [CREDENTIALS System View](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/209fabf875191014b8f2a4731c564884.html).

10. A Google Storage SSL certificate is required to connect to the Google Cloud Storage bucket via the SAP HANA Cloud, SAP HANA database. Open your SQL console within SAP HANA database explorer, and run the first 3 commands.  Then replace \<SELECTED_CERTIFICATE_ID> with the value returned from the previous select statement.

    ```SQL[35]
    SELECT * FROM PSES;
    CREATE PSE HTTPS;
    SELECT * FROM CERTIFICATES;
    CREATE CERTIFICATE FROM '-----BEGIN CERTIFICATE-----
    MIIFVzCCAz+gAwIBAgINAgPlk28xsBNJiGuiFzANBgkqhkiG9w0BAQwFADBHMQsw
    CQYDVQQGEwJVUzEiMCAGA1UEChMZR29vZ2xlIFRydXN0IFNlcnZpY2VzIExMQzEU
    MBIGA1UEAxMLR1RTIFJvb3QgUjEwHhcNMTYwNjIyMDAwMDAwWhcNMzYwNjIyMDAw
    MDAwWjBHMQswCQYDVQQGEwJVUzEiMCAGA1UEChMZR29vZ2xlIFRydXN0IFNlcnZp
    Y2VzIExMQzEUMBIGA1UEAxMLR1RTIFJvb3QgUjEwggIiMA0GCSqGSIb3DQEBAQUA
    A4ICDwAwggIKAoICAQC2EQKLHuOhd5s73L+UPreVp0A8of2C+X0yBoJx9vaMf/vo
    27xqLpeXo4xL+Sv2sfnOhB2x+cWX3u+58qPpvBKJXqeqUqv4IyfLpLGcY9vXmX7w
    Cl7raKb0xlpHDU0QM+NOsROjyBhsS+z8CZDfnWQpJSMHobTSPS5g4M/SCYe7zUjw
    TcLCeoiKu7rPWRnWr4+wB7CeMfGCwcDfLqZtbBkOtdh+JhpFAz2weaSUKK0Pfybl
    qAj+lug8aJRT7oM6iCsVlgmy4HqMLnXWnOunVmSPlk9orj2XwoSPwLxAwAtcvfaH
    szVsrBhQf4TgTM2S0yDpM7xSma8ytSmzJSq0SPly4cpk9+aCEI3oncKKiPo4Zor8
    Y/kB+Xj9e1x3+naH+uzfsQ55lVe0vSbv1gHR6xYKu44LtcXFilWr06zqkUspzBmk
    MiVOKvFlRNACzqrOSbTqn3yDsEB750Orp2yjj32JgfpMpf/VjsPOS+C12LOORc92
    wO1AK/1TD7Cn1TsNsYqiA94xrcx36m97PtbfkSIS5r762DL8EGMUUXLeXdYWk70p
    aDPvOmbsB4om3xPXV2V4J95eSRQAogB/mqghtqmxlbCluQ0WEdrHbEg8QOB+DVrN
    VjzRlwW5y0vtOUucxD/SVRNuJLDWcfr0wbrM7Rv1/oFB2ACYPTrIrnqYNxgFlQID
    AQABo0IwQDAOBgNVHQ8BAf8EBAMCAYYwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4E
    FgQU5K8rJnEaK0gnhS9SZizv8IkTcT4wDQYJKoZIhvcNAQEMBQADggIBAJ+qQibb
    C5u+/x6Wki4+omVKapi6Ist9wTrYggoGxval3sBOh2Z5ofmmWJyq+bXmYOfg6LEe
    QkEzCzc9zolwFcq1JKjPa7XSQCGYzyI0zzvFIoTgxQ6KfF2I5DUkzps+GlQebtuy
    h6f88/qBVRRiClmpIgUxPoLW7ttXNLwzldMXG+gnoot7TiYaelpkttGsN/H9oPM4
    7HLwEXWdyzRSjeZ2axfG34arJ45JK3VmgRAhpuo+9K4l/3wV3s6MJT/KYnAK9y8J
    ZgfIPxz88NtFMN9iiMG1D53Dn0reWVlHxYciNuaCp+0KueIHoI17eko8cdLiA6Ef
    MgfdG+RCzgwARWGAtQsgWSl4vflVy2PFPEz0tv/bal8xa5meLMFrUKTX5hgUvYU/
    Z6tGn6D/Qqc6f1zLXbBwHSs09dR2CQzreExZBfMzQsNhFRAbd03OIozUhfJFfbdT
    6u9AWpQKXCBfTkBdYiJ23//OYb2MI3jSNwLgjt7RETeJ9r/tSQdirpLsQBqvFAnZ
    0E6yove+7u7Y/9waLd64NnHi/Hm3lCXRSHNboTXns5lndcEZOitHTtNCjv0xyBZm
    2tIMPNuzjsmhDYAPexZ3FL//2wmUspO8IFgV6dtxQ/PeEMMA3KgqlbbC1j+Qa3bb
    bP6MvPJwNQzcmRk13NfIRmPVNnGuV/u3gm3c
    -----END CERTIFICATE-----' COMMENT 'GOOGLE_CERT';
    SELECT CERTIFICATE_ID FROM CERTIFICATES WHERE COMMENT = 'GOOGLE_CERT';
    ALTER PSE HTTPS ADD CERTIFICATE <SELECTED_CERTIFICATE_ID>;
    SET PSE HTTPS PURPOSE REMOTE SOURCE;
    ```

    The above commands create a personal security environment (PSE), create a certificate, add the certificate to the PSE, and set the purpose to remote source.

    >The GTS Root R1 certificate used above was downloaded from [Google Trust Services' Repository](https://pki.goog/repository/) under Download CA certificates > Root CAs >.  It was downloaded in the .PEM format.

    Additional details can be found at [Certificate Management in SAP HANA Cloud](https://help.sap.com/viewer/c82f8d6a84c147f8b78bf6416dae7290/latest/en-US/1e6042c4402545f7a0574f7bc91fab25.html).

11. Run the following commands within the SQL Console to perform an export using a Google Cloud Storage bucket.

    There are two ways to use the export commands as shown below.

    ```SQL
    --Uses the previously stored credential
    --EXPORT INTO PARQUET FILE 'gs://<bucket>/<objectKey>' FROM HOTEL.MAINTENANCE WITH CREDENTIAL 'gcsImport';
    EXPORT INTO PARQUET FILE 'gs://hc-storage-bucket/maintenance.parquet' FROM HOTEL.MAINTENANCE WITH CREDENTIAL 'gcsImport';

    --Uses the private key as part of the SQL statement
    --EXPORT INTO PARQUET FILE 'gs://<service_account>:<private_key>@<bucket>/<object_id>' FROM HOTEL.MAINTENANCE;
    EXPORT INTO PARQUET FILE 'gs://hc-service-account@hc-storage-proj.iam.gserviceaccount.com:-----BEGIN PRIVATE KEY-----MIIEv...-----END PRIVATE KEY-----@hc-storage-bucket/maintenance2.parquet' FROM HOTEL.MAINTENANCE;
    ```

    >An alternative to the above SQL commands is to use the Export Data Wizard. The Wizard can be accessed by right-clicking a table or view and choosing **Export Data**. When using the export wizard, the "gs://" prefix is not needed when specifying the GCS Path.
    >
    >    ![Export Data Wizard](exportGCSWizard.png)


12. Verify the export was completed successfully by refreshing your bucket within Google Cloud Console.

    ![Successful Export](successfulExport.png)

13. Enter the SQL statement below to delete the rows in the table.  They will be added back in the next sub-step when the import command is shown.

    ```SQL
    DELETE FROM HOTEL.MAINTENANCE;
    ```


14. Run the following commands within the SQL Console to perform an import using a Google Cloud Storage bucket.

    ```SQL
    --Uses the previously stored credential
    --IMPORT FROM PARQUET FILE  'gs://<bucket>/<objectKey>' WITH CREDENTIAL 'gcsImport';
    IMPORT FROM PARQUET FILE 'gs://hc-storage-bucket/maintenance.parquet' INTO HOTEL.MAINTENANCE WITH FAIL ON INVALID DATA CREDENTIAL 'gcsImport';

    --Uses the private key as part of the SQL statement
    --IMPORT FROM PARQUET FILE 'gs://<service_account>:<private_key>@<bucket>/<object_id>' INTO HOTEL.MAINTENANCE WITH FAIL ON INVALID DATA;
    IMPORT FROM PARQUET FILE 'gs://hc-service-account@hc-storage-proj.iam.gserviceaccount.com:-----BEGIN PRIVATE KEY-----MIIEvg...-----END PRIVATE KEY-----@hc-storage-bucket/maintenance2.parquet' INTO HOTEL.MAINTENANCE WITH FAIL ON INVALID DATA;
    ```

For additional details see the topic [Importing and Exporting Data](https://help.sap.com/viewer/f9c5015e72e04fffa14d7d4f7267d897/latest/en-US/261937915fa5438ca545b8278b2979b7.html) in the SAP HANA Cloud Administration Guide.

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 5: ](Export and import schema or catalog objects)]

The following tables list the different options available in the SAP HANA database explorer to export and import catalog objects.

Methods to export catalog objects

| Method                  | Version | Target                             | Format(s)                             | Limitations |
| ------------------------|---------|-------------------------------|---------------------------------------|-------------|
| [Export catalog wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/1f20a6c4364c4b0680596e74e4ba281d.html) | All | Local computer | CSV, Binary, \*Parquet | 2 GB max |
| [Export catalog wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/1f20a6c4364c4b0680596e74e4ba281d.html)  | SAP HANA Cloud, HANA database | S3, Azure, GCS, Alibaba OSS | CSV, Binary, Parquet | \*\* |
| [Export catalog wizard](https://help.sap.com/viewer/e8d0ddfb84094942a9f90288cd6c05d3/latest/en-US/1f20a6c4364c4b0680596e74e4ba281d.html)  | SAP HANA on-premise | SAP HANA file system | CSV, Binary | \*\* |
| [Export statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20da0bec751910148e69c9668ea3ccb8.html) | SAP HANA Cloud, HANA database  | S3, Azure, GCS, Alibaba OSS                 | CSV, Binary, Parquet | ** |
| [Export statement](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/latest/en-US/20da0bec751910148e69c9668ea3ccb8.html) | SAP HANA on-premise     | HANA file system                 | CSV, Binary data | \*\* |


Methods to import catalog objects

| Method                  | Version | Source                             | Format(s)                             | Limitations |
| ------------------------|---------|-------------------------------|---------------------------------------|-------------|
| [Import catalog wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/80f63855e7854cd3a6144e0021b5f748.html) | All  | Local computer | CSV, Binary   | 2 GB max |
| [Import catalog wizard](https://help.sap.com/viewer/a2cea64fa3ac4f90a52405d07600047b/cloud/en-US/80f63855e7854cd3a6144e0021b5f748.html)  | SAP HANA Cloud, HANA database  | S3, Azure, GCS, Alibaba OSS  | CSV, Binary, Parquet                          | \*\* |
| [Import catalog wizard](https://help.sap.com/viewer/e8d0ddfb84094942a9f90288cd6c05d3/latest/en-US/80f63855e7854cd3a6144e0021b5f748.html)  | SAP HANA on-premise | SAP HANA file system | CSV, Binary                | 2 GB max per object,  \*\* |
| [Import statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20f75ade751910148492a90e5e375b8f.html) | SAP HANA Cloud, HANA database | S3, Azure, GCS, Alibaba OSS                 | CSV, Binary,  Parquet                          | \*\* |
| [Import statement](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/latest/en-US/20f75ade751910148492a90e5e375b8f.html) | SAP HANA on-premise | SAP HANA file system       | CSV, Binary                        | \*\* |

> \* SAP HANA Cloud, HANA database only

> \*\* Max file size in archive is 8 GB (SAP Note [2907201](https://launchpad.support.sap.com/#/notes/2907201)).

> Export and import using cloud storage from Amazon, Microsoft Azure, Google, and Alibaba Cloud is covered in the final step of this tutorial.

Similar to the first section, the maintenance table will be exported and re-imported.  The export statement and the associated export catalog wizard have additional options, including the ability to include other schema objects such as functions and procedures as well as the option to include the SQL statements to recreate the objects.

1. Right-click on the maintenance table and choose **Export Catalog Objects**.  

    ![Open Export Catalog Objects Wizard](exportCatalogObjects.png)

    Choose **Local Computer** for the export location, provide a name for the Local Archive, select an export format and press **Export**.

    ![Export Catalog Objects Wizard](exportCatalogWizard2.png)

    Examine the available export format options.

    ![Format Options](formatOptions.png)

    Note that Binary Raw is the binary format for SAP HANA Cloud and Binary Data is the format option for SAP HANA as a Service and SAP HANA on-premise.

2. Examine the exported file.  Notice that it contains the SQL to recreate the table as well as the data of the table.

    ![Exported file](targz.png)

3. Enter the SQL statement below to drop the table.  It will be added back in the next sub-step.

    ```SQL
    DROP TABLE HOTEL.MAINTENANCE;
    ```

3. Right-click on the tables folder and choose **Import Catalog**.  

    ![Open Import Data Wizard](importCatalogWizard.png)

    Browse to the previously downloaded .tar.gz file and complete the wizard.

    ![Choose file to import](importCatalogWizard2.png)

    The contents of the maintenance table should now be the same as it was before the previously executed drop statement.

[DONE]
[ACCORDION-END]


[ACCORDION-BEGIN [Step 6: ](Use Azure cloud storage for exports and imports of catalog objects (optional))]

The following steps walk through the process of using Microsoft Azure storage service as a target for an export catalog operation.

1. Log in to the [Microsoft Azure Portal](https://portal.azure.com/).

2. Create a resource group.

    ![Resource Group](resourceGroup.png)

3. Create a storage Service

    ![Storage Account](storageAccount.png)

4. Create a blob container.

    ![Blob Container](createBlobContainer.png)

5. Generate an API key.

    ![Generate API Key](GenerateAPIKey1.png)

    Specify that the permissions and the expiry time.

    ![Key Settings](GenerateAPIKey2.png)

    Copy the generated query string and paste it into a text editor.  This will be used in step 7.

    ![Shared Access Signature](queryString.png)

6. In the SAP HANA database explorer, add the certificate used by Microsoft to the HANA Cloud PSE.  Replace the \<SELECTED_CERTIFICATE_ID> with the value returned from the previous select statement.

    ```SQL[6]
    SELECT * FROM PSES;
    CREATE PSE HTTPS;
    SELECT * FROM CERTIFICATES;
    CREATE CERTIFICATE FROM '-----BEGIN CERTIFICATE-----MIIDdzCCAl+gAwIBAgIEAgAAuTANBgkqhkiG9w0BAQUFADBaMQswCQYDVQQGEwJJ
    RTESMBAGA1UEChMJQmFsdGltb3JlMRMwEQYDVQQLEwpDeWJlclRydXN0MSIwIAYD
    VQQDExlCYWx0aW1vcmUgQ3liZXJUcnVzdCBSb290MB4XDTAwMDUxMjE4NDYwMFoX
    DTI1MDUxMjIzNTkwMFowWjELMAkGA1UEBhMCSUUxEjAQBgNVBAoTCUJhbHRpbW9y
    ZTETMBEGA1UECxMKQ3liZXJUcnVzdDEiMCAGA1UEAxMZQmFsdGltb3JlIEN5YmVy
    VHJ1c3QgUm9vdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKMEuyKr
    mD1X6CZymrV51Cni4eiVgLGw41uOKymaZN+hXe2wCQVt2yguzmKiYv60iNoS6zjr
    IZ3AQSsBUnuId9Mcj8e6uYi1agnnc+gRQKfRzMpijS3ljwumUNKoUMMo6vWrJYeK
    mpYcqWe4PwzV9/lSEy/CG9VwcPCPwBLKBsua4dnKM3p31vjsufFoREJIE9LAwqSu
    XmD+tqYF/LTdB1kC1FkYmGP1pWPgkAx9XbIGevOF6uvUA65ehD5f/xXtabz5OTZy
    dc93Uk3zyZAsuT3lySNTPx8kmCFcB5kpvcY67Oduhjprl3RjM71oGDHweI12v/ye
    jl0qhqdNkNwnGjkCAwEAAaNFMEMwHQYDVR0OBBYEFOWdWTCCR1jMrPoIVDaGezq1
    BE3wMBIGA1UdEwEB/wQIMAYBAf8CAQMwDgYDVR0PAQH/BAQDAgEGMA0GCSqGSIb3
    DQEBBQUAA4IBAQCFDF2O5G9RaEIFoN27TyclhAO992T9Ldcw46QQF+vaKSm2eT92
    9hkTI7gQCvlYpNRhcL0EYWoSihfVCr3FvDB81ukMJY2GQE/szKN+OMY3EU/t3Wgx
    jkzSswF07r51XgdIGn9w/xZchMB5hbgF/X++ZRGjD8ACtPhSNzkE1akxehi/oCr0
    Epn3o0WC4zxe9Z2etciefC7IpJ5OCBRLbf1wbWsaY71k5h+3zvDyny67G7fyUIhz
    ksLi4xaNmjICq44Y3ekQEe5+NauQrz4wlHrQMz2nZQ/1/I6eYs9HRCwBXbsdtTLS
    R9I4LtD+gdwyah617jzV/OeBHRnDJELqYzmp-----END CERTIFICATE-----' COMMENT 'Azure';
    SELECT CERTIFICATE_ID FROM CERTIFICATES WHERE COMMENT = 'Azure';
    ALTER PSE HTTPS ADD CERTIFICATE <SELECTED_CERTIFICATE_ID>;
    SET PSE HTTPS PURPOSE REMOTE SOURCE;
    ```

    Additional details can be found at [Certificate Management in SAP HANA Cloud](https://help.sap.com/viewer/c82f8d6a84c147f8b78bf6416dae7290/latest/en-US/1e6042c4402545f7a0574f7bc91fab25.html).

    The certificate string above is from the root certificate used for the Azure Portal.

    ![Certificate](certificate.png)

7. Start the export catalog wizard and export the maintenance table to the storage service.

    The Azure Path is of the format:

    `<Storage Account Name>:<generated shared access string>@<Container Name>/<File Name>`

    An example string is shown below:

    `danstestsa:sp=racwdl&st=2021-01-09T13:00:46Z&se=2021-01-10T13:00:46Z&sv=2019-12-12&sr=c&sig=TP%2BVYhcvSPDc4DZxcls6vN%2BCLHDNagedbei2IuEZsWU%3D@myblobcontainer/maintenance`

    ![Export Data Wizard](exportAzureStorage.png)

    Pressing the Compose button shows the parsed Azure path.

    ![Compose](tokenParsed.png)

    After the Export button is pressed, the results can be seen in the Azure Portal.

    ![Viewing the results](exportResult.png)

    The equivalent SQL statement is shown below:

    ```SQL
    EXPORT HOTEL.MAINTENANCE AS PARQUET INTO 'azure://danstestsa:sp=racwdl&st=2021-01-09T13:00:46Z&se=2021-01-10T13:00:46Z&sv=2019-12-12&sr=c&sig=TP%2BVYhcvSPDc4DZxcls6vN%2BCLHDNagedbei2IuEZsWU%3D@myblobcontainer/maintenance' WITH REPLACE;
    ```

8. Enter the SQL statement below to drop the table.  It will be added back in the next step.

    ```SQL
    DROP TABLE HOTEL.MAINTENANCE;
    ```

9. Import the table using the import catalog objects wizard.

    ![Import Azure](importAzure.png)

    The contents of the maintenance table should now be the same as it was before the previously executed drop statement.

    The equivalent SQL statement is shown below:

    ```SQL
    IMPORT HOTEL.MAINTENANCE FROM 'azure://danstestsa:sp=racwdl&st=2021-01-09T13:00:46Z&se=2021-01-10T13:00:46Z&sv=2019-12-12&sr=c&sig=TP%2BVYhcvSPDc4DZxcls6vN%2BCLHDNagedbei2IuEZsWU%3D@myblobcontainer/maintenance' WITH REPLACE;
    ```

    For additional details see the topic [Importing and Exporting Data](https://help.sap.com/viewer/f9c5015e72e04fffa14d7d4f7267d897/latest/en-US/261937915fa5438ca545b8278b2979b7.html) in the SAP HANA Cloud Administration Guide.

[DONE]
[ACCORDION-END]

[ACCORDION-BEGIN [Step 7: ](Use Amazon Web Services (AWS) S3 for exports and imports of catalog objects (optional))]

The following steps walk through the process of AWS S3 storage service as a target for an export catalog operation.

1. Log in to the [AWS S3 Management Console](https://console.aws.amazon.com/s3/home).

2. Navigate to the S3 storage service.

    ![S3 storage service](awsStorageS3.png)

3. Create an AWS bucket.

    ![Bucket](createAWSBucket1.png)

    Provide a unique bucket name, choose your AWS region, and finish creating the bucket.

    ![Bucket](createAWSBucket2.png)

4. Create a IAM User.

    It is recommended that you create an IAM User instead of using the root account to manage the S3 bucket. If you already have an existing IAM User, then feel free to skip this step, and go ahead and [generate an access key and secret key for an existing IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).

    To create an IAM User, begin by logging in with your root credentials, and navigate to the **Security Credentials** tab.

    ![Security Credentials Tab](securityCredentialsPage.png)

    Select add users.

    ![New User](addUser.png)

    Specify user details such as **User name** and select the **AWS credential type**.

    ![User Details](addUserDetails.png)

    Select **Attach existing policies directly** and provide full Amazon S3 Access.

    ![User Permissions](addUserPermissions.png)

    Finish creating the user.

    !![Finish creating user](finishCreatingUser.png)

    **Copy and Save** the ***Access key ID*** and ***Secret access key***, as it will be required in step 5.

    ![Access Key](accessKey.png)

5. In the SAP HANA database explorer, add the certificate used by AWS to the HANA Cloud PSE. Replace the \<SELECTED_CERTIFICATE_ID> with the value returned from the previous select statement.

    The certificate below is the [Baltimore `CyberTrust` Root certificate](https://www.digicert.com/kb/digicert-root-certificates.htm).

    ```SQL[25]
    SELECT * FROM PSES;
    CREATE PSE HTTPS;
    SELECT * FROM CERTIFICATES;
    CREATE CERTIFICATE FROM '-----BEGIN CERTIFICATE-----
    MIIDdzCCAl+gAwIBAgIEAgAAuTANBgkqhkiG9w0BAQUFADBaMQswCQYDVQQGEwJJ
    RTESMBAGA1UEChMJQmFsdGltb3JlMRMwEQYDVQQLEwpDeWJlclRydXN0MSIwIAYD
    VQQDExlCYWx0aW1vcmUgQ3liZXJUcnVzdCBSb290MB4XDTAwMDUxMjE4NDYwMFoX
    DTI1MDUxMjIzNTkwMFowWjELMAkGA1UEBhMCSUUxEjAQBgNVBAoTCUJhbHRpbW9y
    ZTETMBEGA1UECxMKQ3liZXJUcnVzdDEiMCAGA1UEAxMZQmFsdGltb3JlIEN5YmVy
    VHJ1c3QgUm9vdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKMEuyKr
    mD1X6CZymrV51Cni4eiVgLGw41uOKymaZN+hXe2wCQVt2yguzmKiYv60iNoS6zjr
    IZ3AQSsBUnuId9Mcj8e6uYi1agnnc+gRQKfRzMpijS3ljwumUNKoUMMo6vWrJYeK
    mpYcqWe4PwzV9/lSEy/CG9VwcPCPwBLKBsua4dnKM3p31vjsufFoREJIE9LAwqSu
    XmD+tqYF/LTdB1kC1FkYmGP1pWPgkAx9XbIGevOF6uvUA65ehD5f/xXtabz5OTZy
    dc93Uk3zyZAsuT3lySNTPx8kmCFcB5kpvcY67Oduhjprl3RjM71oGDHweI12v/ye
    jl0qhqdNkNwnGjkCAwEAAaNFMEMwHQYDVR0OBBYEFOWdWTCCR1jMrPoIVDaGezq1
    BE3wMBIGA1UdEwEB/wQIMAYBAf8CAQMwDgYDVR0PAQH/BAQDAgEGMA0GCSqGSIb3
    DQEBBQUAA4IBAQCFDF2O5G9RaEIFoN27TyclhAO992T9Ldcw46QQF+vaKSm2eT92
    9hkTI7gQCvlYpNRhcL0EYWoSihfVCr3FvDB81ukMJY2GQE/szKN+OMY3EU/t3Wgx
    jkzSswF07r51XgdIGn9w/xZchMB5hbgF/X++ZRGjD8ACtPhSNzkE1akxehi/oCr0
    Epn3o0WC4zxe9Z2etciefC7IpJ5OCBRLbf1wbWsaY71k5h+3zvDyny67G7fyUIhz
    ksLi4xaNmjICq44Y3ekQEe5+NauQrz4wlHrQMz2nZQ/1/I6eYs9HRCwBXbsdtTLS
    R9I4LtD+gdwyah617jzV/OeBHRnDJELqYzmp-----END CERTIFICATE-----' COMMENT 'S3';
    SELECT CERTIFICATE_ID FROM CERTIFICATES WHERE COMMENT = 'S3';
    ALTER PSE HTTPS ADD CERTIFICATE <SELECTED_CERTIFICATE_ID>;
    SET PSE HTTPS PURPOSE REMOTE SOURCE;
    ```

    Additional details can be found at [Certificate Management in SAP HANA Cloud](https://help.sap.com/viewer/c82f8d6a84c147f8b78bf6416dae7290/latest/en-US/1e6042c4402545f7a0574f7bc91fab25.html).

6. Create Credentials (Optional)

    Execute the following SQL to store the access key and secret key as a credential in the database.  

    ```SQL
    CREATE CREDENTIAL FOR USER DBADMIN COMPONENT 'SAPHANAIMPORTEXPORT' PURPOSE 'AWS' TYPE 'PASSWORD' USING 'user=<access_key>;password=<secret_key>';
    SELECT * FROM CREDENTIALS;
    --DROP CREDENTIAL FOR USER DBADMIN COMPONENT 'SAPHANAIMPORTEXPORT' PURPOSE 'AWS' TYPE 'PASSWORD';
    ```

    Use the `access_key` and `secret_key` from Step 3. Additionally, if you didn't follow step 3 as you have an existing IAM user, then [generate an access key and secret key for an existing IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).

    !![Create Credential](createAWSCredential.png)

    Additional details can be found at [CREATE CREDENTIAL Statement](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/20d3f464751910148968e73782586ed0.html) and [CREDENTIALS System View](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/latest/en-US/209fabf875191014b8f2a4731c564884.html).

7. Start the export catalog wizard and export the maintenance table to the storage service.

    The AWS S3 Path (in the Export Catalog Objects Wizard) is of the format:

    `<access_key>:<secret_key>@<bucket_name>/<object_id>`

    Use the `access_key` and `secret_key` from Step 3. Additionally, if you didn't follow step 3 as you have an existing IAM user, then [generate an access key and secret key for an existing IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).

    An example string is shown below:

    `AKIA3JHRPYB6KY3LSI76:dW9q+KxA0rgtaoBY3MnAAQIS96ypVEDvgxE8rIpt@maitrysawsbucket/maintenance`

    ![Export Data Wizard](exportAWSStorageWizard.png)

    Pressing the Compose button shows the parsed AWS S3 path.

    ![Compose](exportAWSCompose.png)

    After the Export button is pressed, the results can be seen in the AWS S3 Console.

    ![Export Result](exportAWSResult.png)

    The equivalent SQL statement is shown below:

    ```SQL
    --EXPORT HOTEL.MAINTENANCE AS PARQUET INTO 's3-<region>://<access_key>:<secret_key>@<bucket>/<object_id>' WITH REPLACE;
    EXPORT HOTEL.MAINTENANCE AS PARQUET INTO 's3-us-east-1://AKIA3JHRPYB6KY3LSI76:dW9q+KxA0rgtaoBY3MnAAQIS96ypVEDvgxE8rIpt@maitrysawsbucket/maintenance' WITH REPLACE;
    ```

    Alternatively, the previously stored credentials can be used for export:

    ```SQL
    --EXPORT HOTEL.MAINTENANCE AS PARQUET INTO 's3-<region>://<bucket>/<objectKey>' WITH CREDENTIAL 'AWS';
    EXPORT HOTEL.MAINTENANCE AS PARQUET INTO 's3-us-east-1://maitrysawsbucket/maintenance' WITH CREDENTIAL 'AWS';
    ```    

    > An error regarding an invalid SSL certificate indicates an incorrect SSL certificate is being used.  Additionally, if MFA (Multi-factor Authentication) is enabled, then the export may fail, so ensure that MFA is disabled before exporting.

8. Enter the SQL statement below to drop the table. It will be added back in the next step.

    ```SQL
    DROP TABLE HOTEL.MAINTENANCE;
    ```

9. Import the table using the import catalog objects wizard.

    The AWS S3 Path (in the Import Catalog Objects Wizard) is of the format:

    `<access_key>:<secret_key>@<bucket_name>/<object_id>`

    An example string is shown below:

    `AKIA3JHRPYB6KY3LSI76:dW9q+KxA0rgtaoBY3MnAAQIS96ypVEDvgxE8rIpt@maitrysawsbucket/maintenance`

    Select **Load** to load the catalog object in the wizard.

    ![Import Catalog Wizard](importAWSStorageWizard.png)

    The contents of the maintenance table should now be the same as it was before the previously executed drop statement.

    The equivalent SQL statement is shown below:

    ```SQL
    --IMPORT HOTEL.MAINTENANCE AS PARQUET FROM 's3-<region>://<access_key>:<secret_key>@<bucket>/<object_id>' WITH REPLACE;
    IMPORT HOTEL.MAINTENANCE AS PARQUET FROM 's3-us-east-1://AKIA3JHRPYB6KY3LSI76:dW9q+KxA0rgtaoBY3MnAAQIS96ypVEDvgxE8rIpt@maitrysawsbucket/maintenance' WITH REPLACE;
    ```    

    Alternatively, the previously stored credentials can be used for import:

    ```SQL
    --EXPORT HOTEL.MAINTENANCE AS PARQUET FROM 's3-<region>://<bucket>/<objectKey>' WITH CREDENTIAL 'AWS';
    IMPORT HOTEL.MAINTENANCE AS PARQUET FROM 's3-us-east-1://maitrysawsbucket/maintenance' WITH CREDENTIAL 'AWS';
    ```    

    For additional details see the topic [Importing and Exporting Data](https://help.sap.com/viewer/f9c5015e72e04fffa14d7d4f7267d897/latest/en-US/261937915fa5438ca545b8278b2979b7.html) in the SAP HANA Cloud Administration Guide.

Congratulations! You have imported and exported data and catalog objects.

[VALIDATE_1]
[ACCORDION-END]

---
