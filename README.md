# CIM Enablement on kni30

When RHACM is deployed the Clucter Infrastructure Management (CIM) interface is present however there are two steps in the [documentation](https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.4/html-single/clusters/index#infra-env-prerequisites) that need to be executed to enable the proper use of CIM.

Step 1 is to patch the provisioning configuration to watch all namespaces:

~~~bash
oc patch provisioning provisioning-configuration --type merge -p '{"spec":{"watchAllNamespaces": true }}'
~~~

Next create the following agent install yaml configuration:

~~~bash
$ cat << EOF > ~/agent_service_config-kni30.yaml
apiVersion: agent-install.openshift.io/v1beta1
kind: AgentServiceConfig
metadata:
 name: agent
spec:
  databaseStorage:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 10Gi
  filesystemStorage:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 20Gi
  osImages:
    - openshiftVersion: "4.9"
      version: "49.83.202103251640-0"
      url: "https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.9/4.9.0/rhcos-4.9.0-x86_64-live.x86_64.iso"
      rootFSUrl: "https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.9/4.9.0/rhcos-live-rootfs.x86_64.img"
      cpuArchitecture: "x86_64"
EOF
~~~

Apply the agent install configuration create against the RHACM hub cluster:

~~~bash
oc create -f agent_service_config-kni30.yaml
~~~

Return to the CIM UI and start deploying!
