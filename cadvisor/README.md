# InsightAgent: cadvisor
Agent Type: cadvisor

Platform: Linux

InsightFinder agent can be used to monitor system metrics of docker containers using cadvisor.

Tested with Ubuntu 14.04, Redhat 7.2, Centos 7.2.

Required docker version: 1.9.1 and later.

Required cadvisor version: 0.19.3 and later.

##### Instructions to register a project in Insightfinder.com
- Go to the [insightfinder.com](https://insightfinder.com/)
- Sign in with the user credentials or sign up for a new account.
- Go to Settings and Register for a project under "Insight Agent" tab.
- Give a project name, select Project Type as "Private Cloud".
- Go to Account Info by clicking on your user id at the top right corner of the webpage, and note the license key number.

##### Pre-requisites
Python 2.7.

This pre-requisite is needed on the machine which launches deployInsightAgent.sh.
For Debian and Ubuntu, the following command will ensure that the required dependencies are installed:
```
sudo apt-get upgrade
sudo apt-get install build-essential libssl-dev libffi-dev python-dev wget
```
For Fedora and RHEL-derivatives, the following command will ensure that the required dependencies are installed:
```
sudo yum update
sudo yum install gcc libffi-devel python-devel openssl-devel wget
```
Ensure cAdvisor is running on all hosts. Use the following command to check that the cadvisor container is present
```
sudo docker ps
```
Otherwise run cAdvisor using
```
sudo docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:latest
```




##### To deploy agent on multiple hosts

- Get the deployment script from github using the command
```
wget --no-check-certificate https://raw.githubusercontent.com/insightfinder/InsightAgent/master/deployment/deployInsightAgent.sh
```
and change the permissions with the command.
```
 chmod 755 deployInsightAgent.sh 
```
- Ensure all machines have the same login username and password.
- Obtain the IP address for every machine (or host) the InsightFinder agent will be installed on.
- Include the IP addresses of all hosts in **hostlist.txt**, entering one IP address per line.
- Deploy by running the following command:
```
./deployInsightAgent.sh -n USER_NAME_IN_HOST
                        -i PROJECT_NAME_IN_INSIGHTFINDER
                        -u USER_NAME_IN_INSIGHTFINDER
                        -k LICENSE_KEY
                        -s SAMPLING_INTERVAL_MINUTE
                        -r REPORTING_INTERVAL_MINUTE
                        -t AGENT_TYPE
AGENT_TYPE is *cadvisor*.
SAMPLING_INTERVAL_MINUTE and REPORTING_INTERVAL_MINUTE should be greater than or equal to 2 if number of containers in the host is greater than 10.
```
- When the above script is run, if prompted for password, enter either the password or the name of the identity file along with file path.
Example: /home/insight/.ssh/id_rsa


##### To get more details on the command, run 
```
./deployInsightAgent.sh
```

##### To undo agent deployment on multiple hosts:
- Get the script for stopping agents from github using below command:
```
wget --no-check-certificate https://raw.githubusercontent.com/insightfinder/InsightAgent/master/deployment/stopcron.sh
```
and change the permissions with the command.
```
 chmod 755 stopcron.sh
```
- Include IP addresses of all hosts in hostlist.txt and enter one IP address per line.
- To stop the agent run the following command
```
./stopcron.sh -n USER_NAME_IN_HOST -p PASSWORD

USER_NAME_IN_HOST - username used to login into the host machines
PASSWORD - password or name of the identity file along with path
```

##### To install agent on local machine
1) Use the following command to download the insightfinder agent code.
```
wget --no-check-certificate https://github.com/insightfinder/InsightAgent/archive/master.tar.gz -O insightagent.tar.gz
```
Untar using this command.
```
tar -xvf insightagent.tar.gz
```

2) **In InsightAgent-master directory,** run the following commands to install and use python virtual environment for insightfinder agent:
```
./deployment/checkpackages.sh
```
```
source pyenv/bin/activate
```

3) Run the below command to install agent.
```
./deployment/install.sh -i PROJECT_NAME -u USER_NAME -k LICENSE_KEY -s SAMPLING_INTERVAL_MINUTE -r REPORTING_INTERVAL_MINUTE -t AGENT_TYPE
```
After using the agent, use command "deactivate" to get out of python virtual environment.

##### To check raw data in host machines
- Login into the individual host machines.
- In the InsightAgent-master/data folder, all raw data will be stored in csv files. csv files older than 5 days are moved to /tmp folder.
- To change the retention period, edit the InsightAgent-master/reporting_config.json and change the "keep_file_days" to the required value.

