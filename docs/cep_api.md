The Complex Event Processing API allows you to analyze data from your IoT device and trigger actions.

The following action types are available:

- update: update an entity's attribute
- email: send an email
- post: make an HTTP POST
- twitter: send a tweet

This API allows you for example to define rules to trigger email notifications based on the data value thresholds or the the lack of updates from a certain device.

These rules are expressed as an EPL sentence. [EPL](http://www.espertech.com/esper/index.php) is a domain language of Esper, the engine for processing events used. This EPL sentence matches an incoming event if satisfies the conditions and generates an "action-event" that will be sent back to FIWARE IoT Stack to execute the associated action. 

Remember you can always use the [Management Portal](portal.md) to define basic rules, which are a perfect fit for most frequent use cases.


# Activate rules

You have to choose which data from your IoT device is relevant to be evaluated by your rules at the FIWARE IoT Stack.

In order to do so, you have to select the Data API entity attributes. On the following example, the temperature attribute will be selected:

```
POST /NGSI10/subscribeContext HTTP/1.1
Host: test.ttcloud.net:1026
Accept: application/json
Content-Type: application/json
Fiware-Service: {{Fiware-Service}} 
Fiware-ServicePath: {{Fiware-ServicePath}} 
X-Auth-Token: {{user-token}}

{
    "entities": [
        {"type": "device",
        "isPattern": "false",
        "id": "mydevice"
        }
    ],
    "reference": "http://test.ttcloud.net:xxxx/notify", 
    "duration": "P1Y",
    "notifyConditions": [
           {
                "type": "ONCHANGE", 
                "condValues": ["TimeInstant" ]
} ],
"throttling": "PT1S" }
```

If you are familiar with FIWARE components, on this request you are using the Data API subscription operation to notify new data from your device to the FIWARE CEP component providing the Complex Event Processing API.

# Create rule to send emails

Once you have activated the processing for your data, you can create a rule to trigger actions as follows:

```
POST /NGSI10/subscribeContext HTTP/1.1
Host: test.ttcloud.net:1026
Accept: application/json
Content-Type: application/json
Fiware-Service: {{Fiware-Service}} 
Fiware-ServicePath: {{Fiware-ServicePath}} 
X-Auth-Token: {{user-token}}

{
   "name":"train-rule",
   "text":"@Audit select *,\"train-rule\" as ruleName, \"webinar\" as tenant, \"/\" as service from pattern [every ev=iotEvent(cast(cast(ev.longitude?,String),float)>200.0)]",
    "action": {
        "type": "email",
        "template": "Meter ${Meter} has pression ${Pression} (GEN RULE)",
        "parameters": {
            "to": "someone@yourclient.com",
            "from": "notificactions@yourdomain.com"
            "subject": "Alert! high pression detected"
        }
    }
}
```



# Create rule to send an  HTTP POST

You can also trigger an HTTP POST to an URL specified sending a body built from template:

```
POST /NGSI10/subscribeContext HTTP/1.1
Host: test.ttcloud.net:1026
Accept: application/json
Content-Type: application/json
Fiware-Service: {{Fiware-Service}} 
Fiware-ServicePath: {{Fiware-ServicePath}} 
X-Auth-Token: {{user-token}}

{
   "name":"train-rule",
   "text":"@Audit select *,\"train-rule\" as ruleName, \"webinar\" as tenant, \"/\" as service from pattern [every ev=iotEvent(cast(cast(ev.longitude?,String),float)>200.0)]",

    "action": {
        "type": "post",
        "template": "Meter ${Meter} has pression ${Pression}.",
        "parameters": {
            "url": "localhost:1111"
        }
    }
```

# Create rule to send a Tweet

Updates the status of a twitter account, with the text build from the template field. The field parameters must contain the values for the consumer key and secret and the access token key and access token secret of the pre-provisioned application associated to the twitter user.

```
POST /NGSI10/subscribeContext HTTP/1.1
Host: test.ttcloud.net:1026
Accept: application/json
Content-Type: application/json
Fiware-Service: {{Fiware-Service}} 
Fiware-ServicePath: {{Fiware-ServicePath}} 
X-Auth-Token: {{user-token}}

{
    "name":"train-rule",
    "text":"@Audit select *,\"train-rule\" as ruleName, \"webinar\" as tenant, \"/\" as service from pattern [every ev=iotEvent(cast(cast(ev.longitude?,String),float)>200.0)]",

    "action": {
        "type": "twitter",
        "template": "Meter ${Meter} has pression ${Pression} (GEN RULE)",
        "parameters": {
          "consumer_key": "xvz1evFS4wEEPTGEFPHBog",
          "consumer_secret": "L8qq9PZyRg6ieKGEKhZolGC0vJWLw8iEJ88DRdyOg",
          "access_token_key": "xvz1evFS4wEEPTGEFPHBog",
          "access_token_secret": "L8qq9PZyRg6ieKGEKhZolGC0vJWLw8iEJ88DRdyOg"
        }
    }
}

```

# In more detail ...
