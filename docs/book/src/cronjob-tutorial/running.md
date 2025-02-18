# Running and deploying the controller

To test out the controller, we can run it locally against the cluster.
Before we do so, though, we'll need to install our CRDs, as per the [quick
start](../TODO.md).  This will automatically update the YAML manifests
using controller-tools, if needed:

```bash
make install
```

Now that we've installed our CRDs, we can run the controller against our
cluster.  This will use whatever credentials that we connect to the
cluster with, so we don't need to worry about RBAC just yet.

Note that if you have a webhook and want to deploy it locally, you need to
ensure the certificates are in the right place.

In a separate terminal, run

```bash
make run
```

You should see logs from the controller about starting up, but it won't do
anything just yet.

At this point, we need a CronJob to test with.  Let's write a sample to
`config/samples/batch_v1_cronjob.yaml`, and use that:

```yaml
{{#include ./testdata/project/config/samples/batch_v1_cronjob.yaml}}
```

```bash
kubectl create -f config/samples/batch_v1_cronjob.yaml
```

At this point, you should see a flurry of activity.  If you watch the
changes, you should see your cronjob running, and updating status:

```bash
kubectl get cronjob.batch.tutorial.kubebuilder.io -o yaml
kubectl get job
```

Now that we know it's working, we can run it in the cluster. Stop the
`make run` invocation, and run

```bash
make docker-build docker-push IMG=<some-registry>/controller
make deploy
```

If we list cronjobs again like we did before, we should see the controller
functioning again!
