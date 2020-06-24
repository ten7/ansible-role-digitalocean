# Ansible Role: digitalocean

Create various resources on the Digital Ocean platform.

## Requirements

* You must have a Digital Ocean API key.
* The Digital Ocean API must be accessible.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`).

### API Token

```
digitalocean_api_token: '1234567890abscdefg'
```

Your Digital Ocean API token.

### Creating projects

```yaml
digitalocean_projects:
  - name: 'MYPROJECT'
    purpose: 'Website or blog'
    description: 'My project'
    environment: 'Development'
  - name: 'OTHERPROJECT'
```

An array of Digital Ocean projects to create with the following items:

* **name**: The project name.
* **purpose**: Optional. The project purpose, must be a valid purpose type.
* **description**: Optional. The project description.
* **environment**: Optional. The Digtial Ocean envionment, must be `Produciton`, `Staging`, or `Development`.

### Kubernetes Clusters

```yaml
digitalocean_clusters:
  - name: "my-k8s"
    region: "sfo2"
    version: "1.13.2-do.1"
    tags:
      - "live"
    node_pools:
      - name: "default-pool"
        size: "s-1vcpu-2gb"
        count: 3
```

An array of Kubernetes clusters to create on Digital Ocean, each item representing a single cluster.

* **name**: The name of the cluster.
* **region**: The Digital Ocean region slugline in which to create the cluster.
* **version**: Optional. The Digital Ocean slugline of the Kubernetes version to use.
* **tags**: Optional. An array of tags to apply to the cluster.
* **node_pools**: Optional. The node pools to add to the cluster. If not specified, a default pool of 3 of the smallest node sizes available.

### Kubernetes node labels

This role can also apply k8s labels to a Digital Ocean node pool:

```yaml
digitalocean_clusters:
  - name: "my-k8s"
    region: "sfo2"
    version: "1.13.2-do.1"
    tags:
      - "live"
    node_pools:
      - name: "default-pool"
        size: "s-1vcpu-2gb"
        count: 3
        labels:
          - key: "app"
            value: "web"
```

### Managing node pools

By default, this role will create new node pools, and update existing node pools as they are described under `digitalocean_clusters.node_pools`. You can also be explicit by specifying the `state` item:

```yaml
digitalocean_clusters:
  - name: "my-k8s"
    region: "sfo2"
    version: "1.13.2-do.1"
    tags:
      - "live"
    node_pools:
      - name: "default-pool"
        state: present
        size: "s-1vcpu-2gb"
        count: 3
        labels:
          - key: "app"
            value: "web"
```

This role will also delete node pools listed with a `state` of `absent`:

```yaml
digitalocean_clusters:
  - name: "my-k8s"
    region: "sfo2"
    version: "1.13.2-do.1"
    tags:
      - "live"
    node_pools:
      - name: "default-pool"
        state: present
        size: "s-1vcpu-2gb"
        count: 3
        labels:
          - key: "app"
            value: "web"
      - name: "ye-olde-pool"
        state: absent
```

### Creating droplets

```yaml
digitalocean_droplets:
  - name: 'MYDROPLET'
    size: s-4vcpu-8gb
    region: tor1
    image: ubuntu-18-04-x64
    backups: yes
    tags:
      - "live"
```

An array of Digital Ocean droplets to create with the following items:

* **name**: The human-readable name set for the Droplet instance.
* **size**: The unique slug identifier for the size of this Droplet.
* **region**: The slug identifier of the region that the Droplet instance is deployed in.
* **image**: The slug identifier of the base image used to create the Droplet instance.
* **backups**: Optional. Whether to enable automated backups.
* **tags**: Optional. An array of tags to apply to the droplet.

## Dependencies

None, but the following roles are recommended:

* All requirements for the k8s module.

## Example Playbook

    ---
    - hosts: servers
      vars:
        digitalocean_api_token: '1234567890abscdefg'
        digitalocean_projects:
          - name: 'MYPROJECT'
        digitalocean_clusters:
          - name: "my-k8s"
            region: "sfo2"
            version: "1.13.2-do.1"
            tags:
              - "live"
            node_pools:
              - name: "default-pool"
                state: present
                size: "s-1vcpu-2gb"
                count: 3
      roles:
         - socketwench.digitalocean

## License

GPL v3

## Author Information

This role was created by [socketwench](https://deninet.com/) for [TEN7](https://ten7.com).
