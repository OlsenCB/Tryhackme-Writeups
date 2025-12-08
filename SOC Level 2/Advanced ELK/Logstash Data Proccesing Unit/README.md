# TryHackMe Room: Logstash: Data Processing Unit

This room was a deep dive into Logstash, the "L" in the Elastic Stack (ELK/Elastic Stack), which serves as the data processing pipeline. I focused on the deployment and configuration of the entire stack (Elasticsearch, Logstash, and Kibana) and, more importantly, learned the syntax and function of Logstash's critical input, filter, and output plugins necessary for data manipulation.

---

## Section: Elasticsearch: Installation and Configuration

### Task 1: What is the default port Elasticsearch runs on?

I examined the elasticsearch.yml configuration file on the server to find the network settings.

The default port for Elasticsearch is 9200.

[Screenshot 01: Elasticsearch default port and network host](./screenshots/01-es-port-host.png)

### Task 2: What version of Elasticsearch we have installed in this task?

I checked the running version of the service.

The installed version of Elasticsearch is 8.8.1.

[Screenshot 02: Elasticsearch version](./screenshots/02-es-version.png)

### Task 3: What is the command used to check the status of Elasticsearch service?

The standard systemd command used to check the operational status of the service is: systemctl status elasticsearch.service

### Task 4: What is the default value of the network.host variable found in the elasticsearch.yml file?

Checking the elasticsearch.yml file again, the default value configured for the network.host variable was 192.168.0.1.

---

## Section: Logstash Installation and Configuration

### Task 1: What is the configuration reload interval set by default in logstash.yml?

I checked the main configuration file for Logstash, logstash.yml, to find the default interval at which it checks for configuration changes.

The default configuration reload interval is 3s.

[Screenshot 03: Logstash configuration reload interval](./screenshots/03-logstash-reload-interval.png)

### Task 2: What is the Logstash version we just installed?

I verified the installed version of the Logstash service.

The installed version of Logstash is 8.8.1.

[Screenshot 04: Logstash version](./screenshots/04-logstash-version.png)

--- 

## Section: Kibana: Installation and Configuration

### Task 1: What is the default port Kibana runs on?

I examined the kibana.yml configuration file to determine the default network port used for the web interface.

The default port for Kibana is 5601.

[Screenshot 05: Kibana default port](./screenshots/05-kibana-port.png)

### Task 2: How many files are found in /etc/kibana/ directory?

I used the ls command in the terminal to list and count the files within the /etc/kibana/ directory.

There are 3 files found in the directory.

---

## Section: Logstash: Overview

### Task 1: Which plugin is used to perform mutation of event fields?

The plugin used for various modification operations on event fields, such as renaming or removing fields, is the Mutate plugin.

[Screenshot 07: Mutate plugin documentation](./screenshots/07-mutate-plugin.png)

### Task 2: Which plugin can be used to drop events based on certain conditions?

The plugin specifically designed to discard events that meet certain criteria (e.g., if a field is empty) is the Drop plugin.

[Screenshot 08: Drop plugin documentation](./screenshots/08-drop-plugin.png)

### Task 3: Which plugin parses unstructured log data using custom patterns?

The most powerful and common filter plugin for parsing messy, unstructured log data using regex-like patterns is Grok.

[Screenshot 09: Grok plugin documentation](./screenshots/09-grok-plugin.png)

---

## Section: Writing Configurations

### Task 1: Is index a mandatory option in Elasticsearch plugin? (yay / nay)

By reviewing the Elasticsearch output plugin documentation, I found that an index can be automatically generated if not specified.

The answer is nay.

[Screenshot 10: Elasticsearch index mandatory option (Nay)](./screenshots/10-es-index-mandatory.png)

### Task 2: Look at the file input plugin documentation; which field is required in the configuration?

Reviewing the documentation for the File input plugin, the field that specifies the file(s) to be monitored is mandatory.

The required field is path.

[Screenshot 11: File input plugin required path field](./screenshots/11-file-input-path.png)

### Task 3: Look at the filter documentation for CSV; what is the third field option mentioned?

I reviewed the documentation for the CSV filter plugin and found the third field option available for configuration.

The third field option mentioned is columns.

[Screenshot 12: CSV filter third field option (columns)](./screenshots/12-csv-columns.png)

### Task 4: Which output plugin is used in the above example?

The example configuration showed data being sent to the "L" in the ELK stack, the storage and search engine.

The output plugin used is elasticsearch.

[Screenshot 13: Elasticsearch output plugin example](./screenshots/13-es-output-example.png)

---

## Section: Logstash: Input Configurations

### Task 1: If we want to read a constant stream from a TCP port 5678, which input plugin will be used?

The plugin designed to receive event data over a TCP network connection is the TCP input plugin.

[Screenshot 14: TCP input plugin documentation](./screenshots/14-tcp-input-docs.png)

### Task 2: According to the Logstash documentation, the codec plugins are used to change the data representation. Which codec plugin is used for the CSV based data representation?

The codec plugin used for parsing data formatted as comma-separated values (CSV) is the CSV codec plugin.

[Screenshot 15: CSV codec plugin documentation](./screenshots/15-csv-codec-docs.png)

---

## Section: Logstash: Filter Configurations

### Task 1: Which filter plugin is used to remove empty fields from the events?

The filter plugin used to selectively remove fields (especially empty ones) from an event is Prune.

[Screenshot 16: Prune filter plugin documentation](./screenshots/16-prune-plugin.png)

### Task 2: Which filter plugin is used to rename, replace, and modify event fields?

The most versatile filter plugin for making changes to event fields is the Mutate plugin.

### Task 3: Add a filter plugin to rename the field " src_ip " to " source_ip ". Provide the fixed line as an answer.

I used the mutate filter's rename option to change the field name:

rename => { "src_ip" => "source_ip" }

---

## Section: Logstash: Output Plugins

### Task 1: Can we use multiple output plugins at the same time in order to send data to multiple destinations? (yay / nay)

Logstash is designed to support sending data to multiple destinations simultaneously.

The answer is yay.

### Task 2: Which Logstash output plugin is used to print events to the console?

The output plugin used for debugging and testing configurations by printing the processed events to the standard output (terminal) is stdout.

[Screenshot 17: Stdout output plugin documentation](./screenshots/17-stdout-plugin.png)

### Task 3: Update the following configuration to send the event to the Syslog server. Provide the missing options as an answer. (host, port, and protocol)

By checking the Syslog output plugin documentation, I identified the mandatory configuration options needed to send data to a remote Syslog server.

The answer format is: syslog,host,port

---

## Section: Logstash: Running Configurations

### Task 1: Which command is used to run the logstash.conf configuration from the command line?

The command to execute Logstash, specifying the configuration file path, is: logstash -f logstash.conf

### Task 2: What will be the input, filter, and output plugins in the below-mentioned configuration if we want to get the comma-separated values from the command line, and apply the filter and output back to the terminal to test the result?

This scenario requires reading data interactively, processing CSV data, and displaying the results for testing:

-Input: Reading from the terminal standard input (stdin).
-Filter: Parsing the data as CSV (csv).
-Output: Printing the results back to the console (stdout).

Answer format: input_plugin,filter_plugin,output_plugin The answer is stdin,csv,stdout.