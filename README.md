Satellite Deploy
=========

Ansible playbook to install and config a satellite server and its capsules.

Requirements
------------

This role is tested on Ansible 2.9.

Dependencies
------------
N/A

Encrypted variables
----------------
All encrypted variabled should be placed at "{{ orgs_dir }}/configure_foreman_katello_credentials.yml"

```
cat < EOF > orgs_vars/configure_foreman_katello_credentials.yml 
---
vault_foreman_admin_username: "admin"
vault_foreman_admin_password: "redhat00"
vault_rhn_user: redhat-support-active-account
vault_rhn_pwd: p4$$W0rD
vault_rhn_pool_id: 01123581321245589144233377610987
vault_ldap_account_password: redhat00
EOF
```
Encrypted with vault

```
ansible-vault create orgs_vars/configure_foreman_katello_credentials.yml
ansible-vault encrypt orgs_vars/configure_foreman_katello_credentials.yml
ansible-vault edit orgs_vars/configure_foreman_katello_credentials.yml
```

Playbooks: 
-----------------

- playbooks/prerequisites.yml. 
The variables needed are:
    - "{{ dir_orgs_vars }}/configure_foreman_katello_credentials.yml"
    - "{{ dir_orgs_vars }}/configure_foreman_katello_general.yml"
    - "{{ dir_orgs_vars }}/configure_{{ scenario }}_prerequisites.yml"

- playbooks/install-monitoring.yml
```
$ ansible-playbook -i inventories/inventory -l satellite.bcnconsulting.com playbooks/install-monitoring.yml --tags pcp_packages,pcp_data_collection
```

- playbooks/config-satellite.yml
```
$ ansible-playbook playbooks/config-satellite.yml --tags settings -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags organizations -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}' 
$ ansible-playbook playbooks/config-satellite.yml --tags locations -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}' 
$ ansible-playbook playbooks/config-satellite.yml --tags ldap -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}' 
$ ansible-playbook playbooks/config-satellite.yml --tags manifest -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags external_usegroups -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags lifecycles -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags repositories -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags sync_plans -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags content_views -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags activation_keys -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
$ ansible-playbook playbooks/config-satellite.yml --tags host_collections -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars}'
```

Content Views PUBLISH 
---------------------
Content views will be published on Library environment, this way could be promoted by any other environment. By default, Content Views will be purged except last 3 published. It can be changed with the variable "content_views_purge_count".

##### Publish BY cvtype: [os, app,infra] AND cvname: [os_release, app_name, redhat_product]

```
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: os, cvname: rhel7, env: Library, content_views_purge_count: 6}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: app, cvname: jws, env: Library, content_views_purge_count: 6}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: infra, cvname: satcapsules, env: Library, content_views_purge_count: 6}' 
```

##### Publish BY cvtype: [os, app,infra] AND cvname: [ALL]
```
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: os, cvname: all, env: Library, content_views_purge_count: 6}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: app, cvname: all, env: Library, content_views_purge_count: 6}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: infra, cvname: all, env: Library, content_views_purge_count: 6}' 
```

Compositive Content Views PUBLISH 
---------------------------------
##### Publish BY cvtype: [ccv] AND cvname: [ALL]
```
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: ccv, cvname: all, env: Library, content_views_purge_count: 6}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: publish, cvtype: ccv, cvname: all, env: Library, content_views_purge_count: 6}' 
```

Content Views PROMOTE 
---------------------
##### Promote BY env: [InfraTest,InfraProd] AND cvtype: [os,infra] AND cvname: [ALL]
```
$ ansible-playbook playbooks/config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../..orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: all}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: InfraTest}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: InfraProd}'

$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: infra, cvname: all, env: InfraTest}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: infra, cvname: all, env: InfraProd}'
```

##### Promote BY env: [Development,UAT,Integration,Preproduction,Production] AND cvtype: [os,app] AND cvname: [ALL]
```
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: Development}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: UAT}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: Integration}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: Preproduction}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: os, cvname: all, env: Production}'

$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: app, cvname: all, env: Development}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: app, cvname: all, env: UAT}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: app, cvname: all, env: Integration}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: app, cvname: all, env: Preproduction}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: app, cvname: all, env: Production}'
```

