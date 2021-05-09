Dynamic Database Credentials with Vault and Kubernetes
Providing database credentials for your Kubernetes applications has always proved operationally challenging. For optimum security, we ideally need to implement the following requirements for database credentials:

Each Kubernetes pod should have a unique set of credentials
Credentials should be disabled or deleted when a pod terminates
Credentials should be short-lived and rotated frequently
Access should be restricted by application function, a system which only needs to read a specific table, should have database access which grants this particular purpose
While these requirements are essential for reducing the blast radius in the event of an attack, they are operationally challenging. The reality is that without automation, it is impossible to satisfy them. HashiCorp Vault solves this problem by enabling operators to provide dynamically generated credentials for applications. Vault manages the lifecycle of credentials, rotating and revoking as required.

In this blog post, we will look at how the Vault integration for Kubernetes allows an operator or developer to use metadata annotations to inject dynamically generated database secrets into a Kubernetes pod. The integration automatically handles all the authentication with Vault and the management of the secrets, the application just reads the secrets from the filesystem.

Contents
Dynamic Database Credentials with Vault and Kubernetes
Contents
Summary of Integration Workflow
Prerequisites
Introduction to Vault
Secrets
Authentication
Policy
Secrets - Configuring dynamic secrets for PostgreSQL
Enable the PostgreSQL secrets backend
Creating database roles
Creating database connections
Rotating the root credentials
Authentication - Configuring Kubernetes Authentication in Vault
Policy - Creating policy to allow access to secrets
Assigning Vault policy to Kubernetes Service Accounts
Injecting secrets into Kubernetes Deployments
Summary
Summary of Integration Workflow
When a new deployment is submitted to Kubernetes, a mutating webhook modifies the deployment, injects a Vault sidecar. This sidecar manages the authentication to Vault and the retrieval of secrets. The retrieved secrets are written to a pod volume mount that your application can read.

For example, we can use Vault to dynamically generate database credentials for a PostgreSQL database. Adding the annotations shown in the following example automatically inject secrets controlled by the db-creds role into the pod.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  labels:
    app: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
      annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/agent-inject-secret-db-creds: "database/creds/db-app"
https://www.vaultproject.io/docs/platform/k8s/injector/index.html

By convention Vault will inject these at the path /vault/secrets/<secret name>, the following snippet shows an example of this file.

username: v-kubernet-db-app-QDTv8wn6oGze6aGxuYbQ-1576778182
password: A1a-7kHXqX0jdh8ys74H
Before we will walk through the process of deploying and configuring Vault in a Kubernetes cluster and learn how to inject PostgreSQL credentials into a Kubernetes deployment. Let's introduce some HashiCorp Vault’s core concepts.

Prerequisites
You can install Vault for Kubernetes using the Helm Chart: https://www.vaultproject.io/docs/platform/k8s/helm/index.html

If you do not have access to a Kubernetes cluster with Vault and would like to try the features in this blog, you can use Shipyard to create a local Docker-based Kubernetes cluster with Vault pre-installed.

curl https://shipyard.demo.gs/vault | bash


All the example configuration mentioned in this post is available on GitHub at the following location:

https://github.com/nicholasjackson/demo-vault/tree/master/dynamic-secrets-k8s

Introduction to Vault
Vault is built around three main concepts:

Secrets
Authentication
Policy
In this section, we review how these concepts work in Vault.



Secrets
You can have static secrets like an API key or a credit card number or dynamic secrets like auto-generated cloud or database credentials. Vault generates dynamic secrets on-demand, while you receive static secrets already pre-defined.

With static secrets, you must create and manage the lifecycle of the secret. For example, you could store an email account password in Vault but you need to ensure that it is periodically changed.

With dynamic secrets, you delegate the responsibility to Vault for creating and managing the lifecycle of a secret. For example, you give Vault the root credentia
