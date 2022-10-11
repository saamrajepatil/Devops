Instance components
A SonarQube instance comprises three components:

SonarQube Instance Components
![alt text](https://user-images.githubusercontent.com/51783400/195004020-9e2af475-b1a6-4f19-ab6f-0e1baedffc5c.png)


The SonarQube server running the following processes:

a web server that serves the SonarQube user interface.
a search server based on Elasticsearch.
the compute engine in charge of processing code analysis reports and saving them in the SonarQube database.
The database to store the following:

Metrics and issues for code quality and security generated during code scans.
The SonarQube instance configuration.
One or more scanners running on your build or continuous integration servers to analyze projects.
