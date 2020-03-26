# Test framework

-----

## Environments

Most integrations monitor services like databases or web servers, rather than system properties like CPU usage.
For such cases, you'll want to spin up an environment and gracefully tear it down when tests finish.

We define all environment actions in a [fixture](plugins.md#environment-manager) called `dd_environment` that
looks semantically like this:

```python
@pytest.fixture(scope='session')
def dd_environment():
    try:
        set_up_env()
        yield some_default_config
    finally:
        tear_down_env()
```

This is not only used for regular tests, but is also the basis of our [E2E testing](../e2e.md). The
[start](cli.md#start) command executes everything before the `yield` and the [stop](cli.md#stop)
command executes everything after it.

We provide a few utilities for common environment types.

### Docker

The `docker_run` utility makes it easy to create services using [docker-compose](https://docs.docker.com/compose/).

```python
from datadog_checks.dev import docker_run

@pytest.fixture(scope='session')
def dd_environment():
    with docker_run(os.path.join(HERE, 'docker', 'compose.yaml')):
        yield ...
```

Read [the reference](#docker_1) for more information.

### Terraform

The `terraform_run` utility makes it easy to create services from a directory of [Terraform](https://www.terraform.io) files.

```python
from datadog_checks.dev.terraform import terraform_run

@pytest.fixture(scope='session')
def dd_environment():
    with terraform_run(os.path.join(HERE, 'terraform')):
        yield ...
```

Currently, we only use this for services that would be too complex to setup with Docker (like OpenStack) or
things that cannot be provided by Docker (like vSphere). We provide some ready-to-use cloud
[templates](https://github.com/DataDog/integrations-core/tree/master/datadog_checks_dev/datadog_checks/dev/tooling/templates/terraform)
that are available for referencing by default.

Terraform E2E tests are not run in our public CI as that would needlessly slow down builds.

Read [the reference](#terraform_1) for more information.

## Reference

### Docker

::: datadog_checks.dev.docker
    :docstring:
    :members: docker_run get_docker_hostname get_container_ip compose_file_active

### Terraform

```
::: datadog_checks.dev.terraform
    :docstring:
    :members: terraform_run
```
