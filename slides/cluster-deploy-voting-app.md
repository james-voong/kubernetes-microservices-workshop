### Deploying to Kubernetes Cluster



#### Setting up the Voting Application
* Have a look at kubernetes specs for the vote app
   ```
   cd ~/example-voting-app
   ```
   ```
   ls k8s-specifications
   ```
* Folder contains specification files for 
   + Deployments
   + Services


#### Creating resources
<code>kubectl </code><code style="color:blue;">create </code><code style="color:green;">RESOURCE</code>
* Can create pods, services, etc. <!-- .element: class="fragment" data-fragment-index="0" -->
* Let's create a namespace for our application <!-- .element: class="fragment" data-fragment-index="1" -->

```
kubeptl create namespace vote
```
<!-- .element: class="fragment" data-fragment-index="2" -->
```
namespace "vote" created
```
<!-- .element: class="fragment" data-fragment-index="3" -->


#### Watch cluster
* In another terminal, run the following

```
watch -t -n1 'echo Vote Pods \
   && kubectl --server=127.0.0.1:8001 get pods -n vote -o wide \
   && echo && echo vote Services \
   && kubectl --server=127.0.0.1:8001 get svc -n vote \
   && echo && echo vote Deployments \
   && kubectl --server=127.0.0.1:8001 get deployments -n vote \
   && echo && echo Nodes \
   && kubectl --server=127.0.0.1:8001 get nodes -o wide'
```



#### Load Specification Files
<code>kubectl </code><code style="color:blue;">apply</code><code style="color:green;"> -f specfile.yml</code>

* The <!-- .element: class="fragment" data-fragment-index="0" -->`apply` command _applies_ a configuration to a specific resource
*  <!-- .element: class="fragment" data-fragment-index="1" -->The entire vote app is specified in yaml files
   ```
   cd ~/example-voting-app
   kubeptl apply -n vote -f k8s-specifications
   ```
* <!-- .element: class="fragment" data-fragment-index="2" -->This tells Kubernetes to begin setting up containers
   + creates network endpoints
   + assigns Pods to replication controller
* When you run this, go back to the <!-- .element: class="fragment" data-fragment-index="3" -->_watcher_ terminal



#### View Website
* Once all containers are running you can visit your website
* You first need to find a couple ports:
   <pre><code data-trim data-noescape>
	vote Services
	NAME      TYPE        CLUSTER-IP       ...   PORT(S)          AGE
	db        ClusterIP   10.108.228.228   ...   5432/TCP         3h
	redis     ClusterIP   10.107.101.100   ...   6379/TCP         3h
	result    NodePort    10.107.43.36     ...   5001:<mark>31001/TCP</mark>   3h
	vote      NodePort    10.104.244.69    ...   5000:<mark>31000/TCP</mark>   3h
</code></pre> <!-- .element: style="font-size:13pt;" -->
* Go to the [voting app](http://voting.appl:31000). You may need to
  change the port


#### Exercise: Scale number of replicas for vote

* Increase the number of replicas (pods) for the _vote_ service
   <pre class="fragment" data-fragment-index="0"><code data-trim data-noescape>
      kubeptl -n vote <mark>scale deployment vote</mark> --replicas=9
    </code></pre>
* Keep an eye on <!-- .element: class="fragment" data-fragment-index="1" -->_watcher_ terminal 
* Try varying number of replicas up and down<!-- .element: class="fragment" data-fragment-index="2" -->



##### Exercise:  Update voting app
* Update the _vote_ application with your image
   <pre><code data-trim data-noescape>
   kubeptl -n vote <mark>set image</mark> deployment/vote \
            vote=YOURNAME/vote:v2
  </code></pre>
* Watch the _watcher_ terminal
* Refresh the site several times while update is running


#### Summary
* In this section we deployed a microservice application
* kubectl can scale services up or down
* Deployment rolling updates ensure that the application updates seemlessly
  with zero downtime
