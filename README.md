## **SSH-Key Action**

This action installs SSH key in `~/.ssh`.  
Useful for rsync and SCP over SSH in deployment script.

### **Usage**

---

Add your SSH `private_key` to your secret and your `known_hosts` to your variables.
1. Add your SSH `private_key` to your secrets by clicking `Settings` -> `Actions secrets and variables` -> `New repository secret`.
2. Add your `known_hosts` to your variables by clicking `Settings` -> `Actions secrets and variables` -> `Variables` -> `New repository variable`

```yml
runs-on: ubuntu-latest
steps:
- name: Install SSH key
  uses: datamole-ai/core-actions-ssh-init@v1
  with:
    private_key: "${{ secrets.SSH_PRIVATE_KEY }}"
    known_hosts: "${{ vars.SSH_KNOWN_HOSTS }}"
- name: rsync over SSH
  run: rsync -r ./foo/ user@remote:bar/
```
See [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) for details.


### **NOTE:**

* Server key of github.com will be always set to known_hosts.
* SSH keys will be removed at the end of workflow.  
  

### **Q&A:**

---

### SSH failed even though key has been installed.

* Host key verification failed.
  * Set `known_hosts` parameter correctly (use `ssh-keyscan` command).

### How do I use encrypted SSH key?

This action doesn't support encrypted key directly. Here are some solutions:

* decrypting key beforehand: best bet, and works on any VM
* `sshpass` command: next best bet, but not supported on Windows
* `expect` command: be careful not to expose passphrase to console
* `SSH_ASKPASS` environment variable: might be troublesome

### Which one is the best way for transferring files, SCP or rsync?

`rsync` is recommended. `SCP` is [deprecated by OpenSSH](https://www.openssh.com/txt/release-8.0) due to outdated and inflexible protocol.

```bash
rsync -r ./foo/ user@remote:bar/
```

### Can I omit known_hosts?

Omitting `known_hosts` is NOT secure and for that reeason not possible.
