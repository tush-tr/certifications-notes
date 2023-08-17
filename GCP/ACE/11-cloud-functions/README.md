- [What are Cloud Functions?](#what-are-cloud-functions)
- [Cloud Functions important concepts](#cloud-functions-important-concepts)
  - [Events](#events)
  - [Trigger](#trigger)
  - [Events are triggered from](#events-are-triggered-from)
- [Creating first Cloud Function](#creating-first-cloud-function)
- [Important points](#important-points)
# What are Cloud Functions?
- Imagine you want to execute some code when an event happens?
  - A file is uploaded in Cloud storage
  - An error log is written to Cloud Logging
  - A message arrives to cloud pub/sub
  - A http/https invocation is received
- Enter Cloud Functions
- Run code in response to Events
  - Write your business logic in Node.js, Python, Go, Java, .NET, and Ruby.
  - Don't worry about servers or scaling or availability (only worry about your code)
- Pay only for what you use
  - Number of invocations
  - Compute time of the invocations
  - Memory or CPU provisioned
- Time bound
  - Default 1 min and MAX 60 minutes(3600 seconds)
- 2 Product versions
  - Cloud Functions(1st gen): First version
  - Cloud Functions(2nd gen): New version built on top of Cloud Run and Eventarc

# Cloud Functions important concepts
## Events
- Upload object to cloud storage
## Trigger
- Respond to event with function call
  - Trigger: Which function to trigger when an event happens?
  - Functions: Take event data and perform action?
## Events are triggered from
- Cloud Storage
- Cloud Pub/Sub
- HTTP POST/GET/DELETE/PUT/OPTIONS
- Firebase
- Cloud Firestore
- Stack driver logging

# Creating first Cloud Function
- Enable the cloud function api.
- Click on create function.
- Choose version: 1st gen, 2nd gen.
- Choose trigger type: HTTP
- Timeout(default 60 seconds)
- Runtime service account
- Add function name
- Copy url
- Authentication: no auth or authenticated.
- Write code for your function
- ```js
  exports.helloWorld = (req, res) => {
    let message = req.query.message || req.body.message || 'Hello World!';
    res.status(200).send(message);
  };
  ```
- Deploy the function
- Hit the http url, you can get the response.


# Important points
- No server management: You don't need to worry about scaling or availability of your function.
- Cloud functions automatically spin up and back down in response to events
    - They scale horizontally
- Cloud functions are recommended for responding to events:
  - Not idea for long running processes.

---