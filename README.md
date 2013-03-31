Intructions For my YouTube Series on How to Stream Social Media to Hadoop
===================

This repository contains the commands and links to code that support a YouTube Series on How to Stream Social Media to Hadoop.  Each section corresponds to a video in the series.  Assumes you have working knowledge of the technical components.

http://www.youtube.com/datadansandler

Video 2: Deploy AWS EC2 Instance
---------------
This is part 2 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we deploy the AWS EC2 RHEL 6.3 64-bit M1.small instance.

http://www.youtube.com/watch?v=o0D6vKKuin4&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=2

1) Red Hat 6.3, M1 Large recommended, for this exercise will Make it small

a. Setup security

b. AWS Security Group - allow ports HTTP (80 and 8080), HTTPS (443), add 7180 (Cloudera Mgr), 11000 (Oozie) and 50030 (MR Job tracker) and 51400

c. Get the Access tokens

	i. If you have an EC2 account, an admin should have sent these to you

		1) Do not Lose, Do not Share

			ii. Add environment variables as you normally wouldl

			iii. AWS_ACCESS_KEY

			iv. AWS_SECRET_KEY

		C. Setup a Key Pair (aka .PEM file)

			i. Do not lose, do not share

	A. Get the external IP and external 


	B. SSH to environment

		A. ssh root@ec2-<YOUR_IP_ADDR>.compute-1.amazonaws.com  -i dans_hadoop.pem 

		B. Firewall config

			i. system-config-firewall-tui

			ii. Enable SSH (22), HTTP (80 and 8080), HTTPS (443), add 7180 (Cloudera Mgr), 11000 (Oozie) and 50030 (MR Job tracker) and 500 70

		a. Enable SE Linux

			i. When attempting to install, "SELinux is enabled. It must be disabled to install and use this product."

			ii. vi /etc/selinux/config

				1) SELINUX=disabled 

				2) reboot

				3) sestatus

				4) getenforce

		b. Only if there are port issues

			i. iptables -L -n

			ii. service iptables restart
   
   
Video 3: Install Cloudera CDH4
---------------
This is part 3 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. In this part, we install Cloudera in the Amazon Cloud.

http://www.youtube.com/watch?v=74yl-LekBMQ&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=3

A. mkdir /tmp/cdh_download; cd /tmp/cdh_download

B. download using:
	curl -o cloudera-manager-installer.bin http://archive.cloudera.com/cm4/installer/latest/cloudera-manager-installer.bin

C.  chmod u+rx on cloudera-manager-installer.bin 

D. ./cloudera-manager-installer.bin 

E. Test services running

   a. Login to Cloudera Manager http://aws-host:7180

			a) Check services

			b) Also check 50030 and 50070

		b. Check Job Tracker

F. Install Maven 

   1) wget http://apache.mirrors.lucidnetworks.net/maven/maven-3/3.0.4/binaries/apache-maven-3.0.4-bin.tar.gz

   2) tar xvf apache-maven-3.0.4-bin.tar.gz

Video 4: Register Twitter App
---------------
This is part 4 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we register an app on Twitter.

http://www.youtube.com/watch?v=R6yVAKh21YE&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=4

No special instructions other than those posted in the video.

Video 5: Configure Flume
---------------
This is part 5 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we configure Flume to stream Twitter to HDFS.

http://www.youtube.com/watch?v=rgtRj0viQ1E&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=5

1) Download Twitter  Project from Git Hub

	a) mkdir /tmp/twitter-install

	b) wget https://github.com/cloudera/cdh-twitter-example/archive/master.zip

	c) unzip master

2) Cd /tmp/twitter-install/cdh-twitter-example-master/flume-sources

3) wget http://files.cloudera.com/samples/flume-sources-1.0-SNAPSHOT.jar

4) mv flume-sources-1.0-SNAPSHOT.jar /usr/lib.hadoop

5) sudo cp /etc/flume-ng/conf/flume-env.sh.template /etc/flume-ng/conf/flume-env.sh

6) Vi flume-env.sh, add jar above to FLUME_CLASSPATH

7) create /etc/default/flume-ng-agent

	a) See https://github.com/DataDanSandler/sentiment_analysis/tree/master/src/flume

