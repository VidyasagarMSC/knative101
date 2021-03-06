## A/B Testing with knctl

Maybe we want to slowly roll users over from our old version to the new version, or do some A/B testing of the new version. We can use the knctl rollout command to route traffic percentages to our revisions.

1. Check the current route percentages:

	```
	knctl route list
	```

  Output:
  ```
  Routes in namespace 'default'

  Name         Domain                                                              Traffic                   Annotations  Conditions  Age  
  fib-knative  fib-knative.default.bmv-knative.us-east.containers.appdomain.cloud  100% -> fib-knative  -            3 OK / 3    20h  

  1 routes

  Succeeded
  ```

2. Send 50% of the traffic to the latest revision, and 50% to the previous revision:

	```
	knctl rollout --route fib-knative -p fib-knative:latest=50% -p fib-knative:previous=50%
	```

3. Check the route percentages to confirm they've updated:

	```
	knctl route list
	```

  Output:
  ```
	Routes in namespace 'default'

	Name         Domain                                                                       Traffic                   Annotations  Conditions  Age  
	fib-knative  fib-knative.default.bmv-knative-101-test.us-east.containers.appdomain.cloud  50% -> fib-knative-00002  -            3 OK / 3    9m  
	                                                                                          50% -> fib-knative-00001                             

	1 routes

	Succeeded
```

3. Let's run some load against the app, just asking for the first number in the Fibonacci sequence so that we can clearly see which revision is being called.

	```
	while sleep 0.5; do curl -X POST "http://fib-knative.default.bmv-knative.us-east.containers.appdomain.cloud/fib" -H 'Content-Type: application/json'   -d '{"number":1}' ; done
	```

4. We should see that the curl requests are routed approximately 50/50 between the two applications. Let's kill this process using ctrl-c.


At this point, you should feel that you've gotten a whirlwind tour of Knative. We installed Istio & Knative onto a Kubernetes cluster. We deployed a serverless application and saw it scale up and then back down when it was no longer in use. We also explored the knctl tool to easily create routing rules and deploy serverless applications. Please reach out should you have any questions or issues going through this lab!
