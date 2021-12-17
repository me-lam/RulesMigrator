# rules-migrator

tldr: Migrates rules from one stream to another. Built to help migrate PowerTrack rules to v2 Filtered stream. Also supports transferring rules between two v2 Filtered streams.

Migrating filters involves two stream systems, a 'source' system and a 'destination' system. Rules are transferred from the 'source' system to the 'destination' system. While the source system can be PowerTrack-based or v2 Filtered stream-based, only v2 Filtered stream destinations, by design, are supported. First written to help with PowerTrack migrations to v2, this tool is also designed to be a pure-v2 tool. This tool should help developers move sets of filters between different environments and to 'replay stream' endpoints. 

## Getting started

{getting an approved developer account}

There are several ways to use this tool to migrate streaming filters between real-time streams:
+ Run a simple command-line Python script. 
+ Start up a (React-based) web app with user interface used to configure the 'source' and 'destination' streaming systems. 

Either way requires some shared Python code that manages the rules migration process. So, the next step is all about getting the Rules Migrator project code and folders set up. 

### Setting up Rules Migrator project folder

 The ./lib/rules_migrator.py file defines three core objects and the tool's report and some features have the following defaults:

+ ./config/config.yaml - Default name and location of a YAML file used to define 'source' and 'destination' rules systems.
+ ./reports - Default location to write reports to. 
+ ./rules - Default location to read JSON rules files. This tool more typically reads in 'source' rules using the rules API, the tool can also read in a local file containg response JSON from the rules endpoints (either PowerTrack or v2 Filtered stream). 

{
* Clone repo or unpack project zip file to a 'rules_migrator' folder. 
  * Cloning steps:
  * Unzipping steps: 
}

Geting python environment set-up:
* Tool is written and tested with Python 3.10.
* Uses `requests` and `PyYAML` packages as noted in the requirements.txt project file. These packages can be installed by running the following commands in the project's root directory. 

```
pip3 install requests
pip3 install PyYAML
pip3 install -r requirements.txt
```
* Create logs directory
```
mkdir logs
```

### Testing the Rules Migrator tool

From the project's root folder try running the following command in the terminal:

```
python3 rules_migrator.py --help
```

If you get a message about how to use the tool, that's a good sign the tool is ready for use and it's time to start configuring it. 



## rule_migrator.py command-line script

The root folder of this project includes a ```rules_migrator.py``` Python command-line script. This script has the following command-line options:

```
Usage:
    rules-migrator [--config <config>] [--mode <mode>]
    rules-migrator --help
    rules-migrator --version

Options:
    -c --config CONFIG    Location of YAML configuration (file). Defines 'source' and 'destination' rules systems. Defaults to `./config/config.yaml`.
    -m --mode MODE        Options: `test` or `transfer`. Defaults to `test`.
    -h --help
    -v --version

    https://github.com/twitterdev/rules-migrator
```

Example usage:

* ```$python rules_migrator.py --mode test``` 

--> Retrieves 'source' rules, and tests them with the 'dry_run' option on the 'destination' v2 rules endpoint. Since 'test' is the default mode, this command is equivalent: ```$python rules_migrator.py```. Also, since no configuration filepath is provided, a default filepath of ```./config/config.yaml``` is used. 

* ```$python rules_migrator.py --mode transfer``` 

--> Retrieves 'source' rules, tests them with the 'dry_run' option, removes any that are unsupported with v2
  and writes them to the 'destination' v2 Filtered stream. 
  
* ```$python rules_migrator.py --mode transfer --config './my_configs/pt_prod_to_v2_staging.yaml``` 

--> Same as above except that a custom location and name of a configuration file is provided. This enables having multiple configuration files, each describing a unique source/destination combo.  
  
This `rules_migrator.py` script creates a single RulesMigrator object from the `/lib/rules_migrator.py` class file, and calls the `migrate` method. See below for more details of the core objects defined in that class file.   

### Configuration

When running as a command-line script, a YAML configuration file is used to define the 'source' and 'destination' streams (and their rules endpoints). This configuration file is also used to set options, such as the default 'mode' the tool runs in. 

The script uses `./config/config.yaml` as the default configuration filepath. This project includes an example configuration file at `./config/config.yaml:  

```yaml
source: 
  url: https://gnip-api.twitter.com/rules/powertrack/accounts/{ACCOUNT_NAME}/publishers/twitter/{LABEL}.json
  #url: ./rules/source/my_current_rules.jon

