---
# Path-Based Routing Microservices Project using AWS ALB & Auto Scaling

---
# Project Objective

The main objective of this project is to understand how a real-world microservices application works on AWS.

In this project, different services are deployed on different EC2 instances. Traffic is distributed using **Application Load Balancer (ALB)** and every service has its own **Auto Scaling Group**.

This architecture helps only the required service scale automatically instead of scaling the complete application.

---

# Services Used

- EC2
- Launch Template
- Auto Scaling Group
- Target Group
- Application Load Balancer (ALB)

---

# Project Architecture

```text
                   Users
                     |
                     |
        Application Load Balancer
                     |
        ----------------------------
        |            |             |
      Home        Cloths       Laptops
   Target Group  Target Group  Target Group
        |            |             |
   Auto Scaling  Auto Scaling  Auto Scaling
      Group         Group         Group
        |            |             |
      EC2          EC2           EC2
```

---

# Project Steps

## Step 1

Create **3 Launch Templates**.

One template for each service.

- Home
- Cloths
- Laptops
- 
also add following lines in users additional information while launching the templates

- Home
#!/bin/bash
apt update -y
apt install nginx -y
echo "<h1> welcome to home page </h1>" > /var/www/html/index.html
systemctl start nginx
systemctl enable nginx

-Cloths
#!/bin/bash
apt update -y
apt install nginx -y
mkdir -p /var/www/html/cloths
echo "<h1> 50 % discount on mens wear </h1>" > /var/www/html/cloths/index.html
systemctl start nginx
systemctl enable nginx

-laptop
#!/bin/bash
apt update -y
apt install nginx -y
mkdir -p /var/www/html/laptop
echo "<h1> 5% discount on laptops </h1>" > /var/www/html/laptop/index.html
systemctl start nginx
systemctl enable nginx


---

## Step 2

Create **3 Auto Scaling Groups**.

Each Auto Scaling Group uses its own Launch Template.

EC2 instances will be created automatically by Auto Scaling.

---

## Step 3

Create **3 Target Groups**.

- Home Target Group
- Cloths Target Group
- Laptops Target Group

---

## Step 4

Create **1 Application Load Balancer (ALB).**

Attach the **Home Target Group** as the default target group.

---

## Step 5

Create Rules in ALB.

Add path-based routing rules for the remaining Target Groups.

Example:

| Path | Target Group |
|------|--------------|
| `/` | Home |
| `/cloths` | Cloths |
| `/laptops` | Laptops |

---

# Why We Use ALB?

We are using **Application Load Balancer (ALB)** because this project uses **Content-Based Routing (Path-Based Routing).**

Example:

If user opens

```text
www.amazon.com
```

User lands on the **Home Page**.

If user opens

```text
www.amazon.com/cloths
```

User lands on **Cloths Service**.

If user opens

```text
www.amazon.com/laptops
```

User lands on **Laptop Service**.

Each path goes to its own microservice.

This feature is supported by **Application Load Balancer (ALB)**.

---

# Why We Create Different EC2 Instances?

Every microservice has different traffic.

Suppose there is a sale on the **Cloths** section.

Large number of users will visit only the Cloths service.

Only the Cloths server should increase automatically.

Other services like:

- Home
- Laptops

do not need extra servers.

If all microservices are running on one EC2 instance, then the whole server will scale.

This increases unnecessary resource usage and cost.

That is why every microservice has its own EC2 instance and its own Auto Scaling Group.

---

# Why We Create Different Auto Scaling Groups?

Each Auto Scaling Group controls only one microservice.

Example:

- Home → Auto Scaling Group
- Cloths → Auto Scaling Group
- Laptops → Auto Scaling Group

If Cloths traffic increases,

Only the Cloths Auto Scaling Group launches new EC2 instances.

Other services continue running normally.

This saves cost and improves performance.

---

# Why We Create Different Target Groups?

Each Target Group is connected to one microservice.

ALB forwards requests to the correct Target Group based on the URL path.

Example:

```text
/          → Home Target Group

/cloths    → Cloths Target Group

/laptops   → Laptops Target Group
```

This makes routing simple and organized.

---

# Why We Use Launch Templates?

Launch Templates contain the EC2 configuration.

It includes:

- AMI
- Instance Type
- Security Group
- Key Pair
- Storage
- User Data (if required)

Whenever Auto Scaling creates a new EC2 instance, it uses the Launch Template.

This keeps every new instance identical.

---

# Important Points

- Project is based on **Microservices Architecture**.
- Every service runs independently.
- Every service has its own EC2 instance.
- Every service has its own Auto Scaling Group.
- Every service has its own Target Group.
- ALB routes traffic based on URL path.
- Only the required service scales automatically.
- Cost is reduced because unnecessary instances are not launched.
- Easy to manage and maintain.
- Similar architecture is used in real-world e-commerce applications.

---

# Real-World Example

Amazon is a good example.

```text
www.amazon.com
```

→ Home Page

```text
www.amazon.com/cloths
```

→ Cloths Service

```text
www.amazon.com/laptops
```

→ Laptop Service

Every service works independently.

If traffic increases only on **Cloths**, then only the Cloths servers scale automatically.

Home and Laptop servers remain unchanged.

---

# Project Summary

In this project,

- Created 3 Launch Templates.
- Created 3 Auto Scaling Groups.
- Created 3 Target Groups.
- Created 1 Application Load Balancer.
- Configured Path-Based Routing.
- Connected every microservice with its own Target Group.
- Auto Scaling creates EC2 instances automatically.
- Only the required microservice scales during high traffic.
- This architecture improves performance, reduces cost, and follows real-world microservices deployment.
