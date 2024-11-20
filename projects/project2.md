## Resource Management System for Cluster Partitions

Develop an application that enables users to select a partition and automatically displays the number of available CPUs and GPUs in that partition. The application will leverage ```scontrol```, ```squeue```, and ```sinfo``` commands for data collection and use Dash for creating a user interface.

### Project Details:

- Utilize ```scontrol```, ```squeue```, and ```sinfo``` commands to gather information about the partitions and resources in the cluster.
- Analyze this data to identify the available partitions and determine the number of available CPUs and GPUs in each partition.
- User Interface with Dash:
  - Employ Dash to develop a web interface that allows users to choose a partition from the list of available partitions.
  - Implement a callback function to automatically update the number of available CPUs based on the selected partition.
