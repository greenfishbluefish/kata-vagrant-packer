# -*- mode: ruby -*-
# vi: set ft=ruby :

# This is a Ruby script, with all the capabilities of Ruby. The first two lines,
# or "modelines", set the syntax highlighting for emacs and vim.

# Vagrantfiles are just Ruby scripts, so you can define functions and use all
# the other Ruby capabilities. In this case, a function to ensure that all users
# of this Vagrantfile have the necessary plugins installed.
def require_plugin(name, version='>= 0')
  unless Vagrant.has_plugin?(name, version)
    puts "'#{name}' is not installed!"
    puts ''
    puts "to install:\nvagrant plugin install #{name}"

    # exit with extreme urgency, skipping a redundant error message and Vagrant politeness
    exit! 1
  end
end

# Specify a minimum Vagrant version everyone using this Vagrantfile must use.
Vagrant.require_version('>= 1.9.0')

require_plugin 'vagrant-berkshelf'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (Vagrant supports older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure('2') do |config|
  # This is a provider block. It defines how Vagrant will work when you specify
  # a specific provider. When making a VM, Vagrant use the one provider you
  # choose to boot the machine, then run all the provisioners against it. You
  # can define as many providers as you want, but only one will be used. (You
  # cannot boot the same VM with both Virtualbox and VMWare at the same time.)
  #
  # Vagrant supports dozens of providers, from local virtualization (like
  # Virtualbox) to on-prem virtualization (like VSphere) to cloud providers
  # (like AWS and Azure). Not every feature will function exactly the same on
  # every provider, such as file synchronization. Please read the documentation
  # for each provider carefully before choosing to use one over the other.
  #
  # q.v. https://www.vagrantup.com/docs/virtualbox/
  config.vm.provider :virtualbox do |vb, override|
    override.vm.box = 'ubuntu/trusty64'

    vb.cpus = 1
    vb.memory = 2 * 1024 # This is denominated in MB, so 2GB
  end

  # This line enables the Vagrant-Berkshelf integration.
  #
  # https://supermarket.chef.io/tools/vagrant-berkshelf
  config.berkshelf.enabled = true

  # This is a provisioner block. It defines a step to take when modifying a VM
  # from its initial boot-up state to something that is "fit for purpose." The
  # provisioning steps are taken in order of definition and each has to complete
  # successfully before the next can succeed.
  #
  # q.v. https://www.vagrantup.com/docs/provisioning/
  # For documentation on using Chef in Vagrant, see:
  #   * https://www.vagrantup.com/docs/provisioning/chef_solo.html
  #   * https://www.vagrantup.com/docs/provisioning/chef_common.html
  config.vm.provision :chef_solo do |chef|
    # Vagrant will install the Chef client into the VM for us, if it isn't
    # already there. These three variables control how the Chef client is
    # installed and updated.
    chef.install = true    # Default value
    chef.channel = :stable # Default value
    chef.version = :latest # Default value

    # These control where Chef looks for cookbooks and roles we've created.
    # Like most paths in Vagrant, these are relative to the directory the
    # Vagrantfile is in.
    #
    # Cookbooks provide a place to keep similar functionality together. In Chef,
    # these are called recipes. A recipe is a collection of Chef resources that
    # do a specific thing. For example, a cookbook for Apache would contain a
    # bunch of recipes for setting up different types of Apache configurations.
    # Each recipe would do multiple things, like writing files, installing
    # packages, and the like.
    #
    # https://docs.chef.io/cookbooks.html
    chef.cookbooks_path = "devops/provisioning/chef/cookbooks"

    # This is the recipe that will output "Hello" when "vagrant up" or
    # "vagrant provision" is run. Use this role as a model for building up your
    # answer to this kata.
    #
    # Specifically, this adds the "default" recipe within the "acme" cookbook.
    # Look at devops/provisioning/chef/cookbooks/acme/recipes/default.rb
    #
    # https://docs.chef.io/recipes.html
    chef.add_recipe "acme::default"

    # Roles provide a way of grouping recipes together for a specific purpose.
    # While cookbooks are often generic and can be reused across organizations,
    # roles are usually specific to a project. A role is how *this* team builds
    # *this* type of thing. Common roles include "database", "backend", or even
    # for specific services.
    #
    # https://docs.chef.io/roles.html
    chef.roles_path = "devops/provisioning/chef/roles"

    # Roles don't have namespaces like cookbooks and recipes. Instead, they're
    # just a single name.
    chef.add_role "example"

    # We can specify specific configuration for use within the Vagrant
    # environment. For example, this is usually a developer's environment, so
    # development configuration values (vs. QA or production). These values are
    # used by recipes to configure files and packages correctly. For example,
    # an attribute could be the location of a database server which would be
    # written to a configuration file. Each environment would have a different
    # value and that different would be in the Chef attributes.
    #
    # In this example, the apache2 listen_ports would override the defaults in
    # the example role.
    #
    # https://docs.chef.io/attributes.html
    # chef.json = {
    #   'apache2' => {
    #     'listen_ports' => [ 8080 ],
    #   },
    # }
  end
end
