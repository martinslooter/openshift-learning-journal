# Kubernetes & OpenShift: 16-Week Study Checklist

**Goal:** CKA (Kubernetes) first, then EX280 (OpenShift Administration)
**Pace assumed:** ~6-8 hours per week. Stretch phases if you have less time, but don't skip the labs.
**Started:** ____ / ____ / ______  **Target CKA date:** ____ / ____ / ______  **Target EX280 date:** ____ / ____ / ______

> **How to use this:** For each week, read the theory first (about 30-40% of your time), then do the exercises (60-70%). Tick the box only when you could do the exercise again from memory, with only the official docs open.
> **Link note:** Links point to stable top-level doc pages. Vendor sites reorganise sometimes, so if a link breaks, search the page title on the same site.

---

## Before You Start: Lab Setup

- [ ] Buy 3x HP EliteDesk 800 G3/G2 SFF (check they have 2 DDR4 slots, SSD or NVMe, and a working NIC)
- [ ] Upgrade RAM to 16GB per node (32GB on at least one node if you want Single Node OpenShift later)
- [ ] Install the same Linux distro on all nodes (any RHEL-family or Debian-family distro is fine)
- [ ] Give each node a static IP or DHCP reservation and a hostname (e.g. `k8s-cp1`, `k8s-w1`, `k8s-w2`)
- [ ] Set up SSH keys from your laptop to all nodes
- [ ] Create a Git repository for notes, manifests and scripts (one folder per week)
- [ ] Practice VMs on your laptop with virt-manager (until the hardware arrives, 3 VMs of 2 vCPU / 4GB each is enough for weeks 1-8)
- [ ] Bookmark the docs you are allowed to use in the exams (see "Exam Rules" at the end)

**Tools to know about:**
- kind (Kubernetes in Docker/Podman): https://kind.sigs.k8s.io/
- k3s (lightweight Kubernetes): https://k3s.io/
- Talos Linux (immutable Kubernetes OS): https://www.talos.dev/
- Podman docs: https://docs.podman.io/

---

# PHASE 1: Kubernetes Core (Weeks 1-8)

## Week 1: Foundations and kubectl

**Theory**
- [ ] Kubernetes overview: https://kubernetes.io/docs/concepts/overview/
- [ ] Cluster architecture (control plane, kubelet, kube-proxy, etcd): https://kubernetes.io/docs/concepts/architecture/
- [ ] Interactive basics tutorial: https://kubernetes.io/docs/tutorials/kubernetes-basics/
- [ ] kubectl quick reference: https://kubernetes.io/docs/reference/kubectl/quick-reference/
- [ ] Containers refresher (images, layers, runtimes): https://kubernetes.io/docs/concepts/containers/

**Practice**
- [ ] Create a local cluster with kind or k3s in a VM
- [ ] Run `kubectl get nodes`, `kubectl get pods -A`, `kubectl cluster-info`
- [ ] Create a pod imperatively: `kubectl run web --image=nginx`
- [ ] Generate YAML without creating anything: `kubectl run web --image=nginx --dry-run=client -o yaml > web.yaml`
- [ ] Use `kubectl explain pod.spec.containers` to find the fields for ports, env and resources
- [ ] Set up shell aliases and completion (`alias k=kubectl`, `source <(kubectl completion bash)`)
- [ ] Delete the pod and recreate it from your YAML file

**Done when:** you can create, inspect and delete a pod using only the CLI and `kubectl explain`.

## Week 2: Workloads

**Theory**
- [ ] Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- [ ] Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- [ ] Jobs: https://kubernetes.io/docs/concepts/workloads/controllers/job/
- [ ] CronJobs: https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
- [ ] Labels and selectors: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
- [ ] Probes: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
- [ ] Requests and limits: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

**Practice**
- [ ] Create a Deployment with 3 replicas of nginx
- [ ] Scale it to 5 and back to 2 with `kubectl scale`
- [ ] Update the image to a new tag and watch the rollout: `kubectl rollout status`
- [ ] Break the update with a bad image tag, then roll back with `kubectl rollout undo`
- [ ] Add a readiness probe and a liveness probe, then make the probe fail on purpose and observe the behaviour
- [ ] Set CPU/memory requests and limits. Set a memory limit too low and observe the OOMKilled event
- [ ] Create a Job that runs to completion and a CronJob that runs every minute
- [ ] Label pods and select them with `-l`

**Done when:** you can explain the difference between a Pod, ReplicaSet and Deployment, and can roll back a bad release.

