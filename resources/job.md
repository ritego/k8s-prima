# K8s

## Prima

### Job
1. A job creates Pods that run until successful termination (i.e., exit with 0).

2. Jobs are useful for things you only want to do once, such as database migrations or batch jobs.

3. The Job object is responsible for creating and managing Pods defined in a template in the job specification. These Pods generally run until successful completion. The Job object coordinates running a number of Pods in parallel.

4. Structure
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  ttlSecondsAfterFinished: 100
  parallelism: 1 # If it is specified as 0, then the Job is effectively paused until it is increased
  completions: 1
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```
4. completions and parallelism for a job configuration.
- Non-parallel Job - “run once until completion” pattern, the completions and parallelism parameters are set to 1.
```yaml
spec:
	parallelism: 1
	completions: 1
```
- Parallel Jobs with a fixed completion count
```yaml
spec:
	parallelism: 2
	completions: 10
```

- Parallel Jobs in a worker pool mode
When completions parameter is unset, we put the job into a worker pool mode. Once the first Pod exits with a zero exit code, the job will start winding down and will not start any new Pods. This means that none of the workers should exit until the work is done and they are all in the process of finishing up.
```yaml
spec:
	parallelism: 5
```

5. Because jobs have a finite beginning and ending, it is common for users to create many of them. This makes picking unique labels more difficult and more critical. For this reason, the Job object will automatically pick a unique label and use it to identify the Pods it creates. In advanced scenarios (such as swapping out a running job without killing the Pods it is managing), users can choose to turn off this automatic behavior and manually specify labels and selectors.

6. When a Job completes, no more Pods are created, but the Pods are usually not deleted either. Keeping them around allows you to still view the logs of completed pods to check for errors, warnings, or other diagnostic output. The job object also remains after it is completed so that you can view its status. It is up to the user to delete old jobs after noting their status.

### CronJob
1. responsible for creating a new Job object at a particular interval.

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
