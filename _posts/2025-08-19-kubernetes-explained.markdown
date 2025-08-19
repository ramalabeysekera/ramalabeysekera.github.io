---
layout: post
title:  "Kubernetes: You Ask, It Delivers"
date:   2025-08-19 21:33:48 +0100
categories: kubernetes
---

## <u>The Problem</u>

Let's say you have an application which you have containerized and started running in a container! For your local development, that's fantastic! You can make changes and test them without worrying about dependency issues when you deploy to a production environment, because the container image includes your app as well as the dependency libraries needed for the app to function.

But when you want to deploy it in a production environment, reality hits hard. What happens when:
- Your container crashes?
- The server itself goes down?
- You need to scale to handle more traffic?
- You need to update your application without downtime?

You need something that can manage these containers for you automatically.

## <u>Enter Container Orchestration</u>

That's where container orchestration comes into play. Kubernetes is an open-source container orchestration engine that manages containers for you. Think of it as your personal assistant for containers - **you ask for what you want, and Kubernetes delivers**.

Here's what this looks like in practice:

**You ask**: "I want my web app running with 3 replicas, accessible from the internet"
```bash
kubectl create deployment webapp --image=myapp:v1 --replicas=3
kubectl expose deployment webapp --type=LoadBalancer --port=80
```

**Kubernetes delivers**: Within seconds, you have 3 pods running your app behind a load balancer, complete with health monitoring and automatic restarts.

## <u>How Kubernetes Works: The Big Picture</u>

Instead of telling Kubernetes *how* to do something step-by-step, you simply tell it *what* you want the end result to look like. Kubernetes figures out how to make it happen.

You describe your desired state using **Kubernetes objects** (more on these later), and Kubernetes continuously works to make the actual state match your desired state.

## <u>Real-World "Ask and Deliver" Examples</u>

Let me show you the magic in action:

### Example 1: Handling Crashes
**You ask** (once): "Keep my app running with 2 replicas"
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  ...
```

**Kubernetes delivers** (continuously): Even if a pod crashes at 3 AM, Kubernetes immediately creates a replacement. You wake up to find your app never went down.

### Example 2: Scaling Under Load
**You ask**: "I need 10 replicas now!"
```bash
kubectl scale deployment my-app --replicas=10
```

**Kubernetes delivers**: New pods start spinning up across available nodes, and your load balancer automatically includes them in rotation.

## <u>Architecture Overview</u>

To understand how this works, let's look at the Kubernetes architecture. You can see the detailed architecture [here](https://kubernetes.io/docs/concepts/architecture/).

A Kubernetes cluster consists of two main parts:

### Control Plane: The Brain
The control plane is like the management center of your cluster. It includes:
- **API Server**: The front door - all communications go through here
- **etcd**: The database that stores the cluster's desired state
- **Scheduler**: Decides which node should run your containers
- **Controller Manager**: Runs all the controllers that make things happen

### Nodes: The Workers
Nodes are the machines (physical or virtual) that actually run your containers. Each node has:
- **kubelet**: The agent that communicates with the control plane
- **Container runtime**: Actually runs the containers (like Docker)
- **kube-proxy**: Handles networking

The beauty is that you interact with this complexity through simple requests to the API server - you ask, and all these components work together to deliver.

## <u>Communicating with Kubernetes</u>

When you want to tell Kubernetes what you want, you communicate with the API server. The most popular way to do this is using `kubectl` - a command-line tool that makes API requests on your behalf.

You can:
- Run kubectl commands directly: `kubectl create deployment my-app --image=nginx`
- Apply YAML manifests: `kubectl apply -f my-deployment.yaml`

<center><img src="/assets/images/kubectl-meme.jpg" alt="It works on my machine meme" width="300"></center><br/>

Either way, you're essentially saying "Hey Kubernetes, this is what I want" and kubectl translates that into API calls.

## <u>Understanding Kubernetes Objects</u>

A Kubernetes object is basically a **record of intent** - you're telling Kubernetes "this is how I want things to be" and Kubernetes will work continuously to make that happen.

These objects represent the desired state of your cluster. Think of them as a wishlist you give to Kubernetes:
- "I want 3 copies of my web application running"
- "I want them accessible through a load balancer"
- "I want them to automatically restart if they crash"

## <u>Meet the Pod: The Basic Building Block</u>

Before we dive into how Kubernetes manages your applications, let's understand the fundamental unit it works with.

The smallest deployable unit in Kubernetes isn't a container - it's a **Pod**. Think of a Pod as a wrapper around your container(s).

Most of the time, it's just one container per pod (because let's face it, roommates can be complicated), but sometimes containers need to share resources like storage or network, so they bunk together in the same Pod.

A Pod provides:
- **A shared network**: All containers in a Pod share the same IP address and ports
- **Shared storage volumes**: Containers can share files through mounted volumes
- **A shared lifecycle**: They start together, live together, and die together

### Important Pod Characteristics

- **Ephemeral**: If a Pod dies, Kubernetes doesn't try to resurrect it. Instead, it creates a brand new Pod to replace it.
- **Unique IP**: Each Pod gets its own IP address within the cluster
- **Single Purpose**: Pods should represent a single unit of deployment

Now that you understand Pods, let's see how Kubernetes manages them for you.

## <u>Controllers: The Unsung Heroes</u>

Controllers are like diligent workers who never sleep. Each controller is responsible for a specific type of object and follows a simple **control loop**:

1. **Observe**: What's the current state?
2. **Compare**: How does it differ from the desired state?
3. **Act**: Take action to reconcile the difference
4. **Repeat**: Forever and ever

### Types of Controllers

- **ReplicaSet Controller**: Ensures you have the right number of Pod replicas running
- **Deployment Controller**: Manages ReplicaSets and handles rolling updates
- **Node Controller**: Monitors node health and responds when nodes go down
- **Job Controller**: Ensures batch jobs run to completion

### Real-World Example: The Promise Keeper in Action

Let's say you tell Kubernetes: "I want 3 replicas of my web app running."

1. The ReplicaSet controller constantly monitors how many Pods are actually running
2. If one Pod crashes (now you have 2 instead of 3), the controller notices the mismatch
3. It immediately creates a new Pod to replace the failed one
4. Your desired state of 3 replicas is restored

This is the "You Ask, It Delivers" promise in action - you made one request for 3 replicas, and Kubernetes never stops working to honor that request, even years later.

## <u>The "You Ask, It Delivers" Promise</u>

This is the true power of Kubernetes. Unlike traditional infrastructure where you have to manually:
- Monitor for failures and restart services
- Manually scale up during traffic spikes  
- Coordinate rolling updates across multiple servers

With Kubernetes, you simply declare what you want once, and it continuously delivers:

**You ask once**: "I want a resilient, scalable web service"
**Kubernetes delivers forever**: Automatic healing, scaling, updates, networking, and more

The magic isn't just that Kubernetes can do these things - it's that once you ask, you can forget about the how and focus on the what. That's the real promise: **You ask, it delivers, and it never stops delivering.**

