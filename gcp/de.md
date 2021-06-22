**list account name**
```  
gcloud auth list
```

**list project id**
```
gcloud config list project
```

**get project id**
```
export PROJECT_ID=$(gcloud info --format='value(config.project)')
export BUCKET=${PROJECT_ID}-ml
```

**create cloud sql instance**
```
gcloud sql instances create taxi \
    --tier=db-n1-standard-1 --activation-policy=ALWAYS
```

**set root user password**
```
gcloud sql users set-password root --host % --instance taxi \
 --password
```

**get vm ipaddress**
```
export ADDRESS=$(wget -qO - http://ipecho.net/plain)/32
```

**add ip to authorized list**
```
gcloud sql instances patch taxi --authorized-networks $ADDRESS
```

**get mysql instance ip address**
```
MYSQLIP=$(gcloud sql instances describe \
taxi --format="value(ipAddresses.ipAddress)")
```

**connect to mysql instance**
```
mysql --host=$MYSQLIP --user=root \
      --password --verbose
```

**download csv files**
```
gsutil cp gs://cloud-training/OCBL013/nyc_tlc_yellow_trips_2018_subset_1.csv trips.csv-1
gsutil cp gs://cloud-training/OCBL013/nyc_tlc_yellow_trips_2018_subset_2.csv trips.csv-2
```

**load csvs to table**
```
mysqlimport --local --host=$MYSQLIP --user=root --password \
--ignore-lines=1 --fields-terminated-by=',' bts trips.csv-*
```
