# RIDAGOP toolkit Process-Schema

*A process in the IDS toolkit*

## Properties

- **`name`** *(string)* **(required)** : The name of the process. This is the name for internal identification (although there is only one process).
- **`title`** *(string)*: The title of the process.
- **`description`** *(string)*: A description of the process. (For other developers).
- **`services`** *(object)* **(required)** : Services with their UI-elements and activities.
    - **Additional Properties**: Each key specifies the name of a service. Refer to *[P-Service](#p-service)*.
- **`common`** *(object)*: Common activities and ui elements (access to activities of other services). Default: `{'ui': {}, 'activities': {}}`.
    - **`ui`** *(object)*: UI-elements that are available for the activities defined in the common section. Default: `{}`.
        - **`buttons`** *(object)*: UI-Buttons. These buttons can trigger activities of all services of this process.
            - **Additional Properties** *(object)*: key is the name of the button.
                - **`label`** *(string)* **(required)** : The label of the button.
                - **`triggerActivity`** *(string)* **(required)** : The name of the activity that is triggered. The activity must be of the same service.
                - **`activityService`** *(string)*: The name of the service that contains the activity that is triggered.
                - **`triggerSequence`** *(string)*: The name of the sequence that is triggered. The sequence must be of the same service.
    - **`activities`** *(object)*: Some activities. Consider that the bridge does not have a bridge, so these activities can only have module-functions.
        - **Additional Properties** *(object)*: Each key specifies the name of an activity.
            - **`One of (1)`**: Refer to *[P-Activity](#p-activity)*.
            - **`One of (2)`**: Refer to *[P-CommonActivity](#p-commonactivity)*.
    - **`sequences`** *(object)*: A sequence is a list of activities. This is for convenience for tirggereing multiple activities at once.
        - **Additional Properties** *(object)*
            - **`title`** *(string)*: The user visible title of the sequence.
            - **`activities`** *(array)*: The list of activities that are executed when the sequence is triggered.
                - **Items** *(string)*: The name of an activity.
- **`scriptUri`** *(string/format: uri-reference)*: The URI of the script that is used to run the process.
## Definitions

## P-Service
- **`P-Service`** *(object)*: A service as it is defined in the Process. Includes UI-elements, activities and shared parameters and the bridge.
    - **`title`** *(string)*: The user visible title of the service.
    - **`ui`**: The UI-elements of the service. Refer to *[P-ServiceUI](#p-serviceui)*. Default: `{}`.
    - **`parameters`** *(object)*: Parameters that are usable by all activities of this service.
        - **Additional Properties**: Refer to *[P-Parameter](#p-parameter)*.
    - **`autostart`**: Activities that are started on page load.
    - **`bridge`**: The bridge of this service. Refer to *[P-BridgeDefinition](#p-bridgedefinition)*.
    - **`activities`** *(object)*: The activities of this service.
        - **Additional Properties**: Each key specifies the name of an activity. Refer to *[P-Activity](#p-activity)*.
    - **`sequences`** *(object)*: A sequence is a list of activities. This is for convenience for tirggereing multiple activities at once.
        - **Additional Properties** *(object)*: Each key specifies the name of a sequence.
            - **`title`** *(string)*: The user visible title of the sequence.
            - **`activities`** *(array)*: The list of activities that are executed when the sequence is triggered.
                - **Items** *(string)*: The name of an activity.
## P-Activity
- **`P-Activity`** *(object)*: An activity which uses the bridge or a module function.
    - **`preProcess`** *(string)*: a module function that is called before the activity is executed.
    - **`comment`** *(string)*: A comment.
    - **`parameters`** *(object)*: Parameters that are required by this activity.
        - **Additional Properties**: A parameter-name defined by the bridgeCapability or moduleFunction. Refer to *[P-Parameter](#p-parameter)*.
    - **`requestBody`** *(object)*: The request body of the activity (only relevant when using OpenAPI execution).
        - **Additional Properties** *(object)*: Used when the body is multipart/form-data.
        - **`data`** *(object)*: Used when the body is a JSON object.
            - **`fileInput`** *(string)*: The name of the file input html-Element.
            - **`store`**: Some data in the store. Refer to *[P-StoreAccess](#p-storeaccess)*.
    - **`requiredActivities`** *(array)*: A list of activities that must be executed before this activity is executed.
        - **Items** *(object)*: An activity that needs to be executed before this activity can be executed.
            - **`serviceName`** *(string)*: The name of the service that contains the activity.
            - **`activityName`** *(string)* **(required)** : The name of the activity.
            - **`errorMessage`** *(string)*: The error message that is shown when the activity is not executed (only for generated UI).
    - **`storeResult`**: Storing the result of the activity for later use. Refer to *[P-Store](#p-store)*.
    - **`subActivities`** *(object)*: Sub-activities are activities that are executed after this parent activity is executed.
        - **Additional Properties**: Each key specifies the name of a sub-activity. Refer to *[P-Activity](#p-activity)*.
    - **`ui`** *(object)*: Some UI related settings for the activity.
        - **`includeInStatus`** *(boolean)*: If true, the activity is included in the status of the service (default true).
        - **`resultAsOutputHtml`**: If the result of the activity should be shown on the page. For generated UI, there is a output section for each service. For mapped UI, a string can be used in order to specify the html element (element id).
            - **`One of (1)`** *(boolean)*
            - **`One of (2)`** *(string)*
        - **`resultAsDynamicUI`** *(boolean)*: The result of this activity is used to generate some new UI-elements. The result data needs to validate against the process-Service UI schema.
        - **`alert`** *(boolean)*: Configure if the activity should show an alert when it is finished (default on top level activities is true).
    - **`debug`** *(object)*: Some debug related settings for the activity.
        - **`execute`** *(boolean)*: If false, the activity is not executed (default true).
        - **`resultData`** *(object)*: Instead of executing the activity, the resultData is used as the result of the activity.
    - **`One of (1)`**
        - **`title`** *(string)* **(required)** : The user visible title of the activity.
        - **`bridgeCapability`** **(required)** : The name of the bridge capability that is used to execute the activity. Refer to *[capabilities_names_list](/Schema/capabilities_names_list)*.
    - **`One of (2)`**
        - **`title`** *(string)* **(required)** : The user visible title of the activity.
        - **`moduleFunction`** *(string)* **(required)** : The name of the module function (??? FROM WHERE) that is used to execute the activity.
## P-ServiceUI
- **`P-ServiceUI`** *(object)*: The UI of a service. Consider that many of the properties are only relevant when the Ui is generated. Default: `{}`.
    - **`inputFields`** *(object)*: Inputfields are text-fields or text-areas.
        - **Additional Properties** *(object)*: key is the name of the input field.
            - **`label`** *(string)*: The label of the input field.
            - **`fromQueryParam`** *(string)*: If this property is set, the field is initiated with the value of the query parameter.
            - **`default`** *(string)*: The default value of the input field.
            - **`textArea`** *(boolean)*: If true, the input field is a text area (which is nicer for longer inputs.
            - **`inputActions`** *(object)*: Adds a small button next to the input. Clicking that or pressing enter triggers an action.
                - **`autoAction`** *(string)*: The name of the action that is triggered.
    - **`buttons`** *(object)*: Buttons are used to trigger actions.
        - **Additional Properties** *(object)*: key is the name of the button.
            - **`label`** *(string)*: The label of the button.
            - **`triggerActivity`** *(string)*: The name of the activity that is triggered. The activity must be of the same service.
            - **`triggerSequence`** *(string)*: The name of the sequence that is triggered. The sequence must be of the same service.
    - **`checkBoxes`** *(object)*: Checkboxes are used to trigger actions.
        - **Additional Properties** *(object)*: key is the name of the checkbox.
            - **`label`** *(string)*: The label of the checkbox.
            - **`default`** *(boolean)*: The default value of the checkbox. Default: `False`.
    - **`selects`** *(object)*: Selects items.
        - **Additional Properties** *(object)*: key is the name of the select.
            - **`label`** *(string)*: The label of the select.
            - **`options`** *(array)*: The options of the select.
                - **Items** *(object)*: An individual option.
                    - **`label`** *(string)* **(required)** : The label of the option visible to the user.
                    - **`value`** *(string)* **(required)** : The value of the option.
            - **`default`** *(string)*: The default value of the select.
    - **`fileInputs`** *(object)*: File inputs are used to upload files.
        - **Additional Properties** *(object)*: key is the name of the file input.
            - **`label`** *(string)*: The label of the file input.
            - **`accept`** *(string)*: The file types that are accepted (MIME type).
            - **`binary`** *(boolean)*: If true, the file is read as binary. Otherwise it is read as text.
            - **`readImmediately`** *(boolean)*: If true, the file is read immediately. Otherwise it is read when an activity that requires it is triggered.
            - **`keepAsFile`** *(boolean)*: If true, the file is not read but just used like that in a multipart-form.
## P-CommonActivity
- **`P-CommonActivity`** *(object)*: A activity in the common section of the process, can be a regular activity or a reference to an activity of any service.
    - **`One of (1)`**
        - **`preProcess`** *(string)*: a module function that is called before the activity is executed.
        - **`comment`** *(string)*: A comment for developers.
        - **`parameters`** *(object)*: Parameters that are required by this activity.
            - **Additional Properties**: Refer to *[P-Parameter](#p-parameter)*.
        - **`requestBody`** *(object)*: The request body of the activity (only relevant when using OpenAPI execution).
            - **Additional Properties** *(object)*: Used when the body is multipart/form-data.
            - **`data`** *(object)*: Used when the body is a JSON object.
                - **`fileInput`** *(string)*: The name of the file input html-Element.
                - **`store`**: Some data in the store. Refer to *[P-StoreAccess](#p-storeaccess)*.
        - **`requiredActivities`** *(array)*: A list of activities that must be executed before this activity is executed.
            - **Items** *(object)*: An activity that needs to be executed before this activity can be executed.
                - **`serviceName`** *(string)*: The name of the service that contains the activity.
                - **`activityName`** *(string)* **(required)** : The name of the activity.
        - **`storeResult`**: Storing the result of the activity for later use. Not exactly like in a service. The context can only be process or activity.
            - **`context`** *(string)*: The context of the variable.<br>                - Process: Variable is available by all services and their activities.<br>                - Service: Variable is available by all activities of this service.<br>                - Activity: Variable is available to this activity and its sub-activities and deleted afterwards.
 Must be one of: `['process', 'activity']`. Default: `service`.
            - **`key`** *(string)* **(required)** : key in the store ( dots are not allowed).
        - **`subActivities`** *(object)*: Sub-activities are activities that are executed after this parent activity is executed.
            - **Additional Properties**: Each key specifies the name of a sub-activity (Either a normal activity or a reference). Refer to *[P-CommonActivity](#p-commonactivity)*.
        - **`debug`** *(object)*: Some debug related settings for the activity.
            - **`execute`** *(boolean)*: If false, the activity is not executed (default true).
            - **`resultData`** *(object)*: Instead of executing the activity, the resultData is used as the result of the activity.
        - **`One of (1)`**
            - **`title`** *(string)* **(required)** : The title of the activity.
            - **`bridgeCapability`** **(required)** : The bridge-capability that is used for this activity. Refer to *[capabilities_names_list](/Schema/capabilities_names_list)*.
        - **`One of (2)`**
            - **`title`** *(string)* **(required)** : The title of the activity.
            - **`moduleFunction`** *(string)* **(required)** : The function that is used to execute the activity.
    - **`One of (2)`**: A reference to a activity that is defined in a service of the process.
        - **`title`** *(string)* **(required)** : The user visible title of the activity.
        - **`serviceName`** *(string)* **(required)** : The name of the service that has the activity that should be executed.
        - **`activityName`** *(string)* **(required)** : The name of the activity that should be executed.
        - **`subActivities`** *(object)*: Sub-activities are activities that are executed after this parent activity is executed.
            - **Additional Properties**
                - **`One of (1)`**: A normal activity. Refer to *[P-Activity](#p-activity)*.
                - **`One of (2)`**: An activity of reference activity. Refer to *[P-CommonActivity](#p-commonactivity)*.
        - **`debug`** *(object)*: Some debug related settings for the activity.
            - **`execute`** *(boolean)*: If false, the activity is not executed (default true).
            - **`resultData`** *(object)*: Instead of executing the activity, the resultData is used as the result of the activity.
## P-PostProcess
- **`P-PostProcess`** *(array)*: function that processes the activity result before it is stored or passed on to other activities.
## P-Parameter
- **`P-Parameter`** *(object)*: Parameter for an activity.
    - **`type`** *(string)*: Type of the parameter value. Must be one of: `['string', 'number', 'boolean']`.
    - **`parent`**: Result from the parent activity passed down (to this sub-activity).
    - **`previous`**: Result from the previous sub-activity.
    - **`field`** *(string)*: Data from an Inputfield, Checkbox or Select.
    - **`queryParam`** *(string)*: Data from a URL-query parameter.
    - **`constant`** *(string)*: A constant non changing value specified here.
    - **`fileInput`** *(string)*: Data from a file-input field UI-Element.
    - **`store`**: Data from the store. Refer to *[P-StoreAccess](#p-storeaccess)*.
    - **`generate`** *(object)*: Generate a value based on an activity.
        - **`bridgeCapability`** *(string)*: The name of the bridge capability that is used to execute the activity.
        - **`moduleFunction`** *(string)*: The name of the module function (??? FROM WHERE) that is used to execute the activity.
        - **`parameters`** *(object)*: Parameters that are required by this activity.
            - **Additional Properties**: A parameter-name defined by the bridgeCapability or moduleFunction defined for this 'generate' parameters. Refer to *[P-Parameter](#p-parameter)*.
    - **`dynamic`** *(boolean)*: if true, the parameter is not passed to the activity, but is used to generate a dynamic UI.
    - **`comment`** *(string)*: A comment for other developers.
## P-ServiceBridge
- **`P-ServiceBridge`** *(object)*: The bridge for this service.
    - **`One of (1)`**
        - **`uri`** *(string/format: uri-reference)* **(required)** : The URI of the bridge description json file.
    - **`One of (2)`**
        - **`instance`** **(required)** : The bridge description. Refer to *[bridge](/Schema/bridge)*.
## P-BridgeDefinition
- **`P-BridgeDefinition`** *(object)*: The bridge definition, containing the source (either uri or the whole instance) and some additional properties.
    - **`source`** **(required)** : The source (uri or instance). Refer to *[P-ServiceBridge](#p-servicebridge)*.
    - **`server`**: The server host of this service bridge. Refer to *[P-BridgeServerHost](#p-bridgeserverhost)*.
    - **`authorization`** *(object)*: From where to take the authorization code for OpenAPI bridge. Usually a API/Auth-Token.
        - **Additional Properties**
## P-BridgeServerHost
- **`P-BridgeServerHost`**: The server host of this service bridge.
    - **`One of (1)`** *(string/format: uri)*: The server host of this service bridge.
    - **`One of (2)`** *(object)*: from a input-field UI-Element (textfield/textarea).
        - **`field`** *(string)* **(required)** : Name of the input-field UI-Element.
    - **`One of (3)`** *(object)*
        - **`queryParam`** *(string)* **(required)** : URL query parameter.
    - **`One of (4)`** *(object)*
        - **`constant`** *(string/format: uri)* **(required)** : a constant non changing value.
## P-Store
- **`P-Store`** *(object)*: Description for storing activity results. Results can be stored on 3 levels (context), in order to prevent name collisions: process, service (default), activity.
    - **`context`** *(string)*: The context of the variable.<br>        - Process: Variable is available by all services and their activities.<br>        - Service: Variable is available by all activities of this service.<br>        - Activity: Variable is available to this activity and its sub-activities and deleted afterwards.
 Must be one of: `['process', 'service', 'activity']`. Default: `service`.
    - **`key`** *(string)* **(required)** : key in the store ( dots are not allowed).
## P-StoreAccess
- **`P-StoreAccess`** *(object)*: Description for accessing a stored value.
    - **`context`** *(string)*: From which context to access the variable. See [P-Store](#p-store). Must be one of: `['service', 'process', 'activity']`. Default: `service`.
    - **`key`** *(string)* **(required)** : key in the store.