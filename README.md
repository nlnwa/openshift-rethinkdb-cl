# RethinkDB Cluster for Openshift

MIT Licensed by Ross Kukulinski [@RossKukulinski](https://twitter.com/rosskukulinski)

## Overview

This repository contains Kubernetes configurations to easily deploy RethinkDB.
By default, all RethinkDB Replicas are configured with Resource Limits and Requests for:

* 512Mi memory
* 200m cpu

In addition, RethinkDB Replicas are configured with a 100Mi cache-size.  All
of these settings can be tuned for your specific needs.

## Start without persistent storage

Use the template and set up a route to rethinkdb admin-service.

If the proxy-pod and the admin-pod is not booting (crash loop bakoff), its most likely the serviceaccount that doesnt have the rights to see endpoints in that namespace.
You can check this by opening a terminal to the pod and run this cmd:
curl -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" -k -XGET "https://<ip-adress-here>/api/v1/namespaces/<projectname>/endpoints/"

If you get this error:
"status": "Failure",
"message": "User \"system:deployer\" cannot get namespaces in project \"<projectname>\"",
"reason": "Forbidden",  
Run "oc policy add-role-to-group view system:serviceaccounts -n <projectname>" (may need admin or cluster-admin).
Test the curl-command again.

If everything above is ok, check if the route to the admin-service is working. Test by scaling the rethink-rc up and down (servers will connect and disconnect in admin-gui).
