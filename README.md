

installing and securing elasticsearch

what is elasticsearch? Elasticsearch is a highly scalable and flexible open-source search engine and NoSQL database that enables users to store, search, and analyze large amounts of data in real-time. It provides a RESTful API, supports multiple query languages, and features advanced analytics capabilities like faceted search, aggregations, and geospatial search. It also has a distributed architecture that allows for horizontal scaling and high availability.

What's the difference between an inverted index and index? An index is a simple mapping of keywords or terms to their corresponding document IDs, allowing for quick lookups of document IDs for a given keyword. This type of index is useful for basic search functionality, but it does not provide any additional information about the keyword's frequency or position in the documents. An inverted index, on the other hand, maps each keyword to a list of documents that contain the keyword, along with additional information such as the frequency and position of the keyword in each document. This information enables more sophisticated search operations, such as term frequency-inverse document frequency (TF-IDF) calculations, which are used to rank documents based on relevance.

Install elasticsearch

       sudo apt install default-jdk -y
       sudo apt-get install apt-transport-https -y
       curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
       echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
       sudo apt update -y
       sudo apt install elasticsearch -y

Secure elasticsearch with password

       sudo vim /etc/elasticsearch/elasticsearch.yml

add config

       xpack.security.enabled: true

Set password elasticsearch

       cd /usr/share/elasticsearch/
       sudo bin/elasticsearch-setup-passwords interactive

Config file elasticsearch

       sudo vim /etc/elasticsearch/elasticsearch.yml
       network.host: 0.0.0.0
       transport.host: localhost
       transport.tcp.port: 9300
       http.port: 9200
       discovery.type: single-node

Mount new disk on elasticsearch

       sudo fdisk /dev/sdb
       Run the  n command to create a new partition
       Select the partition number by typing the default number
       After that, you are asked for the starting and ending sector of your hard drive It is  best to type the default number in this section
       The last prompt is related to the size of the partition You can choose to have several sectors or to set the size in megabytes orgigabytes
       To write the changes on disk, run the w command
       sudo mkfs -t ext4 /dev/sdb1
       sudo mount /dev/sdb1 /var/lib/elasticsearch/

permanently mount disk

       sudo vim /etc/fstab
       UUID=<number block> 		/var/lib/elasticsearch 	auto 	defaults 	1 	0

Configure Iptables

       sudo vim /etc/iptables/rules.v4
       -A CHECK_INPUT -i {your_interface} -p tcp --dport 9200 -j ACCEPT
       sudo iptables-apply /etc/iptables/rules.v4

Start elasticsearch

       sudo systemctl restart elasticsearch.service

cluster health check The Elasticsearch Cluster Health API is a tool that allows you to monitor the health of your Elasticsearch cluster. It provides information about the number of nodes, active shards, status, and other relevant information that can help you assess the overall health and stability of your cluster. The API endpoint for retrieving cluster health information is _cluster/health. You can use a curl command like this to access the API

       curl -X GET -u elastic "localhost:9200/_cluster/health?wait_for_status=yellow&timeout=50s&pretty"

curl: This is the command line tool for transferring data using various protocols, including HTTP. -XGET: This option specifies the HTTP request method to use, in this case GET, which is used to retrieve data from the server.

       <Elasticsearch_URL>: This is the URL of your Elasticsearch instance, for example, http://localhost:9200

/_cluster/health: This is the API endpoint for retrieving cluster health information. The endpoint _cluster/health returns information about the health of the entire Elasticsearch cluster, including the number of nodes, active shards, and overall status. When you run this command, the response from the Elasticsearch API will be a JSON object that contains information about the cluster health.

get indices in elasticsearch You can retrieve a list of indices in Elasticsearch by sending a GET request to the _cat/indices endpoint. Here's an example of how to do it using curl

       curl -XGET -u elastic 'localhost:9200/_cat/indices?v' 

This will return a list of indices, including the index name, status, number of documents, and other information.
 
