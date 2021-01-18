# Testing With The Horizontal Pod Autoscaler

Now that we have everything up and running, it is time to test the
Kubernetes' built-in autosclaer called Horizontal Pod Autoscaler (HPA).


Clone this project's git repository:

```console
$ git clone https://github.com/pacslab/EECS6446_Project
Cloning into 'EECS6446_Project'...
remote: Enumerating objects: 138, done.
remote: Counting objects: 100% (138/138), done.
remote: Compressing objects: 100% (97/97), done.
remote: Total 138 (delta 64), reused 100 (delta 33), pack-reused 0
Receiving objects: 100% (138/138), 4.73 MiB | 10.84 MiB/s, done.
Resolving deltas: 100% (64/64), done.
```

Use VS Code to open the jupyter notebook in the path `EECS6446_Project/notebooks/experiment.ipynb`.
This notebook includes an integration of all the components we have deployed
to our cluster and uses their respective APIs to get monitoring/actionable data.

