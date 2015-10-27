# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "scotch/box"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.hostname = "scotchbox"
    config.vm.synced_folder ".", "/var/www", :mount_options => ["dmode=777", "fmode=666"]
    
    # Optional NFS. Make sure to remove other synced_folder line too
    #config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    if Vagrant.has_plugin? 'vagrant-triggers'

	  	db_name  = "scotchbox"
		db_user  = "root"
		db_pass  = "root"

	  	# Importing Database
	  	{
	      [:up, :resume, :reload] => "vagrant ssh -c 'cd /var/www/
	      sql_count=`ls -1 #{db_name}.sql 2>/dev/null | wc -l`
	      if [ $sql_count != 0 ]
	      then
	        echo Database file exists, so start importing
	        mysql -u #{db_user} -p#{db_pass} #{db_name} < /var/www/#{db_name}.sql
	      else
	        echo Database file does not exist  
	      fi'",
	    }.each do |command, trigger|
	      config.trigger.after command, :stdout => true do
	        info "Importing database #{db_name}"
	        run  "#{trigger}" 
	      end
	    end
	  
	  	# Exporting Database
		{
		  [:halt, :suspend, :destroy] => "vagrant ssh -c 'mysqldump -u #{db_user} -p#{db_pass} #{db_name} > /var/www/#{db_name}.sql'",
		}.each do |command, trigger|
		  config.trigger.before command, :stdout => true do
		    info "Backing up #{db_name}"
		    run  "#{trigger}"
		  end
		end  
	else
	  puts 'vagrant-triggers missing, please install the plugin:'
	  puts 'vagrant plugin install vagrant-triggers'
	end

end
