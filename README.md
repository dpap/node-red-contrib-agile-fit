# Introduction 

AGILE provides a Node-RED node which allows access to cloud-based health data via the openmHealth/shimmer library. 
Types of data that can be accessed include:
* Physical Activity
* Step Count
* Calories burnt
* Blood Pressure
* Body Weight
* Heart Rate
* Oxygen Saturation.

Supported platforms include:
* Fitbit
* Google Fit
* Jawbone UP
* Misfit
* RunKeeper
* Withings
* iHealth.
 

# Example  
## Setup Shimmer
A version of the openmHealth/shimmer ([Shimmer) is required. The official version will redirect to localhost Currently this version will not run on low resource gateways.

A modified version of the openmHealth Shimmer library which can run on the raspberryPi can be found here ([https://github.com/dpap/shimmer AGILE-Shimmer](https://github.com/openmhealth/shimmer]) branch:agileRelease). This version allows the redirect gateway via the $AGILE_HOST parameter. 
Set $AGILE_HOST to the gateway name.  

<code><pre>export AGILE_HOST=<local gateway name> ;  export DOCKER_HOST=<local gateway name>:2375 ;  docker-compose build shimmer && docker-compose up -d shimmer </pre></code>

The shimmer library needs developer credentials for the various cloud providers. More information can be found [here
 
## Install AGILE Node-RED nodes  
Clone  [https://github.com/Agile-IoT/node-red-contrib-agile-fit Node-RED shimmer nodes](https://github.com/openmhealth/shimmer]) in Node-RED shared directory (~<user-dir>/.agile/nodered) and restart Node-RED container. 
 
# Usage 

To use the shimmer library the shimmer-config node must be setup to point to the shimmer container which is located on port 8083 of the AGILE-gateway [
Access to data is a two step process. The first step is the authentication process where the user needs to be redirected to a login page to enter his credentials. 

After the user has authenticated with the cloud provider he can access his data.
 
The following example shows this flow.  

<code>[{"id":"a78fd93f.a6a53","type":"inject","z":"4a6e8947.6e9db","name":"Get GoogleFit Step count","topic":"","payload":"{\"username\":\"test\",\"metric\":\"step_count\"}","payloadType":"json","repeat":"","crontab":"","once":false,"x":172.75,"y":56.75,"wires":[["be667713.8042e"]([File:Shimmer-config.jpg|400px]])]},{"id":"be667713.8042e","type":"function","z":"4a6e8947.6e9db","name":"","func":"\n//var payload = JSON.parse(msg.payload);\nvar payload = msg.payload;\n\nif (payload.username # undefined){\n    payload.username = \"test\";\n}    \nmsg.payload = JSON.stringify(payload);\n\nreturn msg;","outputs":1,"noerr":0,"x":379.75,"y":93.51666259765625,"wires":[Response","active":false,"console":"false","complete":"true","x":851.75,"y":90.86666870117188,"wires":[](["1b921104.32c9df"]]},{"id":"d443267d.d76368","type":"debug","z":"4a6e8947.6e9db","name":"authentication)},{"id":"9d8ff68b.1b53d","type":"http in","z":"4a6e8947.6e9db","name":"","url":"/step_count","method":"get","swaggerDoc":"","x":157,"y":126.25,"wires":[helath metric (step_count) and time duration","func":"var payload= JSON.parse(msg.payload);\n\nif (payload.metric  undefined){\n    payload.metric = \"step_count\";\n}\n\npayload.metric = \"step_count\";\npayload.startTime=\"2017-05-01\";\npayload.stopTime=\"2017-06-05\"\n\nmsg.payload = JSON.stringify(payload);\n\nreturn msg;","outputs":1,"noerr":0,"x":235.75,"y":236.86666870117188,"wires":[["35bf4afa.81562e"](["be667713.8042e"]]},{"id":"34f7bb82.823f3c","type":"function","z":"4a6e8947.6e9db","name":"set)]},{"id":"82b7fc71.fb8b18","type":"function","z":"4a6e8947.6e9db","name":"Extract Steps","func":"var payload = JSON.parse(msg.payload);\n\nvar steps = [   payload.body.forEach(function (data){\n        var stepObj = {};\n        stepObj.time = data.body.effective_time_frame.time_interval.start_date_time;    \n        stepObj.value = data.body.step_count;\n        steps.push(stepObj);\n    });\n\n    msg.payload = steps;\nreturn msg;","outputs":1,"noerr":0,"x":786.75,"y":271.3666687011719,"wires":[["900dba21.617aa8"](];\n)]},{"id":"900dba21.617aa8","type":"debug","z":"4a6e8947.6e9db","name":"Steps","active":true,"console":"false","complete":"payload","x":950.75,"y":271.3666687011719,"wires":[response","z":"4a6e8947.6e9db","name":"","x":769.75,"y":213.13333129882812,"wires":[](]},{"id":"2362f077.93cdc","type":"http)},{"id":"1b921104.32c9df","type":"shimmer_authenticate","z":"4a6e8947.6e9db","name":"","server":"1f6490af.8b65d7","x":574.75,"y":91.20001220703125,"wires":[["d443267d.d76368","34f7bb82.823f3c"]]},{"id":"35bf4afa.81562e","type":"shimmer_getData","z":"4a6e8947.6e9db","name":"","server":"1f6490af.8b65d7","x":536.75,"y":236.69998168945312,"wires":[["2362f077.93cdc","82b7fc71.fb8b18"]]},{"id":"1f6490af.8b65d7","type":"shimmer-config","z":"","shimmer_server_url":"http://localhost:8083","shimmer_service":"googlefit"}]</code> 
