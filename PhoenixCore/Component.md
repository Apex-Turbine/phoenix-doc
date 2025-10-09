# Overview
The Component folder provides the base classes required to create and use components. This consists of the following files and classes. Please see the class API documentations for further detail of individual functions.

## Component

This file contains the "Component" base class. This class is used to provide an interface for each component and must be inherited and implemented.

## ComponentCallback
This file contains the base class for implementing a callback object for the component. This provides the "ComponentCallbackItem" base class which provides virtual functions for sending messages, propogating commands or requests, and retrieving the available commands and requests. This file also provides the "ComponentFunctionalCallbackItem" convienance class that allows providing lambdas as the callback.

The callback item is used by the component to allow communication from the component to the object managing it.

## ComponentManager
This file provides the implementation of the "ComponentManager" class. This class is responsible for registering available components, creating component instances, connecting data between components, and parsing a JSON Graph object into a data pipeline with the components requested in the graph. 

The ComponentManager creates and manages a threadpool. This threadpool is used to perform component tasks and queue results for the next component task.

The ComponentManager class will use the ComponentLibraryManager to load available components and register them into its memory. Custom implementations can also create components and register them directly with the manager. Given a JSON, the manager will parse the JSON, find the requested components based on the type UUID and create their instances. Next, it will determine the data connections between the components and connect them using a ComponentCallbackItem and a ComponentSubscriptionHandler.

The ComponentManager will also allow the initialization of a ComponentRemoteManager. The ComponentRemoteManager allows a networked device to control and activate the functions of the ComponentManager.

## ComponentRemoteManager

This file provides the ComponentRemoteManager class. The remote manager allows remote control of the ComponentManager. This is performed using a ZMQ request/reply socket pattern. The class requires an instance of a ComponentManager.

## ComponentSubscriptionHandler
This file contains the implementation for the ComponentSubscriptionHandler class. This class is used by the ComponentManager to connect data between components. This works by the class creating a data queue. When items on the data queu are pushed on, the class will queue the attached component to process the data as a task on the thread pool. This class restricts the work such that the current working task must complete and the data be popped off the queue before it enqueus another processing task for the next data item on the data queue. This class requires a Component and a threadpool object. Additionally, the subscription handler is designed to handle a single data stream. Therefore, it is not advisable to create a single subscription handler to process multiple data streams input from the sending component. This ensures data integrity in a multi-threaded environment.

## ComponentLibrary
This file contains the class definitions for ComponentLibrary, ComponentAdapter, and the ComponentLibraryManager.

### ComponentAdapter
The ComponentAdapter class contains the name of the component as a standard display name, the UUID for the component, a JSON object that describes the component, and a creation function to create an instance of the component. The adapter is used to register the component into the system and provide the means to create a component instance.

### ComponentLibrary
The ComponentLibrary class is a simple container class that contains a list of component adapters. A single DLL should contain a single ComponentLibrary Class This class simply contains the name to identify the library and a function to retrieve the available component adapters.

### ComponentLibraryManager
This class provides the means to load DLL's from provided paths and load the component library contained in each DLL. There is also a global function "getComponentLibraryManager" that creates a static instance of a ComponentLibraryManager for common use throughout the application.

### DLL Structure
A created DLL should export a single function "GetComponents" that returns a pointer to a ComponentLibrary object. The ComponentLibrary object is then used to load the ComponentAdapters within the library. The ComponentAdapters can then be used in the system to create and use components.

# Creating Components
Components can be created in C++ natively or in Python and C# using the SWIG wrapped API. To best explain the process of creating a component, we will walk through a simple example. This example will create a component as a processor that performs a simple scaling operation on the incoming data. While this is an example of a processor, it should be noted that there are 3 classes of components:

1. Source: This is defined as a root component that is a producer of data for the system
2. Processor: This component type receives data from other components, performs a task, and then sends new data to the next node in the data pipeline.
3. Ouput: This component type is defined as a receiver of data from other components and outputs the data external to the data pipeline. This can be a network streamer or file writer as examples. 

This walkthrough will detail these further as we go through and provide information for how they are defined.