Compositive Content Views PROMOTE 
---------------------------------
##### Promote BY env: [InfraTest,InfraProd,Development,UAT,Integration,Preproduction,Production] AND cvtype: [app,infra] AND cvname: [ALL]
```
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: infra, env: InfraTest}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: infra, env: InfraProd}'

$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: app, env: Development}' 
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: app, env: UAT}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: app, env: Integration}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: app, env: Preproduction}'
$ ansible-playbook config-satellite.yml --tags cv_publish_promote -e '{orgs: red_ribbon, dir_orgs_vars: ../orgs_vars, cvaction: promote, cvtype: ccv, cvname: app, env: Production}'
```

Variables 
----------

Variables Must be set in yaml files with follow structure: 

```
{{ dir_orgs_vars }}/{{ orgs }}/configure_{{ foreman/katello }}_{{ entity }}.yml

```

Vars files example:
```
orgs_vars/
├── configure_foreman_katello_credentials.yml
├── configure_foreman_katello_general.yml
└── red_ribbon
    ├── configure_foreman_external_usergroup.yml
    ├── configure_foreman_ldap.yml
    ├── configure_foreman_locations.yml
    ├── configure_foreman_organization.yml
    ├── configure_foreman_settings.yml
    ├── configure_katello_activation_keys.yml
    ├── configure_katello_host_collections.yml
    ├── configure_katello_lifecycle_environments.yml
    ├── configure_katello_repositories.yml
    ├── configure_katello_sync_plans.yml
    └── cv
        ├── configure_katello_content_views.yml
        ├── promote
        │   ├── app
        │   │   ├── configure_katello_cv_versions_promote_app_all_Development.yml
        │   │   ├── configure_katello_cv_versions_promote_app_all_Integration.yml
        │   │   ├── configure_katello_cv_versions_promote_app_all_Preproduction.yml
        │   │   ├── configure_katello_cv_versions_promote_app_all_Production.yml
        │   │   └── configure_katello_cv_versions_promote_app_all_UAT.yml
        │   ├── ccv
        │   │   ├── configure_katello_cv_versions_promote_ccv_app_Development.yml
        │   │   ├── configure_katello_cv_versions_promote_ccv_app_Integration.yml
        │   │   ├── configure_katello_cv_versions_promote_ccv_app_Preproduction.yml
        │   │   ├── configure_katello_cv_versions_promote_ccv_app_Production.yml
        │   │   ├── configure_katello_cv_versions_promote_ccv_app_UAT.yml
        │   │   ├── configure_katello_cv_versions_promote_ccv_infra_InfraProd.yml
        │   │   └── configure_katello_cv_versions_promote_ccv_infra_InfraTest.yml
        │   ├── infra
        │   │   ├── configure_katello_cv_versions_promote_infra_all_InfraProd.yml
        │   │   └── configure_katello_cv_versions_promote_infra_all_InfraTest.yml
        │   └── os
        │       ├── configure_katello_cv_versions_promote_os_all_Development.yml
        │       ├── configure_katello_cv_versions_promote_os_all_InfraProd.yml
        │       ├── configure_katello_cv_versions_promote_os_all_InfraTest.yml
        │       ├── configure_katello_cv_versions_promote_os_all_Integration.yml
        │       ├── configure_katello_cv_versions_promote_os_all_Preproduction.yml
        │       ├── configure_katello_cv_versions_promote_os_all_Production.yml
        │       └── configure_katello_cv_versions_promote_os_all_UAT.yml
        └── publish
            ├── app
            │   ├── configure_katello_cv_versions_publish_app_all_all.yml
            │   ├── configure_katello_cv_versions_publish_app_all_Library.yml
            │   └── configure_katello_cv_versions_publish_app_jws_Library.yml
            ├── ccv
            │   ├── configure_katello_cv_versions_publish_ccv_all_all.yml
            │   ├── configure_katello_cv_versions_publish_ccv_jws_all.yml
            │   ├── configure_katello_cv_versions_publish_ccv_jws_Library.yml
            │   ├── configure_katello_cv_versions_publish_ccv_sat6-capsule_all.yml
            │   └── configure_katello_cv_versions_publish_ccv_sat6-capsule_Library.yml
            ├── infra
            │   ├── configure_katello_cv_versions_publish_infra_all_all.yml
            │   ├── configure_katello_cv_versions_publish_infra_all_Library.yml
            │   └── configure_katello_cv_versions_publish_infra_sat6-capsules_Library.yml
            └── os
                ├── configure_katello_cv_versions_publish_os_all_all.yml
                ├── configure_katello_cv_versions_publish_os_all_Library.yml
                └── configure_katello_cv_versions_publish_os_rhel7_Library.yml
```

License
-------

GPLv3

Author Information
------------------
silvinux - silvinux7@gmail.com
