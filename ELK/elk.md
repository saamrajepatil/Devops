Create Amazon linux Ec2 with T2.large instance type.

Since the Elastic stack runs on java we need a JDK and openjdk is our choice here.

sudo yum install java-11-amazon-corretto-devel

Now we need to add the corresponding repositories with:


cat <<EOF | sudo tee /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-8.x]
name=Elasticsearch repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF
Press enter once more and you should have created the correct file.

Step 4 - import the gpg encryption key


sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
Step 5 - clean and update the system cache
Now we clean the system cache and update it afterwards. This makes sure that our system knows where to get the Elastic Stack components from. It also ensures that our machine can reach the repository sites necessary for step 6.

sudo yum clean all

sudo yum makecache
Step 6 - install Elasticsearch
Did someone say please start already? - ok here we go - install Elasticsearch.

sudo yum -y install elasticsearch
Step 7 - wait. wait. wait until its installed. ok?
Once Elasticearch was downloaded and installed you should see output similar to the screenshot below - make sure to copy the elastic password and save it somewhere secure (you can reset the password later as well - and actually... since you want to install everything without security... why would you need a password?! spoiler - you don't.)

elastic_password

Step 8 - making sure it works
Make sure that everything worked out by running

rpm -qi elasticsearch
which should give a similar output to this one

elasticsearch successfully installed

Half-way time
Wonderful, everything worked out exactly as we planned. If only this thing were running already, we could search for everything! (almost).

Conceptually, we now need to enable the Elasticsearch service and configure it to not use the security features that come per default since version 8.

disable the security features & change the configuration
Since you are still reading I assume you either know what you are doing, just want it to work because everything else failed, or you are a rebel. I like rebels.

You can use any editor and change the /etc/elasticsearch/elasticsearch.yml.

sudo nano /etc/elasticsearch/elasticsearch.yml
change the lines network.host, http.port and discovery.seed_hosts to the following:


network.host: 0.0.0.0

http.port: 9200

discovery.seed_hosts: []
You might need to remove the pound-signs/hashtags at the beginning of each line.

If you want to understand what exactly we are doing: We set the network host to allow access from any IP (0.0.0.0), set the Elasticsearch port to 9200 and clear the hosts (because currently there are none and there will be only one in this setup)

Last but not least look for the following line:


xpack.security.enabled: true
and make change that to flase


xpack.security.enabled: false
This disables the default security features (e.g. https and password/token-based authentication) and lets us use http (unencrypted) communication because we are lazy and don't like certificates.

enable and restart the Elasticsearch service

sudo systemctl enable --now elasticsearch.service

sudo systemctl restart elasticsearch.service
ITS ALIVE
If everything worked you should be able to test that everything is well via:


curl http://127.0.0.1:9200
and be greeted with similar outputelastic is alive

if for some reason this gives you an error / empty response make sure that elastic is running

systemctl status elasticsearch.service
In an ideal world it would look like this:

Elasticsearch service running

If the output is "inactive" try restarting the service manually and curl again

sudo systemctl stop elasticsearch.service
followed by

sudo systemctl start elasticsearch.service
Should all of this not lead to the expected result you can try to check the logs to see if you can pinpoint errors.

journalctl -xe
Kibana
Since Kibana is part of the elastic repositories we added earlier we can directly install it.


Copy

Copy
sudo yum -y install kibana
enabling the service
As with Elasticsearch we also need to enable the Kibana service so that it runs after reboot.

sudo systemctl enable --now kibana
configuring Kibana
Kibana needs to know which port it should run on (default is 5601), which Elasticsearch server it should connect to (we... well we have only 1 machine for everything currently, so probably that one as well). Remove the pound-sign/hashtag and change the following lines in the /etc/kibana/kibana.yml:



server.port: 5601

server.host: "0.0.0.0"

server.publicBaseUrl: "http://<your_server_ip_goes_here>:5601"

elasticsearch.hosts: ["http://127.0.0.1:9200"]
The first line tells Kibana to use its default port 5601. The next one defines who can access the Kibana instance (0.0.0.0 is everyone or the whole internet). The Server public base url is the address that you want to reach the Kibana server at. It is usually the IP of your Kibana server plus the port at the end. The last line tells Kibana where to look for the Elasticsearch server. In our case this would be the loopback address of our localhost.

(re)starting Kibana and testing everything
Once all of the steps above have been done you can restart Kibana, check if it is active and start collecting logs. We can achieve that with commands we already know:


sudo systemctl restart kibana 

systemctl status kibana
If Kibana is active we should be able to open a web browser now... but where?!

We need one more instance that actually we can collect logs from. We also might be interested to see the dashboard so let us catch two fish with one net.

We add a Windows Server 2019 or 2022 machine to our Snaplabs Network with 4GB RAM and 40GB disk space. Make sure that this one is in the same subnet.

Now you can open a browser and type:

http://<your-kibana-server-ip-here>:5601

and you should be greeted with the Kibana dashboard that looks like this:



____
Download and Install Filebeat
First, download the Filebeat package from Elastic's official website or use a package manager.

yum install filebeat
or 

wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.15.3-x86_64.rpm
sudo rpm -ivh filebeat-8.15.3-x86_64.rpm



2. Configure Filebeat
Edit the configuration file to specify how File beat should collect data and where it should send it.

Open the configuration file:

sudo nano /etc/filebeat/filebeat.yml
Copy
Modify the following sections:

Elasticsearch Output: Uncomment these lines if you want to send data directly to Elasticsearch.

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme

Enable Modules: File beat comes with several modules that simplify collecting logs from various services. To enable a module (e.g., system module):

sudo filebeat modules enable system 

Load index template in Elasticsearch:

sudo file beat setup --index-management -E output.elasticsearch.hosts=["localhost :9200"] -E output.elasticsearch.username="elastic" -E output.elasticsearch.password="your_password" 
Copy
Load dashboards into Kibana : bash sudo file beat setup --dashboards

3 .Start and Enable File Beat Service 
Start the File Beat service and enable it to start on boot : bash sudo systemctl start filebeat sudo systemctl enable filebeat

4 .Verify Installation 
Check status of the Filebeat service : bash sudo systemctl status file beat


_____________


Step-by-Step Guide to Create Custom Dashboards
1. Load Sample Data: Kibana provides sample datasets that you can use to create and visualize data on your custom dashboards.

Navigate to the Kibana home page.
Click on "Add Data."
Select one of the available sample datasets (e.g., eCommerce, Flights, Logs).
Click "Load [Dataset] Data."
2. Create Index Patterns: Ensure that index patterns are created for the loaded sample data.

Go to "Management" > "Stack Management" > "Index Patterns."
Click on "Create index pattern."
Follow the prompts to set up an index pattern for your dataset (e.g., kibana_sample_data_ecommerce*).
3. Design Your Dashboard: Now, you can start creating a custom dashboard.

Navigate to the "Dashboard" tab.
Click on "Create new dashboard."
4. Add Visualizations: Use various visualization tools provided by Kibana:

Charts:

Bar charts
Line charts
Pie charts
Tables:

Data tables
Metric visualizations
3 .Maps :  - Coordinate maps - Region maps

To add a visualization: 1 .Click “Add” button . 2 .Select type of visualization . 3 .Configure based on your dataset .

5.Save and Share Dashboard :  Once satisfied with design ,save it : 1.Click “Save” button at top right corner . 2.Give meaningful name description(optional) . You can also share this dashboard via link embed code 