destination: 
  url: https://api.twitter.com/2/tweets/search/stream/rules
  #url:  ./rules/destination/rules_to_add.json

options: 
  mode: test #Other options: transfer #This will commonly be overwriten on command-line.
    
```

### Setting up authentication 

Twitter API credentials are read in from the local environment. Authentication details for two "rules" systems are needed to use this migration tool, one for a 'source' system and one for a 'destination' system. While 'source' systems can be either PowerTrack-based or v2-based, this tool is designed to only migrate rules to a v2 Filtered stream.

These details are needed:
+ SOURCE_TOKEN - Either a Bearer token or, for reading from PowerTrack stream, a username for BASIC authentication.
+ SOURCE_PASSWORD - For reading from PowerTrack, a password for BASIC authentication.
+ DESTINATION_TOKEN - Bearer token for App writing to a v2 Filter stream rules endpoint.

On Linux and MacOS, these can be set with the ```export``` command, e.g.:

```
export DESTINATION_TOKEN='AAAAAAAAAAAAAAAAAAAAAPPBearerToken"
export SOURCE_TOKEN='prod@here.com'
export SOURCE PASSWORD='NotMyPassWord'
```

When running as a command-line script, these variables need to be available in the local environment. 

This tool loads these tokens into a 'creds' dictionary: 

```python
creds['DESTINATION_TOKEN'] = os.environ.get("DESTINATION_TOKEN")
```

When running as a Flask App, these variables also need to be available from its local environment. If you are deploying this code on Glitch or Heroku, or basically anywhere on the cloud, securely storing credentials in the local environment is readily supported. 

Support for having multiple configuration files. This requires the reading of authentication tokens from more than two Filtered stream instances. Each config could provide a 'token name' pointer so we know what token to associate with a source system. 

```yaml
source: 
  url: https://api.twitter.com/2/tweets/search/stream/rules
  token_pointer: SOURCE_TOKEN_DEV 

destination: 
  url: https://api.twitter.com/2/tweets/search/stream/rules
  token_pointer: DESTINATION_TOKEN_STAGING
```


# (more) internal docs

Notes that a developer may be interested in: 


### RulesMigrator library and its core object

The heart of this code lives in the **/lib/rules_migrator.py** file. This file defines three core class:
+ **RulesRequester** - This object knows how to make requests to Twitter rules endpoints (PowerTrack and v2 Filtered stream).
+ **RulesSystem** - This object encapsulates a rules "system", and there is always a 'source' and a 'destination' system when migrating. Each object has its own requester object and also holds fundamental system stats.
+ **RulesMigrator** - This object exposes a 'migrate' method used by calling process (see below). This object also knows how to load credentials and make reports. 

In this project's main directory, there are these three files:

+ **rules_migrator.py** - This is the MVP command-line script that both tests and transfers rules. This script is a wrapped to the underlying /lib/rules_migrator.py class file. See below for example usage. 
+ **rm_app.py** = This is a Flask app that provides a web frontend to the underlying /lib/rules_migrator.py class file. It's main purpose is to provide a /migrate route for other web apps/UIs to call. Those callers need to pass a JSON object that describes the 'source' and 'destination' system and migration options. Migration options include the execution 'mode', either 'test' or 'transfer.' The test mode uses the v2 'dry run' option to check on the readiness of the source rules to be migrated to v2. The transfer mode actually transfers the source rules, automatically removing any unsupported rules if needed. 
+ **test_rm_app.py** - A simple example app that makes a *migrate* request to the rm_app Flask app.    

### RulesMigrator.migrate method

The migrate method provided by the RulesMigrator class is the "main" entry point for calling components. Both the command-line script and the Flask app have this code for providing configuration details and receiving back a report.

```python
from lib.rules_migrator import RulesMigrator

rm = RulesMigrator(config_json)
report = rm.migrate(mode)  # What mode are we running in? Testing rules? Transferring rules? 
```


### Report writing

The migrate method has a primary job of sending back a report. This method is called, a report is started, the tool builds a summary of actions taken and generates lists of different rule types (supported, unsupported, duplicate, and translated). These report attributes are marked up in JSON and returned to the caller (e.g. command-line script or Flask app). 

As the tool moves through the migration steps, it appends updates to a simple string 'summary' variable:

```python
summary = f"{summary}\n{ruleset_summary}"
```

Embedding new lines and tabs enables some level of formatting. Fine-tuning the final format is clumsy. Testing with very large data sets is key here. There needs to a limit to the number of rules any set has, e.g. the number of duplicate and supported rules in the report are limited to 20 examples.   