## Defining the Class
In this example, we will create a comonent class: "CustomScalar". This class will inherit from the base Component class and implement required functions. We will provide a full example here, and will override all the available virtual functions to provide a full context for this example. This will not be needed fully in most cases.

```c++
#include <PhoenixCore/Component/Component.hpp>

class CustomScalar : public Component
{
public:
    struct customScalarStream_t : public stream_info_t{
        customScalarStream_t(GraphJson::StreamJson& strm, uint64_t index);
        ~customScalarStream(){}
        double scale = 1.0;
        double offset = 0.0;
    };
public:
    CustomScalar(const std::string& name);
    ~CustomScalar();
public:
    bool import(GraphJson::SourceJson& info, const std::vector<GraphJson::SourceJson>& parents) override;
    bool setup(std::shared_ptr<const GraphJson> graph, GraphJson::SourceJson& info) override;
    void start(const uint64_t& timestamp=0) override;
    void stop() override;
    void reset() override;
public:
    bool setupStreams(GraphJson::SourceJson& sj) override;
    bool setupStream(GraphJson::StreamJson& strm, uint64_t idx) override;
    bool validateStreams(const GraphJson::SourceJson& sj) override;
    bool validateStream(const GraphJson::StreamJson& sj) override;
    bool checkDuplicateNames(const std::vector<GraphJson::StreamJson>& streams) const override;
    bool checkUnits(const std::vector<Unit>& unitsInput, const JSON& unitsSupported) const override;
    bool checkDataTypes(const Message::data_types_t& dataTypesInput, const JSON& dataTypesSupported) const override;
    bool checkMsgTypes(const Message::msg_types_t& msgTypesInput, const JSON& msgTypesSupported) const override;
    bool mapStreams() override;
    bool typeCheck(const JSON& info, const GraphJson::StreamJson& strm) const override;

    std::string selfTest() override;
    void process(const Message &msg) override;
    const JSON info() override;

    std::vector<std::string> commands() const override;
    std::vector<std::string> requests() const override;
    JSON command(const JSON &) override;
    JSON request(const JSON&) override;

};

```

The above class definition provides all virtual overrides available for the component except for the Automations. The Automations will be described in another document. Next, we will go through the class implementation and describe what is being done.

## Defining the Component
The component is defined by a few informational objects. These define the component type ID, general information about the component, and available settings for the component.

### Component UUID
The component should contain a non-changing UUID as a string that represents the type. UUID's are used to avoid clashes with other defined components. While the API technically allows any string to represent the component type, a UUID is highly recommended. The best method is to use http://uuidgenerator.net to generate a UUID and copy it to your source implementation. This should be Version 4 and without braces as normal usage. In our example, we will define this in the class as a static const std::string variable in our implementation file.

```c++
static const std::string comp_uuid = "39d09444-da10-426c-a8d1-40e605008c15";
```

