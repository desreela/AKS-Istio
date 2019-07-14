# Test
**Intro**

The Istio [Bookinfo](https://istio.io/docs/examples/bookinfo/) sample consists of four separate microservices, each with multiple versions. Three different versions of one of the microservices, reviews, have been deployed and are running concurrently. To illustrate the problem this causes, access the Bookinfo app&#39;s /productpage in a browser and refresh several times. You&#39;ll notice that sometimes the book review output contains star ratings and other times it does not. This is because without an explicit default service version to route to, Istio routes requests to all available versions in a round robin fashion.

1. **Redirect to one version of application**

The initial goal of this task is to apply rules that route all traffic to v1 (version 1) of the microservices. Later, you will apply a rule to route traffic based on other criterias.

**Apply a virtual service**

To route to one version only, you apply virtual services that set the default version for the microservices. In this case, the virtual services will route all traffic to v1 of each microservice.
```
kubectl apply -f samples/bookinfo/networking/destination-rule-all-mtls.yaml

kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```
You now have configured Istio to route 100% of the traffic to the v1 version of the Bookinfo microservices, most importantly the reviews service version 1.

**Test the new routing configuration**

You can easily test the new configuration by once again refreshing the /productpage of the Bookinfo app.

1. Open the Bookinfo site in your browser. The URL is [http://$GATEWAY\_URL/productpage](http://%24GATEWAY_URL/productpage), where $GATEWAY\_URL is the External IP address of the ingress, as explained in the [Bookinfo](https://istio.io/docs/examples/bookinfo/#determining-the-ingress-ip-and-port) doc.
Notice that the reviews part of the page displays with no rating stars, no matter how many times you refresh. This is because you configured Istio to route all traffic for the reviews service to the version reviews:v1 and this version of the service does not access the star ratings service.

You have successfully accomplished the first part of this task: route traffic to one version of a service.

1. **Route based on user identity**

Next, you will change the route configuration so that all traffic from a specific user is routed to a specific service version. In this case, all traffic from a user named Jason will be routed to the service reviews:v2.

Note that Istio doesn&#39;t have any special, built-in understanding of user identity. This example is enabled by the fact that the productpage service adds a custom end-user header to all outbound HTTP requests to the reviews service.

Remember, reviews:v2 is the version that includes the star ratings feature.

1. Run the following command to enable user-based routing:
