# apigeecli Cheat Sheet

This file provides example invocations of the `apigeecli` command-line tool.


## Authentication

Commands will need exactly one of these options for authentication:

```
 --default-token    Use Google default application credentials access token
 --token TOKEN      Disable check for newer versions
 --metadata-token   Obtain a token from the Google Cloud metadata server.
 ```

Obtaining a token from the Google Cloud metadata server works only if the
apigeecli command is running on a machine within Google Compute Engine (also
GKE, Cloud Run, or other things that stack on top of Google Compute Engine).
In this case apigeecli will run as the identify of the machine or service.


## Create, deploy, get, list on sharedflows _or_ API Proxies

The following commands work with either `apis` or `sharedflows`.

Create ("import") a bundle from a path:

```
apigeecli {apis|sharedflows} create bundle -f /path/to/bundle -n NAME --org ORGNAME --token TOKEN
```

Deploy a bundle that has been imported:
```
apigeecli {apis|sharedflows} deploy --name NAME --rev REVISION --wait --ovr --org ORGNAME --env ENVNAME --token TOKEN
```

Get information (including revisions) about a named asset that has been imported:

```
apigeecli {apis|sharedflows} get --name NAME --org ORGNAME --token TOKEN
```

List all deployments for any environment for a given asset:

```
apigeecli {apis|sharedflows} listdeploy --name NAME --org ORGNAME --token TOKEN
```


## apis

Import an API proxy bundle from a filesystem directory, giving it a specific name:

```
apigeecli apis create bundle -f /path/to/apiproxy --name NAME --org ORGNAME --token TOKEN
```

Deploy _the latest revision_ of a sharedflow into an Apigee organization, giving it a service account as identity, overriding any existing deployment. Wait for the deployment to complete:

```
apigeecli apis deploy --name NAME --org ORGNAME --env ENVNAME --sa SERVICE_ACCOUNT --token TOKEN --ovr --wait
```

Deploy _a specific revision_ of a sharedflow into an Apigee organization, giving it a service account as identity, overriding any existing deployment. Wait for the deployment to complete:

```
apigeecli apis deploy --name NAME --rev REVISION  --org ORGNAME --env ENVNAME --sa SERVICE_ACCOUNT --token TOKEN --ovr --wait
```


Deploy _the latest revision_ of a sharedflow into an Apigee organization, without giving it a service account as identity, overriding any existing deployment. Wait for the deployment to complete:

```
apigeecli apis deploy --name NAME --org ORGNAME --env ENVNAME --token TOKEN --ovr --wait
```


Deploy _a specific revision_ of a sharedflow into an Apigee organization, without giving it a service account as identity, overriding any existing deployment. Wait for the deployment to complete:

```
apigeecli apis deploy --name NAME --rev REVISION --org ORGNAME --env ENVNAME --token TOKEN --ovr --wait
```


Import an API proxy bundle from a filesystem directory, giving it a specific name, and then deploy it and wait for completion:

```
apigeecli apis create bundle -e ENV -f FILE -n NAME --ovr --sa SERVICE_ACCOUNT --token TOKEN -o ORG --wait
```

Delete an API Proxy and all its revisions: (not sure if this undeploys)

```
apigeecli apis delete --name NAME --org ORGNAME --token TOKEN
```


Download/export an API (as proxy bundle) from the specified organization:

```
apigeecli apis fetch --name NAME --token TOKEN --org ORGNAME
```

Get information about an API from the specified organization, including revisions, etc:

```
apigeecli apis get --name NAME --org ORGNAME --token TOKEN
```

List deployments of an API Proxy by name, in any environment:

```
apigeecli apis listdeploy --name NAME --org ORGNAME --token TOKEN
```


Undeploy a particular revision of an API Proxy from a particular environment:

```
apigeecli apis undeploy --name NAME --rev REVISION --org ORG --env ENV --token TOKEN
```


Undeploy a particular revision of an API Proxy from a particular environment, _without checking_ to see if any other proxies depend on this one:

```
apigeecli apis undeploy --name NAME --rev REVISION --org ORGNAME --env ENVNAME --token TOKEN --safeundeploy false
```

Create an API Proxy-scoped KVM:

```
apigeecli apis kvm create --org ORGNAME --token TOKEN --name MAPNAME --proxy PROXY_NAME
```


## apps

Create an App belonging to a particular developer, identified by email address. Authorize the app for the given set of API products.

```
apigeecli apps create --email EMAIL --name NAME --org ORG --prods PRODS --token TOKEN
```

Create an App belonging to a particular developer, identified by email address. Authorize the app for the given set of API products. Also set some attributes on the app:

```
apigeecli apps create --name NAME --email ... --prods ... --org ORGNAME --token TOKEN --attrs ...
```

Delete an app, by app ID:

```
apigeecli apps delete --id ID --name NAME --org ORG --token TOKEN
```

Generate a new credential for an app, identified by App Name and developer email, authorized for a specific set of API products:

```
apigeecli apps genkey -d DEV --name NAME --org ORG --prods PRODS --token TOKEN
```

Import a credential (known key/secret pair) into an existing app, and authorize it for a set of API products:

```
apigeecli apps keys create --dev DEV --name NAME --org ORG --prods PRODS --key KEY --secret SECRET --token TOKEN
```


Get information about an App based on the pair of developer email and app name:

```
apigeecli apps get --name NAME --org ORG --token TOKEN
```

Get information about an App based on the App ID:

```
apigeecli apps get --id AppID --org ORGNAME
```

List apps authorized on a particular API Product:

```
apigeecli apps list --api-product PRODUCT --org ORGNAME --token TOKEN
```

Update an app with new attributes or scopes:

```
apigeecli apps update --name NAME --attrs ... --scopes ... --org ORGNAME --token TOKEN
```


## datacollectors

```
apigeecli datacollectors create -d DESCRIPTION -n NAME -p TYPE --org ORG --token TOKEN
```

```
apigeecli datacollectors delete -n NAME --org ORG --token TOKEN
```


## developers

Create a new developer registration, specifying all the required information:

```
apigeecli developers create --email EMAIL --user USERNAME --first FIRSTNAME --last LASTNAME --org ORG --token TOKEN
```

Delete a particular developer by email address:

```
apigeecli developers delete --email EMAIL --org ORG --token TOKEN
```

Get a particular developer by email address:

```
apigeecli developers get --email EMAIL --org ORGNAME --token TOKEN
```

List all the developers:

```
apigeecli developers list  --org ORGNAME --token TOKEN
```


List all the developers with a gmail.com email address:

```
apigeecli developers list  --org ORGNAME --token TOKEN | jq -r '.developer[].email | select(endswith("@gmail.com"))'
```


## envs

List deployments of API proxies in an environment:

```
apigeecli envs deployments get --env ENV --org ORG --sharedflows true --token TOKEN
```

List deployments of sharedflows in an environment:

```
apigeecli envs deployments get --env ENV --org ORG --token TOKEN
```


## envgroups

```
apigeecli envgroups attach --env ENV --name NAME --org ORG --token TOKEN --wait
```

```
apigeecli envgroups create --hosts HOSTS --name NAME --org ORG --token TOKEN --wait
```

```
apigeecli envgroups delete --name NAME --org ORG --token TOKEN
```

```
apigeecli envgroups detach --env ENV --name NAME -o ORG -t TOKEN
```


### environments

List environments in an organization:

```
apigeecli environments list --org ORGNAME --token TOKEN
```

Create an environment in an organization:

```
apigeecli environments create --deptype PROXY --env ENV --org ORG --token TOKEN --wait
```

Delete an environment in an organization:
```
apigeecli environments delete --env ENV --org ORG --token TOKEN --wait
```

Set an environment property for an environment:

```
apigeecli environments set --env ENV --name NAME --org ORG --token TOKEN --value VALUE
```

Get environment properties for an environment:

```
apigeecli environments get --env ENV --name NAME --org ORG --token TOKEN
```



