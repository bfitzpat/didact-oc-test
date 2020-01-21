# OpenShift + Camel K End to End Example in Didact

This tutorial will walk you through doing an end-to-end example of Apache Camel K, using AMQ Online, on an OpenShift platform. 

We will first test using the OC CLI directly.

## OC CLI

First, we will try simply calling `oc status` at the command line.

[Click here for `oc status`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20status "Call `oc status` in a terminal window"){.didact}

## Installing the AMQ Online Operator

Next, we will try getting a list of the available operators from the OperatorHub with `oc get packagemanifests -n openshift-marketplace`.

[Click here to `get packagemanifests`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20get%20packagemanifests%20-n%20openshift-marketplace "Call `oc get packagemanifests -n openshift-marketplace` in a terminal window"){.didact}

Now let's install a new operator from OperatorHub. Something like `oc describe packagemanifests <operator_name> -n openshift-marketplace`. In this case we'll try and install the `amq-online` operator.

[Click here to `oc describe packagemanifests amq-online`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20describe%20packagemanifests%20amq-online%20-n%20openshift-marketplace "Call `oc describe packagemanifests amq-online -n openshift-marketplace` in a terminal window"){.didact}

Lastly, we'll create a Subscription object YAML file to describe a namespace to an Operator -- in this case our `amq-online` operator.

    `
    apiVersion: operators.coreos.com/v1alpha1
    kind: Subscription
    metadata:
        name: amq-online
        namespace: openshift-operators 
    spec:
        channel: alpha
        name: amq-online 
        source: redhat-operators 
        sourceNamespace: openshift-marketplace
`

[Click here to create the file](didact://?commandId=vscode.didact.scaffoldProject&extFilePath=redhat.didact-oc-test/didact/amq-online-sub.json&completion=Created%20subscription%20yaml%20file.){.didact}

[Click here to register the subscription with `oc apply -f amq-online-sub.yaml`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20apply%20-f%20amq-online-sub.yaml "Call `oc apply -f amq-online-sub.yaml` in a terminal window"){.didact}

## Creating a Broker and Queue

Looking through the AMQ docs - https://access.redhat.com/documentation/en-us/red_hat_amq/7.5/html-single/deploying_amq_broker_on_openshift/index

It appears that we will need to download the amq-broker-operator-7.5.0-ocp-install-examples.zip from here https://access.redhat.com/jbossnetwork/restricted/listSoftware.html?product=jboss.amq.broker&downloadType=distributions -- but I do not have access through my RH account.

If we get the examples, we should have access to the deploy/examples and may be able to use address-create-queue.yaml to create an address space and a queue to access in our Camel K route. 

We may then be able to do something similar to how we registered our subscription earlier -- `oc apply -f address-create-queue.yaml`

## Installing the Camel K Operator

NOTE: It's quite possible that we can do this MUCH more simply with `kamel install --cluster-setup`. We should be able to add this as a command available in the Camel K Extension to simplify the process if that's the case. 

Installing the Camel K operator seems to be a community operator - https://operatorhub.io/operator/camel-k 

We should be able to create this camel-k.yaml file:

`
apiVersion: operators.coreos.com/v1alpha1 
kind: Subscription 
metadata: 
    name: my-camel-k 
    namespace: operators 
spec: 
    channel: alpha 
    name: camel-k 
    source: operatorhubio-catalog 
    sourceNamespace: olm
`

And then install it with `oc apply -f camel-k.yaml` in a terminal window

We probably need to create the following custom resource integrationplatform.yaml:

`
apiVersion: camel.apache.org/v1
kind: IntegrationPlatform
metadata:
  name: camel-k
  labels:
    app: "camel-k"
`

And call it with `oc apply -f integrationplatform.yaml`

## Creating a Camel K Route

From here, it's a matter of creating a route and using camel-amq or whatever the correct component is to access AMQ correctly with an address and queue name. Maybe do something like the Camel K example amqpbindtoregistry.java - https://github.com/apache/camel-k/blob/master/examples/AMQPBindToRegistry.java 

This would be more along the lines of scaffolding a new project, defining the right URLs for the broker/queue or whatever else we need, and moving forward from there. 

If the `kamel` cli is available and configured properly, and the Camel K extension is installed, this all should just... work. So you could create a file and deploy it in --dev mode to see it work.

I was able to, connected to Nicola's environment via my linux box - start deploying the `simple.js` example. Unfortunately, it hangs because we're still using version 1.0.0-M4 and not 1.0.0-RC1 I think (see issue below). And I was unable to find the deployed log somewhere via my OCP dashboard. 

Note: Created this issue - https://github.com/camel-tooling/vscode-camelk/issues/270 -- workaround included.