### Component Info
Next, we need to define the ComponentInfo. This is a JSON object and Phoenix uses nlohmann::json as the JSON parser/library. (https://json.nlohmann.me/). There are a few methods to define the JSON such as having it as a string in an included text file, but we will define it directly within the c++ using the syntax defined by nlohmann JSON.

Every item in the information object must be defined and is used in some way. Additionally, every component MUST support the following commands and requests:

1. Requests:
    1. status
1. Commands:
    1. start
    2. stop
    3. reset

These are normally handled in the base class by default. They are mentioned here due to potential overrides. The DX+ Platform requires these in order to provide informational displays to the user during operation. For file based inputs, an additional request of "progress" is needed as a percentage to provide progress feedback. This is not required, but is helpful in this case. 

An example JSON of the informational can be seen below. in C++ we will recreate this as a static const object in the implementation file.

```json
{
    "name": "Custom Scalar",
    "uuid": "39d09444-da10-426c-a8d1-40e605008c15",
    "vendor": "APEX",
    "description": "A custom scalar processor",
    "class": "processor",
    "class_type": "processor",
    "version": "2025.9.9",
    "feature": "custom-processor",
    "attrbutes": "CustomScalar_attrs.json",
    "helpfile": "CustomScalar.md",
    "requests": ["status"],
    "commands": ["start", "stop", "reset"],
    "supported_inputs": {
        "data_types": ["integer", "number"],
        "message_types": ["vector", "entry"],
        "units": [
            {"domain": "time", "name": null},
            {"domain": null, "name": null}
        ]
    }
}
```

Now, lets go through the information and cover its meanings. 

First, we have the Name. This is simply the display name for the component. Next, is the UUID which is the unique identifier for the component type in the system. The vendor and description fields provides the information for who created it and describes what it is/does. 

The class is either "source" meaning a producer of data as a means to bring external data into the data pipeline, "processor" meaning a processor of data, or "output" meaning a consumer of data as a means to export the data external to the data pipeline. The next field "class_type" further defines the specified class. For sources and outputs, this can be "device", "file", "store", "dbms", "web" or "stream". Others may be later added. For Processors, there is only "processor" as the class_type. 

The feature field defines the feature type to be checked out in the Apex License system. If a feature is not defined, the component will not load. This must be "custom-processor" for a processor and will check out ANALYSIS tokens. Other features are "custom-reader", "custom-device", "custom-writer", or custom-network. 

The attributes and helpfile fields provide the file name for the attribute definitions and the help document for the component. The attributes define the settings for the component, and DX+ will display these to the user in the system; the helpfile is the markdown file for the component. These will be detailed in the next section. The requests and commands are the available requests and commands that the component supports. 

Lastly, we have the component's defined supported inputs. This provides information that the component only allows specific data types and units to be allowed. a designation of "any", null, or "*" provides that anything is allowed of that type. This is meant to be generic. The actual checks occur in the type check functions which can be overloaded. By default, the base class will check the stream JSNO information that is being set as an input and ensure the inputs match what is described here.

Current Data Types:
1. boolean
1. integer
1. number
1. complex
1. string
1. struct
1. message

Current Message Types:
1. entry
1. vector
1. matrix
1. tensor
1. point2d
1. pont3d
1. video
1. json
1. bson

The units must be specified with the unit domain and the specific unit name. In this example, we have 2 units given in the array. The first unit is always the reference dimension. For time series data, this is time and normally seconds would be the unit name. Then we have the data unit type. In this example we use null in both domain and name to signal that data of any unit domain and name is accepted, but must be in reference to the time domain. As we add units to this list, we increase the number of data dimensions we are expecting. For example, here we can accept volt data that is in reference to time. Likewise we could add a second dimension of angle, and that would require input of 2 dimensional data (volts+angle) in reference to time.

### Component Attributes
The component attributes is a JSON Schema document that details the settings for the component. This should follow the JSON-Schema syntax provided by http://json-schema.org. Below is an example schema:

```json
{
    "$schema": "http://json-schema.org/draft-05/schema#",
    "definitions": {
        "component_settings": {
            "type": "object",
            "description": "The Settings for the Component",
            "properties": {
                "cs_overall_scalar": {
                    "title": "Scalar",
                    "type": "number",
                    "description": "The scalar to apply to the incoming data",
                    "$require_import": false
                },
                "cs_overall_offset": {
                    "title": "Offset",
                    "type": "number",
                    "description": "The offset to apply to the data",
                    "$require_import": false
                }
            }
        },
        "component_stream_settings": {
            "type": "object",
            "description": "The settings for each stream individually",
            "properties": {
                "cs_stream_scalar": {
                    "title": "Stream Scalar",
                    "type": "number",
                    "description": "The scalar to apply to the incoming data for individual stream",
                    "$require_import": false
                },
                "cs_stream_offset": {
                    "title": "Stream Offset",
                    "type": "number",
                    "description": "The offset to apply to the data for individual stream",
                    "$require_import": false
                }
            }
        }
    }
}
```

The attributes JSON defines properties in 2 areas. The first is the compoennt_settings and the second is the component_stream_settings. As the name suggests, the former defines settings/properties that are meant to be applied to the component as a whole, while the latter defines settings/properties that can be applied individually to each stream. This file must be placed or referenced from the "schemas" directory in the Phoenix installation location. i.e. "Phoenix/bin/components/schemas".

For each property, the standard JSON-Schema attributes are required such as the type, a description, and preferrably a title. Lastly, there is a custom flag in this denoted as "$require_import". This is a flag that requires the import function to be called whenever a change occurs for the specified property.

### Component Help
The help file specified is a file containing markdown style help information. This will allow the DX+ application to display the help along side the settings of the component. This file should be placed or referenced from the doc directory in the Phoenix install location specific to its class type. i.e. Phoenix/doc/phoenix-doc/Components/Processors.

```markdown
# Intro
This is the component.

# Settings
The settings are:
1. scalar
2. offset

# Usage
```

### Source Components for Post Processing
When creating components to read stored data for processing, there are a few additional items needed for the information. First, as stated prior, the "class_type" of these can either be:

1. file - indicating the data is from a flat file and contains in normal circumstances a single data record
2. store - indicating the data is stored in a database file such as sqlite and can contain multiple records
3. dbms - indicating the data is stored in a database management system and provides an interface for connection and data extraction. This can also contain multiple data records.
4. web - indicating the data is accessed through a web api.

For the file and store type, the information JSON must provide the extensions supported for them. For the tore, dbms, and web type, they must provide the query_type field indicating the expected query language that can be supported. an example with comments can be seen below:

```json
{
    "name": "My File Reader",
    "vendor": "Apex",
    "uuid": "00000000-0000-0000-0000-000000000000",
    "description": "file reader",
    "class": "source",
    "class_type": "file", //file, store, dbms, web
    "extensions":[".abc","abcx","abcz"], //Required for file and store
    "query_type": null, //Required for store, dbms, and web and is meant to indicate query language: sql, nosql, etc
    "feature": "myfile-reader",
    "version": "yyyy.mm.dd",
    "attributes": "myFileReader_attrs.json",
    "helpfile": "myFileReader_help.md",
    "requests": ["progress"],
    "commands": [],
    "supported_inputs": null
}
```

## Implement Constructor and informationals
Here we will implement the component constructor and the informational functions for the class. The Constructor will make use of the UUID we defined previously as comp_uuid and supply that to the base class.

```c++
static const JSON info = JSON::parse(json_string);

CustomScalar::CustomScalar(const std::string& name) : Component(name, comp_uuid) {
    //Nothing to initialize here
}
CustomScalar::~CustomScalar() { reset(); }
const JSON CustomScalar::info() { return info; }
std::vector<std::string> commands() { return {"start", "stop", "reset"}; }
std::vector<std::string> requests() { return {"status"}; }

```

## Implement Setup information
The import and setup function implement the core piece for component operation. Due to this, the procedure is multi-steppd. The below chart depicts the procedure.

```mermaid
graph TD
    n1["DX+"] --> n2["import"]
    n2 --> n1
    n1 --> n3["setup"]
    n3 --> n4["setupStreams"]
    n4 --> n5["setupStream"]
    n3 --> n6["validateStreams"]
    n6 --> n12["checkDuplicateNames"]
    n6 --> n7["validateStream"]
    n7 --> n8["typeCheck"]
    n8 --> n9["checkUnits"]
    n8 --> n10["checkDataTypes"]
    n8 --> n11["checkMsgTypes"]
```
The normal process is to first call the import function and supply the GraphJson::SourceJson information of the parents of the component. For source components, the parents list would be empty. For processors and outputs, the parents list would be the list of components that are being connected to the processor/output. The purpose of import is to retrieve the streams list that should be expected as output from the component. After import is called, additional settings may be changed/added for the component and streams. Afterward, setup is called. Setup is called in the ComponentManager class which in turn calls the set-up function for each component. The setup function requres the passing of the GraphJson pointer and the reference of the SourceJson of the component. The setup function can use the graph as reference, but cannot modify. However, it is expected for the setup function to modify the SourceJson as needed. The base class implementation of setup will extract and validate the existance of the streams being input into the component, then call setupStreams that will loop through the the stream list and call setup stream. After the setup portion is done, the type checks occur. The setup function will call "validateStreams" which will first call checkDuplicateNames on the sourceJson object to ensure all names for the streams being output is unique, and then loop through the stream list and call validateStream. In the base class validate stream function, it will find the stream that is the input for the component that is the source for the output, extract the information for the input stream along with the "supported_inputs" JSON from the component's information JSON and supply these to the typeCheck function. This function will validate the units, message type, and data type for the data coming into the component. 

Given this, it is highly recommended that any overload of any of these functions should provide the specialized implementation and call the base class function. This is recommended to keep the setup procedure aligned with the expected flow. 

### C++ Implementation
```c++
customScalarStream_t::customScalarStream_t(GraphJson::StreamJson& strm, uint64_t index) : stream_info_t(strm,index){
    //This allows extraction and saving of stream specific settings
    this->scale = PhoenixJson::getDouble(strm.settings()["cs_stream_scalar"],1.0);
    this->offset = PhoenixJson::getDouble(strm.settings()["cs_stream_offset"], 0.0);
}
bool CustomScalar::import(GraphJson::SourceJson& sj, std::vector<GraphJson::SourceJson> parents){
    //Base class implementation will set streamlist on sj as any supported data, message, unit types from parent
    //Specialize based on need
    return Component::import(sj, parents);
}
bool CustomScalar::setup(std::shared_ptr<const GraphJson> grph, GraphJson::SourceJson& sj){
    //First extract settings
    auto& settings = sj.generalSettings();
    //Use PhoenixJson helpers to get the settings
    this->m_globalScalar = PhoenixJson::getDouble(settings["cs_overall_scalar"],1.0);
    this->m_globalOffset = PhoenixJson::getDouble(settings["cs_overall_offset"], 0.0);
    return Component::setup(grph,sj);
}
bool CustomScalar::setupStreams(GraphJson::SourceJson& sj){
    return Component::setupStreams(sj);
}
bool CustomScalar::setupStream(GraphJson::StreamJson& strm, uint64_t index){
    //Here we have created a specialized stream info class to hold settings specific to streams in this component
    //We call base class first to fill out stream info from parent and ensure parent stream's existance
    //This provides a stream json copy from parent merged and overriden with the stream json in this component
    if(!Component::setupStream(strm,sidx)) return false;
    //Now we create the stream info object and save it to our internal stream list (variable in base class)
    //If the stream is disabled we set a nullptr for the stream in the stream list
    //For the component to work, This function MUST be implemented!
    m_streams[strm.name()] = strm.isEnabled() ? std::make_shared<customScalarStream_t>(strm,index) : nullptr;
    return true;
}
bool CustomScalar::validateStreams(const GraphJson::SourceJson& sj){
    return Component::validateStreams(sj);
}
bool CustomScalar::validateStream(const GraphJson::StreamJson& strm){
    return Component::validateStream(strm);
}
bool CustomScalar::typeCheck(const JSON& info, const GraphJson::StreamJson& strm) const{
    return Component::typeCheck(info,strm);
}
bool CustomScalar::checkDuplicateNames(const std::vector<GraphJson::StreamJson>& streams) const{
    return Component::checkDuplicateNames(streams);
}
bool CustomScalar::checkUnits(const std::vector<Unit>& unitsInput, const JSON& unitsSupported) const{
    return Component::checkUnits(unitsInput, unitsSupported);
}
bool CustomScalar::checkDataTypes(const Message::data_types_t& dataTypesInput, const JSON& datatypesSupported) const{
    return Component::checkDataTypes(dataTypesInput, dataTypesSupported);
}
bool CustomScalar::checkMsgTypes(const Message::msg_types_t& msgTypesInput, const JSON& msgTypesSupported) const{
    return Component::checkMsgTypes(msgTypesInput, msgTypesSupported);
}
```

> Please note that out of these functions the one required to be implemented (with the exception of source type components) is setupStream. This function requires the implementer to create an instance of stream_info as a shared pointer to be saved in the base class's stream map. This is later used by the start, mapStreams, and process functions.

For the Import and Setup function, these functions must set the status of the component to the following states. This will provide information to DX+ as to the state of the component configuration.

```c++
m_health["status"] = "importing"; //indicating import progress
m_health["status"] = "configuring"; //indicating the component is being setup
m_health["status"] = "ready"; //Successful setup and ready to start
m_health["status"] = "error"; //indicating error state
```

### The Import Function
The import function as previously mentioned is used to pull the available data streams and add them the passed SourceJson object. The GraphJson classes provide some helper funtions to assist in the requirements for this function. First, the SourceJson class provides the "importStreams" function which will take the passed parents as arguments and pull the streams from the parents into the SourceJson object. This function will attempt to match any existing streams defined in the SourceJson and re-apply their settings. Additionally, it will check for naming duplicates and defaultly rename the output. When this function is called, one of the arguments is the reference to the SourceJson object. It is recomended to save a temporary copy of this object specifically to save the streams list that may be already present. Then after the streams from the passed parents or through the device are imported into the SourceJson object, use the << operator to merge the temporary with the updated. This will find the matching streams and re-apply settings/names that may have been previously set.

```c++
bool CustomScalar::import(GraphJson::SourceJson& sj, std::vector<GraphJson::SourceJson> parents){
    auto srcTmp = sj;
    sj.importStreams(parents);
    sj << srcTmp;
    return true;
}
```

### The SourceJson and StreamJson
The import and setup functions should ensure the SourceJson and StreamsJson objects are up to date with their information. Please see the below example of a fully filled out SourceJson object that should be the result of this function with one stream.

```json
{
    "name": "{000000-0000-0000-0000-000000000000}",
    "type": "111111-1111-1111-1111-111111111111",
    "logicalName": "MyComponent",
    "enabled": true,
    "settings": {
        "setting1": "val1",
        "setting2": 2,
        "streams": [
            {
                "originName": "devcode0001",
                "name": "Stream1",
                "group": "group1",
                "delta": 0.1,
                "mtype": "vector",
                "dtyoe": "number_32",
                "enabled": true,
                "type": "stream",
                "streamclass": 3,
                "vars": null,
                "uniform": true,
                "continuous": true,
                "maxref": null,
                "minref": null,
                "limits": {
                    "alert": 90,
                    "warn": 70,
                    "min": 0,
                    "max": 100
                },
                "sources": [{
                    "sourcename": "rootComponent",
                    "streamName": "Stream1"
                }],
                "units": [
                    {
                        "domain": {
                            "time": 1.0,
                        },
                        "name": "s"
                    },
                    {
                        "domain":{
                            "current":-1.0,
                            "length": 2.0,
                            "mass": 1.0,
                            "time": -3.0
                        },
                        "name": "V",
                    }
                ]
            }
        ]
    }
}
```

Now let's unpack the contents of the SourceJson and the StreamJson. 

#### SourceJson
The SourceJson object needs the name defined and is the primary identifier of the instance of the component. DX+ will normally assign the component a name of a UUID. The logical name is the user set name for the component and is used for display purposes. Next the SourceJson has an "enabled" flag to indicate if the component is enabled. Lastly, the SourceJson contains a "settings" object which defines the settings for the component AND contains the "streams" key to list the streams for the component. The settings object should contain the settings keys defined in the component's supplied attributes JSON file in the "component_settings" definition. The streams key will contain a StreamJson object for each stream being output by the component.

#### StreamJson
The StreamJson contains an "originName" key. This value is set by the source that creates the first instance of this data stream. This is normally a root component or a processor that creates new streams such as the StatProcessor or SignalMath components. The originName should not change between components. The Stream then defines the "name". This name is the user defined name for the stream and is used as the primary identifier of the stream between components. The group key specifies a means to group streams under a common identifier. The group can be treated as a path. Adding "/" will create sub group categories that can be used for further grouping of streams.
The delta key defines the delta between data samples for the data in the stream. For time based data, this is normally seconds for time. For FFT results, this will be frequency. This required a uniform sample set in that each sample is an equal delta between the two. If the data is not uniform and delta is not valid, this value should be set to null or 0. For entry message types (Single Value), the delta can be the time duration of the message. mtype and dtype define the message and data types of the stream. These values were previously defined in this document, and should be set for the stream. The enabled key controls the enabled state of the stream for output from the component. The type key, defines the stream type. This defaults to "stream", but can be used to denote the data type of the stream such as speed, temperature, pressure, stat, etc. The type will be able to be used to detect appropriate visualization widgets for the stream. The streamclass key denotes the class of data stream and is used primarily for licensing device based tokens. The class is determined by sample rate. Class 0 is a statistic, class 1 is low rate of < 1KS/s, class 2 is medium rate and < 100KS/s, class 3 is < 800KS/s, and class 4 is >=800KS/s.  The vars key is meant for user/application variable data to be stored for the stream. The uniform key indicates that the samples contained in the result data are uniformly distributed and the delta describes the distribution value. This will be false for samples in the data that do not follow any set distribution, and therefore delta will be 0 or null. The continuous key indicates of the data is continuous (for example in time) OR if it is domain bound (for example FFT frequency or Degrees in angle domain). If the stream is designated as uniform and not continuos, then the streamJSON should define the minimum and maximum reference value. This is done with the maxref and minref keys. This will set the value specified for sample 0 and the value for the final sample in the data based on the defined delta. These can also be used for non-uniform data to provide the bounds for the first dimension of the stream defined in the units array. Next, there is the "limits" object. This object describes the min and max value for the 2nd dimension of the data defined in the units. Additionally, the limits object defines the alert percent value and warn percent value of the range designated by the min and max value. In this, if the min and max value do not cross 0, the alert is determined by the range and the midpoint. For example, if min is 0 and max is 20, the midpoint is 10 and the warn level will 7 offset from the midpoint and alert is 9 offset from the midpoint, resulting in warn occuring at >=17 and <=3 and alert occuring at >=19 and <=1. If the min and max is 0-crossing, the min and max percentages will be determined based on 0-max and 0-min. For example, with range being -5 and 10, the warn will occur at <=-3.5 and >=7 with the alert occuring at <=-4.5 and >=9. The sources array defines the streams from the parent that make the stream being output. Most of the time, this will be a single item. However, for the case of signal math, multiple input streams are to be received to create the output stream. For each source, the sourcename for the component in which it came from and the streamname of the stream from that source is set. Lastly, we have the units. The units array define the units for the data stream. This is always in X,Y,Z order (or more dimensions if required). The first unit defined should be the primary reference, and correlates to the set maxref and minref defined values. If the delta is defined, the first unit defines the unit of the delta, and subsequent units define the data contained in the samples. If delta is not defined, then the data will either be point2d, point3d, matrix, or tensor. For these, the first dimension should always be the primary reference. For example, Peak data is frequency, magnitude, and phase. Here, the primary reference dimension is frequency. The units should contain at the very least the "name" of the unit so that it can be found in the Units Table in Phoenix. The domain defines the unit in its base series of domains and their associated power. The unit definition will also normally supply the offset and scale of the unit from the base unit (For example km is 1000 m) and the primary unit system (Imperial, SI, etc);

#### Source Components for Post Processing
For post processing, the components that implement the readers of stored data are required to provide additional information. If this information is missing, the application will display an error to the user stating the component is missing information. The SourceJson and StreamJson should contain this data when the import function is called. 

Inside the SourceJson, the reader must separate dat streams by record ID (This will normally always be 1 n file types), the event ID, and the source Index. This should be provided under a "metadate" key in the SourceJson. Below is an example:

```json
"metadata": {
    "filename": "myFile",
    "records": [
        {
            "record_start": 178930099887765, //Record Start Time in ns since epoch
            "record_complete": 1790000000000,
            "record_id": 1,
            "graph": {}, //The graph JSON for the processing that was used when the recordwas created
            "metadata": {}, //metadata for the record,
            "sources": [0],
            "events": [
                {
                    "event_id": 1,
                    "event_flag": "s",
                    "event_start": 17883837636336, //Event start timestamp
                    "event_stop": 172627764644836, //Event stop timestamp
                    "metadata": {} //metadata for the event
                }
            ]
        }
    ]
}
```

Additionally, each stream must also provide a metadata key containing primarily, the record ID, the event ID, and the Source index for which the stream came from. It is also recommended to provide the first time and last time of the stream as it exists in the file. An example of this is seen below.

```json
"metadata": {
    "record_id": 1,
    "event_id": null,
    "source_index": 0,
    "firsttime": 17887777353738,
    "lasttime": 17887367856735
}
```

## Implementing the Processing
After completing the setup, the component manager will find the component connections and create instances of ComponentSubscriptionHandlers. These handlers are of a single subscription connection between components. i.e. Component2 uses Stream 1 and Stream 2 from Component 1. There will be subscriptions created of stream 1 component 1 to be sent to component 2 and then another subscription of stream 2 component 1 to be sent to component 2. This is in a multithreaded environment. The subscription handler ensures that data is arriving to destinations serially per stream, however, data from each stream can arrive to the destibation component concurrently. The component processing implementation must handle data streams independently. If several streams are needed to provide a batch or combined operation, then locks must be used along with appropriate buffering to appropriately handle the data. For our example case, each stream will apply a scale factor to each stream independently. 

When processing, it is also recommended to duplicate the message and contained data, otherwise data race conditions will occur on the data iteself for multiple processors or viewers receiving data from the same component.

In addition to the process function the start and stop function must be implemented. The start and stop function must also set the status of the component to "starting", "running", "stopped", or "error" as needed. The Post processing input components will set the status to "complete" when reading the data is done.

When the start function is called, this will build the channel reference map. This will map the incoming long ID of the Message to the list of output data streams that need it. It is recommended to call the base class start and stop functions to best handle this.

```c++
void CustomScalar::start(uint64_t ts){
    Component::start(ts);
}

void CustomScalar::start(uint64_t ts){
    Component::start(ts);
}

void CustomScalar::process(const Message& msg){
    if(!msg.valid()) return;
    if(!m_chanRef.contains(msg.getLongId())) return;
    auto streamOuts = m_chanRef[msg.getLongId()];
    auto fdata = msg.getF32Vector();
    for(auto& so : streamOuts){
        auto strmInfo = (customScalarStream_t*)m_streams[so].get();
        Message outMsg = msg;
        outMsg.setSrcIdx(m_index);
        outMsg.setStreamIdx(strmInfo->idx);
        outMsg.setMessageType(Message::MSG_VECTOR_DATA);
        outMsg.setDataType(Message::DATA_FLOATS);
        outMsg.setSize(sizeof(float)*fdata.size(),true);
        auto data = outMsg.getPtr<float>();
        int i=0;
        for(auto& d : fdata){
            data[i++] = d * strmInfo->scale + strmInfo->offset; 
        }
    }
}

```

## Making the Adapter
Every Component needs to define an adapter. The purpose of the adapter is to provide information about the component and a method to create an instance of the component. An example of the adapter should look something similar to the following:

```c++
class MyCustomScalarAdapter : public ComponentAdapter {
public:
    MyCustomComponentAdapter() {}
    ~MyCustomComponentAdapter() {}
    std::shared_ptr<Component> create(const std::string& name) override {
        return std::make_shared<CustomScalar>(name);
    }
    std::string getName() const override {
        return "Custom Scalar";
    }
    JSON getInfo() const override {
        return info;
    }
    std::string getUUID() const override {
        return info["uuid"].get<std::string>();
    }
}
```

## Creating the Library
The Library provides the main export function for the DLL and provides access to the collection adapters contained in the library. By doing this, the user can provide a full collection of components contained in a single DLL load. This will use the "ComponentLibrary" class. 

```c++
ComponentLibrary* GetComponents() { //Main DLL Entry
    static ComponentLibrary* rtn = nullptr; //Make static to prevent rebuild for every call to getComponents
    if(!rtn){
        static std::vector<std::shared_ptr<ComponentAdapter>> items;
        static const std::string libName = "MyCustomComponents";
        items.push_back(std::make_shared<MyCustomComponentAdapter>());
        rtn = new ComponentLibrary(libName, items);
    }
    return rtn;
}
```

## Results
After creating the component, the adapter, and the library, the ComponentLibraryManager will be able to discover the library placed in the components directory and load. This will make the components within the library usable by the higher application (DX+ or Phoenix Engine or PhoenixPy).