## Week 3: Networking

**Theory**
- [ ] Services: https://kubernetes.io/docs/concepts/services-networking/service/
- [ ] DNS for services and pods: https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- [ ] Ingress: https://kubernetes.io/docs/concepts/services-networking/ingress/
- [ ] Gateway API: https://gateway-api.sigs.k8s.io/
- [ ] Network policies: https://kubernetes.io/docs/concepts/services-networking/network-policies/
- [ ] Cluster networking model: https://kubernetes.io/docs/concepts/cluster-administration/networking/

**Practice**
- [ ] Expose a Deployment as ClusterIP, NodePort and (if available) LoadBalancer. Know the differences
- [ ] Reach a service from another pod by DNS name (`curl http://svc-name.namespace.svc.cluster.local`)
- [ ] Install an ingress controller (for example ingress-nginx) and route two hostnames to two services
- [ ] Create a default-deny NetworkPolicy in a namespace, then allow only one specific pod to talk to another
- [ ] Use `kubectl exec` and a debug pod to test connectivity before and after the policy
- [ ] Inspect endpoints: `kubectl get endpoints` and see how it changes when pods fail readiness

**Done when:** you can expose an app, route traffic by hostname and lock it down with a NetworkPolicy.

## Week 4: Storage and Configuration

**Theory**
- [ ] Persistent volumes and claims: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
- [ ] StorageClasses: https://kubernetes.io/docs/concepts/storage/storage-classes/
- [ ] StatefulSets: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
- [ ] ConfigMaps: https://kubernetes.io/docs/concepts/configuration/configmap/
- [ ] Secrets: https://kubernetes.io/docs/concepts/configuration/secret/
- [ ] Volumes overview: https://kubernetes.io/docs/concepts/storage/volumes/

**Practice**
- [ ] Create a PersistentVolume and a PersistentVolumeClaim manually (hostPath is fine in the lab)
- [ ] Mount the claim in a pod, write a file, delete the pod, and confirm the file survives
- [ ] Install a dynamic provisioner (for example local-path-provisioner) and use a StorageClass
- [ ] Deploy a database (Postgres or MariaDB) as a StatefulSet with a volumeClaimTemplate
- [ ] Inject configuration with a ConfigMap as environment variables and as a mounted file
- [ ] Store a password in a Secret and consume it in a pod. Decode it with `base64 -d` and note why base64 is not encryption
- [ ] Change a ConfigMap and observe which consumption method updates live

**Done when:** a database pod can be deleted and its data is still there on restart.

## Week 5: Build a Real Cluster (kubeadm)

**Theory**
- [ ] Installing kubeadm: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- [ ] Creating a cluster with kubeadm: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
- [ ] Container runtimes: https://kubernetes.io/docs/setup/production-environment/container-runtimes/
- [ ] Optional deep dive: Kubernetes the Hard Way: https://github.com/kelseyhightower/kubernetes-the-hard-way

**Practice**
- [ ] Prepare the nodes: disable swap, load kernel modules, set sysctls, install containerd and kubeadm/kubelet/kubectl
- [ ] Run `kubeadm init` on the control plane node
- [ ] Install a CNI plugin (Calico, Cilium or Flannel)
- [ ] Join two worker nodes with `kubeadm join`
- [ ] Find the static pod manifests in `/etc/kubernetes/manifests/` and explain what each one is
- [ ] Find the kubelet config and service logs (`journalctl -u kubelet`)
- [ ] Deploy a test app and confirm it schedules to all nodes
- [ ] Tear it all down with `kubeadm reset` and build it again from your notes, without looking at the guide

**Done when:** you can build a 3-node cluster from scratch in under 30 minutes.

## Week 6: Cluster Operations

**Theory**
- [ ] etcd backup and restore: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/
- [ ] Upgrading kubeadm clusters: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- [ ] Safely draining a node: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
- [ ] Certificate management with kubeadm: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/
- [ ] Version skew policy: https://kubernetes.io/releases/version-skew-policy/

**Practice**
- [ ] Take an etcd snapshot with `etcdctl snapshot save`
- [ ] Delete a deployment and namespace, then restore etcd from the snapshot and confirm they return
- [ ] Cordon and drain a worker node, observe pods moving, then uncordon
- [ ] Upgrade the cluster by one minor version (control plane first, then workers)
- [ ] Check certificate expiry with `kubeadm certs check-expiration` and renew one
- [ ] Add a new worker node to the cluster (generate a new join token)
- [ ] Remove a worker node cleanly

