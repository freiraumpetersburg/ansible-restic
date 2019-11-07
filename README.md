# Ansible Restic

## Configuration

Make sure to specify `vault_restic_password` in your ansible vault.

To configure your desired restic repositories, use the `restic_repos` variable. E.g.

```yml
restic_repos:
  - name: nextcloud
    url: "{{ restic_home }}/backup/nextcloud"
    password: "{{ restic_password }}"
    init: True
    jobs:
      - at: '0 5 * * *'
        type: 'db_mysql'
        arg: 'nextcloud_database_name'
        tags:
          - database
      - at: '30 5 * * *'
        type: 'files'
        user: 'restic'
        folders:
          - "/path/to/the/data"
      - at: '0 6 * * 1'
        type: 'forget'
        last: 3
        daily: 7
        weekly: 4
        monthly: 12
        yearly: 1
```

## Trigger backup from other roles

Sometimes you need to manually trigger a backup from a different role, e.g. before an upgrade.
Pass the name of the repository via the `restic_instant_backup_repo` variable. Only local repositories are supported at the moment.

```yml
- name: Trigger restic backup
  set_fact:
    restic_instant_backup_repo: "nextcloud"
  notify: "restic backup"
  changed_when: true

- meta: flush_handlers
```

## Credits

- This role was inspired by [ansible-restic](https://github.com/donat-b/ansible-restic) from [donat-b](https://github.com/donat-b) (licsensed under the [BSD 2-Clause](https://opensource.org/licenses/BSD-2-Clause))
