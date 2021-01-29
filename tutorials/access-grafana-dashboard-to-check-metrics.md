
### Access Grafana Dashboard and configure it to show metrics

Logged in to the Grafana UI and configure datasource.

![](_images/dashboard.png)

### Configure Your DataSource

Data source could be a database(MySQL) or a collection of metrics(Prometheus).
Here we will configure Grafana to connect MySQL database.In earlier steps we have created a database named "testdb" with a table: "Population".

***Create your datasource as MySQL***

 Complete the web form with your connection details given in below table:
 
| Parameter  |  Value           |                                                   
|------------|------------------|
| `Host`     | ##DNS.ip##:30685 | 
| `User`     | root             | 
| `Password` | password         | 
| `Database` | testdb           |
 


***Click on save and test***

 ![](_images/mysql-datasource-connection.PNG)


If everything is configured correctly, you should see a green box with the message "Database Connection OK".



### Create Your First Dashboard

Now database is connected, we can create a dashboard showing stats about the testdb database we are connected in the previous section.

1. Click on New dashboard.

2. Click on "Dashboard settings". Give the dashboard Name and click on "Save".

   ![](_images/rename-dashboard.png)

3. Give a meaningful Title to the Panel list such as "mariadb-database-metric".
  - Click on "Add panel". A new panel window will come.    
  - Click on "Add Query".  
   
  ![](_images/add-panel.png) 
  
  - Click on "General" option on left hand side. This will provide option to give a Title to the panel.
  
  ![](_images/general-option.png)
     
    
  ![](_images/panel-name.png)

4. From "Query" dropdown choose "MySQL".
    
   
   ![](_images/add-datasource-as-mysql.png)

5. Click on "Edit SQL".Add below query to fetch data from table "Population" from database "testdb":
   
   
   SELECT
     year as Year,population as Population
   FROM Population

   
   ![](_images/edit-sql.png)
   
  
   ![](_images/query-db-to-get-metrics.png)

6. Click on "Visualization" option to see metrics on different options like : Graph, Gauge, Bar Gauge etc.

  ![](_images/visualization.png)

7. According to the type of metrics we need to choose appropriate Visualization form. In this example we are using "Gauge" to see the MariaDB "testdb" database table:    "Population" data.

8. In calc option you can use the appropriate function to view the data.In below snapshot we are using "max" to check maximum population with the year details.

You have now created your first panel with a dashboard with a Gauge like below:


![](_images/mariadb-gauge-db-metrics-max-population.png)



### Creating a Prometheus data source

To create a Prometheus data source in Grafana:

- Click on the "cogwheel" in the sidebar to open the Configuration menu.

- Click on "Data Sources".

 ![](_images/prometheus-datasource-config-option.png)

- Click on "Add data source".

- Select "Prometheus" as the type.

- Set the appropriate Prometheus server URL : http://prometheus-operated.operators:9090 as mentioned in Grafana datasource yaml file: prometheus-datasources.yaml

- Click "Save & Test" to save the new data source.
  
  
 ![](_images/prometheus-datasource.PNG)
  

###  Creating a Prometheus graph

Follow the standard way of adding a new Grafana graph :

- Click on New dashboard.

- Click on "Dashboard settings".Give dashboard Name and click on "Save".

 ![](_images/Dashboard-name-setting.png)

- Click the "Panel Title", then click "Edit".

- Click on "General" option on left hand side. This will provide option to give a Title to the panel. 
   
      
  ![](_images/panel_list_name.png)


- Under the "Metrics" tab, select your "Prometheus" data source.


- Enter any Prometheus expression into the "Query" field, while using the "Metric" field to lookup metrics via autocompletion.
  For example say here we are executing query : "mysql_global_status_commands_total"  
  

- Tune other graph settings using "Visualization" option until you have a working graph.


- The following shows an example Prometheus graph configuration:

 ![](_images/metric-for-global-status-commands-total.png)


### Conclusion 
You can display all your data (even from multiple sources) in whatever format works best for you. There is a wide selection of visualizations built in and accessible through the community. You can customize your panels with color and transparency—whatever makes sense for your visual. You can even make your own visualization plugins if you want something a little more specific to your use case.

