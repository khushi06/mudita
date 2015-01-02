Service Solahart + (021) 34082652 – 082122541663 service wika swh tidak panas, bocor, bongkar pasang, pemasangan pipa air panas & air dingin pengecekan berkala & lain lain CV. Davinatama Service Jakarta Indonesia
## GET /api/v1/chaos

This will fetch all chaos events in the last 24 hours.  Output is always json.

    $ curl http://localhost:8080/api/v1/chaos
    [   
        {   
            "monkeyType": "CHAOS",
            "eventType": "CHAOS_TERMINATION",
            "eventTime": 1343233763683,
            "groupType": "ASG",
            "groupName": "simianarmy",
            "instanceId": "i-804f7cf8"
       },
       ...
    ]

### Response Description
<table>
<tr><th>Name</th><th>Type</th><th>Description</th></tr>
<tr><td>monkeyType</td><td>string</td><td>The monkey that created the event</td></tr>
<tr><td>eventType</td><td>string</td><td>The event type</td></tr>
<tr><td>eventTime</td><td>long int</td><td>The time the event occurred in milliseconds since the epoch</td></tr>
<tr><td>groupType</td><td>string</td><td>The group type for the termination (ex: ASG for AutoScalingGroup)</td></tr>
<tr><td>groupName</td><td>string</td><td>The group name for the termination (the name of the ASG)</td></tr>
<tr><td>instanceId</td><td>string</td><td>The Instance ID that was terminated.</td></tr>
</table>

### Query Parameters
Any of the above attributes can be passed as query parameters to restrict the query results.  There is an added *since=&lt;ms timestamp&gt;* query parameter to change the time-frame for the interested events.  The default for *since* is 24 hours in the past.

    $ curl 'http://localhost:8080/api/v1/chaos?groupName=simianarmy&since=1343113200000'
    [   
        {   
            "monkeyType": "CHAOS",
            "eventType": "CHAOS_TERMINATION",
            "eventTime": 1343233763683,
            "groupType": "ASG",
            "groupName": "simianarmy",
            "instanceId": "i-804f7cf8"
       },
       {   
            "monkeyType": "CHAOS",
            "eventType": "CHAOS_TERMINATION",
            "eventTime": 1343158781228,
            "groupType": "ASG",
            "groupName": "simianarmy",
            "instanceId": "i-06cae07e"
       }
    ]

## POST /api/v1/janitor

This will add a new Janitor Monkey event. The main purpose is to flag a resource as not being cleaned by Janitor Monkey (opt out the resource) or to remove such flag (opt in the resource). Input is in Json format.

To flag a resource as not being cleaned by Janitor Monkey:
    
    $ curl http://localhost:8080/simianarmy/api/v1/janitor -d '{"eventType":"OPTOUT","resourceId":"foo"}'

To opt in the resource so it can be managed by Janitor Monkey again.
    
    $ curl http://localhost:8080/simianarmy/api/v1/janitor -d '{"eventType":"OPTIN","resourceId":"foo"}'

