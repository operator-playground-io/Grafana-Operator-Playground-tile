
### Access Grafana Dashboard and configure it to show metrics

Once you logged in to the Grafana dashboard, you will see dashboard as below snapshot:

![](_images/dashboard.png)

### Configure Your DataSource

We are going to configure DataSource.
Data source could be a database(MySQL) or a collection of metrics(Prometheus).
In earlier steps we have created a database named "testdb" with a table: "Population".
Here we will configure Grafana to connect MySQL with the DB and table.

***Create your datasource as MySQL***

***Steps:***

1. Click on the Option "DATA SOURCES" shown on Home Page of dashboard.See below snapshot for reference:

 ![](_images/configure-datasource.png)
 
2. Type "MYSQL" in the search box.Then click on "Select" option of MYSQL datasource.See below snapshot for reference:

![](_images/add-data-source-mysql-select.png)

3. Complete the Data Sources/MYSQL web form with your connection details given in below table:
 
``` 
| Parameter |    Value               |                                                   
|-----------|------------------------|
| Host      |   ##DNS.ip##:30685  | 
| User      |   root                 | 
| Password  |   password             | 
| Database  |   testdb               |
``` 

4. Click on "Save & Test".

 ![](_images/mysql-datasource-connection.PNG)


  If everything is configured correctly, you should see a green box with the message "Database Connection OK".



### Create Your First Dashboard

Now database is connected, we can create a dashboard showing stats about the "testdb" database.

***Steps:***

1. Click on the Dashboards Home.See below snapshot for reference:

![](_images/dashboard-home-option.png)

   You will see dashboard like this:

![](_images/create-new-dashboard.png)

2. Click on "DASHBOARDS" to create dashboard.You will see "New dashboard" window as below:

![](_images/dashboard-setting-option.png)

   Click on the "dashboard settings" icon.

3. Give a meaningful dashboard Name and click on "Save dashboard".See below snapshot for reference:


![](_images/Dashboard-name-setting.png)

4. Click on "Add panel" as shown in below snapshot. "Add new panel" window will come. 

![](_images/Add-Panel-option.png)


5. Click on "Add new panel" option as shown in below snapshot:


![](_images/Add-new-panel-mysql.png)

6. Edit Panel window will appear. Provide a meaningful name in the "Panel title" as shown in below snapshot:

![](_images/add-panel-name.png)

7. From "Query" dropdown choose "MySQL" and click on "Edit SQL".

![](_images/query-mysql.png)

8. Add below query to fetch data from table "Population" from database "testdb":      
   
   SELECT
     year,
     population
   FROM Population
   
9. Select "Format as":"Table" and run the query by "Shift+Enter".Refer below snapshot:

![](_images/mysql-query-to-get-mariadb-metrics.png)

10. Click on "Visualization" option to see metrics on different options like : Graph, Gauge, Bar Gauge etc.

    According to the type of metrics,we need to choose appropriate Visualization form. 

    In this example we are using "Gauge" to see the metrics of MariaDB.


11. Under "Display" section,using Calculation option you can use the appropriate function to view the data.In below snapshot we are using "Max" to check maximum population with     the year details.

12. Click on "Apply" button.This will save your panel metric on dashboard.

    You have now created your first panel with a dashboard with a "Gauge" like below:

![](_images/mariadb-metric-mysql-maxvalpopulation.png)



### Creating a Prometheus data source

To create a Prometheus data source in Grafana follow below steps:

1. Click on the Dashboards Home.See below snapshot for reference:

![](_images/dashboard-home-option.png)

  You will see dashboard like this:

![](_images/create-new-dashboard.png)

2. Click on "DASHBOARDS" to create dashboard.You will see "New dashboard" Option as below:

![](_images/dashboard-setting-option.png)

  Click on the "dashboard settings" icon.

3. Give a meaningful dashboard Name and click on "Save dashboard".See below snapshot for reference:


![](_images/create-new-datasource-prom.png)
 
3. You will see "Add data source" on the UI.Type "Prometheus" in the search box and click on "Select" button.See below snapshot for reference:

![](_images/prometheus-data-source.png)

4. In the Data Sources/Prometheus web form set the appropriate Prometheus server URL : http://prometheus-operated.operators:9090. 
   This URL is mentioned in Grafana datasource yaml file: prometheus-datasources.yaml 

5. Click "Save & Test" to save the new data source.
  
  
 ![](_images/prometheus-datasource-configuration.png)
 
 
   If everything is configured correctly, you should see a green box with the message "Data source is working".
 
 ![](_images/prometheus-configuration-success.png)
  

###  Creating a Prometheus graph

Now Prometheus Data Source is configured, we can create a dashboard showing stats about the Prometheus monitoring metrics for MariaDB Server.

Follow the standard way of adding a new Grafana graph :

1. Click on the Dashboards Home.See below snapshot for reference:

![](_images/dashboard-home-option.png)

  You will see dashboard like this:

![](_images/create-new-dashboard.png)

2. Click on "DASHBOARDS" to create dashboard.You will see "New dashboard" Option as below:

![](_images/dashboard-setting-option.png)

   Click on the "Dashboard settings" option.

3. Give a meaningful dashboard Name and click on "Save dashboard".See below snapshot for reference:


![](_images/new-dashboard-name-prometheus.png)

4.  Click on "Add panel" as shown in below snapshot. "Add new panel" window will come. 

![](_images/add-panel-prom.png)


5. Click on "Add new panel" option as shown in below snapshot:


![](_images/add-new-panel-prom.png)

6. Edit Panel window will appear. Provide a meaningful name in the "Panel title" as shown in below snapshot:

![](_images/add-panel-name.png)


7. From "Query" dropdown, select your "Prometheus" data source.Please refer snapshot given in step 9.


8. Enter any Prometheus query in the "Metric" field to lookup metrics via autocompletion.
   For example: we are executing query : "mysql_global_status_commands_total"  
   Please refer snapshot given in step 9.
 
9. Click on "Visualization" option to see metrics on different options like : Graph, Gauge, Bar Gauge etc.

   According to the type of metrics,we need to choose appropriate Visualization form. 

   In this example we are using "Graph" to see the metrics of MariaDB.

![](_images/monitoring-graph-prometheus.png)

10. Under "Display" section you can use the appropriate setting to view the graph in effective way.Please refer below snapshot for display section.

![](_images/monitoring-graph-display-settings-prom.png)

   Click on "Apply" button.
    
   You have now created your dashboard with Graph like below:

![](_images/monitoring-graph-prometheus.png)


### Conclusion 
You can display all your data (even from multiple sources) in whatever format works best for you. There is a wide selection of visualizations built in and accessible through the community. You can customize your panels with color and transparency—whatever makes sense for your visual. You can even make your own visualization plugins if you want something a little more specific to your use case.

