# KubeArchive

KubeArchive is a system that archives Kubernetes objects to permanent storage, which can then be retrieved through an API.
This project was inspired by [Tekton Results](https://github.com/tektoncd/results).

You can check the KubeArchive introduction and demos in:
* [The lightning talk from DevConf.CZ 2024 (15 min)](https://www.youtube.com/watch?v=3N1ut_0hHBo)
* [The presentation with Demo from DevConf.US 2024 (35 min)](https://www.youtube.com/watch?v=YR6jqPv_jaw)

## Documentation

Take a look to our docs in https://kubearchive.github.io/kubearchive

## Architecture

![kubarchive architecture diagram](/profile/arch-diagram.jpg)

KubeArchive consists of the following components:

- Custom Resource Definitions for `NamespaceKubeArchiveConfig`, `ClusterKubeArchiveConfig`, `NamespaceVacuumConfig`, and
`GlobalVacuumConfig`
- An operator that contains the following controllers:
  - A controller that reconciles `NamespaceKubeArchiveConfig` Custom Resources to update a SinkFilters CR
  - A controller that reconciles `ClusterKubeArchiveConfig` Custom Resources to update a
SinkFilters CR
  - A controller that reconciles `SinkFilters` CR to create watchers that receive resource updates from the Kubernetes API
and sends the resources to a sink
- Vacuums that run periodically to send cloud events to the sink based on configuration from `ClusterVacuumConfigs` and
`NamespaceVacuumConfigs`
- A sink that receives the cloud events and write the resource information in a DB
- A REST API server that facilitates the retrieval of the archived data as well as logs from configured logging backends
- A CLI that queries both the KubeArchive REST API and the k8s API to expose the resources

The user interacts with KubeArchive by:

- Configuring ***what*** to archive and ***when*** to archive it in through [KubeArchiveConfig CR](https://kubearchive.github.io/kubearchive/main/configuration/kubearchiveconfig.html)
- Querying the KubeArchive API to get archived resources
- Querying the archived and the cluster data through the CLI

## Database Model

![kubearchive_db_model](/profile/db-model.jpg)
