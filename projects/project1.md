## CPU & GPU usage

Create a dashboard to display the usage of the number of hours per user in the cluster using the ```sacctmgr``` command, you can follow these steps:

### Project Details:

- Use the ```sacctmgr``` command to collect cluster usage data, including hours of usage per user.
- You can run this command regularly and store the results in a CSV file or a database for regular data updates.
- Load the collected data using Python (e.g., with Pandas) for analysis.
- Create a Dashboard with Dash:        
  - Use Dash to create an interactive web dashboard.
  - Create a bar chart or any other type of chart to display hours of usage per user.