8) Create /etc/flume-ng/conf/flume.conf

	a) See https://github.com/DataDanSandler/sentiment_analysis/tree/master/src/flume

Video 6: Install MySQL and Configure Hive
---------------
This is part 6 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we install MySQL and configure Hive.

http://www.youtube.com/watch?v=gam2xJyTOcQ&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=6

1) cd /usr/lib/hadoop

2)  wget http://files.cloudera.com/samples/hive-serdes-1.0-SNAPSHOT.jar

3) Create the Hive directory hierarchy

sudo -u hdfs hadoop fs -mkdir /user/hive/warehouse   

sudo -u hdfs hadoop fs -chown -R hive:hive /user/hive  

sudo -u hdfs hadoop fs -chmod 777 /user/hive  

sudo -u hdfs hadoop fs -chmod 777 /user/hive/warehouse  

4) Permissions

sudo usermod -a -G hive flume

sudo usermod -a -G hive hdfs

sudo usermod -a -G hive oozie

5)  install the MySQL JDBC driver in /usr/lib/hive/lib.

	curl -L 'http://www.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.22.tar.gz/from/http://mysql.he.net/' | tar xz

	sudo cp mysql-connector-java-5.1.22/mysql-connector-java-5.1.22-bin.jar /usr/lib/hive/lib/

6) Setup Hive in MySQL https://ccp.cloudera.com/display/CDH4DOC/Hive+Installation#HiveInstallation-ConfiguringtheHiveMetastore

mysql -u root -p

Enter password:

mysql> CREATE DATABASE metastore;

mysql> USE metastore;

mysql> SOURCE /usr/lib/hive/scripts/metastore/upgrade/mysql/hive-schema-0.9.0.mysql.sql;

mysql> CREATE USER 'hive'@'myhost' IDENTIFIED BY 'mypassword';

...

mysql> GRANT ALL on metastore.* TO hive@localhost IDENTIFIED BY 'mypassword';

	MAKE SURE THIS IS DONE at ROOT MYSQL LEVEL, not JUST METSATORE

mysql> FLUSH PRIVILEGES;

mysql> quit;

Pasted from <https://ccp.cloudera.com/display/CDH4DOC/Hive+Installation#HiveInstallation-ConfiguringtheHiveMetastore> 


7) Test connectivity

	 sudo -u hdfs hive -e 'show tables;'
	
8) Create Tweets table

<pre>CREATE EXTERNAL TABLE tweets (
  id BIGINT,
  created_at STRING,
  source STRING,
  favorited BOOLEAN,
  retweet_count INT,
  retweeted_status STRUCT<
    text:STRING,
    user:STRUCT<screen_name:STRING,name:STRING>>,
  entities STRUCT<
    urls:ARRAY<STRUCT<expanded_url:STRING>>,
    user_mentions:ARRAY<STRUCT<screen_name:STRING,name:STRING>>,
    hashtags:ARRAY<STRUCT<text:STRING>>>,
  text STRING,
  user STRUCT<
    screen_name:STRING,
    name:STRING,
    friends_count:INT,
    followers_count:INT,
    statuses_count:INT,
    verified:BOOLEAN,
    utc_offset:INT,
    time_zone:STRING>,
  in_reply_to_screen_name STRING
)
PARTITIONED BY (datehour INT)
ROW FORMAT SERDE 'com.cloudera.hive.serde.JSONSerDe'
LOCATION '/user/flume/tweets';
</pre>

Video 7: Apache Log File format and rotation
---------------

This is part 7 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we will setup and configure the Oozie workflow.

http://www.youtube.com/watch?v=QX_7k3Y4Sj0&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=7


1) cp /usr/lib/hive/lib/mysql*.jar /usr/lib/oozie/libext

2) Configure oozie to use MySQL https://ccp.cloudera.com/display/CDH4DOC/Oozie+Installation#OozieInstallation-ConfiguringOozietoUseMySQL

   a) Cofigure MySQL to Ooize using http://www.the-sandlers.com:7180/cmf/services/12/config

3) mkdir /home/oozie_lib

4) mkdir home/oozie_lib/oozie-workflows/lib

