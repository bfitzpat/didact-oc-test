# Didact OC Test

The object of this particular Didact tutorial is to test the feasibility of executing OpenShift commands through Didact links.

We will first test using the OC CLI directly.

## OC CLI

First, we will try simply calling `oc status` at the command line.

[Click here for `oc status`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20status "Call `oc status` in a terminal window"){.didact}

Second, we will try getting a list of the available operators from the OperatorHub with `oc get packagemanifests -n openshift-marketplace`.

[Click here to `get packagemanifests`](didact://?commandId=vscode.didact.sendNamedTerminalAString&text=OCTerminal$$oc%20get%20packagemanifests%20-n%20openshift-marketplace "Call `oc get packagemanifests -n openshift-marketplace` in a terminal window"){.didact}

Third, let's install a new operator from OperatorHub. Something like `oc describe packagemanifests <operator_name> -n openshift-marketplace`. In this case we'll try and install the `amq-online` operator.

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

## OpenShift VS Code Extension

The next phase of this experiment will have us see if we can do the same general process using the Red Hat OpenShift Extension Pack and the RedHat OpenShift Connector.
