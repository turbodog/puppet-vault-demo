# Agent lookups with vault

This repo contains a docker-compose setup that will run a Vault container that integrates with the PE master it expects to be running on the same machine

After you have cloned this repo, update the github submodules by running
`git submodule update --init`.

The demo assumes: 
* Everything is running on the same machine, e.g. Vagrant boxes
* A running Puppet Enterprise deployment is on the same machine and is running off Puppet 6. Open source Puppet Server should work too.
* Rich data is turned on, i.e. `rich_data=true` in `/etc/puppetlabs/puppet/puppet.conf`. Restart puppet server after making this change.
* `puppet cert generate vault.docker` certs for the Vault container to use
* Put the Vault demo function in `/opt/puppetlabs/puppet/lib/ruby/vendor_ruby/puppet/functions/vault_lookup.rb` on every node you want to access Vault from
* `/opt/puppetlabs/puppet/bin/gem install vault` on every node you want to access Vault from
* Add `vault.docker` to `/etc/hosts` on every node you want to access Vault from to point to localhost

To run through the demo:
* `docker-compose up` to bring up Vault
* `docker-compose exec vault /unseal.sh` to unseal Vault
* Classify your node with Puppet code that calls vault_lookup and run Puppet on it, e.g. `/etc/puppetlabs/code/environments/production/manifests/site.pp` containing
```
$d = Deferred('vault_lookup',["secret/test"])

node default {
  notify { example :
    message => $d
  }
}
```

Key pieces of the proof of concept are the [Puppet function that calls Vault](https://github.com/puppetlabs/puppet/blob/6e4452cd75f76249bbaa7ad546924833fc1a2b4c/lib/puppet/functions/vault_lookup.rb) and the [Puppet code that fetches a secret from Vault](https://github.com/puppetlabs/puppet-vault-demo/blob/master/code/environments/production/manifests/init.pp)
