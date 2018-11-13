OSE-Quota
=========

Manages and openshift project and quota defaults post deployment. 

Requirements
------------

Run in an environment that has oc client set up and access to openshift as admin, deploys templates to {{ openshift_asset_dir }} before loading them in to openshift. oc login is done either in a seperate role or manually before hand.

Role Variables
--------------

This role has two main vars project_resources and project_resources_defaults. 

**project_resources_defaults** is used for the project template and the quota all projects not defined in *project_resources* get. The set of projects this is applied is determined dynamically at runtime.

    project_resources_defaults:
      quota:
        persistentvolumeclaims: '10'
        requests_storage: '50Gi'
        pods: '20'
        services: '20'
        secrets: '100'
        cpu: '10'
        memory: '20480Mi'
        limits_cpu: '10'
        limits_memory: '20Gi'
      limits:
        pod:
          max_cpu: '10'
          min_cpu: '10m'
          max_memory: '20Gi'
          min_memory: '6Mi'
        container:
          max_cpu: '10'
          min_cpu: '10m'
          default_cpu: '1'
          default_request_cpu: '200m'
          max_memory: '20Gi'
          min_memory: '6Mi'
          default_memory: '4096Mi'
          default_request_memory: '1024Mi'
  
**project_resources**. The complete set of all non default projects and the values that deviate from project_resources_defaults. To create a new quota flavour just define a unique key ('large' and 'larger' in this case) and set of projects to apply to it. If a project is defined that doesnt exist in the cluster the default behaviour is to fail before making any changes.

    project_resources:
    # the minimum required to define a quota, identical to default bar the name. 
      mindef:
        projects:
          - blah
        quota: {}
        limits:
          pod: {}
          container: {}
    # this quota is the same as default except for more cpu
      larger:
        projects:
          - blah
        quota:
          limits_cpu: '18'
        limits:
          pod: {}
          container: {}
    


Dependencies
------------

Openshift is already running and admin user exists. The project template does nothing without having projectRequestTemplate set in the master-config. see https://westworld.usersys.redhat.com:8090/bitbucket/projects/PSO/repos/ose-master-config/browse
 
Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: api-config
      tags:
        - load_templates
      roles:
         - ose-templates

License
-------

BSD

Author Information
------------------

- [Leigh Jenkin](https://intranet.cbr.lab/search/people/lxjenki)