### instances

Attach an instance to an environment:

```
apigeecli instances attachments attach --env ENV --name NAME --org ORG --token TOKEN --wait
```

Detach an instance from an environment:

```
apigeecli instances attachments detach --env ENV --name NAME --org ORG --token TOKEN --wait
```

Get information for an instance, identified by name:

```
apigeecli instances get --name NAME --org ORG --token TOKEN
```

List the instances known in an Apigee organization:
```
apigeecli instances list -o ORG -t TOKEN
```


### kvms

Create an Environment-scoped KVM:

```
apigeecli kvms create --env ENV --name MAPNAME --org ORG --token TOKEN
```

Delete an Environment-scoped KVM:

```
apigeecli kvms delete --env ENV --name MAPNAME --org ORG --token TOKEN
```

Create a single key/value map entry in an Environment-scoped KVM:

```
apigeecli kvms entries create --env ENV --map MAP --org ORG --token TOKEN  --key KEY --value VALUE
```

Import the KVM entries in an KVM export file:

```
apigeecli kvms import -f /path/to/kvm-data-file.json --org ORG --token TOKEN
```

Export the entries in a particular Environment-scoped KVM:

```
apigeecli kvms entries export --map MAPNAME -o ORG --env ENVNAME --token TOKEN
```

Export the entries in a particular Proxy-scoped KVM:

```
apigeecli kvms entries export --map MAPNAME -o ORG -p PROXY --token TOKEN
```

List the entries in a particular Environment-scoped KVM:

```
apigeecli kvms entries list -m MAPNAME --org ORGNAME --env ENVNAME
```

List the Environment-scoped KVMs in an environment:

```
apigeecli kvms list --env ENVNAME --org ORGNAME --token TOKEN
```


### operations

Get the operations defined for a specified API Product:

```
apigeecli operations get -n PRODUCTNAME -o ORG --token TOKEN
```


### organizations

```
apigeecli organizations get --org ORG --token TOKEN
```


### products


Create an API product, using externally defined operations, with auto approval and access=public, and a quota of 100/minute,
and using Application Default Credentials:

```
apigeecli products create --name $product_name \
  --display-name $product_name \
  --opgrp $ops_file \
  --envs $env \
  --approval auto \
  --space $space \
  --attrs access=public \
  --quota 100 --interval 1 --unit minute  --default-token
```

Create an API product, using externally defined operations, with auto approval and access=public, and no quota information:
```
apigeecli products create  --org ORG --name PRODICT_NAME --display-name NAME \
  --approval auto  --attrs access=public \
  --envs ENV --opgrp /path/to/operations.json --token TOKEN
```

```
apigeecli products create --approval auto --display-name NAME --envs ENV --grpcopgrp FILE --org ORG --token TOKEN
```

Create an API product, using externally defined operations, with auto approval and access=public, and specifying quota information:

```
apigeecli products create --name NAME --display-name DISPLAY_NAME \
 --org ORG --envs ENV \
 --approval auto --opgrp FILE \
 --quota QUOTA --interval INTERVAL --unit {hour|minute|day} \
 --token TOKEN
```

```
apigeecli products create --name NAME --approval auto --display-name NAME \
   --org ORG --envs ENV --opgrp FILE --token TOKEN \
  --quota QUOTA  --interval INTERVAL --unit minute
```

```
apigeecli products create --approval auto --display-name NAME --envs ENV \
  --opgrp FILE --org ORG --scopes SCOPES --scopes SCOPES --token TOKEN
```

```
apigeecli products create --approval auto --display-name NAME --envs ENV \
  --opgrp FILE --org ORG --scopes SCOPES --token TOKEN
```

```
apigeecli products create --approval auto --display-name DISPLAYNAME \
  --envs ENV --opgrp FILE --org ORG --token TOKEN
```

```
apigeecli products create --name NAME --display-name DISPLAY_NAME \
  --proxies ... --approval-type ... --org ORGNAME --token TOKEN
```