**Done when:** you can restore etcd and perform a version upgrade without guidance.

## Week 7: Security

**Theory**
- [ ] RBAC: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
- [ ] ServiceAccounts: https://kubernetes.io/docs/concepts/security/service-accounts/
- [ ] Pod Security Standards: https://kubernetes.io/docs/concepts/security/pod-security-standards/
- [ ] Security contexts: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
- [ ] Certificates and users: https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/

**Practice**
- [ ] Create a Role and RoleBinding that allows one user to only view pods in one namespace
- [ ] Test it with `kubectl auth can-i --as=<user>`
- [ ] Create a user certificate through a CertificateSigningRequest and add it to a kubeconfig
- [ ] Create a ClusterRole and ClusterRoleBinding, and explain when to use each
- [ ] Create a ServiceAccount, bind a role, and use its token from inside a pod
- [ ] Run a pod as non-root with a read-only root filesystem and dropped capabilities
- [ ] Enforce the `restricted` Pod Security Standard on a namespace and see which pods are rejected

**Done when:** you can grant least-privilege access to a user or workload in under 5 minutes.

## Week 8: Scheduling, Troubleshooting and Mock Exam

**Theory**
- [ ] Taints and tolerations: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
- [ ] Node affinity and selectors: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
- [ ] Debugging overview: https://kubernetes.io/docs/tasks/debug/
- [ ] Troubleshooting clusters: https://kubernetes.io/docs/tasks/debug/debug-cluster/
- [ ] Debugging pods and services: https://kubernetes.io/docs/tasks/debug/debug-application/

**Practice**
- [ ] Taint a node and schedule a pod with a matching toleration
- [ ] Use nodeSelector and node affinity to pin a pod to a labelled node
- [ ] Break things on purpose and fix each one: wrong image name, failing probe, missing ConfigMap, Pending pod due to resources, stopped kubelet, broken static pod manifest
- [ ] Read events (`kubectl get events --sort-by=.lastTimestamp`) and logs (`kubectl logs`, `--previous`)
- [ ] Practise debugging with `kubectl debug` and ephemeral containers
- [ ] Take a **timed mock exam** (2 hours). Use Killercoda, killer.sh or your own task list
- [ ] Review every task you failed or ran out of time on and repeat it

**Done when:** you score 70%+ on a timed mock exam.

**Resources for this phase**
- CKA exam page and curriculum: https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/
- CNCF curriculum repo: https://github.com/cncf/curriculum
- Killercoda (free browser labs): https://killercoda.com
- killer.sh (exam simulator, included with exam registration): https://killer.sh

---

# PHASE 2: Production Skills (Weeks 9-10, Optional)

> If you want the CKA as soon as possible, take the exam after week 8 and come back to this phase later.

## Week 9: Packaging and GitOps

**Theory**
- [ ] Helm docs: https://helm.sh/docs/
- [ ] Kustomize: https://kubectl.docs.kubernetes.io/references/kustomize/
- [ ] Argo CD: https://argo-cd.readthedocs.io/
- [ ] Flux: https://fluxcd.io/flux/

**Practice**
- [ ] Install an app with Helm, override values, upgrade it, and roll it back
- [ ] Write your own simple Helm chart for one of your earlier apps
- [ ] Create a Kustomize base with `dev` and `prod` overlays
- [ ] Install Argo CD or Flux and sync an app from your Git repo
- [ ] Change a manifest in Git and watch the cluster reconcile automatically

**Done when:** a `git push` is enough to change what is running in your cluster.

## Week 10: Observability and Autoscaling

**Theory**
- [ ] Prometheus overview: https://prometheus.io/docs/introduction/overview/
- [ ] Grafana Loki: https://grafana.com/docs/loki/latest/
- [ ] Metrics Server: https://github.com/kubernetes-sigs/metrics-server
- [ ] Horizontal Pod Autoscaler: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

**Practice**
- [ ] Install Metrics Server and use `kubectl top nodes` and `kubectl top pods`
- [ ] Install a Prometheus and Grafana stack (for example kube-prometheus-stack through Helm)
- [ ] Build a dashboard with CPU, memory and pod restarts
- [ ] Create one alert rule (for example a node or pod down)
- [ ] Create an HPA, generate load with a simple loop, and watch pods scale out and back in
- [ ] Collect logs centrally with Loki (or another log stack)

**Done when:** you can tell from a dashboard which node or pod is unhealthy and why.

---

