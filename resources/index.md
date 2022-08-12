# K8s

## Prima

### Resources
#### Structure
```yaml
apiVersion: string
kind: string
metadata:
  name: string
  labels:
    [key]: [value]
spec:
  ...
```

#### Labels
1. Labels are key/value pairs that can be attached to Kubernetes objects such as Pods, Nodes and ReplicaSets. They can be arbitrary, and are useful for attaching identifying information to Kubernetes objects. Labels provide the foundation for grouping objects.

2. Label keys can be broken down into two parts: an optional prefix and a name, separated by a slash. The prefix,if specified, must be a DNS subdomain with a 253-character limit. The key name is required and must be shorter than 63 characters. Names must also start and end with an alphanumeric character and permit the use of dashes (-), underscores (_), and dots (.) between characters.Label values are strings with a maximum length of 63 characters. The contents of the label values follow the same rules as for label keys.

3. Add labels to resources
```yaml
metadata:
    selector:
        app: alpaca #labels
        ver: 1 #labels
```

```bash
$ kubectl label po kubia-manual creation_method=manual
$ kubectl label po kubia-manual-v2 env=debug --overwrite
$ kubectl label node gke-kubia-85f6-node-0rrx gpu=true
```

4. Filter resources by labels
```bash
$ kubectl get po -l creation_method,env|creation_method=manual|'!env'|creation_method!=manual|env in (prod,devel)|env notin (prod,devel)
$ kubectl get nodes -l gpu=true

# -l="app=bandicoot,ver=2" AND
# -l="app in (alpaca,bandicoot)" OR
# -l="canary" EXISTS
# -l="!canary" NOT EXISTS
# -l="app!=bandicoot,ver!=2" NOT
```
#### Annotations
1. Annotations provide a storage mechanism that resembles labels: annotations are key/value pairs designed to hold non-identifying information that can be leveraged by tools and libraries.
While labels are used to identify and group objects, annotations are used to provide extra information about where an object came from, how to use it, or policy around that object.

2. Annotate Resources
```yaml
metadata:
	annotations:
		example.com/icon-url: "https://example.com/icon.png" #annotation
```

```bash
$ kubectl annotate pod kubia-manual mycompany.com/someannotation="foo bar"
```

3. There is overlap, and it is a matter of taste as to when to use an annotation or a label.
Annotations are used to:
- Keep track of a “reason” for the latest update to an object
- Communicate a specialized scheduling policy to a specialized scheduler
- Extend data about the last tool to update the resource and how it was updated (used for detecting changes by other tools and doing a smart merge)
- Attach build, release, or image information that isn’t appropriate for labels (may include a Git hash, timestamp, PR number, etc.)
- Enable the Deployment object to keep track of ReplicaSets that it is managing for rollouts
- Provide extra data to enhance the visual quality or usability of a UI. For example, objects could include a link to an icon (or abase64-encoded version of an icon)
- Prototype alpha functionality in Kubernetes (instead of creating a first-class API field, the parameters for that functionality are encoded in an annotation)
- A good use case is to specify the name of the person who created the object can make collaboration between everyone working on the cluster much easier.

4. Annotation keys use the same format as label keys. However, because they are often used to communicate information between tools, the “namespace” part of the key is more important.