```
apigeecli products create --name NAME --display-name DISPLAY_NAME \
  --proxies ... --envs ... \
  --approval-type ... --org ORGNAME --token TOKEN
```

```
apigeecli products create --name NAME --display-name DISPLAY_NAME \
 --proxies ... --scopes ... --attrs ... --approval ... \
 --org ORGNAME --token TOKEN
```


Delete an API Product, by name:

```
apigeecli products delete --name NAME --org ORGNAME --token TOKEN
```


Get information about an API Product, by name:

```
apigeecli products get --name NAME --org ORGNAME --token TOKEN
```


### resource files

Create an Environment-scoped resource and populate it with the content from a filesystem file:

```
apigeecli resources create --org ORGNAME" --env ENVNAME --token TOKEN --name NAME_OF_RESOURCE --type {properties|js|py|xsd|wsdl|jar|...} --respath /path/to/resource-filename
```

Delete an Environment-scoped resource:
```
apigeecli resources delete --env ENV --name NAME_OF_RESOURCE --org ORG --token TOKEN  --type {properties|js|py|xsd|wsdl...}
```


### sharedflows

Import a sharedflow into an Apigee organization, from a filesystem bundle:

```
apigeecli sharedflows create bundle  -f /path/to/sharedflowbundle -n NAME --org ORG --token TOKEN
```

Import a sharedflow, and Obtain the revision number:

```
REV_SF=$(apigeecli sharedflows create bundle -f /path/to/sharedflowbundle -n NAME_OF_SF --org ORGNAME --token TOKEN | jq ."revision" -r)
```

Deploy a specific revision of a sharedflow into an Apigee organization, with no service account as identity, overriding any existing deployment. Wait for the deployment to complete:

```
apigeecli sharedflows deploy --name NAME --rev REVISION --org ORGNAME --env ENVNAME --token TOKEN --ovr --wait
```

Deploy a specific revision of a sharedflow into an Apigee organization, with no service account as identity, overriding any existing deployment. Do not wait for the deployment to complete:

```
apigeecli sharedflows deploy --name NAME --rev REVISION --org ORGNAME --env ENVNAME --token TOKEN --ovr
```

Deploy a specific revision of a sharedflow into an Apigee organization, with no service account as identity, without waiting for the deployment to complete, and without forcing overwrite of any existing deployment:

```
apigeecli sharedflows deploy --name NAME --rev REVISION --org ORGNAME --env ENVNAME --token TOKEN
```

Deploy a specific revision of a sharedflow into an Apigee organization, giving it a specific service account as identity:

```
apigeecli sharedflows deploy --name NAME --org ORGNAME --env ENV --ovr --rev REV --sa SERVICE_ACCOUNT --token TOKEN
```

Import a sharedflow, then _deploy_ the sharedflow, giving it a specific serviceaccount, and wait for the deployment to complete:

```
apigeecli sharedflows create bundle -o ORG -f FILE -n NAME -e ENV --ovr --sa SERVICE_ACCOUNT --token TOKEN --wait
```

Delete a sharedflow from an organization, by name:

```
apigeecli sharedflows delete --name NAME --org ORGNAME --token TOKEN
```

Get the information about a named sharedflow:

```
apigeecli sharedflows get --name NAME --org ORGNAME --token TOKEN
```

List deployments of a named sharedflow in all environments in the organization:

```
apigeecli sharedflows listdeploy --name NAME --org ORGNAME --token TOKEN
```

Undeploy a sharedflow from an environment:

```
apigeecli sharedflows undeploy --env ENV --name NAME --org ORG --rev REV --token TOKEN
```

Undeploy a particular revision of a sharedflow from an environment:

```
apigeecli sharedflows undeploy --name NAME --env ENVNAME --rev REVISION --org ORGNAME --token TOKEN
```


### targetservers

```
apigeecli targetservers create --env ENV --name NAME --org ORG --port PORT -s HOST --tls --token TOKEN
```

```
apigeecli targetservers delete --env ENV --name NAME --org ORG --token TOKEN
```
