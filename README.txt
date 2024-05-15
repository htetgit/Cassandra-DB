This is my README file

To unload/load Table for customerinfo1

Test1,

[root@k8s-wnode1 ~]# dsbulk unload -h 172.18.0.2 -port 9042 -k bank -t customerinfo1 -url /root/my_db/my_data.csv
Operation directory: /root/logs/UNLOAD_20240514-074506-183625
 total | failed | rows/s | p50ms | p99ms | p999ms
10,000 |      0 |  7,475 | 13.96 | 97.52 | 162.53
Operation UNLOAD_20240514-074506-183625 completed successfully in less than one second.


Test2,
[root@k8s-wnode1 ~]# dsbulk unload -h 127.0.0.1 -port 9742 -k bank -t customerinfo1 -url /root/test_db/my_data.csv
Operation directory: /root/logs/UNLOAD_20240514-025210-328403
 total | failed | rows/s | p50ms | p99ms | p999ms
10,000 |      0 |  5,996 | 13.38 | 60.03 |  62.13
Operation UNLOAD_20240514-025210-328403 completed successfully in 1 second.


[root@k8s-wnode1 ~]# dsbulk load -h 172.17.0.4 -port 9042 -k bank -t customerinfo1 -url /root/test_db/my_data.csv
Operation directory: /root/logs/LOAD_20240514-052434-099598
 total | failed | rows/s | p50ms | p99ms | p999ms | batches
10,000 |      0 |  3,472 |  8.09 | 44.04 |  73.40 |    1.00
Operation LOAD_20240514-052434-099598 completed successfully in 1 second.
Last processed positions can be found in positions.txt
[root@k8s-wnode1 ~]# docker exec -it my-cassandra bash
root@b23134f69969:/# cqlsh
Connected to Test Cluster at 127.0.0.1:9042
[cqlsh 6.1.0 | Cassandra 4.1.1 | CQL spec 3.4.6 | Native protocol v5]
Use HELP for help.
cqlsh> Select Count (*) From bank.customerinfo1;

 count
-------
 10000

(1 rows)

Warnings :
Aggregation query used without partition key

---------------------------------------------------------------------------------------------------

To unload/load selected query::::

[root@k8s-wnode1 ~]# dsbulk unload -h 172.18.0.3 -port 9042 -query "SELECT * FROM bank.customerinfo2 LIMIT 10;" > /root/my_db/test_data.csv
Operation directory: /root/logs/UNLOAD_20240514-082918-484978
total | failed | rows/s | p50ms | p99ms | p999ms
   10 |      0 |     14 | 23.66 | 23.72 |  23.72
Operation UNLOAD_20240514-082918-484978 completed successfully in less than one second.

[root@k8s-wnode1 ~]# dsbulk load -h 172.17.0.4 -port 9042 -k bank -t customerinfo2 -url /root/my_db/test_data.csv
Operation directory: /root/logs/LOAD_20240514-083814-065936
total | failed | rows/s | p50ms | p99ms | p999ms | batches
   10 |      0 |     11 | 20.98 | 35.13 |  35.13 |    1.00
Operation LOAD_20240514-083814-065936 completed successfully in less than one second.
Last processed positions can be found in positions.txt

cqlsh> Select Count (*) From bank.customerinfo2;

 count
-------
    10

(1 rows)

Warnings :
Aggregation query used without partition key

cqlsh>
-----------------------------------------------------------------------------------------------------------------
To unload/load selected query with Authenticaton:::

[root@k8s-wnode1 ~]# dsbulk unload -h 172.18.0.2 -port 9042 --driver.advanced.auth-provider.class PlainTextAuthProvider -u cassandra -p cassandra -query "SELECT * FROM bank.customerinfo2 LIMIT 10;" > /root/my_db/tt_data.csv
Operation directory: /root/logs/UNLOAD_20240515-030041-396796
[driver] /172.18.0.2:9042 did not send an authentication challenge; This is suspicious because the driver expects authentication
[driver] /172.18.0.2:9042 did not send an authentication challenge; This is suspicious because the driver expects authentication

total | failed | rows/s | p50ms | p99ms | p999ms
   10 |      0 |     12 | 28.25 | 28.31 |  28.31
Operation UNLOAD_20240515-030041-396796 completed successfully in less than one second.
[root@k8s-wnode1 ~]# dsbulk load -h 172.17.0.4 -port 9042 --driver.advanced.auth-provider.class PlainTextAuthProvider -u cassandra -p cassandra -k bank -t customerinfo2 -url /root/my_db/tt_data.csv
Operation directory: /root/logs/LOAD_20240515-030343-316440
[driver] /172.17.0.4:9042 did not send an authentication challenge; This is suspicious because the driver expects authentication
[driver] /172.17.0.4:9042 did not send an authentication challenge; This is suspicious because the driver expects authentication

total | failed | rows/s | p50ms | p99ms | p999ms | batches
   10 |      0 |     11 | 21.11 | 37.49 |  37.49 |    1.00
Operation LOAD_20240515-030343-316440 completed successfully in less than one second.
Last processed positions can be found in positions.txt
-------------------------
echo "dsbulk Installation"
cd /root
curl -OL https://downloads.datastax.com/dsbulk/dsbulk-1.7.0.tar.gz
tar -xzvf dsbulk-1.7.0.tar.gz
echo 'export PATH=/root/dsbulk-1.7.0/bin:$PATH' >> ~/.bashrc
sleep 10
source ~/.bashrc
echo "Checking dsbulk version"
dsbulk --version
---------------------
To install cassandra driver
$npm install cassandra-driver or $npm install -g cassandra-driver
check install or not
$npm list cassandrdriver or $npm list -g cassandra-driver
------------------------------------
To Create Cassandra database's username and password

1. Edit cassandra.yaml Configuration File
vi Cassandra.yaml
authenticator: PasswordAuthenticator
2. Set Up Superuser Credentials
Open the cqlsh shell and connect to your Cassandra database. Then, create a superuser account with a password.
cqlsh> CREATE ROLE regular_user WITH PASSWORD = 'user_password' AND LOGIN = true;
3. Restart Cassandra
4. Create Additional Users (Optional)
cqlsh> CREATE ROLE regular_user WITH PASSWORD = 'user_password' AND LOGIN = true;
5. Connect to Cassandra
cqlsh> cqlsh -u superuser -p superuser_password