# PHASE 3: OpenShift (Weeks 11-16)

> **Hardware reality check (verify against current docs):** full OpenShift needs much more RAM than vanilla Kubernetes.
> - **OpenShift Local (CRC)**, single VM on your laptop: https://developers.redhat.com/products/openshift-local/overview
> - **Developer Sandbox**, free hosted, limited admin rights: https://developers.redhat.com/developer-sandbox
> - **Single Node OpenShift**, one EliteDesk with 32GB RAM
> - **OKD**, free upstream community version: https://www.okd.io/
>
> Main docs: https://docs.redhat.com/en/documentation/openshift_container_platform (choose the current version)

## Week 11: OpenShift Architecture and the `oc` CLI

**Theory**
- [ ] OpenShift documentation: Architecture section (control plane, operators, Cluster Version Operator)
- [ ] What OpenShift adds on top of Kubernetes (Projects, Routes, ImageStreams, SCCs, Operators, console)
- [ ] `oc` CLI getting started (OpenShift docs, CLI tools section)
- [ ] Red Hat Developer site, OpenShift learning material: https://developers.redhat.com/

**Practice**
- [ ] Install OpenShift Local on your laptop (or activate the Developer Sandbox)
- [ ] Log in with `oc login`, create a project, and deploy an app from a container image
- [ ] Explore the web console in both Administrator and Developer views
- [ ] List cluster operators: `oc get clusteroperators`
- [ ] Compare: do the same simple task with `kubectl` and `oc` and note what is different
- [ ] Use `oc explain` and `oc api-resources` to find OpenShift-specific resources

**Done when:** you can explain the difference between a Kubernetes Namespace and an OpenShift Project, and between Ingress and Route.

## Week 12: Application Delivery

**Theory**
- [ ] OpenShift docs: Builds (Source-to-Image, BuildConfig)
- [ ] OpenShift docs: Images (ImageStreams, registry)
- [ ] OpenShift docs: Networking, Routes (edge, passthrough, re-encrypt)
- [ ] Tekton (basis of OpenShift Pipelines): https://tekton.dev/docs/
- [ ] OpenShift docs: CI/CD, Pipelines and GitOps sections

**Practice**
- [ ] Deploy an application from a Git repo using Source-to-Image (`oc new-app <git-url>`)
- [ ] Expose a service with a Route and test HTTP and HTTPS (edge termination)
- [ ] Trigger a new build and watch the ImageStream update the deployment
- [ ] Create a simple pipeline (build, test, deploy) with OpenShift Pipelines
- [ ] Use `oc rollout` and `oc rollback` style operations on a Deployment
- [ ] Set resource quotas and limit ranges on a project

**Done when:** a code change in Git flows to a running app without manual steps.

## Week 13: Security and Access

**Theory**
- [ ] OpenShift docs: Authentication and authorization (identity providers, OAuth, users and groups)
- [ ] OpenShift docs: Security Context Constraints (SCCs)
- [ ] OpenShift docs: RBAC (cluster roles, local roles)
- [ ] OpenShift docs: Network policy and egress controls

**Practice**
- [ ] Configure an htpasswd identity provider and create two test users
- [ ] Create a group, assign a role to it, and verify with `oc auth can-i`
- [ ] Remove the default `kubeadmin` user (only after you have another cluster-admin working)
- [ ] Deploy an image that must run as root, see it fail due to SCCs, then fix it properly (without just granting `anyuid` blindly)
- [ ] Inspect which SCC a pod got: `oc get pod <name> -o yaml | grep scc`
- [ ] Create a network policy between two projects
- [ ] Manage secrets and configure a service account with a pull secret for a private registry

**Done when:** you can explain why OpenShift pods run with random UIDs and how to handle an image that does not support that.

## Week 14: Cluster Administration and Operators

**Theory**
- [ ] OpenShift docs: Operators, Operator Lifecycle Manager (OLM), OperatorHub
- [ ] OpenShift docs: Machine configuration (MachineConfig, MachineConfigPool)
- [ ] OpenShift docs: Updating clusters (channels, update process)
- [ ] OpenShift docs: Monitoring, alerting and the built-in Prometheus stack
- [ ] OpenShift docs: Logging

**Practice**
- [ ] Install an operator from OperatorHub and create a custom resource it manages
- [ ] Inspect the built-in monitoring: view metrics, alerts and dashboards in the console
- [ ] Create a user-defined project alert
- [ ] Review `oc get clusterversion` and the update channels (do not run a real update on a production cluster)
- [ ] Look at a MachineConfig and explain what it would change on a node
- [ ] Use `oc adm` commands: `oc adm top`, `oc adm must-gather`, `oc adm node-logs`
- [ ] Configure cluster-wide settings through the web console and compare with the CLI equivalents

