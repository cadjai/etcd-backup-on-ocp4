# etcd-backup-on-ocp4

This repository provides and automated way to perform etcd backup on OpenShift Container 4.x which has a commumity operator to perform the backup and store the backup files into S3. However the operator does not always work and there are a few bugzilla (https://bugzilla.redhat.com/show_bug.cgi?id=1686312) and RFE (https://issues.redhat.com/browse/ETCD-123 and  https://issues.redhat.com/browse/ETCD-81). 
This has a playbook to deploy and configure the cronjob that is used to perform the backup and content push to S3.
It also has a playbook to perform the content move from the debug node to the either a local ansible controller or to S3.
Add a new playbook that deploy the cron job using the AWS CLI docker container instead of the original playbook that uses two images. This only pushes the backup files as created not like the original which creates a bundle of all file with the date before pushing. This changed was prompted by the solution provided by Dan Clark https://gist.githubusercontent.com/dmc5179/ed7285c5f7d67a5575e5414251d09662/raw/aa278efd57e36e1c0172240822fb43ad7f03fba4/etcd-backup-s3.yaml[here]


Play Variables
--------------

For the cronjob playbook the following variables are used:
- etcdbackup_job_namespace: The name of the namespace or project the cronjob will be deployed in OpenShift to perform the backup.
- etcdbackup_job_namespace_description: Decription of the namespace object. Default to the name of the namespace.
- etcdbackup_service_account: The name of the service account used to run the cronjob. This is the account that will have the privileged scc permissions.
- ocp_cluster_user: The username of the cluster-admin user to use to run this playbook.
- ocp_cluster_user_password: The password associated with the username above.
- ocp_cluster_console_url: The URL to the API for the cluster this will be deployed to.
- ocp_cluster_console_port: The API port for the cluster this will be deployed to. Default to 6443 when not set.
- openshift_cli: The path to the OpenShift client binary used to run the commands use to intereact with the cluster. It could be the path to kubectl or oc. Default to oc when not set assuming that the client on on your path.
- aws_access_key: The AWS Secret Key to use if you want to push the backup files to S3.
- aws_key_secret: The AWS Key Secret to use if you want to push the backup files to S3.
- aws_bucket: The AWS Bucket in which to store the backup files if you want to push the backup files to S3.
- aws_bucket_object: The object to store the files in if you want to push the backup files to S3.
- image_pullsecret_json: The encrypted value of the docker config json for the Red Hat registry where the images used are downloaded from. This is basically the base64 encrypted of runninga command like `podman login -u username -p password registry.redhat.io --authfile ~/.docker/json.

There are few other optional variables that you can change to your liking but don't have to change. 


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.


Installation and Usage
-----------------------
Clone the repository to where you want to run this from and make sure you can connect to your cluster using the CLI . 
You will also need to have cluster-admin run in order to run the playbook since the cron job need to run privileged.
Finally before running the playbook make sure to set and update the variables as appropriate to your use case.

Playbooks
---------
To run the main playbook use the ansible-playbook command as follows   
`ansible-playbook deploy-etcd-backup-cronjob.yml --vault-id @prompt -vvv`  

To run the AWS CLI based playbook use the ansible-playbook command as follows   
`ansible-playbook deploy-etcd-backup-cronjob-awscli.yml --vault-id @prompt -vvv`  


License
-------

BSD

Author Information
------------------

