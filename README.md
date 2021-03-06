**Clone smart-proxy**
```shell
git clone https://github.com/theforeman/smart-proxy
```
Configure smart proxy

**Clone all the repositories**
```shell
for repo in smart_proxy_dynflow smart_proxy_remote_execution_ssh; do
  git clone https://github.com/theforeman/$repo ${repo}
done
```


**In smart-proxy directory**
```shell
mkdir logs
```

Then add a line that contains `:log_file: logs/proxy.log` to file `config/settings.yml`

Configure `smart_proxy_dynflow` and `smart_proxy_remote_execution_ssh` as usually
```bash
cat > config/settings.d/dynflow.yml <<EOF
---
:enabled: true
EOF

cat > config/settings.d/remote_execution_ssh.yml <<EOF
---
:enabled: true
EOF
```


### All-in-one solution
Add all the gems to smart-proxy's bundler.d from local checkouts.
All comands are started from the smart-proxy's directory
```shell
cat <<-END > bundler.d/dynflow.local.rb
gem 'smart_proxy_dynflow', :path => '../smart_proxy_dynflow'
gem 'smart_proxy_dynflow_core', :path => '../smart_proxy_dynflow'
gem 'smart_proxy_remote_execution_ssh', :path => '../smart_proxy_remote_execution_ssh'
gem 'smart_proxy_remote_execution_ssh_core', :path => '../smart_proxy_remote_execution_ssh'
END
```


Install the gems and start smart proxy
```shell
bundle install
bundle exec bin/smart-proxy
```

Your smart proxy should now be usable


### The separate dynflow way
All comands are started from the smart-proxy's directory
```shell
cat <<-END > bundler.d/dynflow.local.rb
gem 'smart_proxy_dynflow', :path => '../smart_proxy_dynflow'
gem 'smart_proxy_remote_execution_ssh', :path => '../smart_proxy_remote_execution_ssh'
END
```

Update the smart proxy config/settings.d/dynflow.yml with the url of core if it's not running on localhost:8008, this is needed if TLS is involved.

Install smart proxy gems and start it
```shell
bundle install
bundle exec bin/smart-proxy
```

Following commands are started from smart_proxy_dynflow folder

Symlink smart_proxy_remote_execuiton_ssh's config from smart-proxy to smart_proxy_dynflow, note the name change
```shell
mkdir config/settings.d
ln -s ../../../smart-proxy/config/settings.d/remote_execution_ssh.yml config/settings.d/smart_proxy_remote_execution_ssh_core.yml
```

Copy smart_proxy_dynflow_core example config and optionally edit it manually
```shell
cp config/settings.yml{.example,}
```

Add the smart_proxy_remote_execution_ssh gem to `Gemfile.local.rb`
```shell
echo "gem 'foreman_remote_execution_core', :path => '../foreman_remote_execution'" >> Gemfile.local.rb
```

Install smart proxy dynflow core's gems and start it
```shell
bundle install
bundle exec bin/smart_proxy_dynflow_core
```

If you want to use TLS, configure certificates that smart proxy uses for communication with Foreman.




