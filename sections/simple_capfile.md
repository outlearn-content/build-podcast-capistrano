  1. We will SSH into our local machine - i know it's very meta, but it's simple (rather than setting up another remote machine or EC2, etc)!
  2. Go to `System Preferences > Sharing > Check Remote Login` and take note of the SSH command e.g. `ssh [[email protected]](/cdn-cgi/l/email-protection)` and the corresponding IP address `10.0.1.22`
  3. Hosts can ofcourse be `www.example.com` or in this case our own local machine `10.0.1.22`
  4. Create a file named `Capfile` with the following contents and run in the command line `cap list_files`:

        task :list_files, :hosts => "10.0.1.22" do
      run "ls"
    end


  5. Let's add in a second task and run `cap whois_user` or `cap list_files`:

        task :whois_user, :hosts => "10.0.1.22" do
      run "echo $USER"
    end

    task :list_files, :hosts => "10.0.1.22" do
      run "ls"
    end


  6. Specifying the host over and over again is not efficient

        role :libs, "10.0.1.22"

    task :whois_user do
      run "echo $USER"
    end

    task :list_files do
      run "ls"
    end


  7. We can also specify, multiple-servers now. `cap whois_user` will be execute in both the servers!

        role :libs, "10.0.1.22", "www.example.com"

    task :whois_user do
      run "echo $USER"
    end

    task :list_files do
      run "ls"
    end


  8. We can also specify different groups of hosts e.g. `analytics` and `information`:

        role :info, "10.0.1.22", "10.0.1.20"
    role :analytics, "10.0.1.22"

    task :whois_user, :roles => :info do
      run "echo $USER"
    end

    task :list_files, :roles => :info do
      run "ls"
    end

    task :show_free_space, :roles => :analytics do
      run "df -h /"
    end


  9. `cap -T` command will give all the task list with descriptions

  10. Include Task list with descriptions and then run `cap -T`:

        role :info, "10.0.1.22", "10.0.1.20"
    role :analytics, "10.0.1.22"

    desc "The Current Username"
    task :whois_user, :roles => :info do
      run "echo $USER"
    end

    desc "List Files in the home Directory"
    task :list_files, :roles => :info do
      run "ls"
    end

    desc "Show Free Spaces"
    task :show_free_space, :roles => :analytics do
      run "df -h /"
    end


  11. Simultaneously run commands in all servers:

        cap invoke COMMAND="ls"