5) cp hive-serdes/target/hive-serdes-1.0-SNAPSHOT.jar oozie-workflows/lib

6) cp /usr/lib/hive/lib/mysql*.jar  oozie-workflows/lib

7) sudo cp /etc/hive/conf/hive-site.xml oozie-workflows

8) sudo chown hdfs:hdfs oozie-workflows/hive-site.xml

9) Install Oozie ShareLib

	a) https://ccp.cloudera.com/display/CDH4DOC/Oozie+Installation#OozieInstallation-InstallingtheOozieShareLibinHadoopHDFS

10) Grab the following files, put them to oozie_workflows

	a) wget https://raw.github.com/cloudera/cdh-twitter-example/master/oozie-workflows/add_partition.q

	b) wget https://raw.github.com/cloudera/cdh-twitter-example/master/oozie-workflows/coord-app.xml

	c) Wget https://raw.github.com/cloudera/cdh-twitter-example/master/oozie-workflows/hive-action.xml

	d) wget https://raw.github.com/cloudera/cdh-twitter-example/master/oozie-workflows/job.properties

		i) Edit Job properties (see c:\ec2\vm_install\job.properties)

11) sudo -u hdfs hadoop fs -mkdir oozie-workflows /user/hdfs

12) sudo -u hdfs hadoop fs -put oozie-workflows /user/hdfs/oozie-workflows

13) sudo -u hdfs hadoop fs -chown oozie:oozie /user/oozie

14) Get HTTPSource JARs

	a) cd /usr/lib/flume-ng/lib
	
	b) Wget http://repo1.maven.org/maven2/org/apache/flume/flume-ng-core/1.3.0/flume-ng-core-1.3.0.jar
	
	c) wget http://repo1.maven.org/maven2/org/apache/flume/flume-ng-sdk/1.3.0/flume-ng-sdk-1.3.0.jar

15) Start flume agent

	1) sudo -u hdfs hadoop fs -mkdir /user/flume/tweets

	2) sudo -u hdfs hadoop fs -mkdir /user/flume/facebook
           
           sudo -u hdfs hadoop fs -chown -R flume:flume /user/flume
	   
	   sudo -u hdfs hadoop fs -chmod -R 777 /
	
	a) /etc/init.d/flume-ng-agent restart
	
	   i) Make sure started cleanly
	
	   ii) Look at /var/log/flume-ng
	
	       One. If you see ConnectionRefused, check flume.conf and oozie-site.xml and hive-xml and coord-app.xml and job.properties, make sure host name (ip-<IP_ADDR>.ec2.internal is used, not localhost or hadoop1
	
16) Start Oozie 

	sudo /sbin/service oozie start
	
17) Start oozie workflow
	
	a) Alias start_oozie_wkf

18) Check logs to make sure it started OK

	a) /var/log/hdfs

19) Check HDFS to see if tweets are streaming

20) Query Hive to get counts of Tweets

21) Notice how we are an hour behind,  Why the wait?

Video 8: Real-time Access to Tweets
---------------
This is part 8 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Now we remove the wait condition from the Oozie workflow and avoid contention with the Flume temp files.

http://www.youtube.com/watch?v=5lJ02Xn7xOQ&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=8

See my blog for the steps

http://www.datadansandler.com/2013/03/making-clouderas-twitter-stream-real.html

Video 9: ODBC Connectivity to Hive and Basic HQL
---------------
This is part 9 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. Here we setup ODBC connections and run basic queries.

http://www.youtube.com/watch?v=zCxSHZn6mq4&list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G&index=9

Please use the video to setup the connection.

The queries can be found in the cloudera site.

http://blog.cloudera.com/blog/2012/09/analyzing-twitter-data-with-hadoop/

Video 10: Streaming Facebook & Sentiment Analysis
---------------
This is part 10 of a series that provides a guide for setting up CDH4 Hadoop in the Amazon cloud, and streaming social media to the environment. In the final installment, we show how to stream Facebook to Flume, how to perform sentiment analysis, and some final thoughts

http://www.youtube.com/playlist?list=PLPrplWpTfYTPU2topP8hJwpekrFj4wF8G

See my GitHub repository for the Facebook code ans intructions.

https://github.com/DataDanSandler/sentiment_analysis