**Done when:** you can install, inspect and remove an operator, and find the first place to look when a cluster operator is degraded.

## Week 15: Installation, Storage and Networking

**Theory**
- [ ] OpenShift docs: Installing, overview of installation methods (IPI, UPI, Assisted Installer)
- [ ] OpenShift docs: Installing on a single node (SNO)
- [ ] OpenShift docs: Storage (CSI, persistent volumes, dynamic provisioning)
- [ ] OpenShift docs: Networking (OVN-Kubernetes, ingress operator)

**Practice**
- [ ] If hardware allows: install Single Node OpenShift on an EliteDesk (32GB RAM) using the Assisted Installer
- [ ] If not: walk through the install documentation step by step and write down requirements and each phase
- [ ] Configure persistent storage and attach a PVC to an application
- [ ] Create a custom ingress/route configuration, including a custom certificate
- [ ] Practice troubleshooting: node not ready, pod pending, route not reachable, operator degraded

**Done when:** you can describe the install flow and the difference between IPI and UPI without notes.

## Week 16: Review and EX280 Preparation

**Theory**
- [ ] Re-read the official EX280 objectives (find it via Red Hat certification pages: https://www.redhat.com/en/services/certification)
- [ ] Review your notes and failed exercises from weeks 11-15

**Practice**
- [ ] Create a checklist of every EX280 objective and mark each as green, orange or red
- [ ] Redo every red and orange objective from scratch
- [ ] Do a timed practice session: 3-4 hours of mixed tasks with only the official docs
- [ ] Repeat any task that took you more than twice the time you expected
- [ ] Book the exam

**Done when:** all objectives are green and you can complete a full practice session within the time limit.

---

# Exam Readiness Checklists

## CKA readiness

- [ ] Can build a cluster with kubeadm and join nodes
- [ ] Can back up and restore etcd
- [ ] Can upgrade a cluster by one minor version
- [ ] Can create RBAC for users and service accounts
- [ ] Can troubleshoot a broken node, kubelet, static pod or service
- [ ] Can create all common resources fast with `--dry-run=client -o yaml`
- [ ] Comfortable with `vim` (or `nano`) and `tmux` in the exam terminal
- [ ] Scored 70%+ twice on timed mock exams
- [ ] Reviewed the current exam rules, allowed documentation and system requirements on the Linux Foundation website
- [ ] Exam booked: ____ / ____ / ______

## EX280 readiness

- [ ] Can configure identity providers, users, groups and roles
- [ ] Can deploy applications with S2I and from images
- [ ] Can manage Routes with TLS
- [ ] Can manage SCCs, secrets, config maps and service accounts
- [ ] Can manage quotas, limit ranges and project templates
- [ ] Can install and manage operators
- [ ] Can troubleshoot degraded operators and failing workloads
- [ ] Comfortable with `oc` and the web console
- [ ] Reviewed the current exam objectives and rules on the Red Hat website
- [ ] Exam booked: ____ / ____ / ______

## Exam Rules (verify for each exam)

- [ ] Which documentation is allowed during the exam, and bookmark the relevant pages
- [ ] Whether Red Hat/CNCF docs search is available in the exam browser
- [ ] System requirements for remote proctoring (webcam, ID, clear desk, stable connection)
- [ ] Time limit and number of tasks, and how scoring works

---

# Daily Speed Habits

- [ ] Use `k` as an alias for `kubectl` and enable auto-completion
- [ ] Use `export do="--dry-run=client -o yaml"` to generate manifests fast
- [ ] Use `kubectl config set-context --current --namespace=<ns>` rather than typing `-n` every time
- [ ] Always check the current context and namespace before running anything
- [ ] Prefer imperative commands for simple objects, `kubectl explain` and YAML for complex ones
- [ ] Commit your manifests and notes to Git at the end of each study session

---

# Weekly Review Log

| Week | Date | Hours studied | What I finished | What was hard | Redo next week |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| 6 | | | | | |
| 7 | | | | | |
| 8 | | | | | |
| 9 | | | | | |
| 10 | | | | | |
| 11 | | | | | |
| 12 | | | | | |
| 13 | | | | | |
| 14 | | | | | |
| 15 | | | | | |
| 16 | | | | | |
