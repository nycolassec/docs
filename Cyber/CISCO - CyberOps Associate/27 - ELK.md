The Elastic Stack consists of Elasticsearch, Logstash, and Kibana (ELK). It is a highly scalable and modular framework for ingesting, analyzing, storing and visualizing data.

Security Onion includes ELK and other components from Elastic including:
- **Beats** - This is a series of software plugins that send different types of data to the Elasticsearch data stores.
- **ElastAlert** - This provides queries and security alerts based on user-defined criteria and other information from data in Elasticsearch. Alert notifications can be sent to a console, or email and other notification systems such as TheHive security incident response platform.
- **Curator** - This provides actions to manage Elasticsearch data indices.

The core open source components of the Elastic Stack are Logstash, Beats, Elasticsearch, and Kibana, as shown in the figure.
![[Pasted image 20250307183434.png]]

**Elasticsearch**, which is the search engine component, uses RESTful web services and APIs, a distributed computing cluster with multiple server nodes, and a distributed NoSQL database made up of JSON documents.

**Logstash** enables the collection and normalization of network data into data indexes that can be efficiently searched by Elasticsearch. **Logstash and Beats** modules are used to ingest data into the Elasticsearch cluster.

**Kibana** provides a graphical interface to data that is compiled by Elasticsearch. It enables visualization of network data and provides tools and shortcuts for querying that data in order to isolate potential security breaches.

****

In Security Onion, the functionalities of SANCP have been replaced by PRADS, however the term SANCP is still used in the Sguil interface. PRADS collects the data, and a SANCP agent records the data in a SANCP data table.

The SANCP functionalities concern collecting and recording statistical information about network traffic and behavior. SANCP provides a means of verifying that network connections are valid. This is done through the application of rules that indicate which traffic should be recorded and the information with which the traffic should be tagged.

****

Elasticsearch ingests data into documents called indices and those documents are mapped to various datatypes using index patterns. The index patterns create a data structure of JSON-formatted fields and values. The datatypes in the fields can be in the following formats:
- Core Datatypes: Text (Strings), Numeric, Date, Boolean, Binary, and Range
- Complex Datatypes: Object (JSON), Nested (arrays of JSON objects)
- Geo Datatypes: Geo-point (latitude/longitude), Geo-shape (polygons)
- Specialized Datatypes: IP addresses, Token count, Histogram, etc.)










